+++
title = "Framing Protections"
description = ""
date = "2020-07-21"
category = "defenses"
menu = "main"
+++

## Explanation

A considerable number of XS-Leaks use certain properties of `iframes` to be successful. If an attacker is unable to embed the contents of a page as an `iframe` then the attack may no longer be possible. To mitigate XS-Leaks which rely on this object, pages can forbid or select which origins can embed its pages. This is possible by using the [X-Frame-Options Header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options) or the [CSP frame-ancestors directive](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors).

{{< hint warning >}}
[X-Frame-Options Header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options) is obsolete and was replaced by the [CSP frame-ancestors directive](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors).
{{< /hint >}}

## Considerations

This protection is very effective against XS-Leaks which rely on `iframes` and can be easily implemented without breaking anything. This protection not only protects applications of some XS-Leaks but also from attacks like Clickjacking.

## Deploy

Deploy framing protections is particularly straightforward, and depend whether applications need to allow cross-origin pages to iframe. Check out this [web.dev](https://web.dev/same-origin-policy/) article to learn more about the the advantages of this header.

## XS-Leaks Mitigation Overview

|                           XS-Leak                                 |    Framing Protections   |  Full Mitigation   |
|:-----------------------------------------------------------------:|:------------------------:|:-------------------:
| [Frame Counting]({{< ref "../../attacks/frame-counting.md" >}})   |         ✔️               |         ❌
| [Navigations]({{< ref "../../attacks/navigations.md" >}})         |         ✔️               |         ❌
| [ID Leaks]({{< ref "../../attacks/id-leaks.md" >}})               |         ✔️               |         ❌

### Table Caption

- **Framing Protections** -  At least one attack alternative of the XS-Leak relies on `iframes`
- **Full Mitigation** -  Does not fully mitigate an XS-Leak if an alternative attack vector that does not rely on the of `iframes`