---
title: 'Hugo theme experiment'
date: 2026-06-04T11:00:00-08:00
lastmod: 2026-06-04T11:00:00-08:00
cover: "images/banner.webp"
summary: "This is a summary of the post."
description: "This is a description of the post."
---

# Title H1
## H2
### H3

## Built-in shortcode

### friendsLink card

{{< friendsLink >}}

### tagRoulette

{{< tagRoulette tags="George,John,Paul,Ringo" icon="👾" >}}

### alertBlockquote

{{< alertBlockquote type="note" >}}
Your content here
{{< /alertBlockquote >}}

### link

{{< link title="Link" link="https://google.com" cover="images/banner.webp" escape="?" >}}

### grid

{{< grid width=240 col=2 >}}
<!-- cell -->
内容1
<!-- cell -->
内容2
<!-- cell -->
内容3
{{< /grid >}}

### details

{{< details summary="Summary" >}}
内容
{{< /details >}}