+++
title = "Fetch Metadata"
description = ""
date = "2020-10-01"
category = [
    "Defense",
]
menu = "main"
+++

Fetch metadata headers are sent by browsers with HTTP requests. These headers provide context on how every request was initiated so that applications are able to make more informed decisions on how to respond to them. This allows servers to behave differently when they detect potential attacks (e.g. unexpected cross-origin requests)[^1]. This mechanism can be very effective against cross-origin attacks like XSSI, XS-Leaks, Clickjacking, and CSRF.

In the scenario of XS-Leaks, servers have the ability to know when a request was made cross-origin (e.g attacker origin) and can return a different response with no user data. This response has no utility to the attacker since it does not carry any information or state about the user. These headers allow the server to simulate the behavior of [SameSite Cookies]({{< ref "same-site-cookies.md" >}}) but with more precise policies.

{{< hint warning >}}
Fetch metadata headers will be only attached to encrypted (HTTPS) requests for security reasons.
{{< /hint >}}

## Fetch Metadata vs. Same-Site cookies

Fetchmetadata headers can be used to extend the functionality of Same-Site cookies, both `Lax` and `Strict`. While both Fetch Metadata headers and Same-Site cookies can reject cross-site requests only Fetch Metadata can perform informed decision based on factors like:
* Was the request cross-site or same-site?
* How the request was initiated? (e.g. fetch, script, top navigation)
* Was the request initiated by the user interaction or browser?

This allows for more reliable deployment of protections in scenarios where Same-Site cookies could break service functionalities. The only disadvantage of Fetchmetadata compared to Same-Site cookies is that the latter can also protect unencrypted requests (HTTP) while the former can't.

{{< hint warning >}}
In _Defense_ sections we often assume that the service runs on HTTPS, and therefore, the protection can be applied.
{{< /hint >}}

## Fetch Metadata & Cache Probing Attacks

Another interesting application of Fetch Metadata is its combination with the [Vary](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Vary) response header. It can be used to partition cached resources by groups of origins. The [Vary](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Vary) header when set with [`Sec-Fetch-Site`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Sec-Fetch-Site) will store and serve different cached entries based on the origin that originally performed the request. With this header, an attacker that tries to [probe a cached resource]({{< ref "../../attacks/cache-probing.md" >}}) of a different origin won't be able to interfere with cached entries created from another site.

This change does not require major changes in the server codebase and is unlikely to have any negative effects other than reducing cache efficiency. This achieves a similar protection enforced by a [partitioned cache]({{< ref "../browser-default/partitioned-cache.md" >}}).

## Considerations

Fetch metadata headers are a useful tool for a defense in depth strategy but should not be seen as a replacement for mechanisms such as [Same-Site Cookies]({{< ref "same-site-cookies.md" >}}), [COOP]({{< ref "coop.md" >}}), or [Framing Protections]({{< ref "xfo.md" >}}). Even though fetch metadata headers can be used to achieve similar results, they do not provide a safe default and do not have the same support across different browsers.

The usefulness of fetch metadata headers is dependent on the application coverage and deployment correctness.

## Deployment

Check out this [web.dev](https://web.dev/fetch-metadata/) article to learn more about the advantages of this protection and how to deploy it.

## References

[^1]: Protect your resources from web attacks with Fetch Metadata, [link](https://web.dev/fetch-metadata/)
