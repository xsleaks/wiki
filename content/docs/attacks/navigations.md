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
- Using an `iframe`, counting the number of times the `onload` event is triggered.
- The value of `History.Length`, accessible through a `window` reference, gives the number of entries in the history of a victim, triggered by `History.pushState` or regular navigations.

### Why is this a problem?

This XS-Leak is very feature s

#### Case Scenarios

- 

## Real World Example

A vulnerability reported to Twitter, used this XS-Leak to leak contents of private tweets using [XS-Search](https://todo). This attack was possible because the page would only trigger a navigation depending on whether there were results to the user query [^1].


## How can I protect my applications?

Developers are encoraged to follow the generic XS-Leaks mitigations to prevent attackers from making authenticated cross-site requests. There are a couple of mechanisms that can be applied:

- {{< hint info >}}
Implement [Same-Site Cookies]({{< ref "../defenses/opt-in/coop.md" >}}) on web applications to force browsers so send cookies, if the request comes from same-site.
{{< /hint >}}

- {{< hint info >}}
Process [Fetch-Metadata Headers]({{< ref "../defenses/opt-in/coop.md" >}}) to get more information about the context in which requests are made. This allows applications to make better decisions when responding to certain requests.
{{< /hint >}}

To stop attackers from abusing this XS-Leak on pages through `iframes` developers can use the following protections:

- {{< hint info >}}
Forbidd cross-origin pages from framing pages using [framing protections]({{< ref "../defenses/opt-in/xfo.md" >}}).
{{< /hint >}}
- {{< hint danger >}}
In case a developer must allow others to frame its pages, [this defensive guideline]({{< ref "../defenses/opt-in/xfo.md" >}}) should be considered to avoid the introduction of this XS-Leak.
{{< /hint >}}

To stop attackers from abusing this XS-Leak on pages through **another** `window` reference, developers can use the following protection:

- {{< hint info >}}
Deploy [Cross-Origin-Opener-Policy]({{< ref "../defenses/opt-in/coop.md" >}}) on the web application to control who is allowed to have a `window` reference on a page.
{{< /hint >}}

## References

[^1]: Protected tweets exposure through the url, [link](https://hackerone.com/reports/491473)
