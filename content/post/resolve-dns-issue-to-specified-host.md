---
title: 'Resolving a Machine-Specific DNS Failure on Windows'
date: 2026-08-24T11:22:24+08:00
lastmod: 2026-08-24T11:22:24+08:00
cover: "images/banner.webp"
summary: "Learn how to diagnose and resolve a Windows DNS failure that affects one machine but not others on the same corporate network or VPN."
description: "A practical Windows troubleshooting guide for isolating a machine-specific DNS failure, comparing resolver configurations, and safely validating the correct DNS server."
---

# Resolving a Machine-Specific DNS Failure on Windows

## Scenario

One Windows PC could not open:

```text
https://service.example.internal
```

The browser reported a DNS error. Another user on the same corporate network or VPN could access the site.

The examples below are intentionally anonymized:

- Target hostname: `service.example.internal`
- Target DNS zone: `example.internal`
- Existing DNS server: `10.20.30.10`
- Working DNS server found on the other computer: `10.20.30.53`
- Network interface: `Wi-Fi`
- Internal comparison hostname: `gateway.corp.example`

Replace these placeholders with values supplied by your organization. Do not use an unfamiliar DNS server without approval.

## Investigation summary

The investigation established the following:

1. General DNS worked, so the PC did not have a complete DNS outage.
2. The affected hostname failed through the DNS servers assigned to the PC.
3. Other internal hostnames resolved, narrowing the problem to a particular DNS zone or resolver path.
4. Clearing the DNS cache and renewing DHCP did not help.
5. The other computer could access the same site under otherwise similar conditions.
6. Comparing both computers showed a DNS-server-list difference.
7. Querying the other computer's additional DNS server directly resolved the affected hostname.

This direct comparison proved that the missing resolver, rather than the browser or web server, explained the machine-specific behavior.

## Step 1: Reproduce the DNS failure

Use `Resolve-DnsName` to ask Windows’ DNS resolver to look up the hostname directly and show whether the name can be resolved before you test HTTPS connectivity. This helps confirm that the problem is DNS, not the browser or web server.

```powershell
Resolve-DnsName service.example.internal -ErrorAction Continue
```

`-ErrorAction Continue` tells PowerShell to display the DNS error but keep the command from stopping the whole script or session. That makes it easier to compare several resolution tests in sequence.

Then test whether Windows can resolve and connect to HTTPS:

```powershell
Test-NetConnection service.example.internal -Port 443 -InformationLevel Detailed
```

Typical failure indicators are:

```text
DNS server failure
The operation timed out
Name resolution failed
RemoteAddress:
```

At this point, an HTTPS test cannot succeed because Windows has no destination IP address.

## Step 2: Determine whether all DNS is broken

Resolve a known public hostname:

```powershell
Resolve-DnsName www.example.com -ErrorAction Continue
```

Also test another known internal hostname:

```powershell
Resolve-DnsName gateway.corp.example -ErrorAction Continue
```

Interpretation:

- If neither public nor internal names resolve, investigate the network connection, DNS service, VPN, or firewall generally.
- If public and other internal names resolve but the target does not, suspect a zone-specific DNS problem.

In this case, general and other internal resolution worked. That ruled out a total DNS outage.

## Step 3: Inspect the active DNS configuration

Show the complete network configuration:

```powershell
ipconfig /all
```

For a more focused PowerShell view:

```powershell
Get-DnsClientServerAddress -AddressFamily IPv4 |
    Select-Object InterfaceAlias, ServerAddresses |
    Format-Table -AutoSize
```

Identify:

- The active interface
- Its IPv4 address
- Its default gateway
- Its ordered DNS server list

Do not confuse the following fields:

- `DHCP Server` assigns network configuration.
- `DNS Servers` answer hostname queries.

A server appearing as the DHCP server is not automatically part of the DNS server list.

## Step 4: Query each assigned DNS server directly

Direct queries bypass normal resolver selection and reveal how each server handles the hostname:

```powershell
nslookup service.example.internal 10.20.30.10
```

PowerShell equivalent:

```powershell
Resolve-DnsName service.example.internal `
    -Server 10.20.30.10 `
    -ErrorAction Continue
```

If UDP behavior is unclear, test DNS over TCP:

```powershell
Resolve-DnsName service.example.internal `
    -Server 10.20.30.10 `
    -TcpOnly `
    -ErrorAction Continue
```

Possible outcomes include:

- A valid `A`, `AAAA`, or `CNAME` answer
- `NXDOMAIN`, meaning the server says the name does not exist
- `SERVFAIL`, meaning the server failed while resolving it
- A timeout, meaning the query or response did not complete

The assigned resolvers timed out or returned a server failure for the target, even though they could resolve unrelated names.

## Step 5: Eliminate common local causes

### Check the hosts file

Inspect:

```text
C:\Windows\System32\drivers\etc\hosts
```

PowerShell command:

```powershell
Get-Content "$env:SystemRoot\System32\drivers\etc\hosts"
```

Confirm that there is no stale or incorrect entry for the target hostname.

### Flush the DNS cache

```powershell
Clear-DnsClientCache
```

Alternative:

```powershell
ipconfig /flushdns
```

Retest:

```powershell
Resolve-DnsName service.example.internal -ErrorAction Continue
```

### Renew the DHCP lease

```powershell
ipconfig /renew "Wi-Fi"
```

Then inspect the assigned DNS servers again:

```powershell
Get-DnsClientServerAddress `
    -InterfaceAlias "Wi-Fi" `
    -AddressFamily IPv4
```

In this case, flushing the cache and renewing DHCP did not change the result. That made stale cache data unlikely.

## Step 6: Compare with another working computer

On both computers, run:

```powershell
ipconfig /all
```

Or use the focused command:

```powershell
Get-DnsClientServerAddress `
    -InterfaceAlias "Wi-Fi" `
    -AddressFamily IPv4 |
    Select-Object -ExpandProperty ServerAddresses
```

Compare:

- Network or VPN state
- Active interface
- DNS suffixes
- DNS server addresses
- DNS server order

### Why the DNS-list gap became the leading hypothesis

The other computer could access the site, while the affected computer could not. At the same time:

- Both had general network access.
- The affected PC resolved unrelated hostnames.
- The problem persisted after local cache and DHCP refreshes.
- The meaningful configuration difference was an additional DNS server on the working PC.

That suggested the missing server might know how to resolve the affected zone while the other assigned servers did not.

This was still only a hypothesis until tested directly.

## Step 7: Test the missing DNS server before changing anything

First confirm that DNS TCP port 53 is reachable:

```powershell
Test-NetConnection 10.20.30.53 `
    -Port 53 `
    -InformationLevel Detailed
```

Then query the target directly:

```powershell
nslookup service.example.internal 10.20.30.53
```

Or:

```powershell
Resolve-DnsName service.example.internal `
    -Server 10.20.30.53 `
    -ErrorAction Stop
```

Also test a general hostname through that server:

```powershell
Resolve-DnsName www.example.com `
    -Server 10.20.30.53 `
    -ErrorAction Continue
```

The important result was that the missing DNS server returned a valid chain such as:

```text
service.example.internal
  -> application.example-cloud.net
  -> edge.example-cdn.net
  -> 203.0.113.25
```

This proved:

- The hostname existed.
- The missing DNS server could resolve it.
- The site failure was caused by the resolver path on the affected PC.

## Step 8: Apply the DNS fix

Run PowerShell as Administrator.

Preserve the existing corporate resolvers as fallbacks, but place the verified working resolver first:

```powershell
Set-DnsClientServerAddress `
    -InterfaceAlias "Wi-Fi" `
    -ServerAddresses @(
        "10.20.30.53",
        "10.20.30.10",
        "10.20.30.11"
    )

Clear-DnsClientCache
```

Why preserve the existing servers?

- They may be required for other internal zones.
- They provide fallback availability.
- Replacing them with a public resolver could break internal resolution or violate company policy.

Avoid changing DNS to public services such as `8.8.8.8` or `1.1.1.1` on a managed corporate device unless explicitly approved.

## Step 9: Verify the configured DNS order

```powershell
Get-DnsClientServerAddress `
    -InterfaceAlias "Wi-Fi" `
    -AddressFamily IPv4 |
    Select-Object -ExpandProperty ServerAddresses
```

Expected example:

```text
10.20.30.53
10.20.30.10
10.20.30.11
```

## Step 10: Verify normal resolution

Do not specify `-Server` here. The purpose is to test Windows' normal resolver path after the configuration change:

```powershell
Resolve-DnsName service.example.internal -ErrorAction Stop
```

Verify that the output contains a final `A` or `AAAA` record.

## Step 11: Verify TCP connectivity

```powershell
Test-NetConnection service.example.internal `
    -Port 443 `
    -InformationLevel Detailed
```

Expected:

```text
RemoteAddress    : 203.0.113.25
RemotePort       : 443
TcpTestSucceeded : True
```

This confirms:

- DNS resolution succeeded.
- A route to the resolved address exists.
- The HTTPS port is reachable.

## Step 12: Verify the HTTP response

Use `curl` to request response headers:

```powershell
curl.exe -I --max-time 20 https://service.example.internal
```

A response such as the following proves that DNS, TCP, TLS, and HTTP are working:

```text
HTTP/1.1 401 Unauthorized
WWW-Authenticate: ...
```

For an authenticated corporate application, `401 Unauthorized` from a command-line request can be expected. It means the request reached the application or its authentication gateway, but no interactive browser credentials were supplied.

The important distinction is:

- DNS error: the hostname cannot be translated to an IP address.
- HTTP `401`: the server was reached and requires authentication.

After this verification, reopen or refresh the browser and sign in normally.

## Verification checklist

- [x] The configured working resolver appears first in the interface DNS list.
- [x] The target resolves without explicitly selecting a DNS server.
- [x] General public DNS still works.
- [x] Other required internal DNS still works.
- [x] TCP port 443 is reachable.
- [x] The web endpoint returns an HTTP response.
- [x] The browser reaches the normal authentication flow.

## Rollback

If DNS was originally supplied automatically by DHCP, restore automatic configuration with:

```powershell
Set-DnsClientServerAddress `
    -InterfaceAlias "Wi-Fi" `
    -ResetServerAddresses

Clear-DnsClientCache
```

Then verify:

```powershell
Get-DnsClientServerAddress `
    -InterfaceAlias "Wi-Fi" `
    -AddressFamily IPv4
```

## Root cause

The affected PC did not have the resolver that correctly answered for the target DNS zone. Its other assigned DNS servers handled general lookups but timed out or returned `SERVFAIL` for the target.

The decisive diagnostic was not merely noticing that the lists differed. It was querying the missing server directly and receiving a valid answer. After that server was placed first in the interface DNS list, normal Windows resolution, TCP connectivity, and the HTTP authentication response all succeeded.

## Compact command sequence

```powershell
# 1. Reproduce
Resolve-DnsName service.example.internal -ErrorAction Continue

# 2. Confirm general DNS works
Resolve-DnsName www.example.com -ErrorAction Continue

# 3. Inspect current DNS servers
Get-DnsClientServerAddress -AddressFamily IPv4

# 4. Test an assigned resolver directly
Resolve-DnsName service.example.internal -Server 10.20.30.10

# 5. Compare with a working PC, then test its additional resolver
Resolve-DnsName service.example.internal -Server 10.20.30.53

# 6. Configure the verified resolver first; run as Administrator
Set-DnsClientServerAddress `
    -InterfaceAlias "Wi-Fi" `
    -ServerAddresses @("10.20.30.53", "10.20.30.10", "10.20.30.11")
Clear-DnsClientCache

# 7. Verify through the normal resolver path
Resolve-DnsName service.example.internal -ErrorAction Stop
Test-NetConnection service.example.internal -Port 443
curl.exe -I --max-time 20 https://service.example.internal
```