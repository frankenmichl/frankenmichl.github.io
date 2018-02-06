---
layout: post
title: "Get these includes right"
categories: C,clang,llvm
author: "Michael Moese"
meta: "include-what-you-use"
---

Have you ever thought about your #include directives if everything is compiling fine?
Sometimes you include header files you don't need. Often you miss some, and your code works because other header files included the ones you need.

Wouldn't it be nice if there was an easy way to tell if you got your #includes right?
In this post I will show you how I explored [Include What You Use](https://include-what-you-use.org/) on a new test case for the [Linux Test Project (LTP)](https://github.com/linux-test-project)

## Include What You Use
`include-what-you-use` builds on clang to analyze C and C++ files for their #include-directives.

At least for my distribution (currently openSUSE Leap 42.3) no packages are available, so I followed the installation instructions for my clang version, which is 3.8.
Be sure to read and follow the instructions in README.md.
I got it up and running in no time, but instead of copying the clang header files over to the install location, I just symlinked them.

## Using include-what-you-use on a LTP test case
Currently I create a bunch of regression tests for already fixed CVE's in the Linux kernel and in order to submit my patches for review, I want them to be as clean as possible.

If you want to change your compiler for building LTP, you would normally do the following (see the documentation):
``` bash
make -k CC=include-what-you-use
```
However, I strongly suggest building the entire LTP with a normal C compiler. The code base is huge, and you would only want to check one file at a time, don't you?
Oh and, you can use clang to build LTP. 

``` bash
$ make
or 
$ make CC=clang 
```
Now I saved my new test case as `testcases/cve/cve-2017-16939.c` and finally analyzed it:
``` bash
$ make -k CC=include-what-you-use
include-what-you-use -g -O2 -g -O2 -fno-strict-aliasing -pipe -Wall -W -Wold-style-definition -D_GNU_SOURCE -D_FORTIFY_SOURCE=2 -I../../include -I../../include -I../../include/old/   -L../../lib  cve-2017-16939.c   -lltp -o cve-2017-16939
warning: -lltp: 'linker' input unused
warning: argument unused during compilation: '-L../../lib'

cve-2017-16939.c should add these lines:
#include <unistd.h>           // for usleep, pid_t

cve-2017-16939.c should remove these lines:
- #include <netinet/in.h>  // lines 31-31
- #include <sys/wait.h>  // lines 30-30

The full include-list for cve-2017-16939.c:
#include <linux/netlink.h>    // for nlmsghdr, sockaddr_nl, NETLINK_XFRM
#include <linux/xfrm.h>       // for XFRMNLGRP_NONE, XFRM_MSG_GETPOLICY
#include <sched.h>            // for unshare, CLONE_NEWNET, CLONE_NEWUSER
#include <stdlib.h>           // for exit, WIFEXITED
#include <string.h>           // for memset
#include <sys/socket.h>       // for socket, AF_NETLINK, PF_NETLINK, SOCK_RAW
#include "tst_res_flags.h"    // for TCONF, TFAIL, TPASS
#include "tst_safe_macros.h"  // for SAFE_MALLOC, SAFE_WAITPID
#include "tst_safe_net.h"     // for SAFE_SENDTO, SAFE_SETSOCKOPT
#include "tst_test.h"         // for tst_brk, tst_res, SAFE_FORK, tst_test
---
```
## Looking at the results
So, `include-what-you-use` tells me to add an include to `unistd.h`. That is totally right, and the build did not fail by accident. So this is to be added.
The header `sys/wait.h` was included by accident - I had a call to `waitpid()` in the code. However, LTP has a `SAFE_WAITPID()` macro, and I replaced the call, but I forgot to remove the include.
If I would remove `netinet/in.h`, the build would fail. This is a mistake of `include-what-you-use`. If you use the `fix_includes.py`-script, you would want to add `// IWYU pragma: keep` at the end
of this line. For my case, I just ignored this one.

The other includes were found to be correct, and the output even shows the symbols that a header file is needed for.

## Conclusion
Why did I do this? Well, primarily because I stumbled upon `include-what-you-use`. I was curious, and I played with it. 
However, it is at least good practice not to include header files you don't need, as they could, at least theoretically, interfere with your software in a way you don't want to.
On the other hand, relying on implicitly included headers may be a portability issue.

I think I will use this tool more regularly now.
