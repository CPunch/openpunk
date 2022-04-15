---
title: "Laika"
author: CPunch
date: 2022-04-04
tags: ["C"]
repo: "https://git.openpunk.com/CPunch/Laika"
---

<script id="asciicast-487180" src="https://asciinema.org/a/487686.js" async></script>

Laika is a simple cross-platform Remote Access Toolkit stack for educational purposes. It allows encrypted communication across a custom binary protocol. The bot client supports both Windows & Linux environments, while the shell & CNC server specifically target Linux environments. Laika is meant to be small and discreet, Laika believes in hiding in plain sight.

Some notable features thus far:
- [X] Lightweight, the bot alone is 183kb (`MinSizeRel`) and uses very little resources minimizing Laika's footprint.
- [X] Authentication & packet encryption using LibSodium and a predetermined public CNC key. (generated with `bin/genKey`)
- [X] Server and Shell configuration through `.ini` files.
- [X] Ability to open shells remotely on the victim's machine.
- [ ] Persistence across reboot: (toggled with `-DLAIKA_PERSISTENCE=On`)
    - [X] Persistence via Cron on Linux-based systems.
    - [ ] Persistence via Windows Registry.
- [ ] Ability to relay socket connections to/from the victim's machine.
- [ ] Uses obfuscation techniques also seen in the wild (string obfuscation, tiny VMs executing sensitive operations, etc.)
- [ ] Simple configuration using CMake:
    - [X] Setting keypairs (`-DLAIKA_PUBKEY=? -DLAIKA_PRIVKEY=?`, etc.)
    - [ ] Obfuscation modes
