+++
title = "Cross-Origin-Opener-Policy"
description = ""
date = "2020-07-21"
category = "defenses"
menu = "main"
+++

## Explanation

A good portion of XS-Leaks has multiple alternatives to execute the same attack, some **common** to each other. One of those alternatives consists of navigating the user away using `window.open`, gaining a reference to the opened `window`. With this reference an attacker can take advantage of some of its properties to perform multiple requests, ultimately inferring private information from them.

Exploiting XS-Leaks with `window` references is seen as the last available option, but it's usually the right way when:

- A page sets [Framing Protections]({{< ref "xfo.md" >}})
- A page sets [`Same-Site Cookies` with `Lax` Mode]({{< ref "same-site-cookies.md" >}}) (navigating a `window` triggers a top-level navigation)

To prevent untrusted origins from gaining arbitrary `window` references to a page, applications can deploy Cross-Origin-Opener-Policy (COOP)[^1]. With COOP, applications are in control of who is allowed to have a reference to their pages, thus mitigating XS-Leaks which make use of such techiniques.

## Considerations

Implementing COOP in applications can often be overlooked by Developers and Security Engineers who are not familiar with such topics. Nonetheless, it’s important to highlight the importance of this Defense Mechanism as it is the **only way** to prevent attackers from exploiting XS-Leaks which makes use of `window` references (unless Same-Site Cookies Strict Mode can be widely deployed).

## Deploy

Check out this [web.dev](https://web.dev/why-coop-coep/) article to learn more about the advantages of this protection and how to deploy it.

## XS-Leaks Mitigation Overview

|                           XS-Leak                                 |    COOP Protection       |  Full Mitigation   |
|:-----------------------------------------------------------------:|:------------------------:|:-------------------:
| [Frame Counting]({{< ref "../../attacks/frame-counting.md" >}})   |         ✔️               |         ❌
| [Navigations]({{< ref "../../attacks/navigations.md" >}})         |         ✔️               |         ❌
| [ID Leaks]({{< ref "../../attacks/id-leaks.md" >}})               |         ✔️               |         ❌

### Table Caption

- **COOP Protection** - At least one attack alternative of the XS-Leak relies on `window` references
- **Full Mitigation** - Does not fully mitigate an XS-Leak if an alternative attack vector that does not rely on `window` references


[^1]: Cross-Origin-Opener-Policy response header (also known as COOP), [link](https://gist.github.com/annevk/6f2dd8c79c77123f39797f6bdac43f3e)
