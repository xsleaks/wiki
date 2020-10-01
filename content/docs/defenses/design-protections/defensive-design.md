+++
title = "Defensive Design"
description = ""
date = "2020-07-21"
category = "defenses"
menu = "main"
bookHidden = true
+++

This section should focus on defensive design techniques to prevent XS-Leaks from happening. Some ideas:

- The way applications use iframes to display information based on a user query (search system)
- How applications use Fetch Metadata with Vary Headers to prevent cache probing attacks and what problems might occur with improper deployments. Are there any drawbacks of deploying this?
- How to ensure all application endpoints implement certain Headers to have the same behavior across different states.
- Quick strategies to mitigate reported XS-Leaks as a short-term solution before deploying web platform security features.

If you want to contribute please check https://github.com/xsleaks/wiki/issues/17
