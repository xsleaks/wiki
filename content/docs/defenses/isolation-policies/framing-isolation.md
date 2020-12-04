+++
title = "Framing Isolation Policy"
description = ""
date = "2020-11-30"
category = [
    "Defense",
]
menu = "main"
weight = 2
+++
Framing Isolation Policy is a stricter version of [Framing Protections]({{< ref "../opt-in/xfo" >}}) where the request gets blocked at the application level rather than by the browser. This is designed to protect against various attacks (e.g. XSSI, CSRF, XS-Leaks) by blocking framing requests to endpoints that are not intended to be framable.

It can be combined with [Resource Isolation Policy]({{< ref "resource-isolation.md" >}}) to effectively tighten the attack surface within cross-site information leaks.

{{< hint tip >}}
Instead of rejecting all non-framable endpoints, the user could be prompted to confirm the action, e.g. *Confirm that you visited this page from a trusted origin*, to mitigate the risk of attacks in the background, and, at the same time, help prevent unintended breakages of an application.
{{< /hint >}}

{{< hint tip >}}
When deployed together with [Resource Isolation Policy]({{< ref "resource-isolation.md" >}}), Framing Isolation Policy does not protect against leaks utilizing window references (e.g. `window.length`), so other navigational protections such as [COOP]({{< ref "../opt-in/coop" >}}) or [Navigation Isolation Policy]({{< ref "navigation-isolation" >}}) can be helpful.
{{< /hint >}}

## Implementation with Fetch Metadata

The below snippet showcases an example implemention of the Framing Isolation Policy by an application:

```py
# Reject cross-site requests to protect from CSRF, XSSI, XS-Leaks, and other bugs
def allow_request(req):
  # Allow requests from browsers which don't send Fetch Metadata
  if not req['headers']['sec-fetch-site']:
    return True
  if not req['headers']['sec-fetch-mode']:
    return True
  if not req['headers']['sec-fetch-dest']:
    return True

  # Allow non-navigational requests
  if req['headers']['sec-fetch-mode'] not in ('navigate', 'nested-navigate'):
    return True

  # Allow requests not originated from embeddable elements
  if req['headers']['sec-fetch-dest'] not in ('frame', 'iframe', 'embed', 'object'):
      return True

  # [OPTIONAL] Exempt paths/endpoints meant to be served cross-site.
  if req.path in ('/my_frame_ancestors_host_src'):
    return True

  # Reject all other requests
  return False
```

## Considerations
Framing Isolation Policy cannot be applied if an endpoint allows framing requests from specific origins via  `X-Frame-Options` and/or Content Security Policy's
`frame-ancestors` directive.
