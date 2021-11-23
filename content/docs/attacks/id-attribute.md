+++
title = "ID Attribute"
description = ""
date = "2020-10-01"
category = [
    "Attack",
]
abuse = [
    "onblur",
    "focus",
    "iframes",
]
defenses = [
    "Fetch Metadata",
    "SameSite Cookies",
    "Framing Protections",
    "Document Policies"
]
menu = "main"
weight = 3
+++


The `id` attribute is widely used to identify `HTML` elements. Unfortunately, cross-origin websites can determine whether a given `id` is set anywhere on a page by leveraging the `focus` event and `URL` fragments. If `https://example.com/foo#bar` is loaded, the browser attempts to scroll to the element with `id="bar"`. This can be detected cross-origin by loading `https://example.com/foo#bar` in an iframe; if there is an element with `id="bar"`, the `focus` event fires. The `blur` event can also be used for the same purpose [^1].

Some web applications set `id` attributes in `focusable` elements that can lead to disclosing user information. These `id`s can either contain information directly related to the user (e.g. a secret), or information associated with a user state (e.g. account status). [Run demo](https://xsinator.com/testing.html#Id%20Attribute%20Leak)

## Code snippet

The below snippet presents an example of detecting the ID attribute from another site:
```javascript
// Listen to onblur event
onblur = () => {
  alert('Focus was lost, so there is a focusable element with the specified ID');
}
var ifr = document.createElement('iframe');
// If a page has a focusable element with id="x" it will gain focus
// E.g. <input id="x" value="test" />
ifr.src = 'https://example.org/#x';
document.body.appendChild(ifr);
```

{{< hint info >}}
The above technique doesn't seem to work in Firefox.
{{< /hint >}}

## Case Scenarios

Some examples of `id`-attribute-based attacks are:
- A bank allows its clients to generate short numeric One-Time PINs (OTP) in the browser application to authenticate sessions on mobile devices. The bank used the OTP as the `id` of a `button` that is used to show the PIN to the client. This approach could be abused to steal these OTP codes by brute-forcing every option and then using them to compromise user accounts.
- A web application uses a specific set of predefined `ids` and `HTML` elements when an account has a premium status or the user is of a certain gender. The attacker can detect whether a specific `id` is present on the victim's page and leak the account information.

## Defense

| [Document Policies]({{< ref "/docs/defenses/opt-in/document-policies.md" >}}) | [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                                          [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                                          |
| :--------------------------------------------------------------------------------: | :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :-----------------------------------------------------------------------------------------------------------------------------------------------------: |
|                                         ✔️                                          |                                         ✔️                                          |                          ✔️                          |                                 ❌                                 | [FIP]({{< ref "/docs/defenses/isolation-policies/framing-isolation" >}}) |


## References

[^1]: Leaking IDs using focus, [link](https://portswigger.net/research/xs-leak-leaking-ids-using-focus)
