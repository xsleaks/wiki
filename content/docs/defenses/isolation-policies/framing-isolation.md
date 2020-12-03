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
Framing Isolation Policy supplements [Resource Isolation Policy]({{< ref "resource-isolation.md" >}}) to protect against cross-origin information leaks by
additionally blocking framing requests to non-framable endpoints.

{{< hint tip >}}
Instead of rejecting all non-framable endpoints, the user could be prompted to confirm the action, e.g. *Confirm that you visited this page from a trusted origin* to mitigate the risk of attacks in the background. This could help with the unintended breakages of an application.
{{< /hint >}}

## Implementation with Fetch Metadata

The below snippet showcases an example implemention of the Framing Isolation Policy by an application.

```py
# Reject cross-origin requests to protect from CSRF, XSSI, and other bugs
def allow_request(req):
  # Allow requests from browsers which don't send Fetch Metadata
  if not req['sec-fetch-site'] or not req['sec-fetch-mode'] or not req['sec-fetch-dest']:
    return True

  # Allow non-navigational requests
  if req['sec-fetch-mode'] not in ('navigate', 'nested-navigate'):
    return True

  # Allow non-frameable requests.
  if req['sec-fetch-dest'] not in ('frame', 'iframe', 'embed', 'object'):
      return True

  # [OPTIONAL] Exempt paths/endpoints meant to be served cross-origin.
  if req.path in ('/my_frame_ancestors_host_src'):
    return True

  # Reject all other requests
  return False
```

## Considerations
1. Framing Isolation Policy cannot be applied if an endpoint allows framing requests from specific origins via  `X-Frame-Options` and/or Content Security Policy's
`frame-ancestors` directive.
