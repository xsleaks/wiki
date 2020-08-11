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

`Scroll to Text Fragment` is a new web platform feature that allows users to create a link to any part of the web page text. The fragment `#:~:text=` carries a text snippet that is highlighted by the browser, and calls the user's attention by focusing the window to that part.
This issue is very similar to the [Scroll to CSS Selector](https://TODO) XS-Leak.

In early discussions of this new feature some security problems, including attack vectors of XS-Leaks[^1].lallalalalalla [^3] [^4] [^5]

- adsdbasjdnask
- asdb asbkjdbaskjdas
- askndsandjsakndkasjdas

Some of the described issues can be avoided depending on how browsers implement the specification. For instance, the `blur` event trigger scenario cannot happen with Chrome implementation[^5]. using the `IntersectionObserver` API, allow attackers to detect when the focus tri[^2].



### Why is this a problem?



#### Case Scenarios

- A user is logged in its national heath system, and its profile contains information regarding the

## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../defenses/opt-in/sec-fetch.md" >}})  | [Cross-Origin-Opener-Policy]({{< ref "../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |
|:-----------------------:|:------------------:|:---------------:|:-----:|:--------------------:|
| IntersectionObserver    |         ✔️         |      ✔️         |  ❌   |          ✔️         |

{{< hint warning >}}
`Scroll to Text Fragment` is only available on Chrome. Its specification is under active discussion.
{{< /hint >}}

[^1]: Privacy concerns with proposal through inducing network requests, [link](https://github.com/WICG/scroll-to-text-fragment/issues/76)
[^2]: Possible side-channel information leak using IntersectionObserver, [link](https://github.com/WICG/scroll-to-text-fragment/issues/79)
[^3]: Text Fragments - Security and Privacy, [link](https://wicg.github.io/scroll-to-text-fragment/#security-and-privacy)
[^4]: Scroll-to-text Fragment Navigation - Security Issues, [link](https://docs.google.com/document/d/1YHcl1-vE_ZnZ0kL2almeikAj2gkwCq8_5xwIae7PVik/edit#)
[^5]: Boldly link where no one has linked before: Text Fragments, [link](https://web.dev/text-fragments/#privacy)