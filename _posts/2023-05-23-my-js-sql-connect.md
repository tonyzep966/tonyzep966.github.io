---
title: Note - Connect to SQL Server from Node.js
date: 2023-05-23 10:33 +0800
categories: [Tech Note]
tags: [SQL Server, Node.js]
---

It sucks to use JavaScript to connect to SQL Server.
<!--more-->

## How to connect to SQL Server from Node.js, with MSSQL driver.

```typescript
const config: sql.config = {
  user: "local_dev",
  password: "abcd1234",
  // server: "localhost\\MSSQLSERVER22;",
  server: "localhost",
  database: "TONY",
  port: 1433,
  pool: {
    max: 10,
    min: 0,
    idleTimeoutMillis: 30000,
  },
  options: {
    instanceName: "MSSQLSERVER22",
    encrypt: false, // If for Azure, set to true
    trustServerCertificate: true,
    trustedConnection: true,
  },
};

try {
  const poolConnection = await sql.connect(config);
  const resultSet = await poolConnection
    .request()
    .query("select * from Customer");
  log.info(`${resultSet.recordset.length} rows returned`);

  let columns = "";
  for (const column in resultSet.recordset.columns) {
    columns += `${column}, `;
  }
  log.info(`Columns: ${columns}`);

  resultSet.recordset.forEach((row: any) => {
    log.info(`Row: ${JSON.stringify(row, null, 4)}`);
  });

  await poolConnection.close();
} catch (error) {
  if (error instanceof Error) {
    log.error(`Error: ${error.message}`);
  } else {
    log.error(`Error: ${error}`);
  }
}
```
