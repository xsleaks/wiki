+++
title = "Performance API"
description = ""
date = "2020-10-01"
category = [
    "Attack",
]
abuse = [
    "window.performance",
]
defenses = [
    "SameSite Cookies",
    "CORB",
]
menu = "main"
weight = 2
+++
## Performance API
The [`Performance API`](https://developer.mozilla.org/en-US/docs/Web/API/Performance) provides access to performance-related information enhanced by the data from the [`Resource Timing API`](https://developer.mozilla.org/en-US/docs/Web/API/Resource_Timing_API)
which provides the timings of network requests such as the duration but when there’s a `Timing-Allow-Origin: *` header sent by the server the transfer size and domain lookup time is also provided.  
This data can be accessed by using [`performance.getEntries`](https://developer.mozilla.org/en-US/docs/Web/API/Performance/getEntries) or [`performance.getEntriesByName`](https://developer.mozilla.org/en-US/docs/Web/API/Performance/getEntriesByName)
It can also be used to get the execution time using the difference of [`performance.now()`](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now) however this seems to be less precise for a chrome fetch because it only provides the milliseconds.

## Get duration of a network request
It is possible to retrieve the network duration of a request from the `performance` API.

The below snippet performs a network request then after 200ms it gets the duration from the `performance` object. 

```javascript
async function getNetworkDuration(url) {
    let href = new URL(url).href;
    // Using an image instead of fetch() as some requests had duration = 0
    let image = new Image().src = href;
    // Wait for request to be added to performance.getEntriesByName();
    await new Promise(r => setTimeout(r, 200));
    // Get last added timings
    let res = performance.getEntriesByName(href).pop();
    console.log("Request duration: " + res.duration);
    return res.duration
}

await getNetworkDuration('https://example.org')
## Detect X-Frame-Options
If displaying a page inside an embed (e.g. because of the `X-Frame-Options` header) it will not be added to the `performance` object in Chrome.
If there was a known cached resource on page load firefox could also work.
```javascript
async function ifFrameBlocked(url) {
    let href = new URL(url).href;
    // There may be requests for this url before the function was run.
    let start_count = performance.getEntriesByName(href).length;
    let embed = document.createElement('embed');
    embed.setAttribute("hidden", true);
    embed.src = href;
    document.body.appendChild(embed);
    // Wait for request to be added to performance.getEntriesByName();
    await new Promise(r => setTimeout(r, 200));
    // Remove test embed
    document.body.removeChild(embed)
    return performance.getEntriesByName(href).length > start_count;
}
```

{{< hint note >}} This technique does seem to only work in Chromium based browsers {{< /hint >}}


## Detecting redirects
{{< hint important >}} This technique doesn't seem to work in Firefox {{< /hint >}}
On chrome the duration is negative when theres a redirect.
```javascript
async function ifRedirect(url) {  
    let href = new URL(url).href;
    await fetch(href, {mode:"no-cors"});
    // Wait for request to be added to performance.getEntriesByName();
    await new Promise(r => setTimeout(r, 200));
    // Get last added timings
    let res = performance.getEntriesByName(href).pop();
    console.log("Request duration: " + res.duration);
    if(res.duration >= 0) return false
    if(res.duration > -10) console.log("Redirect was cached");
    return true;
}
```
# Detect if a resource is cached
{{< hint important >}} If your using firefox please change the Image() to ```await fetch(href, {mode:"no-cors", credentials: "include"});``` {{< /hint >}}
Unless [Cross-Origin Read Blocking]({{< ref "../../defenses/secure-defaults/corb.md" >}}) is triggered (resource is html) the resource will get cached in the processs of the check.  
```javascript
async function ifCached2(url) {
    let href = new URL(url).href;
    // Using an image instead of fetch() as some requests had duration = 0
    let image = new Image().src = href;
    // Wait for request to be added to performance.getEntriesByName();
    await new Promise(r => setTimeout(r, 200));
    // Get last added timings
    let res = performance.getEntriesByName(href).pop();
    console.log("Request duration: " + res.duration);
    // Check if is 304
    if (res.encodedBodySize > 0 && res.transferSize > 0 && res.transferSize < res.encodedBodySize) return true
    if (res.transferSize > 0) return false;
    if (res.decodedBodySize > 0) return true;
    // Use duration if theirs no Timing-Allow-Origin header
    return res.duration < 10;
}
```
## Connection speed

It is possible to measure the speed of the connection in octets.
```javascript
async function getSpeed(count = 10) {
    var total = 0;
    for (let i = 0; i < count; i++) {
        await fetch(location.href, {cache: "no-store"});
        await new Promise(r => setTimeout(r, 200));
        let page = window.performance.getEntriesByName(location.href).pop();
        total += (page.responseEnd - page.responseStart) / page.transferSize;
    }
    return total/count
}
```
