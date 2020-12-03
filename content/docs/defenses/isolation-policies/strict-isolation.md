+++
title = "Strict Isolation Policy"
description = ""
date = "2020-11-30"
category = [
    "Defense",
]
menu = "main"
weight = 4
+++
Strict Isolation Policy is intended to protect against all cross-site interactions. This is a very strict policy that could potentialy break applications from functioning properly.

{{< hint tip >}}
Instead of rejecting all cross-site interactions, the user could be prompted to confirm the action, e.g. *Confirm that you visited this page from a trusted origin* to mitigate the risk of attacks in the background. This could help with the unintended breakages of an application.

However, this would only work for navigational requests, since the resources are loaded in the background.
{{< /hint >}}

## Implementation with Fetch Metadata

The below snippet showcases an example implementation of Strict Isolation Policy by an application.

```py
# Reject cross-origin requests to protect from CSRF, XSSI, and other bugs
def allow_request(req):
  # Allow requests from browsers which don't send Fetch Metadata
  if not req['headers']['sec-fetch-site']:
    return True

  # Block any cross-site request
  if req['headers']['sec-fetch-site'] == 'cross-site':
    return False

  # Allow all other requests
  return True
```

## Implementation with SameSite cookies
If a server sends a cookie with [`SameSite=strict`]({{< ref "../opt-in/same-site-cookies/#samesite-cookie-modes" >}}) flag, any received request back that doesn't contain that cookie could be rejected.

```py
# Reject cross-origin requests to protect from CSRF, XSSI, and other bugs
def allow_request(req):

  if req['cookies']['strict-cookie'] == 'true':
    return True

  # Block reuqests without a strict cookie
  return False
```

## Implementation with Referer
It is also possible to reject requests from untrusted origins with the [`Referer`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referer) header.

```py
# Reject requests that came from untrusted referrers
def allow_request(req):

  # check if the referer header is trusted, i.e. exists in trusted_referers dict
  if req['headers']['referer'] in trusted_referers:
    return True

  # Block reuqests without a strict cookie
  return False
```

{{< hint important >}}
It is not guaranteed that every request will contain the Referer header which could potentialy break an application. It is also possible to set the value of the `Referer` to `null` so be aware of that!
{{< /hint >}}
