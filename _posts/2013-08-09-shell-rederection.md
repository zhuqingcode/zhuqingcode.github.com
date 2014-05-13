---
layout: post
title: "shell脚本中重定向之谁动了我的输出"
description: ""
category: shell
tags: [shell]
---

“重定向”是shell脚本中一个比较重要的知识点！简单来说，就是把一个命令或脚本中的输出重新“挪到”另一个地方，而不是输出到标准输出。在linux中，所谓的标准输出就是文件描述符为“1”的输出。还记得C语言中的printf（）函数吗？他就是把内容输出到标准输出的！看一下这段程序：  

	#include <stdio.h>
	int main()
	{
		printf("hello,world!\n");
	}  

上述的程序会再屏幕上输出hello,world!这里的屏幕就是标准输出！
当然，在shell脚本中和C语言中有异曲同工之妙！只不过不是printf（）函数而已！而是echo或者printf！例如：
在linux的命令提示符下直接键入： 
 

	echo hello，world！  


然后，回车！
看一下输出结果：  

![shell0](/images/shell-rederection0.jpg)   

看到了吗？
当然，上面的命令在默认的情况下输出到标准输出的！所以我们能在屏幕上看到  


	“hello，world”


下面就来看看怎么把标准输出重定向到另外一个地方！这样，就不会在屏幕上显示出来了！
看一下这个：  

![shell1](/images/shell-rederection1.jpg)   

看到这里，你也许就纳闷儿了！我的“hello，world！”到哪里去了？后面的temp.txt又是什么东东？
what a fucking command！
好了，我们来看看，我们的“hello，world！”到底去了哪里？  

![shell2](/images/shell-rederection2.jpg)  

看到了吗？我用cat命令把temp.txt中的内容输出到了标准输出了！注意了：这里又是标准输出哦！
明白了吧？原来  

	echo hello,world! > temp.txt  
 
这条命令是把"hello,world!"输出到了temp.txt文件中了！也就是重定向到了temp.txt文件中了！呵呵！懂了吗？
再来看一下：  

![shell3](/images/shell-rederection3.jpg)  

看到了这条命令中我框出来的地方了吗？是不是和上一条命令中有点不一样？多了一个“1”！其实"1>"和">"是一样的东西！就表示标准输出重定向到另外一个地方！
看到这里，你应该就明白了好多了吧！当然了，这是最基本的了！还是比较好理解的。
如果，你想把标准输出直接扔掉，不想输出到任何地方！你可以这样来做：  

![shell4](/images/shell-rederection4.jpg)  

这样子，你就再也找不到“hello，world！”了。注意/dev/null在linux下是一个“垃圾场”，只要你不要的东西，你都可以扔到里面去，例如：  

![shell5](/images/shell-rederection5.jpg)  

这样子会输出来很多东西！如果你嫌他烦，你可以这样子：  

![shell6](/images/shell-rederection6.jpg)  

嘿嘿，输出是不是不见了！好了，关于重定向的输出问题就介绍到这了！ 

# 完