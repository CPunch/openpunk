---
title: "Productivity: lessons learned & a 'how to be'"
date: 2022-12-21
author: CPunch
tags: ["opinion"]
---

I wanted to start this off by saying that you're worth more than your productivity. In today's world, it's easy to fall into the trap of thinking that your worth is directly tied to what you produce. After all, if you don't produce and create, what value are you bringing into this world? ***You're worth more than your work.*** I do believe that the way our social structures are setup really tends to reinforce this braindead idea that "productivity = value." Sure, if I were running a business and my coworkers weren't being productive, I'd let them go, but life isn't a business. Lives aren't measured in opportunity costs and profit margins. We create relationships, build communities, and do small selfless acts. If being productive and producing were all that was important in life, well, that'd be inhumane.

That being said, if you are in a good space and already realize that you are worth more than what you produce, I'd like to touch on some tips I've learned from talking to various people on how they stay productive and some lessons learned through trial and error.

## Have a clearly defined problem

Some software developers (myself included!) enjoy the process of programming so much that they lose track of why software exists. Sure, that idea is cool and technically impressive, but does it solve any problems?

Take for example, 'blockchain technology.' Crypto-bros have been searching for a problem to solve with this for years, and so far they've come up with: NFTs (which are just tickets with extra steps) & decentralized currencies (monero & ethereum are the only coins worth mentioning at this point). What can we learn from this? Of course I don't think throwing the idea out completely is the right thing, but so far we've only witnessed the poor lose money and the rich get richer, which supposedly bitcoin was going to solve.
> Please enlighten me if you believe there is a problem that blockchain solves. You might not realize it, but I am a huge fan of distributed systems. I think the technology is incredibly impressive; however, I haven't seen it truly solve anything yet.

For me, the first step in a project is clearly laying out the problem I need to solve. For example, say I have a problem where I need to send a large, sensitive file to a friend. Your immediate thought is to use one of those "fileupload.io !!!! real free fast download!!!" sites, but if you're anything like me, I'd rather not have sensitive files in the hands of a third party. Now think about the scope of this problem. Sure, I can just upload the file to a vps I own (using `scp` or whatever) and host it over http using nginx. Now yes, this works great and solves my problem quickly and easily. But what if I need to do this on the regular? Maybe I need to send multiple files to this person? Maybe I need this person to be able to share sensitive files with me! This is where I would start thinking of a more convenient solution. Elaborating and clearly defining your problem, leads to good solutions that are tailor-made to solve your problem. If the issue is just "I need to share this one file with this one person quickly," then the first proposed solution would work perfectly.

## Stop over-engineering

If your problem can be solved with simpler tools, do so! Like in my previous example, choosing the solution that solves your problem in the quickest and easiest way tends to be the better option. This also applies to managing a codebase as well. So many of my projects have died a slow and painful death due to the codebase becoming increasingly complex and unmaintainable. Remember, your job is to solve problems, not to flex your skills. If a solution already exists, use it! Most of the time, the simplest solution is the best. Don't assume problems exist before they actually pop up. Creating unnecessary complex solutions not only wastes time, but it also creates an unmaintainable and unapproachable codebase that makes it extremely difficult to get new contributors up-to-speed. Try the simplest solution first, and if in the rare case something more complex needs to be written, reach out to others for help before you waste time spinning your wheels.

Other people have run into the same problems you're facing, and if you're able to convey your problem well enough, you might learn that a simple solution already exists.

## Finish projects!!

I've realized that actually finishing projects is just another skill you need to practice. I've been guilty of "project hopping" for a long time. I've left so many projects half-finished, and the sad truth is you can't show off half-finished projects, especially if they don't do anything! One thing I've learned about myself is that I am somewhat of a perfectionist, and the fear of making shitware outweighs my fear of never finishing. During the slog to the finish line, I tend to wander off and start working on different projects instead of just finishing things. The best advice I can give for this is to just slog to the finish line. It's okay if things didn't come out perfect; you've now given yourself a chance to improve them later! The most important part is that it is complete. If it solves the problem you were trying to solve, then you've finished. Anything after that is just quality-of-life improvements.

## Setting deadlines

I know that it may seem dumb to create artificial "crunch time," especially if your project is exclusively worked on during your free time and you're juggling a full-time job and a family or whatever. However, this is something that has kept me from wandering projects and never finishing anything. I follow the general advice of setting your deadline approximately twice (or three!) times longer than you estimate it will take you. This will give you some breathing room and allow you to completely throw out the idea and start anew if things aren't working out.

Keep in mind however, that the deadline shouldn't be "the project has to be perfect by this day, everything has to look pristine"; instead, view this as "the project should be functional and somewhat solve the problem I needed to solve, in all of its imperfect glory." It is much better to actually have an imperfectly completed project than a perfect, forever-incomplete one. This isn't to say you shouldn't improve upon what you've built post-deadline! Just make sure your finished goal leaves room for improvement!

If you thought any of this was interesting, I've also touched on some personal tips on how to [avoid burnout and practice self-care](/journal/burnout). These tips won't mean anything if you're unable to maintain motivation to implement them.