+++
title = "Sandboxed iframes"
description = ""
date = "2024-08-20"
category = [
    "Attack",
]
abuse = [
    "iframes",
]
menu = "main"
weight = 3
+++

## Base URL

`about:srcdoc` iframes inherit the parent's base URL if the initiator is same-origin with the parent. It can be read via `document.baseURI` property which contains the full URL of the parent.
(TODO(AO): Think about when to include and how to phrase "closest parent" in most sentences, since this seems relevant in most scenarios, but can make sentences hard to read.)

Currently, this behavior is preserved even for sandboxed iframes without `allow-same-origin` (i.e. opaque origin). This is problematic if the rendered content is untrusted, which is a common use case of opaque-origin `about:srcdoc` frames, and the parent's URL contains sensitive information. 

In cases where there are nested frames, and some frames do not have an `http(s)://` URL, then the closest parent with an `http(s)://` URL will be used.
{{< hint example >}}
`A(example.com) -> B(about:srcdoc, opaque origin) -> C(about:srcdoc)`, page C will have `document.baseURI` set to page A's full URL despite page B being the initiator.
{{< /hint >}}
(TODO(AO): Verify where Chromium checks for `http(s)://` (in origin or URL?). This is relevant in scenarios where non-opaque `about:blank` page creates `about:srcdoc` frames, or non-opaque `about:srcdoc` frame creates opaque `about:srcdoc` frames.)

This also works in nested frames, with the baseURI value set to the closest document's URL that has an `http(s)://` origin. For example, nesting multiple `about:srcdoc` within `https://example.com/path?query#hash` will still leak the full `example.com` URL.

{{< hint warning >}}
For frames with non-opaque origins, the origin is set to the initiator's origin. Untrusted code should not be run in non-opaque `about:srcdoc` frames since it has access to the same origin.
{{< /hint >}}

{{< hint info >}}
`about:blank` iframes inherit their initiator's (in most cases, a direct ancestor) base URL. This is less useful for attackers since this does not leak cross-origin URLs, but can still be useful to leak origins via `location.ancestorOrigins`.
{{< /hint >}}

### Code Snippet

The below snippet demonstrates how a sandboxed `about:srcdoc` iframe can leak its closest `http(s)://` parent's `document.baseURI`.
```javascript
f = document.createElement("iframe");
f.sandbox = "allow-scripts";
f.srcdoc = "<script>document.write('origin: ' + origin + ', baseURI: ' + document.baseURI);</script>";
document.body.appendChild(f);
```

{{< hint info >}}
As of August 2024, base URL inheritance works as described in this page in Chrome, Edge, and Firefox.
(TODO(AO): Re-verify all scenarios in Firefox and re-verify HTML spec alignment.)
{{< /hint >}}

## Defense

To prevent leaking full URLs, applications should avoid using opaque iframes with about:srcdoc to render untrusted code.
To prevent leaking URL origins, applications should avoid using opaque iframes to render untrusted code.
Browsers are researching ways of mitigating the issues with sandboxed iframes [^html-spec-9025] [^crbug-40867031] [^crbug-330744612].

____

## References

[^crbug-330744612]: Chromium bug: 
`Consider not inheriting base url in sandboxed srcdoc iframes`, [link](https://issues.chromium.org/issues/330744612)
[^crbug-40867031]: Chromium bug: 
`Consider limiting how much of URL is inherited for base URL`, [link](https://issues.chromium.org/issues/40867031)
[^html-spec-9025]: WHATWG HTML specification issue: Sandboxed iframes with opaque origin should not inherit fallback base URL, [link](https://github.com/whatwg/html/issues/9025)
