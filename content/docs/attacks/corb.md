+++
title = "CORB Leaks"
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

[Cross-Origin Read Blocking](({{< ref "../defenses/browser-intrinsic/corb.md" >}})) (CORB) is a web platform security feature to reduce the impact of speculative side-channel attacks such as Spectre. Unfortunately, the principle of blocking certain types of requests introduced new types of XS-Leaks.

An attacker tries to embed a cross-origin resource in a `script` tag which returns `200 OK` with `text/html` as `Content-Type` and a `nosniff` Header, in order to protect sensitive contents from entering the attacker's process, `CORB` will replace the original response with an empty one. Since an empty response is **valid** JavaScript, the `onerror` event won't be fired and `onload` will fire instead. This behavior is unfortunate, as the response could contain something other than JavaScript, considering a non-CORB environment would trigger an error. On the other hand, if the request returns something other than `200 OK` the `onerror` event will fire, and the same would happen in a non-CORB environment. This introduces an XS-Leaks as these situations are now distinguishable.


### Why is this a problem?

The implications of this attack are similar to the [Error-based](https://TODO) and [CORP](https://TODO) XS-Leaks. This issue allows attackers to get a **difference** from the same request, which occurs depending on the targeted user.

#### Case Scenarios

- An Attacker wants to identify if Jane Doe visits its site. To perform this, the attacker exploits this XS-Leak by attacking a social network Jane might use. The attacker notices https://social-network.com/user/janedoe/edit is only accessible to Jane, causing an error for other users. Since this endpoint is vulnerable to this XS-Leak, the attacker can identify the identity of Jane by detecting when CORB strips the response and trigger the `onload` event.

## Defense

There is no clear solution on the browser side to mitigate this issue [^1]. Particularly to this XS-Leak, developers can add a `CSRF` token to the vulnerable endpoints or deploy [CORP](https://TODO).

| Attack Alternative  | [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../defenses/opt-in/sec-fetch.md" >}})  | [COOP]({{< ref "../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |  [CSRF Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |  [CORP](https://TODO)
|:-------------------:|:------------------:|:---------------:|:-----:|:--------------------:|:--------------------:|:--------------------:|
| iframe              |         ✔️         |      ✔️         |  ❌   |          ❌         |         ✔️          |         ✔️            |



{{< hint warning >}}
[CORP](https://TODO) must be applied in **all** endpoints for defense completeness and to avoid introducing similar issues like [CORP XS-Leaks](https://TODO).
{{< /hint >}}

[^1]: CORB vs side channels, [link](https://docs.google.com/document/d/1kdqstoT1uH5JafGmRXrtKE4yVfjUVmXitjcvJ4tbBvM/edit?ts=5f2c8004)
