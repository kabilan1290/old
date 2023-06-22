---
layout: post
title:  "Prefix Match CORS in a Twisted place"
date:   2023-06-22 09:29:20 +0700
categories: bugbountywriteup
---
<img src="https://media.tenor.com/EERR4LXoJBoAAAAd/hi-wave.gif"></img>


- Hola ! It's been really a long time since i posted anything btw i came up with a writeup showcasing a CORS exploit in a twisted place.

- Most of the bughunters search for CORS by quickly changing the origin and check several other tricks but forgot about prefix and suffix match cors.

### Prefix and Suffix match CORS?

- Lets take something.someone.com as a target.

- If the server accepts `something.someone.com`.evil.com as a valid origin, its a prefix match CORS [ When the prefix is matched,it accepts whats comes after]
- If the server accepts evil`something.someone.com` as a valid origin, its a suffix match CORS [ When the suffix is matched, it accepts whats come before]

- I founded such vulnerability in target.com.

- But the target.com is complete blank, no functionalities :/
  
- I fired up directory bruteforce to find something , but to my luck nothing been found.
  
- At first glance, the page source contains nothing after looking towards the js files and everything,i just noted down the domain and left it open.

- <img src="https://github.com/kabilan1290/kabilan1290.github.io/assets/45006244/de1f3137-9527-4f27-8d94-657520b213d5"></img>
