---
layout: post
title: "git checkout使用总结"
description: ""
category: git
tags: [git,checkout]
---
{% include JB/setup %}

* **切换分支**： 

如果你想切换到名为*branch-name*分支上，可以这么来干：  

		git checkout branch-name  
* **放弃文件的修改**：  

如果你想放弃工作目录下名为*file-name*的修改，可以这么来干：  

		git checkout -- file-name  
如果有多个文件：

		git checkout -- file-name0 file-name1 ... 

* **分离HEAD**:  

在某些情况下，我们要切换到历史中的某次*commit*或者*tag*上，查看或者修改源代码或者直接新建某个新的分支，我们可以这么来干：  

		git checkout commit-name  
或者  

		git checkout tag-name  
如果你这么干了，git会提醒你当前处于*detach HEAD*的状态  

* **提取文件**  

假设有这么一种情况：你想从某个*commit*中直接提取名为*file-name*的文件到当前的工作目录和暂存区，你可以这么来干：  

		git checkout commit-name file-name  
注意其中的*commit-name*也可以是分支名或者*tag*名  

完
=

		