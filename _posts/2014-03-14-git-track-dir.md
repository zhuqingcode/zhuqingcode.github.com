---
layout: post
title: "git跟踪某些目录"
description: ""
category: git
tags: [git,.gitignore]
---
{% include JB/setup %}

自己在使用git的过程中常常碰到这么一种情况：我只想跟踪这个工程下的某一个目录，其余目录不想关心，因为我本上不修改不感兴趣目录下的源代码。这种情况经常会在一些移植的时候碰到，例如U-boot，linux kernel等等。  

那么，问题来了：如果碰到这种情况，我的**.gitignore**文件该怎么写呢？下面就来简单介绍一下，给个例子：  

	/*
	!include
	!common
	.gitignore
	.gitignore.bak
	*.o
	*.a
	*.S  

前面三行表示我只想跟踪当前目录下的**include**、**common**，后面五行表示忽略某类文件，很简单吧！  

#完
