---
layout: post
title: "git reflog解救git reset引起的血案"
description: ""
category: git
tags: [git,reflog]
---

不知道大家在使用*git*的过程中有没有遇到这样一种情况：一不小心操作*git reset*的时候出错导致这几个星期辛辛苦苦写的代码全丢失了，加上如果没有推送到远程仓库，这下子你就快疯掉了，心中暗想：难道要老子重新写一遍，靠！其实不然，相反，你该庆幸，因为你使用了*git*进行代码跟踪的。记住，只要是你在*git*中提交过的*commit*没那么容易就真正地消失的。除非你想让他彻底的消失。这时候*git reflog*就排上用场了，下面就结合实例做出解释。  

* 新建一个目录git-test，初始化一下仓库，做一次简单的提交：  

	mkdir git-test  
	cd git-test/  
	git init  
	echo 0 > test  
	git add .  
	git commit -am "init"  

* 假设这几个星期你做了三次重要代码的提交：  

	linux-geek:/home/tmp/git-test # echo "important code0" >> test  
	linux-geek:/home/tmp/git-test # git commit -am "important code0"  
	[master 03f85a1] important code0  
	 1 files changed, 1 insertions(+), 0 deletions(-)  
	linux-geek:/home/tmp/git-test # echo "important code1" >> test     
	linux-geek:/home/tmp/git-test # git commit -am "important code1"  
	[master 83547ba] important code1  
	 1 files changed, 1 insertions(+), 0 deletions(-)  
	linux-geek:/home/tmp/git-test # echo "important code2" >> test     
	linux-geek:/home/tmp/git-test # git commit -am "important code2"  
	[master b826ae5] important code2  
	 1 files changed, 1 insertions(+), 0 deletions(-)   
	linux-geek:/home/tmp/git-test # cat test  
	0  
	important code0  
	important code1  
	important code2  

* 就在这个时候，假设你头脑突然短路，用了*git reset*这条命令：  

	linux-geek:/home/tmp/git-test # git reset --hard HEAD~3  
	HEAD is now at 2e71cf6 init  

再来看一下代码的情况：  

	linux-geek:/home/tmp/git-test # cat test
	0  

心中不禁暗自自责，怪自己手贱，等等。总之，懊恼的一塌糊涂。到这个时候说什么都晚了，有没有什么办法补救呢？答案是肯定的！  

我们用*git reflog*来看一下：  

	linux-geek:/home/tmp/git-test # git reflog
	2e71cf6 HEAD@{0}: HEAD~3: updating HEAD
	b826ae5 HEAD@{1}: commit: important code2
	83547ba HEAD@{2}: commit: important code1
	03f85a1 HEAD@{3}: commit: important code0
	2e71cf6 HEAD@{4}: commit (initial): init  

其中，我们可以清楚地看到*HEAD*每次移动的情况以及哈希值，于是乎我们再采用*git reset*去试一下：  

	linux-geek:/home/tmp/git-test # git reset --hard HEAD@{1}
	HEAD is now at b826ae5 important code2  

我们再来看一下代码的情况：  

	linux-geek:/home/tmp/git-test # cat test
	0
	important code0
	important code1
	important code2  

心中暗想：“我操！代码又回来了”，再来看一下历史提交记录：  

	linux-geek:/home/tmp/git-test # git log
	commit b826ae515f93e628fba64d6e9713c25ecca83d5e
	Author: zhuqinggooogle <zhuqinggooogle@gmail.com>
	Date:   Fri May 16 15:05:19 2014 +0800
	
	    important code2
	
	commit 83547baf8a68e7630bb5eb5d005f0049d36e9565
	Author: zhuqinggooogle <zhuqinggooogle@gmail.com>
	Date:   Fri May 16 15:05:10 2014 +0800
	
	    important code1
	
	commit 03f85a15d10ea3143532fa1747a4311fc42bcb8a
	Author: zhuqinggooogle <zhuqinggooogle@gmail.com>
	Date:   Fri May 16 15:04:21 2014 +0800
	
	    important code0
	
	commit 2e71cf61266863305b81b00f199c175763225b6c
	Author: zhuqinggooogle <zhuqinggooogle@gmail.com>
	Date:   Fri May 16 15:02:42 2014 +0800
	
	    init  

也是完完整整的，真是太神奇了，有一种屌丝逆袭的感觉。

