---
title: "Memory Signatures and self-updating cheats"
date: 2020-04-22
author: CPunch
tags: ["lua", "ida", "reverse-engineering"]
draft: true
---

If you're anything like me 3 years ago, while getting into cheats you'll often wonder, "huh, if the addresses change after every game update.... how does the cheat still work?" While the method I'm going to talk about isn't always used (especially if your target has a public SDK and you can just find the addresses by asking the library nicely), it's been proven to work as long as:
- They don't change compilers, or if they do, they're linking prebuilt versions of whatever you're messing with.
- The codebase doesn't change drastically.

As you can see, this means that as long as whatever you're messing with in the codebase is rarely changed, this method should work great! The method I'll be talking about is called "Memory Signatures." It's a way to identify a segment of memory not by it's address, but by its bytes and/or page permissions. Take this for example:

```
0x83 0xEC 0x08 0x8B 0x44 0x24 0x10 0x8B 
0x54 0x24 0x18 0x8B 0x4C 0x24 0x14 0x52
0x89 0x44 0x24 0x04 0x8D 0x44 0x24 0x04
0x50 0x89 0x4C 0x24 0x0C 0x8B 0x4C 0x24
0x14 0x68
```

That may not seem like much to you, but that's the memory signature of luaL_loadbuffer from the Roblox client I used in my original [Manipulating Embedded Lua VMs](/pages/manipulating-lua-vms-1) series! Speaking of which, to give you an example, I'll be extending upon the source (repo here) and adding memory scanning capabilities to that source! So, by the end of this post our Roblox cheat should work across multiple versions of the client! Let's do a quick refresher of what addresses we already have and what they're for :) (So if you haven't read that series you probably should.)

For that specific client, (which you can [redownload here](/ROBLOX2012.zip))
- lua_gettop [0x0081A4A0] - We hook this function to grab the state because it's called all the time in the Lua VM.
- lua_newthread [0x0081B790] - After we grabbed a state, we'll need to create a new state to mess with while keeping the ROBLOX environment.
- lua_settop [0x0081A4B0] - We'll use this to pop our new thread from the other lua state.
- luaL_loadstring [0x0081C070] - We use this to compile our script source into a lua function ready to be called!
- lua_pcall [0x0081B220] - We use this function to call our compiled function on the stack.
- lua_tolstring [0x0081A8A0] - This is so we can grab errors off the lua stack if pcall failed.

Well, first thing I'll do is go ahead and add a function to our original code base in the Lua namespace.

```cpp
namespace Lua
{
	extern TluaL_loadbuffer luaL_loadbuffer;
	extern Tlua_pcall lua_pcall;
	extern Tlua_gettop lua_gettop;
	extern Tlua_settop lua_settop;
	extern Tlua_newthread lua_newthread;
	extern Tlua_tolstring lua_tolstring;

	// oooo spooky new function!!!
	void scanAddresses();
}
```

Then open up the RobloxApp.exe in IDAPro and go ahead to the first address in our list, lua_gettop at 0x0081A4A0.

![](lua_gettop_addr.png)

We'll goto the hexview and select the begining bytes. Now since this subroutine is pretty small, we'll actually end of selecting all of the bytes until the 0xC3 (retn) and the aligning slackspace.

![](lua_gettop_bytes.png)
> C3 is the return and those trailing 0xCC are NOPs and only serve to align the next subroutine in memory with a multiple of 16 :)

So, we have our first memory signature.. How are we going to actually search for that in memory? Well, I wrote this compact signature scanner for 32bit applications (but you can very easily modify it to work with 64bit applications!)

```cpp
DWORD scanSignature(DWORD start, const char* signature) {
	// we'll start looking at start, keep looking until we hit the max 32-bit address 0xFFFFFFFF
	for (DWORD addr = start; addr < 0xFFFFFFFF; addr++) {
		char* tmpAddr = (char*)addr; // casts the address to a pointer
		bool isOurAddress = true;
		for (int i = 0; i < strlen(signature); i++) {
			// our hard-coded wild-card is the NOP instruction (0xCC)
			if (signature[i] == '\xCC')
				continue;

			if (tmpAddr[i] != signature[i]) { // if it doesn't match our signature, it's not our address!
				isOurAddress = false;
				break;
			}
		}

		// if we made it all the way through that loop without setting isOurAddress to false, it matched the signature so return the address!
		if (isOurAddress)
			return addr;
	}

	// it'll probably crash before it reaches this part, but go ahead and return 0
	return NULL;
}
```

Basically we look at every address in the address-space and compare our signature with a wildcard until it finds a match and it'll return the address as a DWORD! Now let's use our signature with our function with the start address of the executable in our new Lua::scanAddresses() function!

```cpp
void Lua::scanAddresses() {
	DWORD startAddr = (DWORD)GetModuleHandle(NULL); // returns the start address of the application (RobloxApp.exe) 

	DWORD gettopAddr = scanSignature(startAddr, "\x8B\x4C\x24\x04\x8B\x41\x08\x2B\x41\x0C\xC1\xF8\x04\xC3");
	std::cout << "Found lua_gettop: 0x" << std::hex << (gettopAddr - (DWORD)GetModuleHandle(NULL)) + 0x00400000 << std::endl;
	lua_gettop = (Tlua_gettop)(gettopAddr);
}
```

![](found_gettop.png)

It looks like our scanSignature function works! Let's go ahead and get the other signatures and try those.