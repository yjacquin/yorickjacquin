---
title: "Why metaprogramming in Ruby is not for you"
author: ""
date: 
lastmod: 2020-04-26T17:00:02+02:00
draft: true
description: ""

subtitle: "I once stumbled upon some dark, mysterious code during a code review. It was not like anything I’ve ever seen before, it was messy, with…"




aliases:
    - "/"
---

#### I once stumbled upon some dark, mysterious code during a code review. It was not like anything I’ve ever seen before, it was messy, with interpolation everywhere, blocks were flying, the dev even used the forbidden #send method in a loop. It was a horrific thing…

TODO: ADD PHOTOSBehind all this exaggeration are reasons, true reasons, that you do not want to use metaprogramming in ruby. In this article, I will detail when metaprogramming is ok to use, and when it is not.

### When it is ok to use it:

Metaprogramming in ruby allows you to do pretty things and can be very powerful. It is the &#34;magic&#34; you can (or cannot) see everywhere when using Ruby on Rails
