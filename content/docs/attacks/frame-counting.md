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

## Explanation

`window` references allow cross-origin pages to get access to some attributes of other pages. These references become available when using or allowing `iframe` and `window.open`. They provide some information (although limited) about the window as they still respect the Same Origin Policy.

One of the accessible attributes is `window.length` which provides the number of frames in the window. This attribute can give valuable information about a page to an attacker.

### Why is this a problem?

Websites commonly use frames (or `iframes`) and this doesn't necessarily imply security issues.  There are however cases where a website might change the amount of frames in a page depending on some user information. This could happen for example in a page that changes layout depending on the `GET` parameters and the victim data. It might be possible for an attacker to infer information on the victim by navigating a frame or a window with different `GET` requests and checking the value of `window.length` afterwards.


#### Case Scenarios

- A website lets a user search for user information in a search engine, if the page structure has a different number of `iframes` depending on whether there are results to the user query, an attacker could use the [XS-Search]({https://TODO) technique to leak those secrets.
- A website structures the user profile page differently based on gender or other PII. An attacker can easily leak this information by just opening the page and counting frames.

### Attack alternatives

In some cases, different application states have the same number of `frames`, preventing attackers to be able to distinguish them. However, continuously recording the frame count when the page is loading can lead to a **pattern** attackers might be able to use.

```javascript
const win = window.open("https://target"); // Or any Window reference
const pattern = [];

const recorder = setInterval(() => pattern.push(win.frames.length), 0);

setTimeout(() => {
   clearInterval(recorder);
   console.log(pattern);
}, 6 * 1000);
```


## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../defenses/opt-in/sec-fetch.md" >}})  | [COOP]({{< ref "../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |
|:-------------------:|:------------------:|:---------------:|:-----:|:--------------------:|
| iframe              |         ✔️         |      ✔️         |  ❌   |          ✔️         |
| window.open         |         ✔️ (If Strict)         |      ✔️         |  ✔️   |          ❌         |


## Real World Example

A Vulnerability reported to Facebook used this technique to leak user-related information such as specific contents published in posts, religious information about friends, or photo locations[^1].

## References

[^1]: Patched Facebook Vulnerability Could Have Exposed Private Information About You and Your Friends. [link](https://www.imperva.com/blog/facebook-privacy-bug/)
