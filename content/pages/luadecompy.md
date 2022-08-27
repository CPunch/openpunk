---
title: "LuaDecompy: Lua 5.1 Decompiler"
date: 2022-08-27
author: CPunch
repo: "https://github.com/CPunch/LuaDecompy"
tags: ["Lua", "python", "decompiler"]
---

Recently I struck an interest in decompilers because of a small passion project [a friend of mine](https://github.com/gsemaj) showed me. Gent's project dealt with turning compiled DirectX shaders back into HLSL (High level shader language). His project (also written in python!) inspired me to write a decompiler of my own. I settled on writing a Lua 5.1 decompiler since I had already written a [Lua 5.1 dump disassembler](/pages/lua-bytecode-parser) a couple years back (which needed [some cleanup](https://github.com/CPunch/LuaDecompy/commits/main/lundump.py), but worked none-the-less). There were also multiple Lua decompilers already made which would help me if I got stuck. I won't be focusing on code, so much as the *theory* behind my decompiler. If you're interested in the 'behind the scenes' on how I implemented this in practice, checkout the decompiler [here](https://github.com/CPunch/LuaDecompy/blob/main/lparser.py)!

## Theory Overview
Our end goal: take a compiled Lua 5.1 bytecode dump and turn it back into a human-readable Lua script. I'll walk you through my basic workflow, however this only applies to Lua 5.1! When writing a decompiler you are *very* tied to your specific architecture/virtual machine, this means that the technique I'll be describing is mostly hacked together by trial and error. Let's take a look at where I started.

## From disassembly to source
I started by taking a classic favorite, a "Hello World" script and dumped it using luac:

```sh
> cat hello.lua && luac5.1 -o hello.luac hello.lua
print('Hello World!')
```

Let's take a look at the disassembly of our dump. If you don't remember the different Lua 5.1 opcodes, I'd recommend taking a look at [lvm.c from the Lua 5.1 source](https://www.lua.org/source/5.1/lvm.c.html) (also [lparser.c](https://www.lua.org/source/5.1/lparser.c.html) && [lcode.c](https://www.lua.org/source/5.1/lcode.c.html) were referenced as well). These were my bible while developing my decompiler and it's as accurate and official as you can get for documentation about the Lua virtual machine and instruction set.

```sh
==== [[@hello.lua's constants]] ====

0: [STRING] print
1: [STRING] Hello World!

==== [[@hello.lua's locals]] ====


==== [[@hello.lua's dissassembly]] ====

[  0]  GETGLOBAL :   R[0]   K[0]               ; move _G["print"] into R[0]
[  1]      LOADK :   R[1]   K[1]               ; load "Hello World!" into R[1]
[  2]       CALL :      0      2      1        ; 
[  3]     RETURN :      0      1      0        ;
```

So, we used 2 registers, R[0] for the print function, and R[1] for the "Hello World!" string. There's not any locals (at all) so we know those registers are just used as temporary values for the current expression (more on that later!). Here's what OP_CALL does during the main vm loop (lvm.c:LuaV_execute()):

```C
case OP_CALL: {
    int b = GETARG_B(i);
    int nresults = GETARG_C(i) - 1;
    if (b != 0) L->top = ra+b;  /* else previous instruction set top */
    L->savedpc = pc;
    switch (LuaD_precall(L, ra, nresults)) {
    case PCRLUA: {
        nexeccalls++;
        goto reentry;  /* restart LuaV_execute over new Lua function */
    }
    case PCRC: {
        /* it was a C function (`precall' called it); adjust results */
        if (nresults >= 0) L->top = L->ci->top;
        base = L->base;
        continue;
    }
    default: {
        return;  /* yield */
    }
    }
}
```

`ra` is set to the `A` argument of the current instruction. So we call the function at R[0], passing R[1] as an argument and return 0 results. Expressing that as a Lua expression looks like:

```Lua
print("Hello World!")
```
> Look familiar?

Every Lua proto (the chunk we disassembled) ends with a OP_RETURN instruction. We'll ignore this for now.

## Buh muh locals!!!
In the previous section I mentioned that we knew the used registers weren't locals. If you didn't know, when you declare a variable as a local in Lua, it actually reserves a register on the stack as "the local." There's a bit more to it, like upvalues which move the local from a register to the heap so closures can use it after the stack has been cleared, but the basic gist is "Locals live exclusively in a register."

Let's take a look at a bit more complex dump.
```sh
==== [[@hello.lua's constants]] ====

0: [STRING] Hello
1: [STRING] World!
2: [STRING] print
3: [STRING]  

==== [[@hello.lua's locals]] ====

R[0]: hi
R[1]: world

==== [[@hello.lua's dissassembly]] ====

[  0]      LOADK :   R[0]   K[0]               ; load "Hello" into R[0]
[  1]      LOADK :   R[1]   K[1]               ; load "World!" into R[1]
[  2]  GETGLOBAL :   R[2]   K[2]               ; move _G["print"] into R[2]
[  3]       MOVE :      3      0      0        ; move R[0] into R[3]
[  4]      LOADK :   R[4]   K[3]               ; load " " into R[4]
[  5]       MOVE :      5      1      0        ; move R[1] into R[5]
[  6]     CONCAT :      3      3      5        ; concat 3 values from R[3] to R[5], store into R[3]
[  7]       CALL :      2      2      1        ; 
[  8]     RETURN :      0      1      0        ; 
```
> These are generated by my [deserializer](https://github.com/CPunch/LuaDecompy/commits/main/lundump.py).

Alright let's break this down. We know R[0] and R[1] are reserved for the 'hi' and 'world' locals respectively.
```sh
==== [[@hello.lua's locals]] ====

R[0]: hi
R[1]: world
```

So any instruction that sets those registers, is doing an assignment operation on the corresponding local using '='.

Knowing this, let's take a look at the first 2 instructions:
```sh
[  0]      LOADK :   R[0]   K[0]               ; load "Hello" into R[0]
[  1]      LOADK :   R[1]   K[1]               ; load "World!" into R[1]
```
> If you want to improve the disassembler output, feel free to contribute!

The equivalent Lua expression to this is
```Lua
local hi = "Hello"
local world = "World!"
```

Our next main block deals with setting things up for the `print` call.
```sh
[  2]  GETGLOBAL :   R[2]   K[2]               ; move _G["print"] into R[2]
[  3]       MOVE :      3      0      0        ; move R[0] into R[3]
[  4]      LOADK :   R[4]   K[3]               ; load " " into R[4]
[  5]       MOVE :      5      1      0        ; move R[1] into R[5]
```

Let's express moves *from* a register reserved to a local as the local's identifier. Eg. after this block our registers will look like:
```
R[0]: hi (local!)
R[1]: world (local!)
R[2]: print (the function)
R[3]: hi (copy of the local!)
R[4]: " "
R[5]: world (copy of the local!)
```

our next interesting instruction simply concatenates 3 strings on the stack.
```sh
[  6]     CONCAT :      3      3      5        ; concat 3 values from R[3] to R[5], store into R[3]
```

Looking at our trusty lvm.c, OP_CONCAT is defined as
```C
case OP_CONCAT: {
    int b = GETARG_B(i);
    int c = GETARG_C(i);
    Protect(luaV_concat(L, c-b+1, c); luaC_checkGC(L));
    setobjs2s(L, RA(i), base+b);
    continue;
}
```

expressing this instruction as a Lua expression would look something like:
```Lua
R[3] = hi .. " " .. world
```
> R[3] = (R[3] .. R[4] .. R[5])

and finally, our CALL calls the function in R[2], passing 1 argument (R[3]) and expecting 0 results
```Lua
print(hi .. " " .. world)
```

You can see how the expression values are built and stored in temporary registers. So putting everything together, our decompiled script looks like:
```Lua
local hi = "Hello"
local world = "World!"
print(hi .. " " .. world)
```

## Closing thoughts
Writing a decompiler is a pretty challenging and fun project to get into. I'd recommend it if you're into language hacking at all! Of course this is just a brief overview, I omitted things like conditional jumps, closures, loops and tons of other things but this should give you a great start if you were lost like I was.

Some cool things I'd like to explore after working on this project:
- Obfuscating Lua dumps? (Striping locals, line numbers, debugging info)
- Can I fork my decompiler to decompile stripped/obfuscated Lua dumps?