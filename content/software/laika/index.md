---
title: "Laika"
author: CPunch
date: 2022-04-04
tags: ["C"]
repo: "https://git.openpunk.com/CPunch/Laika"
---

<script id="asciicast-487180" src="https://asciinema.org/a/487686.js" async></script>

Laika is a simple cross-platform Remote Access Toolkit stack for educational purposes. It allows encrypted communication across a custom binary protocol. The bot client supports both Windows & Linux environments, while the shell & CNC server specifically target Linux environments. Laika is meant to be small and discreet, Laika believes in hiding in plain sight.

## Why?

Most public malware sources in the wild are nerf'd or poorly made. Laika is written in modern C, and strives to adhere to best practices while keeping a maintainable and readable code base. The reader is encouraged to compile a `MinSizeRel` build of Laika and open it up in their favorite disassembler. Take a look at how certain functions or subroutines look compared to its plaintext source. See if you can dump strings during runtime with a debugger, try to break Laika. Play both sides by breaking Laika, and improving it to make reversing and analysis harder. Most malware depend on the time that it takes to analyze a sample, this gives their malware time to do whatever before eventually being shutdown. Playing both sides will help give you insight into the methods and bitterness that is this cat and mouse game.