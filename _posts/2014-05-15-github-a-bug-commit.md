---
layout: post
title: "向Github提交一次错误的commit后该怎么办"
description: ""
category: git
tags: [git,Github]
---

在使用*Github*作为远程仓库的时候，不知道你有没有遇到这种情况：刚刚*git push*了一个*commit*到*Github*，突然发现刚刚那个*commit*有错误，而现在想从*Github*上删掉这个错误的*commit*。这种情况下，我们该怎么办呢？这里就介绍两种方法来解决这个问题。  

### 方法一（推荐）  

####采用*git reset*结合*git push -f*的方法。  

我们来结合实例说一下。  

* 在本地*clone Github*上的一个仓库：  

	linux-geek:/home/tmp # git clone git@github.com:zhuqingcode/sample-git.git  
	Cloning into sample-git...  
	Enter passphrase for key '/root/.ssh/id_rsa':   
	remote: Counting objects: 27, done.  
	remote: Compressing objects: 100% (9/9), done.  
	remote: Total 27 (delta 0), reused 25 (delta 0)  
	Receiving objects: 100% (27/27), done.  

* 进入sample-git目录，故意做一次错误的提交，并且推送到Github上：  
	
	linux-geek:/home/tmp # cd sample-git/  
	linux-geek:/home/tmp/sample-git # ls -l  
	total 4  
	drwxr-xr-x 8 root root 328 May 15 16:15 .git  
	-rwxr-xr-x 1 root root  40 May 15 16:15 README.md  
	linux-geek:/home/tmp/sample-git # cat README.md   
	linux-geek:/home/tmp/sample-git # echo X-bug > README.md   
	linux-geek:/home/tmp/sample-git # cat README.md   
	X-bug  
	linux-geek:/home/tmp/sample-git # git commit -am "X-bug"  
	[b0 1a69808] X-bug  
	 1 files changed, 1 insertions(+), 1 deletions(-)  
	linux-geek:/home/tmp/sample-git # git push origin b0   
	Enter passphrase for key '/root/.ssh/id_rsa':   
	Counting objects: 5, done.  
	Writing objects: 100% (3/3), 245 bytes, done.  
	Total 3 (delta 0), reused 0 (delta 0)  
	To git@github.com:zhuqingcode/sample-git.git  
	   6926d03..1a69808  b0 -> b0  

这样子我们就推送了这个错误的提交到*github*上了。我们来看一啊*github*上的相关内容：   

![github0](/images/github0.png)    

![github1](/images/github1.png)   

的确，我们是*push*上去了。  

* 修正错误  

	linux-geek:/home/tmp/sample-git # git reset --hard HEAD~1  
	HEAD is now at 6926d03 sample  
	linux-geek:/home/tmp/sample-git # git push -f origin b0  
	Enter passphrase for key '/root/.ssh/id_rsa':   
	Total 0 (delta 0), reused 0 (delta 0)  
	To git@github.com:zhuqingcode/sample-git.git  
	 \+ 1a69808...6926d03 b0 -> b0 (forced update)  

说明一下，第一步我们先*reset*到原来*HAED~1*的位置，相当于把那次错误的*commit*删掉了。第二步，强制推送到*Github*上。注意*-f*选项不能丢。  

我们来看一下*github*上的*networks*： 

![github2](/images/github2.png)  

那一次错误的commit的确被删掉了，任务完成。  
 
### 方法二  

####采用*git revert的方法。  

这里假设我们回到那个错误的*commit*并且推送到了*Github*上：  

	linux-geek:/home/tmp/sample-git # git revert HEAD
	[b0 4a65779] Revert "X-bug"
	 1 files changed, 1 insertions(+), 1 deletions(-)
	linux-geek:/home/tmp/sample-git # git push origin b0 
	Enter passphrase for key '/root/.ssh/id_rsa': 
	Counting objects: 5, done.
	Writing objects: 100% (3/3), 309 bytes, done.
	Total 3 (delta 0), reused 2 (delta 0)
	To git@github.com:zhuqingcode/sample-git.git
	   1a69808..4a65779  b0 -> b0  

我们采取*revert*的方法，相当于重新生成一个提交，来撤销前一次错误的*commit*。  

我们来看一下*github*上的*networks*： 

![github3](/images/github3.png)   

注意图中的不同之处额。这里并没有直接删除那次错误的*commit*，而是重新生成一个提交，来撤销前一次错误的*commit*。


