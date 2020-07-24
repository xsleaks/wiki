+++
title = "Frame Counting"
description = ""
date = "2020-07-21"
category = "attacks"
attacks = [
    "dom property",
    "second example",
]
defenses = [
    "XFO",
    "COOP",
]
menu = "main"
+++

## Introduction

`window` references allow cross-origin pages to get access to some attributes of other pages. These references become available when using or allowing `iframe` and `window.open`. They provide some information (although limited) about the window as they still respect the Same Origin Policy.

One of the accessible attributes is `window.length` which provides the number of frames in the window. This attribute can give valuable information about a page to an attacker.

### Why is this a problem?

Websites commonly use frames (or `iframes`) and this doesn't necessarily imply security issues.  There are however cases where a website might change the amount of frames in a page depending on some user information. This could happen for example in a page that changes layout depending on the `GET` parameters and the victim data. It might be possible for an attacker to infer information on the victim by navigating a frame or a window with different `GET` requests and checking the value of `window.length` afterwards.


#### Specific Case Scenarios

- A website lets a user search for user information in a search engine, if the page structure has a different number of `iframes` depending on whether there are results to the user query, an attacker could use the [XS-Search]({/TODO) technique to leak those secrets.
- A website structures the user profile page differently based on gender or other PII. An attacker can easily leak this information by just opening the page and counting frames.

### Attack alternatives

In some cases, different application states have the same number of `frames`, preventing attackers to be able to distinguish them. However, continuously recording the frame count when the page is loading can lead to a **pattern** attackers might be able to use.

```javascript
const tab = window.opener; // Any Window reference
const pattern = [];
tab.location = 'https://target';

const recorder = setInterval(() => pattern.push(tab.frames.length), 0);

setTimeout(() => {
   clearInterval(recorder);
   console.log(pattern);
}, 6 * 1000);
```


## Real World Example

A Vulnerability reported to Facebook used this technique to leak user-related information such as specific contents published in posts, religious information about friends, or photo locations[^1].


## How can I protect my applications?

Fixing this XS-Leak is complicated as an attacker can get a `window` reference in different ways. 

To stop attackers from abusing this XS-Leak on pages through `iframes` developers use the following protections:

- {{< hint info >}}
Forbidd cross-origin pages from framing pages using [framing protections]({{< ref "../defenses/opt-in/xfo.md" >}}).
{{< /hint >}}
- {{< hint danger >}}
In case a developer must allow others to frame its pages, [this defensive guideline]({{< ref "../defenses/design-protections/defensive-design.md" >}}) should be considered to avoid the introduction of this XS-Leak.
{{< /hint >}}

To stop attackers from abusing this XS-Leak on pages through `window.opener` and `window.open` developers can use the following protection:

- {{< hint info >}}
Deploy [Cross-Origin-Opener-Policy]({{< ref "../defenses/opt-in/coop.md" >}}) on the web application to prevent cross-origin `window` references.
{{< /hint >}}


## References

[^1]: Patched Facebook Vulnerability Could Have Exposed Private Information About You and Your Friends. [link](https://www.imperva.com/blog/facebook-privacy-bug/)
