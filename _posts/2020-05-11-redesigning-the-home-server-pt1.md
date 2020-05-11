---
layout: post
title: "Redesigning the home server (Part 1)"
categories: server, NAS, virtualization, storage
author: "Michael Moese"
meta: "home-server"
---

I am writing this as part of my design process for re-implementing
my home server in 2020, after many, many years of (production-) usage.
This article is the first in a series of posts I am planning describing
what I do, why I do it and what the benefits are.

This first post is intended to describe the status quo, describing the
hardware I am using, and the services I am running. On top, I will try
to summarize some of the weakness I discovered. In the end, there is a
reason for me to re-design this monster.

# Hardware overview
The hardware changed over time. It changed a lot. I started with some
cheap Intel Celeron processor with 4 GB of RAM. Some years later, 
I upgraded to a third generation Core i5 and upgraded the RAM to 8 GB.
The latest upgrade was a (used) Supermicro X10SLL-F with an Intel
Xeon CPU E3-1270 v3 and 32 gigabytes of ECC memory and a LSI SAS2008 
HBA that just passes the individual disks to the OS. 
I have five hard drives of 3TB each in a RAID 5 configuration.

# Software overview
First, to the operating system. After a lot of trial and error, I ended
up again with the distribution I used as my first one, back in 1998:
openSUSE. I migrated the installation from Tumbleweed to Leap 42.1 when 
it was released, and kept it up-to-date over the years. While there is 
always something you don't like, it did never break completely and in 
the meantime, I'm really familar with this. And more than two years ago,
I started my job at SUSE, but that did not even impact decision.
This distribution is rock-solid and never let me down.

I have my disks in one big RAID 5 array, using XFS as the file system.
While again, this never let me down, I miss the advanced features of 
modern filesystems, like snapshots. But more on that later.

## Services
The services I am running can be grouped into two groups: essential 
production services, especially for my wife on the one hand, and my 
personal playground.
The most important services are actually simple file serving with 
samba and NFS and automatic encrypted backups of importand data.
So the documents my wife has to access for her business are stored 
on samba shares. Every night, an incremental, encrypted off-site
backup is created by a cron job.
As a second service, I'm running a nextcloud instance for her (and
myself, of course) on our 100 MBit/s dialup connection. Good enough 
for our requirements.
Then there are a lot of private files stored on the array. Mainly
media files and photos. That's too much data for periodic backups at
a price I am willing to spend, so I just backup my photos and personal
documents.

Next, I am running a Plex media server. If you never used a proper media
center software: take a look [1]. We really use this a lot, even in the
days of Netflix & co.
This is running inside a docker container. The second service I run
containerized is gitea. A lightweight, personal git server, where I 
keep repositories I don't want on Github, like my smart home configuration
or my password store.
Talking about smart home, I'm running Home Assistant as my personal 
smart home hub. This is just running in a python virtual environment. 
The same for Octoprint, the amazing interface to my 3D printer.

Finally, right now I am playing and investigating PiHole ad blocker 
in a virtual machine.

I used to run a lot of websites and -services for me and my family, 
but basically only a few, like a photo gallery, survived. But as you 
can see, there is quite a lot going on. And most of the services are 
used on a regular basis.

## Problems with the current setup
I run most of the services for a reason. However, right now something
is running in a container, another thing in a VM, the next is just a 
plain process running as some user and residing somewhere on the 
filesystem.
Sometimes it is even difficult to quickly dive into problems, because
I tend to forget things once I don't touch them for some weeks. After
gathering some experience, I want to ditch docker. Containers just don't serve
me well, and I think in my case, the added complexity is totally not worth. 
Plus, on top, you end up just docker pull <something> and you even give up
control about what is running. I intend to use docker solely for evaluating
services before setting them up properly.
I gained a lot of experience with administrative tasks, so I am partly writing
this series as my personal "lessons learned" - and trying to get things right
now. I hope this will be useful for someone else, too.
My root filesystem resides on a 256 GB SSD drive and is formatted with btrfs.
Together with snapper, this saved my life many many times. I really love
snapshots, and I will never again use a filesystem that does not offer them.
This leads me to the next problem: XFS does not support snapshotting. You
could achieve this functionality with LVM [2], but as XFS is not a
copy-on-write-filesystem, this will never be fun.
So basically, I want to replacy my software-raid-with-xfs with something more
modern and feature-rich, like btrfs. More on that in one of the next posts.
And I would prefer moving my services into one (or a few) virtual machine, so
I could maybe even run them in a HA-setup or at least have a cold standby
machine sitting somewhere in case my hardware dies. 

# Conclusion
Even if you build a server for your personal/home use, you should have a plan
in advance. What started as a toy for me during my university days ended up
being a production grade system that has to be up and running 24/7.
This was never foreseeable, but I need to get this right with only minimal
service disruptions. 
However, the following posts will be very useful if you want to setup your
home server - and do it in a way you could use it for a long time and keep it
maintainable.


|---|-----------------
|[1]| https://plex.tv |
|[2]| https://www.tecmint.com/take-snapshot-of-logical-volume-and-restore-in-lvm/ |
