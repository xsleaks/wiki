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

## Explanation

The `id` attribute is widely used to identify some `HTML` elements. Unfortunately, these `ids` can be leaked by leveraging the `focus` event and the `URL` fragment. There are two alternatives to complete this attack:

- By using an `iframe` an attacker can load the target website once and brute-force the `URL` hash until the `focus` event fires. The `onblur` event can be used to detect when the target has focus [^1].
- If the website sets [framing protections]({{< ref "../defenses/opt-in/xfo.md" >}}), the previous technique can be applied using the `portal` element [^2].


### Why is this a problem?

Sometimes web applications set `id` attributes in `focusable` elements that may lead to user information disclosure. These `ids` either be direct information related to the user (i.e a secret) or information associated with a user state (i.e account status).

#### Case Scenarios

- A bank allows its clients to generate 4 digit One-Time PINs (OTP) in the browser application to authenticate sessions on the mobile. The bank did a couple of mistakes and put the 4 digit OTP as the `id` of a `button`, used to show the PIN to the client. The attacker notices it can make a `GET` request to that endpoint, and by targeting clients with this XS-Leak, it might be possible to compromise their accounts.
- A web application uses a specific set of predefined `ids` and `HTML` elements when an account has a premium status. The attacker can detect whether a specific `id` is present in the victim's page and leak the account status.


## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../defenses/opt-in/sec-fetch.md" >}})  | [Cross-Origin-Opener-Policy]({{< ref "../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |
|:-------------------:|:------------------:|:---------------:|:-----:|:--------------------:|
| iframe              |         ✔️         |      ✔️         |  ❌   |          ✔️         |
| portal              |         ✔️         |      ✔️         |  ❌   |          ❌         |

{{< hint warning >}}
The [`portal`](https://web.dev/hands-on-portals/) element is only available on Chromium-based browsers under a preference flag. The corresponding specification is still under active discussion.
{{< /hint >}}



## References

[^1]: Leaking IDs using focus, [link](https://portswigger.net/research/xs-leak-leaking-ids-using-focus)

[^2]: Detecting IDs using Portal, [link](https://portswigger.net/research/xs-leak-detecting-ids-using-portal)
