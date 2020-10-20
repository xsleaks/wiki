+++
title = "Fetch Metadata"
description = ""
date = "2020-10-01"
category = [
    "Defense",
]
menu = "main"
+++

Fetch metadata headers are sent by browsers with every HTTP request. These headers provide context on how a request was initiated so that applications are able to make more informed decisions on how to respond to them. This allows servers to behave differently when they detect potential attacks (e.g. unexpected cross-origin requests)[^1]. This can be very effective against cross-origin attacks like XSSI, XS-Leaks, Clickjacking, and CSRF if a strict policy is deployed on the server. 

In the scenario of XS-Leaks, servers have the ability to know when a request was made cross-origin (e.g attacker origin) and can return a different response with no user data. This response has no utility to the attacker since it does not carry any information or state about the user. Fetch Metadata can also be used to block framing or even navigational requests. 

## Considerations

Fetch metadata headers are a useful tool for a defense in depth strategy but should not be seen as a replacement for mechanisms such as [Same-Site Cookies]({{< ref "same-site-cookies.md" >}}), [COOP]({{< ref "coop.md" >}}), or [Framing Protections]({{< ref "xfo.md" >}}). Even though fetch metadata headers can be used to achieve similar results, it is a best practice to enforce these restrictions on the client side in addition to the server. 

The usefulness of fetch metadata headers is dependent on the application coverage and deployment correctness.

## Deployment

Check out this [web.dev](https://web.dev/fetch-metadata/) article to learn more about this protection, some different policies, and tips on how to deploy it. 

## References

[^1]: Protect your resources from web attacks with Fetch Metadata, [link](https://web.dev/fetch-metadata/)
