+++
title = "baseURI"
description = ""
date = "2024-08-20"
category = [
    "Attack",
]
abuse = [
    "iframes",
]
defenses = [
    "Browser Fix",
    "Application Fix",
]
menu = "main"
weight = 3
+++

`document.baseURI` can be used in an opaque-origin sandboxed iframe to leak the full URL of an ancestor page.

If a URL has sensitive information, the origin, query, or fragment (hash) are most likely to contain this sensitive information.

## Sandboxed opaque-origin about:srcdoc iframe

An iframe loaded with `about:srcdoc` and sandboxed without `allow-same-origin` (i.e. has opaque origin) can read `document.baseURI` to leak the closest http(s):// origin document's URL.

This also works in nested frames, with the baseURI value set to the closest document's URL that has an http(s):// origin. For example, nesting multiple `about:srcdoc` within `https://example.com/path?query#hash` will still leak the full `example.com` URL.

## Code Snippet

Adapted from crbug 40867031[^crbug-40867031]:
1. Navigate to a URL, where the URL contains secrets. e.g. https://example.com/path?query#hash
2. Run the following JavaScript in DevTools:
```javascript
f = document.createElement("iframe");
f.sandbox = "allow-scripts";
f.srcdoc = "<script>document.write('origin: ' + origin + ', baseURI: ' + document.baseURI);</script>";
document.body.appendChild(f);
```

{{< hint info >}}
Technically this also works with `about:blank` (verified via DevTools), but only an extension might be able to script this, so it's not that useful.
{{< /hint >}}

## Verified Browser Versions
As of August 20th, 2024:
* Chrome 127.0.6533.120 Stable + 129.0.6668.9 Canary
* Edge 127.0.2651.105 Stable
* Firefox 128.0 Stable

## Defense


* HTML specification fix [^html-spec-9025]
* Browser fixes: [^crbug-40867031] [^crbug-330744612] 
* Application mitigation: Applications should avoid having sensitive information in URL if the page may include sandboxed `about:srcdoc` iframes with untrusted data.

____

## References

[^crbug-330744612]: Chromium bug: 
`Consider not inheriting base url in sandboxed srcdoc iframes`, [link](https://issues.chromium.org/issues/330744612)
[^crbug-40867031]: Chromium bug: 
`Consider limiting how much of URL is inherited for base URL`, [link](https://issues.chromium.org/issues/40867031)
[^html-spec-9025]: WHATWG HTML specification issue: Sandboxed iframes with opaque origin should not inherit fallback base URL, [link](https://github.com/whatwg/html/issues/9025)
