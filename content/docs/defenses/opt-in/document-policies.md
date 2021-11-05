+++
title = "Document Policies"
category = [
    "Defense",
]
menu = "main"
+++

`Document-Policy` is an experimental mechanism, similar to another experimental Feature Policy [^2], used to cover features which are more about configuring a document, or removing features (sandboxing) from a document or a frame. [^1] It can be for example set in a header response as shown in the example below.

{{< hint example >}}
Document-Policy: unsized-media=?0, document-write=?0, max-image-bpp=2.0, frame-loading=lazy
{{< /hint >}}

# ForceLoadAtTop 
The ForceLoadAtTop feature provides an opt-out for [Scroll To Text]({{< ref "/docs/attacks/experiments/scroll-to-text-fragment.md" >}}) (and other load-on-scroll behaviors) for privacy sensitive sites. The feature allows sites to indicate that they should always be loaded at the top of the page, blocking any scroll-on-load behaviors including text fragments and element fragments. It can be set via `Document-Policy: force-load-at-top` response header. 

The feature could be useful in preventing attacks such as [ID Attribute]({{< ref "/docs/attacks/id-attribute.md" >}}) or [Scroll to Text Fragment]({{< ref "/docs/attacks/experiments/scroll-to-text-fragment.md" >}}).

# References
[^1]: Feature Policy, [link](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Feature-Policy)
[^2]: Document-Policy proposal, [link](https://github.com/wicg/document-policy/blob/main/document-policy-explainer.md)
[^3]: Document-Policy: force-load-at-top, [link](https://www.chromestatus.com/feature/5744681033924608)
