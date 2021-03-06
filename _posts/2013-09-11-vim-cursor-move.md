---
layout: post
title: "vim光标移动 升级篇"
description: ""
category: linux
tags: [vim]
---

上篇简单介绍了*vim*光标(cursor)移动，如果你觉得*vim*只能这么移动光标，你就大错特错了。可以这么说*vim*完全颠覆了我们在windows下使用IDE的一些思想，可以和鼠标说拜拜了。其实，鼠标在*vim*里面也没有什么用处。废话少说，直接进入主题。  

###技巧一：[number][move action]  

你知道了[h][j][k][l]的移动方式，但是单独使用它们你不觉得移动起来很费劲吗？如果我想一下子从光标位置开始向下移动**100**行，单用[j]，岂不是要累死？其实，很简单你只需要在[j]加个数字就可以了，例如*100j*，我们来看一下gif效果图：  

![vim-100j][1]  

[1]:https://github.com/zhuqingcode/zhuqingcode.github.com/blob/master/images/vim-100j.gif?raw=true


看到效果了吧！举一反三，其他的[move action]，例如：  

>h k l  
>w W e E b B  
>\# *  


等等都适用这条规则。  

###技巧二：[number]G  (number > 0)

这条命令适用于以行的移动，例如，你想跳到100行去，直接  
>100G  

就过去了。同样，在『last-line』模式下，这条命令同样可以实现相同的功能：  

>:100[Enter]  

如果不加数字，直接一个『G』，就跳到最后一行去了。如果在回到行首：『gg』或者『1G』就可以了，是不是很快！  

###技巧三：F，f，T，t  

这条命令适用于一行中的移动。解释一下：  

>F[x]:以当前行中光标所在位置为基准，向『前』查找字符『x』。如果找到，移动光标到『x』上；如果没找到，光标位置不动。   
>f[x]:以当前行中光标所在位置为基准，向『后』查找字符『x』。如果找到，移动光标到『x』上；如果没找到，光标位置不动。  
>T[x]:以当前行中光标所在位置为基准，向『前』查找字符『x』。如果找到，移动光标到『x』后一个字符上；如果没找到，光标位置不动。  
>t[x]:以当前行中光标所在位置为基准，向『后』查找字符『x』。如果找到，移动光标到『x』前一个字符上；如果没找到，光标位置不动。  

有了这招，在一行中移动光标是不是更加得心应手了！  

#完