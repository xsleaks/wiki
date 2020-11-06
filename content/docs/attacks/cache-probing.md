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

The principle of Cache Probing consists of detecting whether some resource was cached by the browser. The concept is known since the beginning of the web [^4] and initially relied on detecting timing differences.

When a user visits a website some resources such as images, scripts, and HTML content are fetched and later cached by the browser (under certain conditions). This optimization will make future navigations faster as the browser will serve those resources from disk instead of requesting them again. If an attacker can detect which
resources are cached it may be enough to leak whether a user accessed a specific page in the past.

A variation on cache probing abuses [Error Events]({{< ref "../attacks/error-events.md" >}}) to perform more accurate and impactful attacks.

## Attack Principle

An attacker wants to know whether a user visited a certain social network.

1. A user visits a social network and some of the subresources will be cached.
2. The user visits an attacker controlled page which will fetch a resource that is usually fetched by that social network.
3. Using a [Network Timing XS-Leak]({{< ref "../attacks/timing-attacks/network-timing.md" >}}), the attacker page can detect the difference from a response coming from the cache (step 1 happened) or coming from the network (step 1 did not happen). The delay will be significantly lower in a request served from the cache.

## Cache Probing with Error Events

Cache Probing with [Error Events]({{< ref "../attacks/error-events.md" >}}) [^2] allows more accurate attacks. Instead of relying on timing measurements, this leverages [Error Events]({{< ref "../attacks/error-events.md" >}}) and some server-side behavior to detect whether a resource was cached. The attack works as follows:

1. [Invalidate the resource]({{< ref "#invalidate-the-cache" >}}) from the browser cache. This step is required to make sure the attack will not consider a resource previously cached in another visit.
2. Perform a request that will cause different items to be cached depending on the user's state. For example, load a page that will include a specific image only if the user is logged in. This request can be triggered by navigating to the target website with `<link rel=prerender..`, embedding the website in an `iframe`, or opening a new window with `window.open`.
3. Trigger a request that will cause the server to reject the request. For example, include an [overlong referer header](https://lists.archive.carbon60.com/apache/users/316239) that will make the server reject the request. If the resource was cached in step 2, this request will succeed instead of triggering an error event.

### Invalidate the cache

To invalidate a resource from the cache the attacker must force the server to return an error when fetching that subresource. There are a couple of ways to achieve this:

- A fetch request with `cache:'reload'`option that is aborted with [`AbortController.abort()`](https://developer.mozilla.org/en-US/docs/Web/API/AbortController/abort) before new content has been received, but after the request was initiated by the browser. 
- A request with an [overlong referer header](https://lists.archive.carbon60.com/apache/users/316239) and `'cache':'reload'`. This might not work as browsers [capped](https://github.com/whatwg/fetch/issues/903) the length of the referrer to prevent this.
- A `POST` request with a `fetch` `no-cors`. Sometimes even in cases where an error is not returned the browser invalidates the cache.
- Request headers such as Content-Type, Accept, Accept-Language, etc that may cause the server to fail (more application dependent).
- Other request properties.

Often some of these methods might be considered a bug in the browser (e.g. [this bug](https://bugs.chromium.org/p/chromium/issues/detail?id=959789#c9)).

## Fetch with AbortController
The below snippet shows how the [`AbortController`](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) interface could be combined with *fetch* and *setTimeout* to both detect whether the resource is cached and to evict a specific resource from the browser cache. A nice feature about this technique is that the probing occurs without caching new content in the process.
```javascript
async function ifCached(url, purge = false) {
    var controller = new AbortController();
    var signal = controller.signal;
    // After 9ms, abort the request. If the timeout was successful, 
    // The timeout might need to be adjusted for the attack to work.
    // The timeout may get trottled if the tab is inactive so maybe use web workers.
    // Purging content takes less time
    var max = (purge) ? 3 : 9;
    var timeout = await setTimeout(() => { 
        controller.abort();
    }, max);
    try {
        // credentials option is needed for Firefox
        let options = {
            mode: "no-cors", 
            credentials: "include", 
            signal: signal
        };
        // if the option "cache: reload" is set, the browser will purge 
        // the resource from the browser cache
        if(purge) options.cache = "reload";
        
        await fetch(url, options);
    } catch (err) {
        // When controller.abort() is called, the fetch will throw an Exception
        if(purge) console.log("The resource was purged from the cache");
        else console.log("The resource is not cached");
        return false
    }
    // clearTimeout will only be called if this line was reached in less than the fetch timeout,
    // which means that the resource must have arrived from the cache
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
let result = await ifCached('https://example.org');
```

## Defense

Currently there are no good defense mechanisms that would allow websites to fully protect against Cache Probing attacks. Nonetheless, a website might mitigate the attack surface by deploying [Cache Protections]({{< ref "cache-protections.md" >}}), such as:
- [Cache-control header]({{< ref "cache-protections.md#cache-protection-via-cache-control-headers" >}})  used to prevent the resource from caching.
- [Random Tokens]({{< ref "cache-protections.md#cache-protection-via-random-tokens" >}}) used to make the URLs unpredictable for attackers.
- [Vary: Sec-Fetch-Site]({{< ref "cache-protections.md#cache-protection-via-fetch-metadata" >}}) used to segregate the cache by a group of origins.

A promising defense against this attack is [partitioning the HTTP cache]({{< ref "../defenses/secure-defaults/partitioned-cache.md" >}}) by the requesting origin. This browser provided protection prevents an attacker's origin from interfering with cached resources of other origins.

{{< hint warning >}}
As of November 2020, Partitioned Caches are not available in most browsers, so applications cannot rely on them.
{{< /hint >}}

## Real World Example

1. An attacker using the [Error Events Cache Probing]({{< ref "#cache-probing-with-error-events" >}}) could detect whether a user watched a specific YouTube Video by checking if the video thumbnail ended up in browser cache [^3].

## References

[^1]: Abusing HTTP Status Codes to Expose Private Information, [link](https://www.grepular.com/Abusing_HTTP_Status_Codes_to_Expose_Private_Information)
[^2]: HTTP Cache Cross-Site Leaks, [link](http://sirdarckcat.blogspot.com/2019/03/http-cache-cross-site-leaks.html)
[^3]: Mass XS-Search using Cache Attack, [link](https://terjanq.github.io/Bug-Bounty/Google/cache-attack-06jd2d2mz2r0/index.html#VIII-YouTube-watching-history)
[^4]: Timing Attacks on Web Privacy, [link](http://www.cs.jhu.edu/~fabian/courses/CS600.424/course_papers/webtiming.pdf)
