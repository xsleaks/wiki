+++
title = "Cache Probing"
description = ""
category = "Attack"
abuse = [
    "window.open",
    "Error Events",
    "Cache",
    "iframes",
    "AbortController"
]
defenses = [
    "SameSite Cookies",
    "Vary: Sec-Fetch-Site",
    "Subresource Protections",
]
menu = "main"
weight = 2
+++

The principle of Cache Probing consists of detecting whether a resource was cached by the browser. The concept has been known since the beginning of the web [^4] and initially relied on detecting timing differences.

When a user visits a website, some resources such as images, scripts, and HTML content are fetched and later cached by the browser (under certain conditions). This optimization  makes future navigations faster as the browser serves those resources from disk instead of requesting them again. If an attacker can detect which resources are cached, this information can be enough to leak whether a user accessed a specific page in the past.

A variation of Cache Probing abuses [Error Events]({{< ref "../attacks/error-events.md" >}}) to perform more accurate and impactful attacks.

## Attack Principle

An attacker wants to know whether a user visited a certain social network:

1. When the user visits the social network some of the subresources are cached.
2. The user visits an attacker-controlled page which fetches a resource that is usually fetched by the social network.
3. Using a [Network Timing XS-Leak]({{< ref "../attacks/timing-attacks/network-timing.md" >}}), the attacker page can detect the difference between a response coming from the cache (i.e. step 1 occurred) or coming from the network (i.e. step 1 did not occur): the delay is significantly lower if a request is served from the cache.

## Cache Probing with Error Events

Cache Probing with [Error Events]({{< ref "../attacks/error-events.md" >}}) [^2] allows more accurate attacks. Instead of relying on timing measurements, this approach leverages [Error Events]({{< ref "../attacks/error-events.md" >}}) and some server-side behavior to detect whether a resource was cached. The attack requires the following steps:

1. [Invalidating the resource]({{< ref "#invalidating-the-cache" >}}) from the browser cache. This step is required to make sure the attack does not consider a resource previously cached in another visit.
2. Performing a request that causes different items to be cached depending on the user's state. For example, loading a page that includes a specific image only if the user is logged in. This request can be triggered by navigating to the target website with `<link rel=prerender..`, embedding the website in an `iframe`, or opening a new window with `window.open`.
3. Triggering a request that causes the server to reject the request. For example, including an [overlong referer header](https://lists.archive.carbon60.com/apache/users/316239) that  makes the server reject the request. If the resource was cached in step 2, this request succeeds instead of triggering an error event.

### Invalidating the cache with errors

To invalidate a resource from the cache, the attacker must force the server to return an error when fetching that subresource. There are a couple of ways to achieve this:

- A request with an [overlong referer header](https://lists.archive.carbon60.com/apache/users/316239) and `'cache':'reload'`. This might not work as browsers [capped](https://github.com/whatwg/fetch/issues/903) the length of the referrer to prevent this.
- Request headers such as Content-Type, Accept, Accept-Language, etc. that may cause the server to fail (more application dependent).
- Other request properties.

Often, some of these methods might be considered a bug in the browser (e.g. [this bug](https://bugs.chromium.org/p/chromium/issues/detail?id=959789#c9)).

### Invalidating the cache without errors
It's also possible to remove resources from cache without server errors. For example, the above techniques could be used as well:

- A fetch request with a `cache:'reload'` option that is aborted with [`AbortController.abort()`](https://developer.mozilla.org/en-US/docs/Web/API/AbortController/abort) before new content has been received, but after the request was initiated by the browser.
- A `POST` request from a `fetch` with `no-cors`. [Run demo](https://xsinator.com/testing.html#Cache%20Leak%20(POST))
- A `POST` request from a [HTMLFormElement](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form) may purge using the key of the top-level site to bypass the [Partitioned HTTP Cache]({{< ref "../defenses/secure-defaults/partitioned-cache.md" >}})
- Exceed the browser cache limit.

## CORS error on Origin Reflection misconfiguration

Origin reflection is a behavior in which a globally accessible resource is provided with a [Access-Control-Allow-Orign (ACAO)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header whose value reflects the origin that initialized the request. This can be considered as CORS misconfiguration [^5] and can be used to detect whether the resource exists in the browser cache.

{{< hint info >}} 
For example, Flask framework [promotes](https://flask-cors.readthedocs.io/en/latest/api.htm) origin reflection as the default behavior.
{{< /hint >}}

If a resource hosted on `server.com` is requested from `target.com` then the origin could be reflected in the response headers as: `Access-Control-Allow-Origin: target.com`. If the resource is cached, this information is stored together with the resource in the browser cache. With that, if `attacker.com` tries to fetch the same resource there are two possible scenarios:
- The resource is not in cache: the resource could be fetched and stored together with the `Access-Control-Allow-Origin: attacker.com` header.
- The resource was already in cache: fetch attempt will try to fetch the resource from the cache but it will also generate a CORS error due to the ACAO header value mismatch with the requesting origin (`target.com` origin was expected but `attacker.com` was provided). Here below is provided an example code snippet epxloting this vulnerability to infer the cache status of the victim's browser. 
```javascript
// The function simply takes a url and fetches it in CORS mode.
// If the fetch raises an error, it will be a CORS error due to the 
// origin mismatch between attacker.com and victim's IP.
function ifCached(url) {
    // returns a promise that resolves to true on fetch error 
    // and to false on success
    return fetch(url, {
        mode: "cors"
    })
    .then(() => false)
    .catch(() => true);
}

// This makes sense only if the attacker already knows that
// server.com suffers from origin reflection CORS misconfiguration.
var resource_url = "server.com/reflected_origin_resource.html"
var verdict = await ifCached(resource_url)
console.log("Resource was cached: " + verdict)
```

{{< hint tip >}}
The best way to mitigate this is to avoid origin reflection and use the header `Access-Control-Allow-Origin: *` for globally accessible and unauthenticated resources.
{{< /hint >}}

## Fetch with AbortController

The below snippet shows how the [`AbortController`](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) interface could be combined with *fetch* and *setTimeout* to both detect whether the resource is cached and to evict a specific resource from the browser cache. A nice feature of this technique is that the probing occurs without caching new content in the process.
```javascript
async function ifCached(url, purge = false) {
    var controller = new AbortController();
    var signal = controller.signal;
    // After 9ms, abort the request (before the request was finished).
    // The timeout might need to be adjusted for the attack to work properly.
    // Purging content seems to take slightly less time than probing
    var wait_time = (purge) ? 3 : 9;
    var timeout = await setTimeout(() => {
        controller.abort();
    }, wait_time);
    try {
        // credentials option is needed for Firefox
        let options = {
            mode: "no-cors",
            credentials: "include",
            signal: signal
        };
        // If the option "cache: reload" is set, the browser will purge
        // the resource from the browser cache
        if(purge) options.cache = "reload";

        await fetch(url, options);
    } catch (err) {
        // When controller.abort() is called, the fetch will throw an Exception
        if(purge) console.log("The resource was purged from the cache");
        else console.log("The resource is not cached");
        return false
    }
    // clearTimeout will only be called if this line was reached in less than
    // wait_time which means that the resource must have arrived from the cache
    clearTimeout(timeout);
    console.log("The resource is cached");

    return true;
}

// purge https://example.org from the cache
await ifCached('https://example.org', true);

// Put https://example.org into the cache
// Skip this step to simulate a case where example.org is not cached
open('https://example.org');

// wait 1 second (until example.org loads)
await new Promise(resolve => setTimeout(resolve, 1000));

// Check if https://example.org is in the cache
await ifCached('https://example.org');
```

## Defense

Currently, there are no good defense mechanisms that would allow websites to fully protect against Cache Probing attacks. Nonetheless, a website can mitigate the attack surface by deploying [Cache Protections]({{< ref "cache-protections.md" >}}) such as:
- [Cache-control headers]({{< ref "cache-protections.md#cache-protection-via-cache-control-headers" >}})  used to prevent the resource from caching.
- [Random Tokens]({{< ref "cache-protections.md#cache-protection-via-random-tokens" >}}) used to make the URLs unpredictable for attackers.
- [Vary: Sec-Fetch-Site]({{< ref "cache-protections.md#cache-protection-via-fetch-metadata" >}}) used to segregate the cache by a group of origins.
- User content that is capable of making networks requests should be on its own eTLD+1 by using a separate domain or the public suffix list (if applicable) to allow for partitioned caches.

A promising defense against Cache Probing attacks is [partitioning the HTTP cache]({{< ref "../defenses/secure-defaults/partitioned-cache.md" >}}) by the requesting origin. This browser-provided protection prevents an attacker's origin from interfering with cached resources of other origins.

{{< hint info>}}
As of September 2021, Partitioned Caches is available in most browsers to split the cache by eTLD+1, however applications cannot rely on them.
The protection is ineffective for requests from subdomains and [window navigations]({{< ref "../attacks/navigations.md#partitioned-http-cache-bypass" >}})
{{< /hint >}}

## Real World Example

An attacker using [Error Events Cache Probing]({{< ref "#cache-probing-with-error-events" >}}) was able to detect whether a user watched a specific YouTube Video by checking if the video thumbnail ended up in browser cache [^3].

## References

[^1]: Abusing HTTP Status Codes to Expose Private Information, [link](https://www.grepular.com/Abusing_HTTP_Status_Codes_to_Expose_Private_Information)
[^2]: HTTP Cache Cross-Site Leaks, [link](http://sirdarckcat.blogspot.com/2019/03/http-cache-cross-site-leaks.html)
[^3]: Mass XS-Search using Cache Attack, [link](https://terjanq.github.io/Bug-Bounty/Google/cache-attack-06jd2d2mz2r0/index.html#VIII-YouTube-watching-history)
[^4]: Timing Attacks on Web Privacy, [link](http://www.cs.jhu.edu/~fabian/courses/CS600.424/course_papers/webtiming.pdf)
[^5]: CORS misconfiguration, [link](https://web-in-security.blogspot.com/2017/07/cors-misconfigurations-on-large-scale.html)
