---
layout: post
title: "cannot close fd before spawn"
description: ""
category: Jekyll
tags: [Jekyll]
---
{% include JB/setup %}

关于怎么使用*jekyll*写博客，这儿不作介绍，网上有大把大把的教程，这里只想介绍一下*Jekyll build*时产生的警告*"cannot close fd before spawn"*而导致*jekyll build*失败（反馈消息：*"Conversion error: There was an error converting _posts/xxx.md"*），因为这个问题实在困扰了我好久，想把解决方法分享给大家。  

其实是*pygments.rb*版本导致。产生这个警告的大多数原因是*pygments.rb*版本不正确。如果你安装了*0.5.0*以外的版本，请这样子来干：  

>gem uninstall pygments.rb --version "=0.5.*"(你安装的版本号)  
>gem install pygments.rb --version "=0.5.0"  

然后再去*jekyll build*，应该就会成功了。看一下我的反馈消息：  

![Jekyll-build](/images/Jekyll-build.png)  

最后*jekyll serve*，访问*localhost:4000*应该没什么问题。

#完