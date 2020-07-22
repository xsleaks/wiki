+++
title = "Frame Counting"
description = ""
date = "2020-07-21"
category = "attacks"
attacks = [
    "dom property",
    "second example",
]
defenses = [
    "XFO",
]
menu = "main"
+++

## Description

Using iframes can be useful in a lot of ways, and they don’t necessarily bring any risk when used properly. However in particular cases


## How to protect my applications?

You can avoid this XS-Leak using one the following options:

- {{< hint good >}}
Forbidd cross-origin pages from framing your pages using [framing protections]({{< ref "../defenses/opt-in/xfo.md" >}}).
{{< /hint >}}
- {{< hint warning >}}
In case you must allow others to frame your pages, you should consider [this defensive guideline]({{< ref "../defenses/opt-in/xfo.md" >}}) to avoid this XS-Leak.
{{< /hint >}}

