---
title: "Switching to Ansible & minor workflow changes"
date: 2022-06-01
author: CPunch
tags: ["opinion"]
---

Ansible if you hadn't heard, is IaC's (Infrastructure as Code) most loved obsession. Supported by RedHat, it allows deployment and management of servers in a sustainable and easy package through things called 'Playbooks.' These playbooks are written in YAML and specify actions to setup/maintain a server. Of course I didn't *need* to use Ansible, but after writing one for the [OpenPunk VPS](https://github.com/CPunch/openpunk-ansible) I can confidently say that yes, ***you don't need it.*** Ansible is also a fairly desired skill. Looking at a simple indeed job search with the keyword 'Ansible' yields TONS of results.

![](indeed.png)

But of course, just because people like Ansible, doesn't necessarily make it good. So what **good** does it do? 

## The 'good'

Ansible (for me at least) can make server migrations super easy. In the event that the OpenPunk VPS is shutdown, hacked or the server farm it's hosted on is miraculously burned to the ground, I can fairly quickly recover my site in a mater of minutes. This isn't to say Ansible replaces backups (***ALWAYS BACKUP***) but for my use case, my VPS uses a lot of cron jobs and self-written scripts. Resetting all of those back up manually is a pain. Ansible makes this process fairly easy.

Ansible also maintains the 'state' of your server. If you need to muck around on your production server and accidentally break something, a good Ansible playbook should restore the server back to a functional state.

Ansible is also really simple in terms of installment. There's no package you need to install on your remote server, which lightens bloat! It works through ssh to do all of it's magic. The only thing to install is a package probably already in the core repository of your own computer.

## The 'bad'

For smaller projects, you will spend more time writing your playbook than you would if you were to just deploy your server yourself. Of course if your goal is to just 'learn Ansible', you should do it! I did learn quite a bit about automation and I'm glad I wrote the playbook.

## Conclusion

I think I just enjoy automation. Ansible is a pretty solid automation tool, the ecosystem is fairly mature nowadays so anything you're looking to do has probably been done before (if not something similar.) I know some people have a vendetta against RedHat, but I find that fairly childish. If the software is of good quality and the project has innocent goals, why not use it?
