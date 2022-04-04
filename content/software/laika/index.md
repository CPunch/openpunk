---
title: "Laika"
author: CPunch
date: 2022-04-04
tags: ["C"]
repo: "https://git.openpunk.com/CPunch/Laika"
---

Laika is a simple Remote Access Toolkit stack for educational purposes. It allows authenticated communication across a custom protocol with generated key pairs which are embedded into the executable). The bot client supports both Windows & Linux environments, while the shell & CNC server specifically target Linux environments.

![DEMO](https://github.com/CPunch/Laika/raw/main/img/demo.gif)

Some notable features thus far:
- [X] Lightweight, the bot alone is 270kb (22kb if not statically linked with LibSodium) and uses very little resources minimizing Laika's footprint.
- [X] Authentication & packet encryption using LibSodium and a predetermined public CNC key.
- [X] Ability to open shells remotely on the victim's machine.
- [ ] Ability to relay socket connections to/from the victim's machine.
- [ ] Uses obfuscation techniques also seen in the wild (string obfuscation, tiny VMs executing sensitive operations, etc.)
- [ ] Simple configuration using CMake
    - [X] Setting keypairs (`-DLAIKA_PUBKEY=? -DLAIKA_PRIVKEY=?`, etc.)
    - [ ] Obfuscation modes

## Configuration and compilation

Make sure you have the following libraries and tools installed:
- CMake (>=3.10)
- Compiler with C11 support (GCC >= 4.7, Clang >= 3.1, etc.)

The only dependency (LibSodium) is vender'd and statically compiled against the `/lib`. This should be kept up-to-date against stable and security related updates to LibSodium.

First, compile the target normally

```sh
$ cmake -B build && cmake --build build
```

Now, generate your custom key pair using `genKey`

```sh
$ ./bin/genKey
```

Next, rerun cmake, but passing your public and private keypairs

```sh
$ rm -rf build &&\
    cmake -B build -DLAIKA_PUBKEY=997d026d1c65deb6c30468525132be4ea44116d6f194c142347b67ee73d18814 -DLAIKA_PRIVKEY=1dbd33962f1e170d1e745c6d3e19175049b5616822fac2fa3535d7477957a841 -DCMAKE_BUILD_TYPE=MinSizeRel &&\
    cmake --build build
```

Output binaries are put in the `./bin` folder