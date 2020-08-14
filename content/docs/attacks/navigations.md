+++
title = "Navigations"
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

Detecting if a cross-site page triggered a navigation can be useful to an attacker. This can be done in two ways:
- Using an `iframe` and counting the number of times the `onload` event is triggered.
- Checking the value of `History.length`, accessible through any `window` reference, gives the number of entries in the history of a victim either changed by `History.pushState` or regular navigations. To get the value of `History.length` an attacker changes the location of the `window` reference with the target website, changes back to same-origin, and finally reads the value.

### Why is this a problem?

Similarly to the [Frame Counting Attack]({{< ref "../attacks/frame-counting.md" >}}), triggering navigations can be problem when they are done depending on user private information. The main problem is the lack of consistency in pages to always have the same behavior.

#### Case Scenarios

- An online bank decides to redirect wealthy users to unmissable stock opportunities by triggering a navigation to a reserved space in the website when users are consulting the account balance. If this is only done to a specific group of users, it becomes possible for an attacker to leak the "client status" of the user.
<!--TODO(manuelvsousa): Add better examples-->

## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../defenses/opt-in/sec-fetch.md" >}})  | [COOP]({{< ref "../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |
|:----------------------------------:|:--------------------------:|:---------------:|:-----:|:--------------------:|
| iframe                             |         ✔️                 |      ✔️         |  ❌   |          ✔️         |
| `History.length` (iframe)          |         ✔️                 |      ✔️         |  ❌   |          ✔️         |
| `History.length` (window.open)     |         ✔️ (If Strict)     |      ✔️         |  ✔️   |           ❌        |

## Real World Example

A vulnerability reported to Twitter used this technique to leak the contents of private tweets using [XS-Search](https://TODO). This attack was possible because the page would only trigger a navigation depending on whether there were results to the user query [^1].

## References

[^1]: Protected tweets exposure through the url, [link](https://hackerone.com/reports/491473)
