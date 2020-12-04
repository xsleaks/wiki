+++
title = "CORB Leaks"
description = ""
date = "2020-10-01"
category = "Attack"
abuse = [
    "Browser Feature",
    "Error Events",
    "Content-Type",
    "nosniff",
]
defenses = [
    "Fetch Metadata",
    "SameSite Cookies",
]
menu = "main"
weight = 2
+++

[Cross-Origin Read Blocking]({{< ref "/docs/defenses/secure-defaults/corb.md" >}}) (CORB) is a web platform security feature aimed at reducing the impact of speculative side-channel attacks such as Spectre. Unfortunately, blocking certain types of requests introduced a new type of XS-Leaks [^1] that allows attackers to detect if CORB was enforced on one request, but wasn't on another. Nevertheless, the introduced XS-Leaks are much less problematic than the issues actively protected by CORB (e.g. Spectre).


{{< hint info >}}
This is a known issue in Chromium, and while it [might remain unfixed](https://docs.google.com/document/d/1kdqstoT1uH5JafGmRXrtKE4yVfjUVmXitjcvJ4tbBvM/edit?ts=5f2c8004), its impact is greatly reduced by the [rollout of SameSite Cookies by default](https://blog.chromium.org/2020/05/resuming-samesite-cookie-changes-in-july.html) in Chromium-based browsers.
{{< /hint >}}

## CORB & Error Events


Attackers can observe when CORB is enforced if a response returns a *CORB protected* `Content-Type` (and `nosniff`) with the status code `2xx` which results in CORB stripping the body and headers from the response. Detecting this protection allows an attacker to leak the combination of both the status code (success vs. error) and the `Content-Type` (protected by CORB or not). This allows the distinction of two possible states as shown in these examples:
- One state results in a request being protected by CORB and the second state in a client error (404).
- One state is protected by CORB and the second state is not.

The following steps allow abusing this protection in the context of the first example:

1. An attacker can embed a cross-origin resource in a `script` tag which returns `200 OK` with `text/html` as `Content-Type` and a `nosniff` Header.
2. To protect sensitive contents from entering the attacker's process, `CORB` will replace the original response with an empty one.
3. Since an empty response is valid JavaScript, the `onerror` event won't be fired, `onload` will fire instead.
4. The attacker triggers a second request (corresponding to a second state), similar to 1., which returns something other than `200 OK`. The `onerror` event will fire.

The interesting behavior is that CORB creates a valid resource out of the request which could contain something other than JavaScript (causing an error). Considering a non-CORB environment, both 1. and 4. requests would trigger an error. This introduces an XS-Leak as these situations are now distinguishable.

## Detecting the `nosniff` Header

CORB can also allow attackers to detect when the `nosniff` header is present in the request. This problem originated due to the fact that CORB is only enforced depending on the presence of this header and some sniffing algorithms. The example below shows two distinguishable states:

1. CORB will prevent an attacker page which embeds a resource as a `script` if the resource is served with `text/html` as `Content-Type` along with the `nosniff` header.
2. If the resource does not set `nosniff` and CORB [fails](https://chromium.googlesource.com/chromium/src/+/master/services/network/cross_origin_read_blocking_explainer.md#what-types-of-content-are-protected-by-corb) to infer the `Content-Type` of the page (which remains `text/html`), a `SyntaxError` will be fired since the contents can't be parsed as valid JavaScript. This error can be caught by listening to `window.onerror` as `script` tags only trigger error events under [certain conditions](https://developer.mozilla.org/en-US/docs/Web/API/HTMLScriptElement).

## Defense


| [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                                          [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                                          |
| :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :-----------------------------------------------------------------------------------------------------------------------------------------------------: |
|                                         ✔️                                          |                          ❌                          |                                 ❌                                 | [RIP]({{< ref "/docs/defenses/isolation-policies/resource-isolation" >}}) 🔗 [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}}) |

🔗 – Defense mechanisms must be combined to be effective against different scenarios.

{{< hint tip >}}
Developers can deploy [CORP]({{< ref "/docs/defenses/opt-in/corp.md" >}}) in an application's subresources to force a protection similar to CORB that does not inspect responses to decide when to act. To prevent attackers from abusing this XS-Leak, generic XS-Leaks defense mechanisms are also effective.
{{< /hint >}}

## References

[^1]: CORB vs side channels, [link](https://docs.google.com/document/d/1kdqstoT1uH5JafGmRXrtKE4yVfjUVmXitjcvJ4tbBvM/edit?ts=5f2c8004)
