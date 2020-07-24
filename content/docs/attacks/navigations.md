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

## Introduction

Detecting if a cross-site page triggered a navigation can be useful to an attacker. This can be done in two ways:
- Using an `iframe` and counting the number of times the `onload` event is triggered.
- Checking the value of `History.length`, accessible through a `window` reference, gives the number of entries in the history of a victim either changed by `History.pushState` or regular navigations.

### Why is this a problem?

Similarly to the [Frame Counting Attack]({{< ref "../attacks/frame-counting.md" >}}), making Navigations are only a problem when they are triggered based on user private information. The main problem is the lack of consistency in pages to always have the same behavior.

#### Case Scenarios

- An online bank decides to redirect wealthy users to unmissable stock opportunities, by triggering a navigation to a reserved space in the website when users are consulting the account balance. If this is only done to a specific group of users, it becomes easy for an attacker to verify this situation and leak the "client status" of the user.
<!--TODO(manuelvsousa): Add better examples-->

## Real World Example

A vulnerability reported to Twitter, used this XS-Leak to leak the contents of private tweets using [XS-Search](https://todo). This attack was possible because the page would only trigger a navigation depending on whether there were results to the user query [^1].


## How can I protect my applications?

Developers are encouraged to apply generic XS-Leaks mitigations to prevent attackers from making authenticated cross-site requests. There are a couple of mechanisms that can be applied:

- {{< hint info >}}
Implement [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}}) on web applications to force browsers to send cookies only if the request comes from same-site (strict mode).
{{< /hint >}}

- {{< hint info >}}
Process [Fetch-Metadata Headers]({{< ref "../defenses/opt-in/sec-fetch.md" >}}) to get more information about the context in which requests are made. This allows applications to make better decisions when responding to certain requests.
{{< /hint >}}

To stop attackers from abusing this XS-Leak using `iframes` developers can use the following protections:

- {{< hint info >}}
Forbidd cross-origin pages from framing pages using [framing protections]({{< ref "../defenses/opt-in/xfo.md" >}}).
{{< /hint >}}
- {{< hint danger >}}
In case a developer must allow others to frame its pages, [this defensive guideline]({{< ref "../defenses/design-protections/defensive-design.md" >}}) should be considered to avoid the introduction of this XS-Leak.
{{< /hint >}}

Finally, to stop attackers from abusing this XS-Leak on pages using **other** `window` references, developers can use the following protection:

- {{< hint info >}}
Deploy [Cross-Origin-Opener-Policy]({{< ref "../defenses/opt-in/coop.md" >}}) on the web application to control who is allowed to have a `window` reference on a page.
{{< /hint >}}

## References

[^1]: Protected tweets exposure through the url, [link](https://hackerone.com/reports/491473)
