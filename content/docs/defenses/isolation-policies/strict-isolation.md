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
Strict Isolation Policy is intended to protect against all cross-site interactions (including navigations to the application through hyperlinks). This is a very strict policy that has the potential to prevent applications from functioning properly.

{{< hint tip >}}
Instead of rejecting all cross-site interactions, the user could be prompted to confirm the action, e.g. *Confirm that you visited this page from a trusted origin*, to mitigate the risk of attacks in the background, and, at the same time, help prevent unintended breakages of an application.

However, this would only work for navigational requests, since other resources are loaded in the background.
{{< /hint >}}


## Implementation with Fetch Metadata

The below snippet showcases an example implementation of Strict Isolation Policy by an application:

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
If a server sends a cookie with the [`SameSite=strict`]({{< ref "../opt-in/same-site-cookies/#samesite-cookie-modes" >}}) flag, any returned request that doesn't contain that cookie can be rejected, as showcased in this snippet:

```py
# Reject cross-origin requests to protect from CSRF, XSSI, and other bugs
def allow_request(req):

  if req['cookies']['strict-cookie'] == 'true':
    return True

  # Block requests without a strict cookie
  return False
```

## Implementation with Referer
It is also possible to reject requests from untrusted origins with the [`Referer`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referer) header:

```py
# Reject requests that came from untrusted referrers
def allow_request(req):

  # check if the referer header is trusted, i.e. exists in trusted_referers dict
  if req['headers']['referer'] in trusted_referers:
    return True

  # Block requests without a strict cookie
  return False
```

{{< hint important >}}
It is not guaranteed that every request will contain the Referer header (e.g. extensions can strip the header) which could potentially break an application. Also be aware that it is possible to set the value of `Referer` to `null`.

Twitter deployed [^twitter_silhouette] a similar protection against XS-Leaks.
[^twitter_silhouette]: Protecting user identity against Silhouette, [link](https://blog.twitter.com/engineering/en_us/topics/insights/2018/twitter_silhouette.html)
{{< /hint >}}
