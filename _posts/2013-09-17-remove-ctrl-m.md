---
layout: post
title: "vim之删除^M"
description: ""
category: linux
tags: [vim]
---
{% include JB/setup %}  

游离在windows和linux下的猿人肯定遇到过这个问题：在windows写的脚本程序，在linux下打开的时候出现了**^M**。这就牵扯到windows下回车和换行与linux下的区别，关于这个我不解释，去google吧，人家比我说的好多了！这里说一下在vim下怎么删除**^M**。  

这里为了对比纯字符型的**^M**和回车换行的**^M**(0x0d 0x0a)的区别，我们需要先来产生一个这样的文件。

>1.vim test[Enter]  
>2.键入‘i’，进入『insert』模式  
>3.『Ctrl+v Ctrl+M』输入**^M**(0x0d 0x0a)  
>4.『Esc』yy，9p，生成10行**^M**(0x0d 0x0a)  
>5.『G』跳到最后一行  
>6.『o』在当前行下方插入一行  
>7.『Shift+^ Shift+M』输入纯字符型的**^M**  
>8.同第【4】步，生成10行纯字符型的**^M**  
>9.『Esc』进出『normal』模式  

如下图：  

![vim-ctrl0](/images/vim-ctrl0.png)  

为了证明这两个**^M**的确不一样，我们进入vim十六进制编辑模式：  

>键入『1,$!xxd』『Enter』  ，如下图：  

![vim-ctrl1](/images/vim-ctrl1.png) 

看到不一样的地方了吧！  我们回到正常模式：  

>键入『1,$!xxd -r』『Enter』

下面就来具体讨论怎么删除**^M**(0x0d 0x0a)：  

>键入『1,$s/『Ctrl+v Ctrl+M』//g』『Enter』  

为了说明清楚，我录制了一个gif动态图：  

![vim-ctrl-m0](https://github.com/zhuqingcode/zhuqingcode.github.com/blob/master/images/vim-ctrl-m0.gif?raw=true)  

或者：  

>键入『1,$s/\r//g』『Enter』   

如下图：  

![vim-ctrl-m1](https://github.com/zhuqingcode/zhuqingcode.github.com/blob/master/images/vim-ctrl-m1.gif?raw=true)   

是不是删除了**^M**(0x0d 0x0a)了！  

下面就说说如何删除纯字符型的**^M**，这个较简单：  

>键入『1,$s/『Shift+^ Shift+M』//g』『Enter』   

如下图：  

![vim-ctrl-m2](https://github.com/zhuqingcode/zhuqingcode.github.com/blob/master/images/vim-ctrl-m2.gif?raw=true)  

看到了吧？就这么简单！  

#完 




