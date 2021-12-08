---
title: "LuaPytecode"
author: CPunch
date: 2019-09-15
tags: ["lua", "python"]
repo: "https://github.com/CPunch/LuaPytecode"
---

A Lua5.1 cross-platform bytecode deserializer. This module pulls int and size_t sizes from the chunk header, meaning it should be able to deserialize lua bytecode dumps from most platforms, regardless of the host machine.

For details on the Lua5.1 bytecode format, I read [this PDF](https://archive.org/download/a-no-frills-intro-to-lua-5.1-vm-instructions/a-no-frills-intro-to-lua-5.1-vm-instructions_archive.torrent) as well as read the [lundump.c](https://www.lua.org/source/5.1/lundump.c.html) source file from the Lua5.1 source.

```lua
total = 1

function setTotal()
    for i=1,10 do
        total=total*i
    end
end

setTotal()
print(total)
```

Compile our example script with

```sh
$ luac -o example.luac -- example.lua
```

Now disassemble it

```python
import luac

lc = luac.LuaUndump()
chunk = lc.loadFile("example.luac")

lc.print_dissassembly()
```

```

==== [[example.lua's constants]] ====

0: [STRING] total
1: [NUMBER] 1.0
2: [STRING] setTotal
3: [STRING] print

==== [[example.lua's dissassembly]] ====

[  0]      LOADK : 0 1
[  1]  SETGLOBAL : 0 0
[  2]    CLOSURE : 0 0
[  3]  SETGLOBAL : 0 2
[  4]  GETGLOBAL : 0 2
[  5]       CALL : 0 1 1
[  6]  GETGLOBAL : 0 3
[  7]  GETGLOBAL : 1 0
[  8]       CALL : 0 2 1
[  9]     RETURN : 0 1 0

==== [[example.lua's protos]] ====


==== [['s constants]] ====

0: [NUMBER] 1.0
1: [NUMBER] 10.0
2: [STRING] total

==== [['s dissassembly]] ====

[  0]      LOADK : 0 0
[  1]      LOADK : 1 1
[  2]      LOADK : 2 0
[  3]    FORPREP : 0 3
[  4]  GETGLOBAL : 4 2
[  5]        MUL : 4 4 3
[  6]  SETGLOBAL : 4 2
[  7]    FORLOOP : 0 -4
[  8]     RETURN : 0 1 0

==== [['s protos]] ====

```
