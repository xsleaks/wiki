+++
title = "Scroll to Text Fragment"
description = ""
date = "2020-07-21"
category = "attacks"
attacks = [
    "dom property",
]
defenses = [
    "same-site cookies",
    "sec-fetch metadata",
]
menu = "main"
+++

## Explanation

`Scroll to Text Fragment` (STTF) is a new web platform feature that allows users to create a link to any part of a web page text. The fragment `#:~:text=` carries a text snippet that is highlighted and brought in the viewport by the browser. This feature can introduce a new XS-Leak if attackers are able to detect when this behavior occurs. This issue is very similar to the [Scroll to CSS Selector](https://TODO) XS-Leak.

In early discussions for the specification of this feature, it was showed several XS-Leaks could be introduced with a naive implementation [^1]. The specification considers various attack scenarios [^3], so does some research from Google [^4]. These are some of the **possible** XS-Leaks browsers would need to be aware of when implementing this feature:

- A web page that embeds an attacker-controlled iframe might allow the attacker to determine whether a scroll to the text has occurred. This can be done using the [`IntersectionObserver`](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) API [^2] [^3] [^4].
- An attacker can, by embedding the page as an `iframe`, detect if the paged scrolled to the text by listening to the `blur` of the parent document. This approach is similar to the [ID Attribute XS-Leak]({{< ref "id-attribute.md" >}}). This scenario is mitigated in Chrome implementation [^5] as it only allows the fragment navigation to occur in top-level navigations.

Apart from browsers implementation, these issues are also application dependent, not feasible all the time. Nevertheless, XS-Leaks [Defense Mechanisms](({{< ref "../../defenses/" >}})) should be applied to reduce the attack surface of possible exploitation.

{{< hint warning >}}
`Scroll to Text Fragment` is only available in Chrome. Its [draft](https://wicg.github.io/scroll-to-text-fragment/) specification is under active discussion.
{{< /hint >}}

### Why is this a problem?

Attackers can abuse the principle of the `STTF` to leak private information about the user, since its highly connected with the contents of a web page.

#### Case Scenarios

- A user is logged in its National Health System website, where its possible to access information about the user past diseases and health problems. An attacker can lure the user to one of its pages and use `STTF` to possibly infer specific deceases of the user. The attacker will know the user suffers "Diabetes" if it detects a page scroll.


## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata](https://TODO)  | [Cross-Origin-Opener-Policy]({{< ref "../../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}) |
|:-------------------:|:------------------:|:---------------:|:-----:|:--------------------:|
| IntersectionObserver (iframes)|         ✔️         |      ✔️         |  ❌   |          ✔️         |
| IntersectionObserver (portals)|         ✔️         |      ✔️         |  ❌   |          ❓         |
| Lazy Loading        |         ✔️         |      ✔️         |  ❌   |          ❌         |

[^1]: Privacy concerns with proposal through inducing network requests, [link](https://github.com/WICG/scroll-to-text-fragment/issues/76)
[^2]: Possible side-channel information leak using IntersectionObserver, [link](https://github.com/WICG/scroll-to-text-fragment/issues/79)
[^3]: Text Fragments - Security and Privacy, [link](https://wicg.github.io/scroll-to-text-fragment/#security-and-privacy)
[^4]: Scroll-to-text Fragment Navigation - Security Issues, [link](https://docs.google.com/document/d/1YHcl1-vE_ZnZ0kL2almeikAj2gkwCq8_5xwIae7PVik/edit#)
[^5]: Boldly link where no one has linked before: Text Fragments, [link](https://web.dev/text-fragments/#privacy)