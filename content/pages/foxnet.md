---
title: "FoxNet: "
date: 2021-09-02
author: CPunch
tags: ["C++", "networking"]
repo: "https://github.com/CPunch/FoxNet"
draft: true
---

Recently I've been trying to find a lightweight networking library for another project. I had looked at several other libraries (RakNet namely) and wasn't impressed. RakNet hasn't been maintained in over 7 years and also included lots of features that weren't needed and unnecessarily made the library FAT. Such as specific reserved packets for consoles like the XBOX and a built-in team balancer. It's pretty obvious that RakNet was geared towards gaming-related applications. After seeing the state of things, I thought it'd be easier to write a minimal networking library that had just the very basics of features and left the rest to be extensible by the user.

## Enter: FoxNet

After spending about a month of trying different styles and ways of making the protocol extensible by the user, I finally landed on something that seems to have the best of both worlds. Let's walk through an example.

First things first, lets clone the repo and setup our CMake environment

```bash
mkdir FoxNetExample && cd FoxNetExample && git clone https://github.com/CPunch/FoxNet.git
```

```CMakeList