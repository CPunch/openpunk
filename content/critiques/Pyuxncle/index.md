---
title: "Pyunxcle: C-like compiler for the Uxntal assembly language"
date: 2022-06-20
author: CPunch
repo: "https://github.com/CPunch/Pyuxncle"
tags: ["opinion"]
---

Pyuxncle is a small compiler for a C-Like language that targets the [Uxntal](https://wiki.xxiivv.com/site/uxntal.html) assembly language. This is an older project, and I definitely didn't put as much effort into keeping the codebase clean as I have on other projects. I was also fairly uncomfortable with Python while writing it, so I fell into the common pitfalls every new Python dev does.

# The 'good'

## It works

The good news is that it does in fact work. I even had some support for defining I/O devices so you could actually interact with the screen & other devices provided to you.

![](dev_example.png)

## First project that made me actually use Pylint type annotations

Yeah, it definitely helped me understand the importance of using type annotations. Without them, the codebase would be almost incomprehensible.

# The 'bad'

## Spaghetti implementation

Looking at parser.py, you can pretty quickly see why I burnt out. There's no real type system in place, I was developing the language *as* I wrote the compiler. This forced me to rewrite huge sections, and of course I got lazy on some parts and just 'made it work.' This is a great example of how *not* to organize a python project LOL.

> NOTE: There's actually a [really nice C compiler](https://github.com/lynn/chibicc) made for uxn now, they forked a smaller toy compiler and just [retargeted the codegen](https://github.com/lynn/chibicc/commit/2ea83ebfe1c8dbc751946cdd75ce226d3b0971d2), really cool project and definitely worth checking out :D
