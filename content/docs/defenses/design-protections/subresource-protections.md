+++
title = "Subresource Protections"
description = "Subresources Protections"
date = "2020-10-01"
category = [
    "Defense",
]
menu = "main"
+++

The fundamental idea behind designing protections for subresources is that subresources cannot be targeted by XS-Leaks if the attacker cannot make them return any user data. If implemented correctly, this approach can be a very strong defense, though it is likely to be tough to implement and could negatively impact the user experience.

{{< hint tip >}}
It can be very effective to deploy this approach on any specific resources that are known to be especially sensitive to XS-Leaks. But, due to the challenges of deploying this protection universally, applications are encouraged to deploy [opt-in web platform security features]({{< ref "../_index.md" >}}) as the default approach.
{{< /hint >}}

## Token-Based Protections

A strong protection for subresources can be achieved by including a user-specific token in every request. This protects against most XS-Leak techniques if implemented correctly. The idea is that in order to verify a request for a resource as being legitimate, a token must be included. This token must be provided to the client in a way that prevents an attacker from including it in their own requests.

{{< hint example >}}
Suppose there is a search bar in an application.

1. When the user loads the main page, the server includes a secure token somewhere in the body of the page.
2. When the user searches for something, a request is made to `/search?query=<QUERY>&token=<SECURE_TOKEN>`.
3. The backend verifies that the provided token is valid for the current user.
4. If it is not valid, the request is rejected.

In this scenario, there is no way for an attacker to trigger any requests to the endpoint because they cannot obtain a valid token for a given user. Note that this relies on it not being possible for an attacker to obtain or forge a token for other users. If they can do so, this approach is not effective.
{{< /hint >}}

This style of protection can be applied to:

- Authenticated subresources such as API endpoints or regular authenticated URLs. While tokens can be used in this case, security mitigations like [Same-Site Cookies]({{< ref "../opt-in/same-site-cookies.md" >}}) may be easier to deploy at scale.
- Unauthenticated subresources such as images can use this protection to prevent some types of [Cache Probing Attacks]({{< ref "../../attacks/cache-probing.md" >}}). While this does work, see [Cache Protections]({{< ref "./cache-protections.md" >}}) for other strategies to defend against cache probing attacks.

{{< hint warning >}}
Implementing token-based protections might break the ability of users to save or share links (e.g. bookmarks).
{{< /hint >}}

## User Consent

Another strong defense is to require user interaction before returning any sensitive data. This ensures that sensitive endpoints cannot be included via `script` or `img` tags. For example, Facebook requires user confirmation before viewing search results or private messages. Since attackers cannot simulate this user interaction, they are unable to leak the contents of the search results.

This can be a very useful way of protecting especially sensitive endpoints, but note once again that this is likely to be time-consuming to implement.
