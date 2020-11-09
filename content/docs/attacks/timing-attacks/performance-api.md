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
# Performance API
The [`Performance API`](https://developer.mozilla.org/en-US/docs/Web/API/Performance) provides access to performance-related information enhanced by the data from the [`Resource Timing API`](https://developer.mozilla.org/en-US/docs/Web/API/Resource_Timing_API)  
This data can be accessed by using [`performance.getEntries`](https://developer.mozilla.org/en-US/docs/Web/API/Performance/getEntries) or [`performance.getEntriesByName`](https://developer.mozilla.org/en-US/docs/Web/API/Performance/getEntriesByName)  
It can also be used to get the execution time using the difference of [`performance.now()`](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now) however this seems to be less precise for chrome.

# Resource Timing API
Using the [`Resource Timing API`](https://developer.mozilla.org/en-US/docs/Web/API/Resource_Timing_API) you can get the timings of network requests.  
The duration is provided for all requests but when there’s a `Timing-Allow-Origin: *` header sent by the server the [`transfer size`](https://developer.mozilla.org/en-US/docs/Web/API/PerformanceResourceTiming/transferSize) and domain lookup time is also provided.

# Get duration of a network request (Not firefox)
```javascript
async function getDuration(url) {
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
```
# Detect X-Frame-Options (Not firefox)
If a frame embed is blocked it will not be added to performance.getEntries  
```javascript
async function ifFrame(url) {
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
# Detect if a redirect is used or cached (Not firefox)
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
Unless [CORB](https://fetch.spec.whatwg.org/#corb) is triggered (resource is html) the resource will get cached in the processs of the check.  
If your using firefox please change the Image() to ```await fetch(href, {mode:"no-cors", credentials: "include"});```
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
