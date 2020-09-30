+++
title = "Cross-Origin-Opener-Policy"
description = ""
date = "2020-07-21"
category = "defenses"
menu = "main"
+++

## Explanation

A good portion of XS-Leaks has multiple alternatives to execute the same attack, some in **common** with each other. For instance, some XS-Leaks abuse properties of `iframes` to succeed which can be fully mitigated by deploying [Framing Protections]({{< ref "xfo.md" >}}). However, this same group of XS-Leaks can usually be abused by other **common** alternatives, that need other protections. One of those alternatives consists of navigating the user away using `window.open`, allowing attackers to gain a reference to the opened `window`. With this reference an attacker can take advantage of some of its properties to perform multiple requests, ultimately inferring private information from them.

Exploiting XS-Leaks with `window` references is seen as the last available option mainly because the user can see it happen. However, it's usually the right way when:

- A page sets [Framing Protections]({{< ref "xfo.md" >}})
- A page sets [`Same-Site Cookies` with `Lax` Mode]({{< ref "same-site-cookies.md" >}}) (navigating a `window` is a top-level navigation)

To prevent untrusted origins from gaining arbitrary `window` references to a page, applications can deploy Cross-Origin-Opener-Policy (COOP)[^1]. With COOP, applications are in control of who is allowed to have a reference to their pages, thus mitigating XS-Leaks which make use of such techniques.

## Considerations

Since COOP is an `opt-in` mechanism and a very recent one, can be overlooked by Developers and Security Engineers. Nonetheless, it’s important to highlight the importance of this Defense Mechanism as it is the **only way** to prevent attackers from exploiting XS-Leaks which makes use of `window` references (unless Same-Site Cookies Strict Mode can be widely deployed).

## Deployment

Check out this [web.dev](https://web.dev/why-coop-coep/) article to learn more about the advantages of this protection and how to deploy it.

## References

[^1]: Cross-Origin-Opener-Policy response header (also known as COOP), [link](https://gist.github.com/annevk/6f2dd8c79c77123f39797f6bdac43f3e)
