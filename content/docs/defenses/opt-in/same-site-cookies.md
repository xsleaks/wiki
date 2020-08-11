+++
title = "Same-Site Cookies"
description = ""
date = "2020-07-21"
category = "defenses"
menu = "main"
+++


## Explanation

`Same-Site Cookies` are one of the most impactful Security Mechanisms towards fixing security issues that involve `cross-site` requests. This mechanism allows applications to add a special restriction to cookies, forcing browsers to only append them in requests that are issued `same-site` [^1]. This type of cookies has two modes: `Lax` and `Strict`.

### Lax V.s Strict

The only difference between `Lax` and `Strict` is that `Lax` mode allows cookies to be added to requests triggered by top-level navigations. If `bank.com` sets `Same-Site=Lax` and `attacker.com` makes a request using the `fetch API` cookies won't be sent. `bank.com`, instead, is allowed to make requests to itself since they are `same-site`. Unfortunately, if the attacker navigates the user with `window.open` (which triggers a top-level navigation) the cookies will be sent and the attacker would have a reference to that window, making them able to exploit some XS-Leaks.

## Considerations

`Same-Site Cookies` are not bulletproof [^2] nor they can fix everything. To complete the defense strategy against XS-Leaks users should consider implementing [other protections]({{< ref "coop.md" >}}), for instance, to stop an attacker from controlling pages using a window reference even if `Same-Site Cookies` are in use.


## Deployment

Anyone interested in deploying this mechanism in web applications should take a careful look in this [web.dev](https://web.dev/samesite-cookie-recipes/) article.

## XS-Leaks Mitigation Overview

|                           XS-Leak                                 |  None |  Lax   | Strict |
|:-----------------------------------------------------------------:|:-----:|:------:|:------:|
| [Frame Counting]({{< ref "../../attacks/frame-counting.md" >}})   |   ❌  |   ❌   |  ✔️   |
| [Navigations]({{< ref "../../attacks/navigations.md" >}})         |   ❌  |   ❌   |  ✔️   |
| [ID Leaks]({{< ref "../../attacks/id-leaks.md" >}})               |   ❌  |   ❓  |  ✔️   |

[^1]: SameSite cookies explained, [link](https://web.dev/samesite-cookies-explained/)
[^2]: Bypass SameSite Cookies Default to Lax and get CSRF [link](https://medium.com/@renwa/bypass-samesite-cookies-default-to-lax-and-get-csrf-343ba09b9f2b)
