+++
title = "Subresource Protections"
description = "Subresources Protections"
date = "2020-07-21"
category = "defenses"
menu = "main"
+++

### Random tokens

One of the principles of protecting subresources is the same as protecting endpoints from [CSRF attacks](https://owasp.org/www-community/attacks/csrf). The difference from [CSRF protections](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html) is that in the case of XS-Leaks, GET requests are the ones usually worth protecting. To apply this protection applications can append a (cryptographically strong) pseudorandom value, unique to each request/session, to make the URL of a subresource unpredictable to an attacker. The protection can be applied to the following types of subresources:

- Authenticated subresources such as API endpoints or regular authenticated URLs. While pseudorandom values can be used in this case, security mitigations like [Same-Site Cookies](https://TODO) can be cheaper to deploy and more effective.
- Unauthenticated subresources such as images can use this protection to prevent some types of [Cache Probing Attacks](https://TODO). In this scenario, this protection can be highly effective.

### User Consent

Some applications might ask for user consent to trigger a certain sensitive action. Facebook deploys this protection in some sensible search endpoints like `https://www.facebook.com/messages/?qa=UserMustConsent`, where a user musk press OK to advance with the search query. Since attackers can't surpass this verification, the page won't leak any special behavior.

## Deployment

While this protection might work in some scenarios, it has some disadvantages:

- Hard to deploy as it requires substantial changes in the codebase. 
- It might break the desired behavior for the feature.
- In the case of Random tokens, it will break bookmarks and other permanent references.

{{< hint warning >}}
This protection can be enough to fix attacks temporarily in certain scenarios. Due to the challenges of deploying this protection, applications are encouraged to deploy [opt-in web platform security features](https://TODO) as the default approach.
{{< /hint >}}
