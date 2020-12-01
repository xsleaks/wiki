+++
title = "Resource Isolation"
description = ""
date = "2020-10-01"
category = [
    "Defense",
]
menu = "main"
+++
Resource Isolation Policy prevents your resources from being requested by external websites. Blocking such traffic mitigates common web vulnerabilities such as CSRF,
XSSI, timing attacks, and xs-leaks. The policy can be enabled for applications whose endpoints are not loaded in a cross-site context and will allow
resource requests coming from your application as well as direct navigations.

## Example
```
# Reject cross-origin requests to protect from CSRF, XSSI, and other bugs
def allow_request(req):
  # Allow requests from browsers which don't send Fetch Metadata
  if not req['sec-fetch-site']:
    return True

  # Allow same-site and browser-initiated requests
  if req['sec-fetch-site'] in ('same-origin', 'same-site', 'none'):
    return True

  # Allow simple top-level navigations except <object> and <embed>
  if req['sec-fetch-mode'] == 'navigate' and req.method == 'GET':
      return True

  # [OPTIONAL] Exempt paths/endpoints meant to be served cross-origin.
  if req.path in ('/my_CORS_endpoint', '/favicon.png'):
    return True

  # Reject all other requests that are cross-site and not navigational
  return False
```

## Deployment

Check out this [web.dev](https://web.dev/fetch-metadata/) article to learn more about this protection, some different policies, and tips on how to deploy it.

## References

[^1]: Protect your resources from web attacks with Fetch Metadata, [link](https://web.dev/fetch-metadata/)
