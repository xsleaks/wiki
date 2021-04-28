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
]
menu = "main"
weight = 2
+++

CSS can be used to trick a user into exposing information by making visual changes that are affected by the embed.

## Getting history
An PoC of this attack can be found [here](https://lcamtuf.coredump.cx/whack/).  
Because CSS has a [`:visited`](https://developer.mozilla.org/en-US/docs/Web/CSS/:visited) class it’s possible to apply a different style for URLs that have been visited. [source](https://developer.mozilla.org/en-US/docs/Web/CSS/Privacy_and_the_:visited_selector)  
Previously it was possible to use [`getComputedStyle()`](https://developer.mozilla.org/en-US/docs/Web/API/Window/getComputedStyle) to detect this difference but now browsers prevent this,  
So, it may be needed to trick the user into clicking an area that the CSS has affected this can be done using  [`mix-blend-mode`](https://developer.mozilla.org/en-US/docs/Web/CSS/mix-blend-mode).  [source](https://lcamtuf.blogspot.com/2016/08/css-mix-blend-mode-is-bad-for-keeping.html)   
Render timings have also been abused to do this without tricking the user. [source](https://www.contextis.com/en/resources/white-papers/pixel-perfect-timing-attacks-with-html5)  

## Evil Captcha
Using CSS, it’s possible to take an embed out of context. [source](https://ronmasas.com/posts/the-human-side-channel)  
This can be prevented with XFO.


## Defence
Some of the attacks can be prevented by using XFO,
The history attack can be prevented by disabling history or if on Firefox it’s possible to fix this by setting the `layout.css.visited_links_enabled` option in `about:config` to `false`.

| [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                  [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                   |
| :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------: |
|                                         ❌                                          |                          ❌                          |                                 ✔️                                 |  |
