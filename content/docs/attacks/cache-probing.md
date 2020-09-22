+++
title = "Cache Probing"
description = ""
category = "attacks"
attacks = [
    "dom property",
]
defenses = [
    "same-site cookies",
    "sec-fetch metadata",
]
menu = "main"
+++

The principle of Cache Probing consists of detecting whether some resource was cached by the browser. The concept is known since the begging of the web [^4] and initially used timing differences to succeed. 

When a user visits a website some resources such as images, scripts and HTML content are fetched and later cached by the browser (under certain conditions). This optimization will make future navigations faster as the browser will serve those resources from disk instead of requesting them again. If an attacker can detect which 
resources are cached it may be enough to leak whether a user accessed a specific page in the past.

A variation of the original concept abused [Error Events](https://TODO-REFFERSUBSECTIONBELLOW) to perform more accurate and impactful attacks, including tricks to invalidate resources from the cache (to make better inferences).

## Attack Principle

An attacker wants to know whether a user visited a certain social network.

1. A user visits a social network and some of the subresources will be cached. This step might not happen depending on the user behavior.
2. The user visits an attacker-controlled page which will fetch a resource that is usually fetched by that social network. 
3. Using a [Network Timing XS-Leak](https://TODO), the attacker page can infer the difference from a request coming from the cache (step 1 happened) or coming from a network request based on the request timing (step 1 did not happen). The delay will be significantly lower in a request served from the cache.

## Cache Probing with Error Events

Cache Probing with [Error Events](https://TODO-REFFERSUBSECTIONBELLOW) [^2] allows more accurate and impactful attacks. Instead of relying on timing measurements, they leverage [Error Events](https://TODO-REFFERSUBSECTIONBELLOW) and some server behaviors to detect whether a resource was cached. It also uses a trick to invalidate resources from the cache. The attack works as follows:

1. [Invalidate the resource]({{< ref "#invalidate-the-cache" >}}) from the browser cache. This step is required to make sure the attack will not consider a resource previously cached in another visit.
2. Preform a request to load subresources of the target website. This can be done by navigating to the target website with `<link rel=prerender..`, embedding the website in an `iframe` or navigating away with `window.open`.
3. An attacker performs a request with an [overlong referrer](https://lists.archive.carbon60.com/apache/users/316239), forcing the server to fail when receiving the request. If the resource was cached in step 2, this request will succeed and fail otherwise.

### Invalidate the cache

To invalidate a resource from the cache the attacker must force the server to return an error when fetching that subresource. There are a couple of ways to achieve this:

- By performing a request with a [overlong referrer](https://lists.archive.carbon60.com/apache/users/316239) and `'cache':'reload'`. Browsers [capped]((https://github.com/whatwg/fetch/issues/903)) the length of the referrer, to prevent some server engines from failing when computing the request.
- Request Headers such as Content-Type, Accept, Accept-Language, etc that may cause the server to fail (more application dependent).
- Other request properties.

Often, some of these alternatives might be considered a [browser bug](https://bugs.chromium.org/p/chromium/issues/detail?id=959789#c9).

## Defense

### Opt-in Defense Mechanisms

| [Same-Site Cookies](https://TODO)   | [Vary: Sec-Fetch-Site]({{< ref "../defenses/opt-in/fetch-metadata.md#fetch-metadata--cache-probing-attacks" >}})  | [Subresource Protections]({{< ref "../defenses/design-protections/subresource-protections.md" >}}) |
|:---------------------------------:|:-------------------------------------:|:---------------------------------------:|
|        ✔️ (if strict)             |                  ✔️                   |   ✔️ & ❌ [*](https://TODO-referdeploysectioninsubresourceprotection)   | 

- If a resource can be fetched with user authentication, [Same-Site Cookies](https://TODO) (Strict) should be considered to protect that resource from being abused by an attacker origin.
- [Vary: Sec-Fetch-Site]({{< ref "../defenses/opt-in/fetch-metadata.md#fetch-metadata--cache-probing-attacks" >}}) allows applications to force cache segregation by a group of origins. It does not enforce full segregation, since some resources come from external origins of Content Delivery Networks, shared across multiple websites. Nevertheless, it's good protection against Cache Probing attacks.
- [Subresource Protections]({{< ref "../defenses/design-protections/subresource-protections.md" >}}) allow application to set random tokens in URLs to make them unpredictable from attackers. Useful for both authenticated and unauthenticated subresources.

### No-opt Defense Mechanisms

[Paritioned Caches](https://TODO), a no-opt feature implemented in browsers create a unique cache for each origin. This protection prevents an attacker origin to interfere with cached resources of other origins. Applications do not have to opt-in to enforce this cache.

{{< hint warning >}}
Partitioned Caches are not available in most browsers by default, so applications cannot rely on them yet.
{{< /hint >}}

## Real World Example

1. An attacker using the [Error Events Cache Probing](https://TODO) could detect wether a user watched a specific YouTube Video by checking if the video thumbnail ended up in browser cache [^3].

## References

[^1]: Abusing HTTP Status Codes to Expose Private Information, [link](https://www.grepular.com/Abusing_HTTP_Status_Codes_to_Expose_Private_Information)
[^2]: HTTP Cache Cross-Site Leaks, [link](http://sirdarckcat.blogspot.com/2019/03/http-cache-cross-site-leaks.html)
[^3]: Mass XS-Search using Cache Attack, [link](https://terjanq.github.io/Bug-Bounty/Google/cache-attack-06jd2d2mz2r0/index.html#VIII-YouTube-watching-history)
[^4]: Timing Attacks on Web Privacy, [link](http://www.cs.jhu.edu/~fabian/courses/CS600.424/course_papers/webtiming.pdf)
