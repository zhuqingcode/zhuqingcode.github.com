---
layout: post
title: "【翻译】你应该知道的15个Linux Bash History扩展的例子"
description: ""
category: shell
tags: [shell]
---

##原文地址：[http://www.thegeekstuff.com/2011/08/bash-history-expansion/](http://www.thegeekstuff.com/2011/08/bash-history-expansion/)  

Bash命令历史是非常强大的。理解如何有效地使用Bash历史扩展将会使你在Linux命令行上很得心应手。  

这篇文章主要介绍了使用以下Linux Bash History扩展特点的15个例子：  

* 事件标志 - 在Bash历史中代表一个特殊的命令。它是以！开头的  
* 单词标志 - 在Bash历史中代表一个特殊的单词。通常和事件标志结合在一起使用。事件标志和单词标志以冒号分隔  
* 修饰符 - 修饰事件标志和单词标志执行替换的结果  

这篇文章是《Bash Tutorial Series》的一部分。  

正如你知道的，使用*history*命令查看所有历史。这样子会连同序号一起以一张表的形式显示以前执行过的所有命令。

	$ history
	1 tar cvf etc.tar /etc/
	2 cp /etc/passwd /backup
	3 ps -ef | grep http
	4 service sshd restart
	5 /usr/local/apache2/bin/apachectl restart  

#Bash历史事件标志  

###1.通过使用！n从历史中执行一个指定的命令  

如果你已经执行过某个命令，你能通过其在历史中相应的序号很快地执行它，而不用再敲一次。  

例如，要执行#4号命令，按照以下方式。这样子将会显示那条命令并且很快地执行它。  

	$ !4
	service sshd restart  

要执行历史中倒数第二条命令，可以这样子来做。  

	$ !-2  

要执行历史中倒数第一条命令，可以使用以下任意一种方法：  

	$ !!

	$ !-1

如果你在emacs的默认模式，你可以通过按下<Ctrl\>-P以获取先前的命令。  

如果你通过命令‘set -o vi’使能了vi风格编辑，可以使用<Esc\>-k获取先前的命令。  

###2.使用!string和!？string执行某个带有关键字string的命令  

你可以同时使用关键字从历史记录中挑一条命令执行。  

接下来的一个例子将会在先前的历史记录中搜索以“ps”开头的命令，并且执行它。在这个例子中它挑选出“ps -ef | grep http”这个命令并且执行它。  

	$ !ps
	ps -ef | grep http  

接下来的一个例子将会在先前的历史记录中搜索含有“apache”的命令，并且执行它。在这个例子中它挑选出“/usr/local/apache2/bin/apachectl restart”这个命令并且执行它。  

	$ !?apache
	/usr/local/apache2/bin/apachectl restart

###3.使用^str1^str2^从历史记录中替换字符串  

在下面这个例子中，我们先执行ls命令以确认一个文件。稍后，我们将看一下其内容。我们只用在先前的命令中替换ls而不是敲入整个文件的名字即可，如下：  

	$ ls /etc/cron.daily/logrotate
	
	$ ^ls^cat^
	cat /etc/cron.daily/logrotate  

#Bash历史单词标志  

###4.使用:^获取第一个参数

单词标志在我们想键入一个新命令的时候非常有用，但是是使用我们早些执行命令中的参数。一些例子如下：  

	$ cp /etc/passwd /backup
	
	$ ls -l !cp:^
	ls -l /etc/passwd  

以下命令是从先前的命令中获取第一个参数。  

	$ ls -l !!:^  

###5.使用:$获取最后一个参数  

在接下来的例子中，“!cp:$”是作为“ls -l”的参数的。“!cp:$”定位先前命令中以“cp”开头的命令，然后获取其最后一个参数。  

	$ cp /etc/passwd /backup
	
	$ ls -l !cp:$
	ls -l /backup  

接下来的例子将会从先前命令中获取最后一个参数。  

	$ls -l !!:$  

###6.使用:n获取第n个参数  

在接下来的例子中，“!tar:2”是作为“ls -l”的参数的。“!tar:2”定位先前命令中以“tar”开头的命令，然后获取其第二个参数。 

	$ tar cvfz /backup/home-dir-backup.tar.gz /home

	$ ls -l !tar:2
	ls -l /backup/home-dir-backup.tar.gz  


###7. 使用:*获取全部参数


在接下来的例子中，“!cp:\*”是作为“ls -l”的参数的。“!cp:*”定位先前命令中以“cp”开头的命令，然后获取其所有参数。  

	$ cp /etc/passwd /backup
	
	$ ls -l !cp:*
	ls -l /etc/passwd /backup    

###8.使用!%表示最近搜索的内容  

正如我们上面介绍的一样， “!?apache”将会在先前的历史记录中搜索包含“apache”的命令，并且执行它。  

	$ /usr/local/apache2/bin/apachectl restart
	
	$ !?apache
	/usr/local/apache2/bin/apachectl restart

!%将会表示“?”搜索的全部内容。  

举个例子，如果你已经搜索“?apache”，“!%”将会表示“/usr/local/apache2/bin/apachectl”。注意，“/”将会作为内容的一部分。  

所以，在这种情况下，执行以下命令，你能停止apache服务。  

	$ !% stop
	/usr/local/apache2/bin/apachectl stop  

###9.使用x-y获取参数范围  

在接下来的例子中，“!tar:3-5”是作为“ls -l”的参数的。“!tar:3-5”定位先前命令中以“tar”开头的命令，然后获取其第3~5个参数。   

	$ tar cvf home-dir.tar john jason ramesh rita
	
	$ ls -l !tar:3-5
	ls -l john jason ramesh  

下面的例子是获取第2个到最后一个参数。  

	$ ls -l !tar:2-$  

注意：  

!!:* 获取先前命令中的所有参数  

!!:2* 获取先前命令中的第2个到最后一个参数  

!!:2-$ 同上，获取先前命令中的第2个到最后一个参数  

!!:2- 获取先前命令中的第2个到倒数第二个参数  

#Bash历史修饰符  

修饰符在单词标志后给出，下面做出解释。  

###10.使用:h移除尾部的路径  

在接下来的例子中，“!!:$:h”获取先前命令中最后一个参数。然后移除尾部的路径。在这个例子中，它移除了文件名。  

	$ ls -l /very/long/path/name/file-name.txt
	
	$ ls -l !!:$:h
	ls -l /very/long/path/name  

###11.使用:t移除头部的路径  

这个正好和上面一个例子相反。  

在接下来的例子中，“!!:$:t”获取先前命令中最后一个参数。然后移除所有开头的路径。在这个例子中，它只获取文件名。  
	
	$ ls -l /very/long/path/name/file-name.txt
	
	$ ls -l !!:$:t
	ls -l file-name.txt  

###12.使用:r移除文件拓展名  

在接下来的例子中，“!!:$:r”获取先前命令中最后一个参数。然后移除文件拓展名。在这个例子中，它移除了.txt。  

	$ ls -l /very/long/path/name/file-name.txt
	
	$ ls -l !!:$:r
	ls -l /very/long/path/name/file-name     

###13.使用:s/str1/str2/做类似sed的替换  

在接下来的例子中，我们将使用一种类似sed的替换，而不使用上面讨论过的“^original^replacement^”。这样子可能又助于记忆。!!是调用先前一条命令，“:s/original-string/replacement-string/”是类似sed的替换语法。  

	$ !!:s/ls -l/cat/  

你也可以像下面一样使用g标志（和s标志一起）做全局替换。这个在你已经敲错了几个单词，又想一起改掉所有错误以再一次执行的时候非常有用。  

在接下来的例子中，两次错误的敲错了“password”（应该是passwd）。  

	$ cp /etc/password /backup/password.bak  

要修正这个，只需做一次sed类似的全局替换即可。  
	
	$ !!:gs/password/passwd/
	cp /etc/passwd /backup/passwd.bak  

###14.使用:&快速替换  

如果你已经如上述一样成功地做了一次替换，你可以使用:&重复一次替换。  

假设我已经在另外一条命令中敲错了“password”（应该是“passwd”）。  

	$ tar cvf password.tar /etc/password  

现在，我可以只使用“:&”重复一次替换，而不是重敲一次命令或者做一次“gs/password/passwd”。使用 “:g&”重复一次全局替换。  
	
	$ !!:g&
	tar cvf passwd.tar /etc/passwd  

###15.使用:p打印而不执行命令  

这在你做复杂的历史替换和在执行之前查看一条命令的时候非常有用。  

接下来的例子中，“!tar:3-:p”并不真正的执行。  

由于“:p”，它只是做一次替换和显示一条新命令。一当你已经核对了bash历史拓展，并且你认为这是你像执行的命令，就去掉“:p”，再一次执行。  
	
	$ tar cvf home-dir.tar john jason ramesh rita
	
	$ tar cvfz new-file.tar !tar:3-:p
	tar cvfz new-file.tar john jason ramesh  

#完