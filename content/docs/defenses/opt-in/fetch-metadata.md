+++
title = "Fetch Metadata"
description = ""
date = "2020-10-01"
category = [
    "Defense",
]
menu = "main"
+++

Fetch Metadata Headers are sent by browsers in every request or navigation made by a page. These Headers provide context on how every request/navigation was initiated so that applications are able to make more informed decisions on how to respond to them. This allows servers to behave differently when they detect potential attacks (e.g unexpected cross-origin requests)[^1]. This mechanism can be very effective against cross-origin attacks like XSSI, XS-Leaks, Clickjacking, and CSRF. 

In the scenario of XS-Leaks, servers have the ability to know when a request was made cross-origin (e.g attacker origin) and decide to answer with a response analogous to the user session. This response has no utility to the attacker since it does not carry any information or state about the user. These Headers allow the server to replicate the behavior of [SameSite Cookies]({{< ref "same-site-cookies.md" >}}) with fine-grained policies.

## Fetch Metadata & Cache Probing Attacks

Another interesting application of Fetch Metadata is its combination with the [Vary](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Vary) Response Header, used to **segregate** served cached resources by groups of origins. The [Vary](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Vary) Header when set with [`Sec-Fetch-Site`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Sec-Fetch-Site) will store and serve different cached entries based on the origin who originally performed the request. With this Header, an attacker which tries to [probe a cached resource]({{< ref "../../attacks/cache-probing.md" >}}) of a different origin won't be able to interfere with cached entries created from same-origin/same-site.

The behavior enforced by this Header does not require major changes in the server codebase. This protection can achieve a similar protection enforced by a [partitioned cache]({{< ref "../browser-default/partitioned-cache.md" >}}).

## Considerations

Fetch Metadata Headers are an **in-depth Defense Mechanism** and should **not be seen as a replacement** for mechanisms such as [Same-Site Cookies]({{< ref "same-site-cookies.md" >}}), [Cross-Origin-Opener-Policy]({{< ref "coop.md" >}}) or [Framing Protections]({{< ref "xfo.md" >}}). Even though Fetch Metadata Headers share some capabilities with these mechanisms (e.g act differently upon cross-site requests), **they do not provide a safe default**. 
Its success is dependent on the application coverage and deployment correctness.

## Deployment

Check out this [web.dev](https://web.dev/fetch-metadata/) article to learn more about the advantages of this protection and how to deploy it.

## References

[^1]: Protect your resources from web attacks with Fetch Metadata, [link](https://web.dev/fetch-metadata/)
