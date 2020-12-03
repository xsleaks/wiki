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
Navigation Isolation Policy is a server-side protection mechanism intended to mitigate clickjacking, reflected XSS and some XS-Leaks that take use of the window contexts.

{{< hint tip >}}
Instead of rejecting all cross-site interactions, the user could be prompted to confirm the action, e.g. *Confirm that you visited this page from a trusted origin* to mitigate the risk of attacks in the background. This could help with the unintended breakages of an application.
{{< /hint >}}

## Implementation with Fetch Metadata

The below snippet showcases an example implemention of the Navigation Isolation Policy with the use of [Fetch Metadata]({{< ref "../opt-in/fetch-metadata.md">}}) headers [^secmetadata].

```py
# Reject cross-origin requests to protect from CSRF, XSSI, and other bugs
def allow_request(req):
  # Allow any request that is not cross-site
  if req['sec-fetch-site'] != 'cross-site':
    return True

  # Block all top-level cross-site navigations
  if req['sec-fetch-mode'] in ('navigate', 'nested-navigate'):
      return False

  # Allow all other requests
  return True
```
## References
[^secmetadata]: Fetch Metadata Request Headers playground, [link](https://secmetadata.appspot.com/) -->
