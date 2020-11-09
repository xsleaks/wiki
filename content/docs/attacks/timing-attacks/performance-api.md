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
It can also be used to get the execution time using the difference of [`performance.now()`](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now) however this seems to be less precise.

# Resource Timing API
Using the [`Resource Timing API`](https://developer.mozilla.org/en-US/docs/Web/API/Resource_Timing_API) you can get the timings of network requests.  
The duration is provided for all requests but when there’s a `Timing-Allow-Origin: *` header sent by the server the [`transfer size`](https://developer.mozilla.org/en-US/docs/Web/API/PerformanceResourceTiming/transferSize) and domain lookup time is also provided.

## Get duration of a network request
```javascript
async function getDuration(url) {
    // Using an image instead of fetch() as some requests had duration = 0
    let image = new Image();
    image.src = url;
    // Wait for request to be added to performance.getEntries();
    await new Promise(r => setTimeout(r, 1000));
    // Get last added timings
    let res = performance.getEntries().pop();
    console.log("Request duration: " + res.duration);
    return res.duration
}
```
## Detect X-Frame-Options
If a frame embed is blocked it will not be added to performance.getEntries  
The URL needs to end with a forward slash.
```javascript
async function ifFrame(url) {
    let embed = document.createElement('embed');
    embed.setAttribute("hidden", true);
    embed.src = url;
    document.body.appendChild(embed);
    // Wait for request to be added to performance.getEntries();
    await new Promise(r => setTimeout(r, 1000));
    // Remove test embed
    document.body.removeChild(embed)
    return performance.getEntriesByName(url).length > 0;
}
```
## Detect X-Frame-Options
If a frame embed is blocked it will not be added to performance.getEntries  
The URL needs to end with a forward slash.
```javascript
async function ifFrame(url) {
    let embed = document.createElement('embed');
    embed.setAttribute("hidden", true);
    embed.src = url;
    document.body.appendChild(embed);
    // Wait for request to be added to performance.getEntries();
    await new Promise(r => setTimeout(r, 1000));
    // Remove test embed
    document.body.removeChild(embed)
    return performance.getEntriesByName(url).length > 0;
}
```
## Detect if a redirect is used or cached
```javascript
async function ifRedirect(url) {
    await fetch(url, {mode:"no-cors"});
    // Wait for request to be added to performance.getEntries();
    await new Promise(r => setTimeout(r, 1000));
    // Get last added timings
    let res = performance.getEntries().pop();
    console.log("Request duration: " + res.duration);
    if(res.duration >= 0) return false
    if(res.duration > -10) console.log("Redirect was cached");
    return true;
}
```
