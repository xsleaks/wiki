+++
title = "Subresource Protections"
description = "Subresources Protections"
date = "2020-10-01"
category = [
    "Defense",
]
menu = "main"
+++

The fundamental idea behind designing protections for subresources is that subresources cannot be targeted by XS-Leaks if the attacker cannot cause them to return any user data. If implemented correctly, this can be a very strong defense though it is likely to be very time consuming to implement. 

{{< hint warning >}}
It can be very effective to deploy this on any specific resources that are known to be especially senstive to XS-Leaks. But, due to the challenges of deploying this protection universally, applications are encouraged to deploy [opt-in web platform security features]({{< ref "../_index.md" >}}) as the default approach.
{{< /hint >}}

## Token Based Protections

A strong protection for subresources can be achieved by including a user specific token in every request. This protects against most XS-Leak techniques if implemented correctly. The idea is that in order to verify a request for a resource as being legitimate, a token must be included. This token must be provided to the client in some way that an attacker would not be able to cause it to be included in their own requests. 

{{< hint info >}}
Suppose there is some search bar on an application. When the user loads the main page, the server includes a token in the body of the page. When the user searches for something, a request is made to `/search?query=...&token=$SECURE_TOKEN`. The backend verifies that the provided token is valid for the current user. If it is not valid, the request is immediately rejected. 

In this scenario, there is no way for an attacker to trigger any requests to the endpoint because they cannot obtain a valid token for a given user. Note that this relies on it not being possible for an attacker to obtain or forge a token for other users. If they can do so, this is not effective. 
{{< /hint >}}

This style of protection can be applied to:

- Authenticated subresources such as API endpoints or regular authenticated URLs. While tokens can be used in this case, security mitigations like [Same-Site Cookies]({{< ref "../opt-in/same-site-cookies.md" >}}) may be easier to deploy at scale.
- Unauthenticated subresources such as images can use this protection to prevent some types of [Cache Probing Attacks]({{< ref "../../attacks/cache-probing.md" >}}). While this does work, see [Cache Protections]({{< ref "./cache-protections.md" >}}) for other strategies to defend against cache probing attacks. 

{{< hint warning >}}
Implementing this might break the ability for users to save or share links (eg bookmarks).
{{< /hint >}}

## User Consent

Another strong defense is to require user interaction before returning any sensitive data. This ensures that sensitive endpoints cannot be included via `script` or `img` tags. For example, Facebook requires user confirmation before viewing search results or private messages. Since attackers cannot simulate this user interaction, they will be unable to leak the contents of the search results. 

This can be a very useful way of protecting especially sensitive endpoints though note once again that this is likely to be time consuming to implement. 
