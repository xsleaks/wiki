+++
title = "ID Attribute"
description = ""
date = "2020-07-21"
category = "attacks"
attacks = [
    "ids",
]
defenses = [
    "same-site cookies",
]
menu = "main"
+++

## Introduction

The `id` attribute is widely used to identify `HTML` elements. Unfortunately, these `ids` can be leaked by leveraging the `focus` event and the `URL` fragment. There are two alternatives to complete this attack:

- By using an `iframe` an attacker can load the target website once and bruteforce the `URL` hash until the `focus` event fires. The `onblur` event can be used to detect when the target has focus [^1].
- If the website sets [framing protections]({{< ref "../defenses/opt-in/xfo.md" >}}), the previous technique can be applied using the `portal` element [^2].



### Why is this a problem?

Using `ids` is very common

#### Specific Case Scenarios

- 
- 


## How can I protect my applications?

To stop attackers from abusing this XS-Leak on pages through `iframes` developers should use the following protections:

- {{< hint info >}}
Forbid cross-origin pages from framing pages using [framing protections]({{< ref "../defenses/opt-in/xfo.md" >}}).
{{< /hint >}}
- {{< hint danger >}}
In case a developer must allow others to frame its pages, [this defensive guideline]({{< ref "../defenses/design-protections/defensive-design.md" >}}) should be considered to avoid the introduction of this XS-Leak.
{{< /hint >}}

To stop attackers from abusing this XS-Leak on pages through `portal` developers should use the [generic XS-Leak Protections](TODO) to prevent attackers from performing cross-site requests freely. 

{{< hint warning >}}
The [`portal`](https://web.dev/hands-on-portals/) element is only available on Chromium-based browsers under a preference flag. The corresponding specification is still under active discussion.
{{< /hint >}}

## References

[^1]: Leaking IDs using focus, [link](https://portswigger.net/research/xs-leak-leaking-ids-using-focus)

[^2]: Detecting IDs using Portal, [link](https://portswigger.net/research/xs-leak-detecting-ids-using-portal)
