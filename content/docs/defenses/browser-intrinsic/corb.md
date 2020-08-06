+++
title = "Cross-Origin Read Blocking"
description = ""
date = "2020-07-21"
category = "defenses"
menu = "main"
+++


## Explanation

Cross-Origin Read Blocking (CORB) is a browser defense mechanism which prevents malicious origin from loading certain cross-origin resources. [^1]

Chrome is the only browser with CORB deployed.

Developers can extend this feature to other 

An important complement of CORB is the [Cross-Origin Resource Policy (CORP)]({{< ref "../opt-in/corp.md" >}}) which allow applications to **opt in to protection** against other resource requests that might not be covered by CORB itself.


## Considerations

{{< hint danger >}}
CORB ended up introducing a [new set of XS-Leaks](https://TODO) as attackers are able to observe whether a request is blocked or allowed by CORB. This could lead info information leaks if CORB blocks certain requests dependent on user information.
{{< /hint >}}

[^1]: Cross-Origin Read Blocking for Web Developers, [link](https://www.chromium.org/Home/chromium-security/corb-for-developers)
<!-- [^2]: Cross-Origin Read Blocking for Web Developers, [link](https://www.chromium.org/Home/chromium-security/site-isolation) -->
