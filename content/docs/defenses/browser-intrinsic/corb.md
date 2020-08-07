+++
title = "Cross-Origin Read Blocking"
description = ""
date = "2020-07-21"
category = "defenses"
menu = "main"
+++


## Explanation

Cross-Origin Read Blocking (CORB) is a browser defense mechanism that prevents attackers from loading certain cross-origin resources in a situation that does not make sense[^1]. For instance, if an attacker tries to load an  HTML, XML, or JSON as an `img` or `script` tag, CORB will prevent this from happening. To classify resource types CORB uses the `Content-Type` header (and `nosniff`), but when this is not possible, it sniffs the contents of the resources to infer the type.

An important complement of CORB is the [Cross-Origin Resource Policy (CORP)]({{< ref "../opt-in/corp.md" >}}) which allow applications to **opt in to protection** against other resource requests that might not be covered automatically by CORB itself.

Chrome is the only browser with CORB deployed.

## CORB and XS-Leaks

Some XS-Leaks [timing attacks](https://TODO) and [size leaks](https://TODO) rely on loading `HTML` resources on `tags` that are not appropriate to those contents. Some attacks rely on this tags to . Alternatively, if an attacker tries to use the `video` or `audio` tag and its properties to infer the size of an HTML page, CORB should block.


## Considerations

{{< hint danger >}}
CORB introduced a [new set of XS-Leaks](https://TODO) as attackers are able to observe when a request is blocked or allowed by CORB. This could lead info information leaks if CORB blocks certain requests dependent on user information.
{{< /hint >}}

[^1]: Cross-Origin Read Blocking for Web Developers, [link](https://www.chromium.org/Home/chromium-security/corb-for-developers)
<!-- [^2]: Cross-Origin Read Blocking for Web Developers, [link](https://www.chromium.org/Home/chromium-security/site-isolation) -->
