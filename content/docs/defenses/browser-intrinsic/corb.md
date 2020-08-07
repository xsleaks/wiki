+++
title = "Cross-Origin Read Blocking"
description = ""
date = "2020-07-21"
category = "defenses"
menu = "main"
+++


## Explanation

Cross-Origin Read Blocking (CORB) is a browser defense mechanism that prevents attackers from loading certain cross-origin resources in a situation that does not make sense[^1]. This protection was created to prevent speculative side-channel attacks such as Spectre which allow attackers to read the memory of its process. CORB aims to prevent attackers from loading cross-origin contents into its own process, which might contain private data. For instance, if an attacker tries to load an  HTML, XML, or JSON as an `img` or `script` tag, CORB will prevent this from happening. To classify resource types CORB uses the `Content-Type` header but when it senses a page is serving content under the wrong `Content-Type` (e.g `text/plain`) it looks to the start of the resource to apply with the correct classification, thus protecting the resource.

{{< hint info >}}
Chrome is the only browser with CORB deployed.
{{< /hint >}}

{{< hint info >}}
An important complement of CORB is the [Cross-Origin Resource Policy (CORP)]({{< ref "../opt-in/corp.md" >}}).
{{< /hint >}}


## Considerations

{{< hint danger >}}
CORB introduced a [new set of XS-Leaks](https://TODO) as attackers are able to observe when a request is blocked or allowed by CORB. This could lead info information leaks if CORB blocks certain requests dependent on user information.
{{< /hint >}}

[^1]: Cross-Origin Read Blocking for Web Developers, [link](https://www.chromium.org/Home/chromium-security/corb-for-developers)
<!-- [^2]: Cross-Origin Read Blocking for Web Developers, [link](https://www.chromium.org/Home/chromium-security/site-isolation) -->
