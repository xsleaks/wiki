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
- Using an `iframe`, counting the number of times the `onload` event triggered.
- Checking the value of `History.Length`, gives an attacker the number of entries in the history of the victim, triggered by `History.pushState` or regular navigations.

### Why is this a problem?




## Real World Example

A vulnerability reported to Twitter, leveraged `History.Length` [^1]


## How can I protect my applications?

Since this XS-Leak is attached tobut developers are encoraged to follow the generic XS-Leaks mitigations to prevent attackers from making cross-site requests. There are a couple of mechanisms that can be applied:

- {{< hint info >}}
Implement [Same-Site Cookies]({{< ref "../defenses/opt-in/coop.md" >}}) on web applications to force browsers to use user cookies only
{{< /hint >}}

- {{< hint info >}}
Use [Fetch-Metadata Headers]({{< ref "../defenses/opt-in/coop.md" >}}) on web applications to force browsers to use user cookies only
{{< /hint >}}

## References

[^1]: Protected tweets exposure through the url, [link](https://hackerone.com/reports/491473)
