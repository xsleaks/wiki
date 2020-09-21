+++
title = "typeMustMatch"
description = ""
date = "2020-07-21"
category = "historical"
attacks = [
    "dom property",
]
defenses = [
    "deprecation"
]
menu = "main"
+++

The [`typeMustMatch`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLObjectElement/typeMustMatch) is a boolean that reflects the `typeMustMatch` attribute of the `object` element. It enforces a certain MIME type must be enforced when loading an object, verifying if the `Content-Type` of the resource is the same as the one provided in the object. Unfortunately, this enforcement would allow attackers to leak the `Content-Type` and Status Codes returned by a website [^1]

## Root Cause

Considering the snippet below, `not_loaded` would be rendered if the returned `Content-Type` of `https://target/api` matched the one in `type`, or the server returned a status different then `200`.

```javascript
<object type="application/json" data="https://target/api" typemustmatch> not_loaded </object>
```

### Issues

An attacker could leak the `Content-Type` and Status Codes of a website by detecting whether the object rendered, which will happen when [all the conditions]({{< ref "#root-cause" >}}) are met. The attacker could check the values of `clientHeight` and `clientWidth` which will likely be different than 0 when the object renderers (and returned status `200`). Since `typeMustMatch` requires the server to return status `200` to load a resource, it would be possible to detect error pages, similarly to [Error Events](https://TODO) XS-Leaks.

The example below shows how to detect this behavior by embedding an object inside an iframe, and checking the values of `clientHeight` and `clientWidth` when the `iframe` triggers the `onload` event.


```javascript
let url = 'https://target'
let mime = 'application/json'
let x = document.createElement('iframe');
x.src = `data:text/html,<object id=obj type="${mime}" data="${url}" typemustmatch><script>onload = ()=>{console.log(obj.clientHeight)}%3c/script></object>`;
document.body.appendChild(x);
```

## Fix

Firefox was the only browser supporting the `typeMustMatch` attribute [^2] and since no other browsers offered support, it was removed in version 68 and from the HTML Living Standard.

## References

[^1]: Cross-Site Content and Status Types Leakage, [link](https://medium.com/bugbountywriteup/cross-site-content-and-status-types-leakage-ef2dab0a492)
[^2]: Remove support for typemustmatch, [link](https://bugzilla.mozilla.org/show_bug.cgi?id=1548773)