---
layout: post
title: "删除github上的master分支"
description: ""
category: git
tags: [git, github,master]
---
{% include JB/setup %}

使用过git和[github][1]来管理和托管代码的人都知道在[github][1]上默认的分支是**master**。  

但是如果你不想使用**master**分支而且你想在[github][1]上删除它。我想你会想当然的这么干：  

	git push origin :master  

如果你在git中这么做，你得到的反馈类似这样：  

	remote: error: refusing to delete the current branch: refs/heads/master
	To git@github.com:zhuqingcode/zhuqingcode.github.com.git
 	! [remote rejected] master (deletion of the current branch prohibited)
	error: failed to push some refs to 'git@github.com:zhuqingcode/zhuqingcode.github.com.git'  

如下图：  

![delete-master](/images/delete-master.png)  

很显然，你这种做法不能达到你想要的效果。正确的做法：  

* 先去github上更改默认的分支：  

	Settings-->Default Branch  

更改你想要的分支。如下图：  

![github-settings](/images/github-settings.png)  


* 然后，回到git里面重新做一下上一条删除命令即可。  

# 完  

[1]:https://github.com/

