+++
title = "Navigation Isolation Policy"
description = ""
date = "2020-11-30"
category = [
    "Defense",
]
menu = "main"
weight = 3
+++
Navigation Isolation Policy is a server-side protection mechanism intended to mitigate CSRF, clickjacking, reflected XSS, and XS-Leaks that make use of cross-site window contexts. This is a strict policy and has the potential to break an application since it blocks all cross-site navigations, including navigations through hyperlinks.

{{< hint tip >}}
Instead of rejecting all cross-site interactions, the user could be prompted to confirm the action, e.g. *Confirm that you visited this page from a trusted origin*, to mitigate the risk of attacks in the background, and, at the same time, help prevent unintended breakages of an application.
{{< /hint >}}

## Implementation with Fetch Metadata

The below snippet showcases an example implemention of the Navigation Isolation Policy with the use of [Fetch Metadata]({{< ref "../opt-in/fetch-metadata.md">}}) headers [^secmetadata]:

```py
# Reject cross-site requests to protect from clickjacking, XS-Leaks, and other bugs
def allow_request(req):
  # Allow any request that is not cross-site
  if req['headers']['sec-fetch-site'] != 'cross-site':
    return True

  # Allow requests to endpoints meant to be navigated to, e.g. homepage
  if req.path in whitelisted_paths:
    return True

  # Block all top-level cross-site navigations, including embeds
  if req['headers']['sec-fetch-mode'] in ('navigate', 'nested-navigate'):
      return False

  # Allow all other requests
  return True
```
## References
[^secmetadata]: Fetch Metadata Request Headers playground, [link](https://secmetadata.appspot.com/)
