---
layout: post
title: "git checkout分支间快速切换"
description: ""
category: git
tags: [git,checkout]
---
{% include JB/setup %}

#在
使用*git*的分支*branch*功能时常常要在两个分支间来回切换，一般都是这么操作的：  

>git checkout b0(切换到*b0*分支)  
>git checkout b1(切换到*b1*分支)  

现在介绍一种快速在两个分支*branch*间切换的技巧，具体来说：假设你当前处于分支*b0*上，现在切换到分支*b1*上去：  

>git checkout b1  

现在你就处于分支*b1*上了，这时候我们再切换到分支*b0*上去，可以这么来干：  

>git checkout -  

上述的**“-”**是连接符，这样我们又回到了分支*b0*上了。我们再回到分支*b1*上：  

>git checkout -  

看看是不是到分支*b1*上了，是不是比写分支名来的快。  

#完