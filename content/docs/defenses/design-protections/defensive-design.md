+++
title = "Defensive Design"
description = ""
date = "2020-07-21"
category = "defenses"
menu = "main"
bookHidden = true
+++

This section should focus on defensive design techniques used by companies to prevent XS-Leaks from happening. This could be:

- The way applications they use iframes to display information based on a user query (search system)
- How they use Fetch Metadata with Vary headers to prevent cache probing attacks
- How to ensure all application endpoints implement certain Headers to have the same behavior across different states.
- Strategies used by companies to mitigate reported XS-Leaks, as a short-term solution before deploying web platform security features.