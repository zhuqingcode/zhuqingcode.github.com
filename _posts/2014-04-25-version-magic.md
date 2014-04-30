---
layout: post
title: "version magic"
description: ""
category: linux
tags: [version-magic,git]
---
{% include JB/setup %}

在调试linux驱动的过程中偶然遇到这么一种情况：  

	cfg80211: version magic '3.0.8+ mod_unload ARMv7 ' should be '3.0.8 mod_unload ARMv7 '
	insmod: can't insert 'cfg80211.ko': invalid module format  

这令我感到很奇怪，本来就是3.0.8的版本，怎么编译出来的驱动的*version magic*会多了一个*+*，google一番后，找到如下一段话：  

	The plus sign is there in the first place because the kernel was
	compiled with a git repository, which displayed modifications relativeto the official tree. To avoid it, I could have compiled 
	the kernel after renaming the .git directory to something else prior
	to compiling the kernel. But I forgot to do that.  

大概意思就是说我的内核目录下存在*.git*这个目录，导致编译后的*version magic*或多一个*+*，我是用git进行代码跟踪的，当然会有这个目录，解决办法如下：  

	mv .git anothername  

即可。

#完