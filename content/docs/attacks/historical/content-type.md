+++
title = "Content-Type"
description = ""
date = "2020-10-01"
category = "Historical"
abuse = [
    "typeMustMatch",
    "iframes",
    "Content-Type",
    "Status Code",
]
defenses = [
    "Deprecation"
]
menu = "main"
+++

Leaking the Content-Type of a request could offer an attacker a new way to distinguish two requests from each other.

## typeMustMatch

[`typeMustMatch`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLObjectElement/typeMustMatch) is a boolean that reflects the `typeMustMatch` attribute of the `object` element. It ensures a certain MIME type must be enforced when loading an object verifying if the `Content-Type` of the resource is the same as the one provided in the object. Unfortunately, this enforcement would allow attackers to leak the `Content-Type` and Status Codes returned by a website [^1]

### Root Cause

Considering the snippet below, `not_loaded` would be rendered if the returned `Content-Type` of `https://target/api` did not match the one in `type`, or the server returned a status different than `200`.

```html
<object type="application/json"
        data="https://example.org"
        typemustmatch>
not_loaded </object>
```

#### Issues

An attacker could leak the `Content-Type` and Status Codes of a website by detecting whether the object rendered, which will happen when [all the conditions]({{< ref "#root-cause" >}}) are met. The attacker could check the values of `clientHeight` and `clientWidth` which will likely be different than 0 when the object renderers (and returned status `200`). Since `typeMustMatch` requires the server to return status `200` to load a resource, it would be possible to detect error pages, similarly to [Error Events]({{< ref "../error-events.md" >}}) XS-Leaks.

The example below shows how this behavior could be detected by embedding an object inside an `iframe` and checking the values of `clientHeight` and `clientWidth` when the `iframe` triggers the `onload` event.


```javascript
// Set the destination URL
var url = 'https://example.org';
// The content type we want to check for
var mime = 'application/json';
var ifr = document.createElement('iframe');
// Load an object inside iframe since object does not trigger onload event
ifr.srcdoc = `
  <object id="obj" type="${mime}" data="${url}" typemustmatch>
    error
  </object>`;
document.body.appendChild(ifr);

// When the iframe loads, read the height of the object. If it is the height 
// of a single line of text, then the content type of the resource was not 
// `application/json`. If it is a different height, then it was `application/json`. 
ifr.onload = () => {
    console.log(ifr.contentWindow.obj.clientHeight)
};
```

### Fix

Firefox was the only browser supporting the `typeMustMatch` attribute [^2] and since no other browsers offered support, it was removed in version 68 and from the HTML Living Standard.

## References

[^1]: Cross-Site Content and Status Types Leakage, [link](https://medium.com/bugbountywriteup/cross-site-content-and-status-types-leakage-ef2dab0a492)
[^2]: Remove support for typemustmatch, [link](https://bugzilla.mozilla.org/show_bug.cgi?id=1548773)
