+++
title = "Cross-Origin Read Blocking"
description = ""
date = "2020-07-21"
category = "defenses"
menu = "main"
+++


## Explanation

Cross-Origin Read Blocking (CORB) is a browser defense mechanism that prevents attackers from loading certain cross-origin resources in impossible scenarios [^1]. This protection was created to prevent speculative side-channel attacks such as Spectre which allow attackers to read the memory of their own process. CORB aims to prevent attackers from loading cross-origin contents which might contain sensitive information into an attacker controlled process. For instance, if an attacker tries to load an  HTML, XML, or JSON as an `img` or `script` tag, CORB will prevent this from happening. To classify resource types CORB uses the `Content-Type` header but when it detects a page does not serve a `nosniff` header it detects if the resource is worth protecting by looking at the beginning of the response body.

{{< hint info >}}
Chrome is the only browser with CORB deployed.
{{< /hint >}}

{{< hint info >}}
An important complement of CORB is the [Cross-Origin Resource Policy (CORP)]({{< ref "../opt-in/corp.md" >}}).
{{< /hint >}}


## Considerations

{{< hint danger >}}
CORB introduced a [new set of XS-Leaks](https://TODO) since attackers are able to observe when a request is blocked or allowed by CORB. This leads to information leaks when CORB blocks certain requests depending on user information the attacker is after.
{{< /hint >}}

[^1]: Cross-Origin Read Blocking for Web Developers, [link](https://www.chromium.org/Home/chromium-security/corb-for-developers)
