---
title: "Sins of NPM"
date: 2022-03-19
author: CPunch
tags: ["opinion"]
---

NPM has changed the way developers (especially webdevs) develop software. It's common to see big projects pull in tens of packages, each of which pulls in their own dependencies totalling to hundreds of packages. This forces the developer to trust hundreds of different authors. If you haven't heard the news recently, an NPM package [node-ipc](https://www.npmjs.com/package/node-ipc) was recently updated to include a malicious payload. Obviously this isn't the first instance of malicious NPM packages, in fact NPM themselves have removed hundreds of malicious packages in the past 6 months. What is uncommon however is having such a common and widely depended on package suddenly being updated to introduce malicious behavior purposely.

While this isn't the first time and certainly not the last time, this is an extreme violation of the trust between developers in the FOSS community. Not to say this hasn't happened before but this breaks our common trust as developers. FOSS is the only environment in our world where regardless of what background or opinions you share you're judged solely on merit. Stunts like this break our mutual trust and hurts the whole community.

Package managers like NPM make it extremely easy to pull in hundreds of packages, it is unrealistic to expect a developer to audit and continuously re-audit every update. To end this opinion piece, here's a github issue opened by an anonymous user claiming to represent an American NGO group. Keep in mind the group in question was never named and these events are *unconfirmed*.

![](ngo.jpg)

