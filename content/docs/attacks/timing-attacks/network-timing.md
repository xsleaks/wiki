+++
title = "Network Timing"
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

Timing side-channels have been present on the web since its beginning [^1]. These attacks gained different levels of reliability, gaining a different impact with the begging of the modern web when browsers started shipping high precision timers APIs

To obtain timing measurements attackers must use a [clock]({{< ref "../../defenses/opt-in/same-site-cookies.md" >}}), either an implicit or explicit one. For simplicity, this article will address only the `performance.now()` API, an explicit clock present in all modern browsers.

{{< hint info >}}
Learn more about the different types of clocks in the [Clocks Article]({{< ref "../../defenses/opt-in/same-site-cookies.md" >}}).
{{< /hint >}}

## History


## Cross-window timing 



## Modern Web Timing Attacks



# Case Scenarios





## Defense

<!-- | Attack Alternative  | [Same-Site Cookies]({{< ref "../../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../../defenses/opt-in/sec-fetch.md" >}})  | [Cross-Origin-Opener-Policy]({{< ref "../../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}) |
|:-------------------:|:------------------:|:---------------:|:-----:|:--------------------:|
| iframe              |         ✔️         |      ✔️         |  ❌   |          ✔️         |
| cross-window timing |         ✔️ (if Strict)       |      ✔️         |  ❌   |          ❌         | -->

<!-- {{< hint warning >}}
The [`portal`](https://web.dev/hands-on-portals/) element is only available on Chromium-based browsers under a preference flag. The corresponding specification is still under active discussion.
{{< /hint >}} -->


[^1]: Exposing Private Information by Timing Web Applications. [link](https://crypto.stanford.edu/~dabo/papers/webtiming.pdf)
[^2]: The Clock is Still Ticking: Timing Attacks in the Modern Web. [link](https://tom.vg/papers/timing-attacks_ccs2015.pdf)
[^3]: HEIST: HTTP Encrypted Information can be Stolen through TCP-windows. [link](https://www.blackhat.com/docs/us-16/materials/us-16-VanGoethem-HEIST-HTTP-Encrypted-Information-Can-Be-Stolen-Through-TCP-Windows-wp.pdf)
[^4]: Timeless Timing Attacks: Exploiting Concurrency to Leak Secrets over Remote Connections. [link](https://www.usenix.org/system/files/sec20-van_goethem.pdf)
