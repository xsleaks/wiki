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

`window` references allow cross-origin pages to get access to some attributes about other pages and these references come in use when using `iframe`, `window.opener`, and `window.open`. They provide limited information about the window to respect the Same-origin policy and run in a different browsing context. 

One of the accessible attributes is `window.length` which is the number of frames in the window. This attribute can give too much information about a page to an attacker.

### Why is this a problem?

Websites use `frames` (or `iframes`) all the time, and they don’t necessarily bring any risk when used by developers. However, in particular, cases where a website makes the use of `frames` depending on user-provided information, for example, through a `GET` request, it might be possible for an attacker to infer that information by triggering changes on the `GET` request and listen to those changes by checking the value of `window.length`. 


#### Specific Case Scenarios

- A website lets a user search for secrets in a search engine, if the page structure has a different number of `iframes` depending on whether there are results to the user query, an attacker could use the [XS-Search]({https://future.article) technique, to leak those secrets.
- A website structure the user profile page differently based on gender. An attacker can easily get this difference and infer the gender of the user.

### Attack alternatives

In some cases, different states have the same number of `frames`, preventing attackers from distinguishing them. However, continuously recording the frame count when the page is loading can lead to a **pattern** attackers might be able to use.

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

A Vulnerability reported to Facebook, used this technique to leak user-related information such as specific contents published in posts, religious information about friends, or photo locations[^1].


## How can I protect my applications?

Fixing this XS-Leak is tricky as an attacker can get a `window` reference from different ways. 

To stop attackers from abusing this XS-Leak on your pages through `iframes` you can use the following protections:

- {{< hint good >}}
Forbidd cross-origin pages from framing your pages using [framing protections]({{< ref "../defenses/opt-in/xfo.md" >}}).
{{< /hint >}}
- {{< hint danger >}}
In case you must allow others to frame your pages, you should consider [this defensive guideline]({{< ref "../defenses/opt-in/xfo.md" >}}) to avoid this XS-Leak.
{{< /hint >}}

To stop attackers from abusing this XS-Leak on your pages through `window.opener`, and `window.open` you can use the following protection:

- {{< hint good >}}
Deploy [Cross-Origin-Opener-Policy]({{< ref "../defenses/opt-in/coop.md" >}}) on your website gives you control on who can get a `window` reference to your pages.
{{< /hint >}}


# References

[^1]: Patched Facebook Vulnerability Could Have Exposed Private Information About You and Your Friends. [link](https://www.imperva.com/blog/facebook-privacy-bug/)