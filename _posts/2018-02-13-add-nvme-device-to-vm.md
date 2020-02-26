---
layout: post
title: "NVMe device in QEMU-VM"
categories: QEMU, NVMe
author: "Michael Moese"
meta: "qemu nvme"
---

Have you ever wanted to play with fancy new NVMe, but lack the hardware?
Well, luckily, QEMU has all you need to play with NVMe (and even NVMe over Fabrics).

## manual starting of QEMU
Just create a backing image file (using ```dd if=/dev/zero of=/path/to/nvme.img bs=1M count=4096``` for example) and start QEMU like this:

``` bash
$ qemu-system-x86_64 -enable-kvm -m 4096 -smp 4 -cpu host-hda ~/path/to/qemu_disk.qcow2 -boot c \
        -drive file=/path/to/nvme.img,if=none,id=D22 \
	-device nvme,drive=D22,serial=1234
``` 

## NVMe and virt-manager
Unfortunately, if you want to use virt-manager, things are (as of the time of this writing) 
not that easy - you need to configure the additional command line arguments manually.
Find the UUID of your QEMU-VM in virt-manager, for example ```b11b447d-00a9-4764-80d9-3d68e88ef686```

Just use virsh:
``` bash
$ virsh
virsh # connect qemu:///system

virsh # edit b11b447d-00a9-4764-80d9-3d68e88ef686
```
This opens the XML-file describing your VM in your $EDITOR.
Change the first line from:
``` xml
domain type='kvm'
```
to 
``` xml
<domain type='kvm' xmlns:qemu='http://libvirt.org/schemas/domain/qemu/1.0'>
```
and add at the very end, just before the ```</domain>``` the following:
``` xml
<qemu:commandline>
	<qemu:arg value='-drive'/>
	<qemu:arg value='file=/path/to/nvme.img,if=none,id=D22'/>
	<qemu:arg value='-device'/>
	<qemu:arg value='nvme,drive=D22,serial=1234'/>
</qemu:commandline>
```
And save and exit the editor. Then run your VM again

Have a look in /dev:
``` bash
$ dmesg | grep nvme
[    4.906319] nvme nvme0: pci function 0000:00:0a.0

$ ls /dev/nvme*
/dev/nvme0
```

Have fun playing with nvme!

