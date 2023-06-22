---
layout: post
title:  "Prefix Match CORS in a Twisted place"
date:   2023-06-22 09:29:20 +0700
categories: bugbountywriteup
---

- Hola ! It's been really a long time since i posted anything btw i came up with a writeup showcasing a CORS exploit in a twisted place.

- Most of the bughunters search for CORS by quickly changing the origin and check several other tricks but forgot about prefix and suffix match cors.

- Prefix and Suffix match CORS?

- Lets take something.someone.com as a target.

- If the server accepts `something.someone.com`.evil.com as a valid origin, its a prefix match CORS [ When the prefix is matched,it accepts whats comes after]
- If the server accepts evil`something.someone.com` as a valid origin, its a suffix match CORS [ When the suffix is matched, it accepts whats come before]
