+++
title = "Portals"
description = ""
date = "2020-10-01"
category = "Experiments"
menu = "main"
+++

[Portals](https://web.dev/hands-on-portals/) are a new feature of the web similar to `iframes` with more emphasis on speed and user experience. The [`portal`](https://web.dev/hands-on-portals/) element is only available on Chromium-based browsers under a preference flag. The corresponding [specification](https://wicg.github.io/portals/) is still under active discussion. 

Unfortunately, some research over this new feature found critical issues, including new XS-Leaks [^2].

## ID Leaks

Portals can be abused as an alternative for the [ID Attribute XS-Leak]({{< ref "../id-attribute.md" >}}). If the website sets [framing protections]({{< ref "../../defenses/opt-in/xfo.md" >}}), the same technique can be applied using the `portal` element instead [^1].

## References

[^1]: Detecting IDs using Portal, [link](https://portswigger.net/research/xs-leak-detecting-ids-using-portal)
[^2]: Security analysis of \<portal\> element, [link](https://research.securitum.com/security-analysis-of-portal-element/)
