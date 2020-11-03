+++
title = "Scroll to Text Fragment"
description = ""
date = "2020-10-01"
category = "Experiments"
abuse = [
    "onblur",
    "focus",
    "iframes",
]
menu = "main"
+++

Scroll to Text Fragment (STTF) is a new web platform feature that allows users to create a link to any part of a web page text. The fragment `#:~:text=` carries a text snippet that is highlighted and brought into the viewport by the browser. This feature can introduce a new XS-Leak if attackers are able to detect when this behavior occurs. This issue is very similar to the [Scroll to CSS Selector](https://docs.google.com/document/d/15HVLD6nddA0OaI8Dd0ayBP2jlGw5JpRD-njAyY1oNZo/edit#heading=h.wds2qckm3kh5) XS-Leak.

## Expected & Discussed Issues

In early discussions for the specification of this feature it was shown that several XS-Leaks could be introduced with a naïve implementation [^1]. The specification considers various attack scenarios [^3], as does some research from Google [^4]. One possible XS-Leak browsers need to be aware of when implementing this feature is:

- An attacker can, by embedding a page as an `iframe`, detect whether the page scrolled to the text by listening to the `onblur` of the parent document. This approach is similar to the [ID Attribute XS-Leak]({{< ref "id-attribute.md" >}}). This scenario is mitigated in the Chrome implementation [^5] as it only allows the fragment navigation to occur in top-level navigations.

## Current Issues

{{< hint info >}}
These XS-Leaks require some type of markup injection on the target page.
{{< /hint >}}

During the development process of STTF new attacks and tricks to detect a fragment navigation were found. Some of them still work:

- A web page that embeds an attacker-controlled `iframe` might allow the attacker to determine whether a scroll to the text has occurred. This can be done using the [`IntersectionObserver`](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) API [^2] [^3] [^4].
- If a page contains images with [Lazy Loading](https://web.dev/native-lazy-loading/) an attacker might known if a fragment navigation that included an image occurred by checking whether it was [cached in the browser]({{< ref "../cache-probing.md" >}}). This occurs because [Lazy Loading](https://web.dev/native-lazy-loading/) images are only fetched (and cached) when they appear in the viewport.

{{< hint warning >}}
Scroll to Text Fragment is only available in Chrome. Its [draft](https://wicg.github.io/scroll-to-text-fragment/) specification is under active discussion.
{{< /hint >}}

{{< hint good >}}
Scroll to Text Fragment XS-Leaks allow attackers to extract 1 bit of information at a time as it's only possible to observe whether a group of words is present in a page. This is because STTF matching is based on words, so attackers won't be able to leak information character by character.
{{< /hint >}}

## Why is this a problem?

Attackers can abuse STTF to leak private information about the user that is displayed on a web page.

### Case Scenarios

- A user is logged in to their National Health System website, where it is possible to access information about the user's past diseases and health problems. An attacker can lure the user to one of their pages and use STTF to possibly infer the user's health details. For example an attacker would find out if the victim suffers from a disease if they detect a page scroll when searching for that disease name.


## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../../defenses/opt-in/fetch-metadata.md" >}})  | [COOP]({{< ref "../../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}) |
|:-------------------:|:------------------:|:---------------:|:-----:|:--------------------:|
| IntersectionObserver (iframes)|         ❌         |      ❌         |  ❌   |          ❌         |
| Lazy Loading        |         ✔️         |      ✔️         |  ❌   |          ❌         |

## References

[^1]: Privacy concerns with proposal through inducing network requests, [link](https://github.com/WICG/scroll-to-text-fragment/issues/76)
[^2]: Possible side-channel information leak using IntersectionObserver, [link](https://github.com/WICG/scroll-to-text-fragment/issues/79)
[^3]: Text Fragments - Security and Privacy, [link](https://wicg.github.io/scroll-to-text-fragment/#security-and-privacy)
[^4]: Scroll-to-text Fragment Navigation - Security Issues, [link](https://docs.google.com/document/d/1YHcl1-vE_ZnZ0kL2almeikAj2gkwCq8_5xwIae7PVik/edit#)
[^5]: Boldly link where no one has linked before: Text Fragments, [link](https://web.dev/text-fragments/#privacy)
