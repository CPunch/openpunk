---
title: "Cosmo"
author: CPunch
date: 2020-10-28
tags: ["C", "scripting-lang", "cosmo"]
repo: "https://git.openpunk.com/CPunch/Cosmo"
---

Cosmo is an eccentric scripting language, taking inspiration from [Lua](https://www.lua.org/), Cosmo is easily extensible through the use of 'Proto-objects,' which describe the behavior of Objects. For example the following is a simple Vector Proto which describes behavior for a Vector-like object.

```lua
proto Vector
    function __init(self)
        self.vector = []
        self.x = 0
    end

    function __index(self, key)
        return self.vector[key]
    end

    function push(self, val)
        self.vector[self.x++] = val
    end 

    function pop(self)
        return self.vector[--self.x]
    end
end

var vector = Vector()

for (var i = 0; i < 4; i++) do
    vector:push(i)
end

for (var i = 0; i < 4; i++) do
    print(vector:pop() .. " : " .. vector[i])
end

```

Output:

```
3 : 0
2 : 1
1 : 2
0 : 3
```

Usage:

```sh
$ cosmo script.cosmo
```

or a REPL can be opened by providing no arguments.

{{< img REPL.png "720x q100 jpg" "" >}}


Full documentation on language features can be found [here](https://git.openpunk.com/CPunch/Cosmo/src/branch/main/docs)