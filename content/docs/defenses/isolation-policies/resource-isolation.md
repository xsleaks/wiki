+++
title = "Resource Isolation Policy"
description = ""
date = "2020-11-30"
category = [
    "Defense",
]
menu = "main"
weight = 1
+++
Resource Isolation Policy prevents external websites from requesting your resources. Blocking such traffic mitigates common web vulnerabilities such as CSRF, XSSI, or XS-Leaks. The policy can be enabled for applications whose endpoints are not intended to be loaded in a cross-site context and will allow resource requests coming from your application as well as direct navigations.

## Implementation with Fetch Metadata

The below snippet showcases an example implemention of the Resource Isolation Policy with the use of [Fetch Metadata]({{< ref "../opt-in/fetch-metadata.md">}}) headers:

```py
# Reject cross-origin requests to protect from , XSSI, XS-Leaks, and other bugs
def allow_request(req):
  # [OPTIONAL] Exempt paths/endpoints meant to be served cross-origin.
  if req.path in ('/my_CORS_endpoint', '/favicon.png'):
    return True

  # Safe to set `Cross-Origin-Resource-Policy: same-site`. (see Considerations)

  # Allow requests from browsers which don't send Fetch Metadata
  if not req['headers']['sec-fetch-site']:
    return True

  # Allow same-site and browser-initiated requests
  if req['headers']['sec-fetch-site'] in ('same-origin', 'same-site', 'none'):
    return True

  # Allow simple top-level navigations, this includes embeds
  if req['headers']['sec-fetch-mode'] == 'navigate' and req.method == 'GET':
      return True

  # Reject all other requests
  return False
```

## Considerations
It should be safe to set a `Cross-Origin-Resource-Policy: same-site` response header on all requests that have not explicitly been exempted from Resource Isolation Policy. See [CORP]({{< ref "../opt-in/corp.md" >}}).


## Deployment

Check out this [web.dev](https://web.dev/fetch-metadata/) article to learn more about this protection, some different policies, and tips on how to deploy it.
