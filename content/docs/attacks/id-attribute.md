+++
title = "ID Attribute"
description = ""
date = "2020-10-01"
category = [
    "Attack",
]
abuse = [
    "onblur",
    "focus",
    "iframes",
]
defenses = [
    "Fetch Metadata",
    "SameSite Cookies",
    "Framing Protections",
]
menu = "main"
weight = 3
+++


The `id` attribute is widely used to identify some `HTML` elements. Unfortunately, these `ids` can be leaked by leveraging the `focus` event and the `URL` fragment. For example, some web applications set `id` attributes in `focusable` elements that may lead to user information disclosure. These `ids` can either be direct information related to the user (e.g a secret) or information associated with a user state (e.g account status). There are two alternatives to complete this attack:

- By using an `iframe` an attacker can load the target website once and brute-force the `URL` hash until the `focus` event fires. The `onblur` event can be used to detect when the target has focus [^1].

## Case Scenarios

- A bank allows its clients to generate 4 digit One-Time PINs (OTP) in the browser application to authenticate sessions on the mobile. The bank did a couple of mistakes and put the 4 digit OTP as the `id` of a `button`, used to show the PIN to the client. By targeting clients using the properties of this XS-Leak, it might be possible to compromise their accounts.
- A web application uses a specific set of predefined `ids` and `HTML` elements when an account has a premium status or the user is of a certain gender. The attacker can detect whether a specific `id` is present on the victim's page and leak the account information.


## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../defenses/opt-in/fetch-metadata.md" >}})  | [COOP]({{< ref "../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |
|:-------------------:|:------------------:|:---------------:|:-----:|:--------------------:|
| iframe              |         ✔️         |      ✔️         |  ❌   |          ✔️         |

## References

[^1]: Leaking IDs using focus, [link](https://portswigger.net/research/xs-leak-leaking-ids-using-focus)