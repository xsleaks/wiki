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

[Cross-Origin Read Blocking](({{< ref "../defenses/browser-intrinsic/corb.md" >}})) (CORB) is a web platform security feature to reduce the impact of speculative side-channel attacks such as Spectre. Unfortunately, the principle of blocking certain types of requests introduced a new type of XS-Leaks, allowing attackers to detect CORB was enforced in one request, but wasn't on another. Nevertheless, the introduced XS-Leaks are much less problematic than the issues actively protected by CORB (e.g Spectre).

## CORB & Error Events

To detect when CORB is enforced, the following steps could be observed in order **to leak the status code** returned by a page:

1. An attacker can embed a cross-origin resource in a `script` tag which returns `200 OK` with `text/html` as `Content-Type` and a `nosniff` Header.
2. To protect sensitive contents from entering the attacker's process, `CORB` will replace the original response with an **empty** one. 
3. Since an **empty** response is **valid** JavaScript, the `onerror` event won't be fired and `onload` will fire instead. 
4. The attacker triggers a second request (corresponding to a second state), similar to 1., which returns something other than `200 OK`. The `onerror` event will fire.

The interesting behavior is that CORB creates a valid resource out of request which could contain something other than JavaScript (causing an error). Considering a non-CORB environment, both 1. and 4. requests would trigger an error. This introduces an XS-Leak as these situations are now distinguishable.

## Detect `nosniff` Header

CORB could also allow attackers do detect when the `nosniff` Header is present in the request. The example bellow shows two different states that could occur, and allow an attacker to distinguish both:

1. CORB will prevent an attacker page which embeds a resource as a `script` if the resource is served with `text/html` as `Content-Type` along with the `nosniff` Header. 
2. If the resource does not set `nosniff` and CORB [fails](https://chromium.googlesource.com/chromium/src/+/master/services/network/cross_origin_read_blocking_explainer.md#what-types-of-content-are-protected-by-corb) to infer the `Content-Type` of the page (which remains `text/html`), a `SyntaxError` will be fired since the contents can't be parsed as valid JavaScript. This error can be caught by listening to `window.onerror` as `script` tags only trigger error events in [certain conditions](https://developer.mozilla.org/en-US/docs/Web/API/HTMLScriptElement).


## Case Scenarios

- An Attacker wants to identify if Jane Doe visits its site. To perform this, the attacker exploits this XS-Leak by attacking a social network Jane might use. The attacker notices https://social-network.com/user/janedoe/edit is only accessible to Jane, causing an error for other users. Since this endpoint is vulnerable to this XS-Leak, the attacker can identify the identity of Jane by detecting when CORB strips the response and trigger the `onload` event.

## Defense


| [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata](https://TODO)  | [Cross-Origin-Opener-Policy]({{< ref "../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |
|:------------------:|:---------------:|:-----:|:--------------------:|
|         ✔️         |      ✔️         |  ❌   |          ❌         |


{{< hint info >}}
Chromium is the only browser currently implementing [CORB](https://TODO). With the [default rollout](https://www.chromium.org/updates/same-site) of [Same-Site cookies](https://TODO) this attack becomes fairly **powerless**.
{{< /hint >}}

{{< hint info >}}
This issue is known by Chromium, and while it might remain unfixed [^1], its impact is highly reduced by the [rollout of Same-Site Cookies by default](https://blog.chromium.org/2020/05/resuming-samesite-cookie-changes-in-july.html) in Chromium-based browsers.
{{< /hint >}}

{{< hint info >}}
Developers can deploy [CORP](https://TODO) in application resources to force a protection similar to CORB that does not inspect responses to decide when to act. To prevent attackers from abusing this XS-Leak, generic XS-Leaks defense mechanisms are also effective.
{{< /hint >}}

[^1]: CORB vs side channels, [link](https://docs.google.com/document/d/1kdqstoT1uH5JafGmRXrtKE4yVfjUVmXitjcvJ4tbBvM/edit?ts=5f2c8004)
