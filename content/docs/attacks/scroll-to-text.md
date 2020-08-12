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

`Scroll to Text Fragment` (STTF) is a new web platform feature that allows users to create a link to any part of a web page text. The fragment `#:~:text=` carries a text snippet that is highlighted by the browser, and calls the user's attention by scrolling the window to focus the specified text(fragment navigation). This feature can introduce a new XS-Leak if attackers are able to detect when this behavior occurs. This issue is very similar to the [Scroll to CSS Selector](https://TODO) XS-Leak.

In early discussions for the specification of this feature, several XS-Leaks would arise with a naive implementation [^1]. The specification considers various attack scenarios [^3], so does some research from Google [^4]. These are some of the **possible** XS-Leaks browsers would need to avoid when implementing the specification:

- A webpage which embeds an attacker controlled iframe might allow said attacker to determine whether a scroll to the text has occurred. This can be done using the `IntersectionObserver` API [^2] [^3] [^4].
- An attacker by embedding the page as an `iframe`, can detect if the paged scrolled to the text by listening to the `blur` of the parent document. This approach is similar to the [ID Attribute XS-Leak](https://TODO). This scenario is prevented in Chrome implementation [^5] as it only allows the fragment navigation to occur in top-level navigations.

Apart from the browsers implementation, they are also application depend, so not feasible in most of the times. Nevertheless, XS-Leaks [Defense Mechanisms](({{< ref "../defenses/" >}})) can be applied to reduce the attack surface of a possible exploitation.

{{< hint warning >}}
`Scroll to Text Fragment` is only available on Chrome. Its draft specification is under active discussion.
{{< /hint >}}

### Why is this a problem?



#### Case Scenarios

- A user is logged in its national heath system, and its profile contains information regarding the asdkjn asjkdnasjndjsakndjkasndjsandjasndjkasnjdnasndjsakndjasndjnasdnjasndjksndjasndjnasjkdnajksndjkandjkasndjksa
- loasdnjkasdn askjdnasjkndajks ndjkasn djkasn djkas ndjksan dasjk ndasjk ndsajkdn asjk ndjkasndjksandjkasndjkasndjaksndasjkndjskandjksandjksanjksandjksan jkas ndjsak ndjaksndjkasndjksandjkasndjsandjksandjkasnjkdsa


[^1]: Privacy concerns with proposal through inducing network requests, [link](https://github.com/WICG/scroll-to-text-fragment/issues/76)
[^2]: Possible side-channel information leak using IntersectionObserver, [link](https://github.com/WICG/scroll-to-text-fragment/issues/79)
[^3]: Text Fragments - Security and Privacy, [link](https://wicg.github.io/scroll-to-text-fragment/#security-and-privacy)
[^4]: Scroll-to-text Fragment Navigation - Security Issues, [link](https://docs.google.com/document/d/1YHcl1-vE_ZnZ0kL2almeikAj2gkwCq8_5xwIae7PVik/edit#)
[^5]: Boldly link where no one has linked before: Text Fragments, [link](https://web.dev/text-fragments/#privacy)