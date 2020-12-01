+++
title = "Fetch Metadata"
description = ""
date = "2020-11-30"
category = [
    "Defense",
]
menu = "main"
+++

[Fetch Metadata Request Headers](https://www.w3.org/TR/fetch-metadata/) are sent by browsers with HTTPS requests. These headers provide context on how a request was initiated so that applications are able to make more informed decisions on how to respond to them. This allows servers to behave differently when they detect potential attacks (e.g. unexpected cross-origin requests)[^1]. This can be very effective against cross-origin attacks like XSSI, XS-Leaks, Clickjacking, and CSRF if a strict policy is deployed on the server.

In the XS-Leaks scenario, servers have the ability to know when a request was made cross-origin (e.g. attacker origin) and can return a different response with no user data. This kind of response is not useful to the attacker since it does not carry any information or state about the user. Fetch Metadata can also be used to block framing or even navigational requests.

{{< hint important >}}
For security reasons, Fetch Metadata headers are only attached to encrypted (HTTPS) requests.
{{< /hint >}}

## Fetch Metadata vs. SameSite cookies

Fetch Metadata headers can be used to extend the protections provided by SameSite cookies. While both Fetch Metadata headers and SameSite cookies can be used to reject cross-site requests, Fetch Metadata can make more informed decisions based on factors like:
* Was the request same-origin or same-site?
* How was the request initiated? (e.g. fetch, script, top navigation)
* Was the request initiated by user interaction?
* Was the request initiated by the browser (e.g. by entering the URL directly in the omnibox)?

This allows for a more precise deployment of protections in scenarios where SameSite cookies could break a service's functionalities. One disadvantage of Fetch Metadata compared to SameSite cookies is that the latter can also protect unencrypted requests (HTTP) while the former can't.

## Considerations

Fetch Metadata headers are a useful tool for a defense-in-depth strategy, but should not be seen as a replacement for mechanisms such as [SameSite Cookies]({{< ref "same-site-cookies.md" >}}), [COOP]({{< ref "coop.md" >}}), or [Framing Protections]({{< ref "xfo.md" >}}). Even though Fetch Metadata headers can be used to achieve similar results, it is a best practice to enforce these restrictions on the client side in addition to the server.

The usefulness of Fetch Metadata headers is dependent on the application coverage and correctness of the deployment.

## Policies

See [Resource Isolation Policy]({{< ref "../isolation-policies/resource-isolation.md" >}}) and [Framing Isolation Policy]({{< ref "../isolation-policies/framing-isolation.md" >}}) for specific policies utilizing Fetch Metadata Request Headers.
