+++
title = "Subresource Protections"
description = "Subresources Protections"
date = "2020-10-01"
category = [
    "Defense",
]
menu = "main"
+++

The fundamental idea of designing protections for subresources is that subresources cannot be targeted by XS-Leaks if the attacker cannot cause them to return any user data. 

## Secure tokens

One way of achieving this is similar to how endpoints can be protected from [CSRF attacks](https://owasp.org/www-community/attacks/csrf). While [CSRF protections](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html) tend to focus on state changing requests, XS-Leak protections are meant to defend `GET` requests. 

To apply this protection, each request for a subresource needs to include some unique value that attackers cannot obtain. One way would be to place an unguessable random ID in the URL. For example, instead of `victim.com/chat_room?name=Secret-Chat` one could use `victim.com/chat_room?name=Secret-Chat&xsrf=<random ID>`. While this would mitigate cache based attacks, this might not protect against forging the requests. Another potential strategy is to include signed attestations in the URLs, which would protect both the cache and the request forgery. But this may not provide sufficient protection in case where the attacker is able to obtain a secure token that is valid for retrieving the user's data. 

This style of protection can be applied to:

- Authenticated subresources such as API endpoints or regular authenticated URLs. While secure tokens can be used in this case, security mitigations like [Same-Site Cookies]({{< ref "../opt-in/same-site-cookies.md" >}}) can be easier to deploy and more effective.
- Unauthenticated subresources such as images can use this protection to prevent some types of [Cache Probing Attacks]({{< ref "../../attacks/cache-probing.md" >}}). This protection can be highly effective for unauthenticated subresources, but it would negatively impact the cache efficiency and might be otherwise hard to deploy. 

## User Consent

By prompting for user interaction prior to returning sensitive results, one can ensure that sensitive results cannot be included in places they should not end up (e.g.`script` or `img` tags). For example, Facebook requires user confirmation before viewing search results or private messages. Since attackers cannot simulate this user interaction, they will be unable to leak the contents of the search results. 

Another common application of this is when applications are redirecting users to another website. It is common to require user confirmation before the redirect. This prevents attackers from [detecting some types of navigations]({{< ref "../../attacks/navigations.md" >}}).

## Deployment

While this protection can work in some scenarios, it has some disadvantages:

- Hard to deploy as it requires substantial changes in the codebase. 
- It might break the desired behavior for the feature.
- In the case of random tokens, it will break bookmarks and other permanent references.
- Consent pages might add friction to using the application.

{{< hint warning >}}
It can be very effective to deploy this on any specific resources that are known to be especially senstive to XS-Leaks. But, due to the challenges of deploying this protection universally, applications are encouraged to deploy [opt-in web platform security features]({{< ref "../_index.md" >}}) as the default approach.
{{< /hint >}}
