---
layout:     post
title:      "测试"
subtitle:   "一篇测试笔记，使用锚点跳转"
date:       2018-12-22
author:     "Jorce"
header-img: "img/post-bg-unix-linux.jpg"
tags:
    - OS
    - Unix
    - Linux
---

> This document is not completed and will be updated anytime.


## Catagory

1. [Unix](#unix)
	1. [Bell Labs](#bell-labs)
	2. [Xenix](#xenix)
	3. [BSD](#bsd)
	4. [FreeBSD & Apple](#freebsd--apple)
	5. [NeXTStep](#nextstep)
	6. [Darwin](#darwin)
	7. [POSIX](#posix)

---

## Unix


> Unix is a **family** of multitasking, multiuser computer OS.


Derive from the original **AT&T Unix**, Developed in the 1970s at **Bell Labs** (贝尔实验室), initially intended for use inside the **Bell System**.

- #### Bell Labs
Bell 和 AT&A 在那时已经是一家了，可以看到那时的通信公司真是一线 IT 公司呢。
**C 语言也是 Bell Labs 的产物**，从一开始就是为了用于 Unix 而设计出来的。所以 Unix （在 73 年用 C 重写）在高校流行后，C 语言也获得了广泛支持。



AT&T licensed Unix to outside parties(第三方) from the late 1970s, leading to a variety of both **academic** (最有有名的 BSD ) and **commercial** (Microsoft Xenix, IBM AIX, SunOS Solaris)

- #### Xenix
微软 1979 年从 AT&A 授权来的 Unix OS，配合着 x86 成为当时最受欢迎的 Unix 发行版。后来 M$ 和 IBM 合作开发 OS/2 操作系统后放弃，后来最终转向 **Windows NT**。

- #### BSD
**Barkeley Software Distribution**, also called Berkeley Unix. Today the term "BSD" is used to refer to any of the BSD descendants(后代) which together form a branch of the family of Unix-like OS.(共同组成了一个分支)
	- **BSD 最大的贡献是在 BSD 中率先增加了虚拟存储器和 Internet 协议**，其 TCP/IP(IPv4 only) 代码仍然在现代 OS 上使用（ Microsoft Windows and most of the foundation of Apple's OS X and iOS ）
	- BSD 后来发展出了众多开源后代，包括 FreeBSD, OpenBSD, NetBSD 等等……很多闭源的 vendor Unix 也都从 BSD 衍生而来。

- #### FreeBSD & Apple
FreeBSD 不但是 Open Source BSD 中占有率最高的，还直接影响了 Apple Inc : NeXT Computer 的团队在 FreeBSD 上衍生出了 NeXTSTEP 操作系统，这货后来在 Apple 时期演化成了 **Darwin** ，这个“达尔文”居然还是个开源系统，而且是 the Core of **Mac OS X** and **iOS**.

- #### NeXTSTEP
An **object-oriented**, multitasking OS. Low-level C but High-level OC language and runtime the first time, combined with an **OO aplication layer** and including several "kits".    
大家都知道 NeXT 是 Steve Jobs 被 forced out of Apple 后和 a few of his coworkers 创办的，所以 **NeXTSTEP 绝对是证明 Jobs 实力的作品。**

- #### Darwin
[Darwin](http://en.wikipedia.org/wiki/Darwin_(operating_system\)), the core set of components upon which Mac OS X and iOS based, mostly POSIX compatible, but has never, by itself, been certified as being compatible with any version of **POSIX**. (OS X, since Leopard, has been certified as compatible with the Single UNIX Specification version 3)  
**所以说 Mac OS X 算是很正统 Unix 的了**

- #### POSIX
可移植操作系统接口, Portable Operating System Interface, is a family of standards specified by the IEEE from maintaining compatibility between OS, defines the API along with Command Line Shells and utility interfaces, for software comaptibility with variants of Unix and other OS.
	- Fully POSIX compliant:
		- OS X
		- QNX OS (BlackBerry)
	- Mostly complicant:
		- Linux
		- OpenBSD/FreeBSD
		- Darwin (Core of **iOS** & OS X)
		- **Android**
	- Complicant via compatibility feature （通过兼容功能实现兼容）
		- Windows NT Kernel
			- Windows Server 2000, 2003, 2008, 2008 R2, 2012
		- Symbian OS (with PIPS)
			- Symbian was a closed-source OS.


---

