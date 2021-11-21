+++
title = "Frame Counting"
description = ""
date = "2020-10-01"
category = [
    "Attack",
]
abuse = [
    "iframes",
]
defenses = [
    "Fetch Metadata",
    "SameSite Cookies",
    "COOP",
    "Framing Protections",
]
menu = "main"
weight = 2
+++


Window references allow cross-origin pages to get access to some of the attributes of other pages. These references become available when using or allowing `iframe` and `window.open`. The references provide (limited) information about the window as they still respect the same-origin policy.

One of the accessible attributes is `window.length` which provides the number of frames in the window. This attribute can provide valuable information about a page to an attacker.

Websites commonly use frames (or `iframes`) and this choice doesn't necessarily imply security issues. There are, however, cases where a website might change the number of frames on a page depending on some user information. For example, this could happen on a page that changes its layout depending on the `GET` parameters and the victim's data. It might be possible for an attacker to infer information about the victim by measuring the value of `window.length` on different pages.

If a website normally contains an iframe but the length is 0 this may mean the user does not have access to that page like when signed out,
Or some other errors like a tab crash it may also allow for detecting when a page has loaded with the length increase.

## Code Snippet
The below snippet demonstrates how to access the information about the number of frames on a cross-site page:
```javascript
// Get a reference to the window
var win = window.open('https://example.org');

// Wait for the page to load
setTimeout(() => {
  // Read the number of iframes loaded
  console.log("%d iframes detected", win.length);
}, 2000);
```

## Attack Alternatives

In some cases, different application states have the same number of `frames`, preventing attackers from being able to distinguish them. However, continuously recording the frame count while the page is loading may show a pattern that might leak information to an attacker:

```javascript
// Get a reference to the window
var win = window.open("https://example.org");
var pattern = [];

// In a loop, register the number of iframes at 60ms interval
var recorder = setInterval(() => {
  pattern.push(win.length)
}, 60);

// Break the loop after 6 seconds
setTimeout(() => {
   clearInterval(recorder);
   console.log("The pattern is: %s", pattern.join(', '));
}, 6 * 1000);
```

## Case Scenarios

Some examples of frame counting attacks are:

- A website lets a user search for user information in a search engine. If the page structure has a different number of `iframes` depending on whether there are results to the user query, an attacker could use the [XS-Search]({{< ref "xs-search.md" >}}) technique to leak those secrets.
- A website structures the user profile page differently based on gender or other PII. An attacker can easily leak this information by opening the page and counting frames.

## Defense

| Attack Alternative | [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |    [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})    |
| :----------------: | :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :-------------------------------------------------------------------------: |
|      iframes       |                                         ✔️                                          |                          ❌                          |                                 ✔️                                 |  [FIP]({{< ref "/docs/defenses/isolation-policies/framing-isolation" >}})   |
|      windows       |                                         ❌                                          |                          ✔️                          |                                 ❌                                 | [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}}) |

## Real World Example

A vulnerability reported to Facebook used this technique to leak user-related information such as specific content published in posts, religious information about friends, or photo locations[^1].

## References

[^1]: Patched Facebook Vulnerability Could Have Exposed Private Information About You and Your Friends. [link](https://www.imperva.com/blog/facebook-privacy-bug/)
