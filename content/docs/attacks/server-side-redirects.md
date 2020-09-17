+++
title = "Server Side Redirects"
description = ""
date = "2020-07-21"
category = "attacks"
attacks = [
    "dom property",
]
defenses = [
    "same-site cookies",
    "sec-fetch metadata",
]
menu = "main"
+++


## Inflation

This allows an attacker detect whether a certain URL redirected.

## CSP Violations


https://bugs.chromium.org/p/chromium/issues/detail?id=313737&q=csp%20detect%20redirect%20leak&can=1
http://homakov.blogspot.com/2014/01/using-content-security-policy-for-evil.html

## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../defenses/opt-in/fetch-metadata.md" >}})  | [COOP]({{< ref "../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |
|:----------------------------------:|:--------------------------:|:---------------:|:-----:|:--------------------:|
| iframe                             |         ✔️                 |      ✔️         |  ❌   |          ✔️         |
| `History.length` (iframe)          |         ✔️                 |      ✔️         |  ❌   |          ✔️         |
| `History.length` (window.open)     |         ✔️ (If Strict)     |      ✔️         |  ✔️   |           ❌        |

## References


