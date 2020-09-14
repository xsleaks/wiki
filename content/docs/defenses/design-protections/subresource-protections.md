+++
title = "Subresource Protections"
description = "Subresources Protections"
date = "2020-07-21"
category = "defenses"
menu = "main"
+++


The principle of protecting subresources is the same as protecting endpoints from [CSRF attacks](https://owasp.org/www-community/attacks/csrf). The difference from [CSRF protections](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html) is that in the case of XS-Leaks, mostly GET requests are the ones worth protecting. To apply this protection, applications can append a (cryptographically strong) pseudorandom value, unique to each request/session, to make the URL of a subresource unpredictable to an attacker. The protection can be applied to the following types of subresources:

- Authenticated subresources such as API endpoints or regular authenticated URLs. While pseudorandom values can used in this case, security mitigations like [Same-Site Cookies](https://TODO) can be rather more effective.
- Unauthenticated subresources such as images can use this protection to prevent some types of [Cache Probing Attacks](https://TODO). In this scenario this protection can be highly effective.

## Deployment

This protection might work in some scenarios but it has some disadvantages:

- Hard to deploy as it requires substantial changes in the codebase. 
- It might break the desired behavior for the feature.
- It will break bookmarks and other permanent references.

{{< hint warning >}}
This protection can be enough to fix attacks temporarily in certain scenarios. Due to the challenges of deploying this protection, applications are encouraged to deploy opt-in web platform security features as default approach.
{{< /hint >}}


