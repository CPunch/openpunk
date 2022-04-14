---
title: "Laika"
author: CPunch
date: 2022-04-04
tags: ["C"]
repo: "https://git.openpunk.com/CPunch/Laika"
---

Laika is a simple Remote Access Toolkit stack for educational purposes. It allows authenticated communication across a custom protocol with generated key pairs which are embedded into the executable). The bot client supports both Windows & Linux environments, while the shell & CNC server specifically target Linux environments.

<script id="asciicast-487180" src="https://asciinema.org/a/487180.js" async></script>

Some notable features thus far:
- [X] Lightweight, the bot alone is 270kb (22kb if not statically linked with LibSodium) and uses very little resources minimizing Laika's footprint.
- [X] Authentication & packet encryption using LibSodium and a predetermined public CNC key.
- [X] Server and Shell configuration through `.ini` files.
- [X] Ability to open shells remotely on the victim's machine.
- [ ] Ability to relay socket connections to/from the victim's machine.
- [ ] Uses obfuscation techniques also seen in the wild (string obfuscation, tiny VMs executing sensitive operations, etc.)
- [ ] Simple configuration using CMake
    - [X] Setting keypairs (`-DLAIKA_PUBKEY=? -DLAIKA_PRIVKEY=?`, etc.)
    - [ ] Obfuscation modes
