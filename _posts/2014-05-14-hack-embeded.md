---
layout: post
title: "如何hack人家的嵌入式产品"
description: ""
category: linux
tags: [linux]
---
从我毕业到现在也有四年多的时间了，期间一直从事于嵌入式Linux的开发工作。工作中经常要接触到人家同类型的产品（较为成熟），这类产品都是采取相同的方案甚至相同的芯片来开发的，所以弄懂人家的产品对我们自己开发也有一定的好处。下面就来简单介绍我工作中常用hack人家嵌入式产品的方法。

* 首先，得在硬件上找到人家产品的串口输出（控制台串口）

这个对于嵌入式开发来说太重要了。几乎所有的一切都离不开控制台串口；或者能破译其telnet服务的密码也基本上可以。如果能顺利找到串口，接上USB转串口线，上电后，在```secureCRT```或者其他什么串口工具上能看到串口log，一些```u-boot```，```kernel```启动过程中打印出来的log，你就离成功迈进了一大步了。这期间可能遇到这么一种情况：串口是可以输出的，但是只有些```u-boot```的启动log和```kernel```解压前部分的log，启动后的log完全看不到。在这种情况下，你可能要重新烧写一个```kernel```进内存，然后利用```u-boot```命令```bootm```从内存直接启动来查看完整的启动信息。至于怎么重新烧写```kernel```进内存，你可以采用以下几种方式：  

1. ```tftp```
2. ```kermit```
3. ```U盘（u-boot usb 命令集）```

等等。

这里假设你已经进入了```Shell```里面了，可以正常敲击命令了。  

* 看一下Flash的分区情况，有这么几种方法：  

  ```cat /proc/mtd```，输出类似如下：  

> /mnt/3520d $ cat /proc/mtd  
> dev:    size   erasesize  name  
> mtd0: 00040000 00040000 "boot"  
> mtd1: 00040000 00040000 "env"  
> mtd2: 001c0000 00040000 "kernel"  
> mtd3: 00dc0000 00040000 "rootfs"  

   ```ls /dev/mtdblock?```，输出类似如下：  

> /dev/mtdblock3  /dev/mtdblock2  /dev/mtdblock1  /dev/mtdblock0  

这里可以清楚的看到有4个分区，也许还有会有更复杂的情况。我们一般对```rootfs```也就是```根文件系统```分区比较感兴趣。  

* 利用命令```dd```拷贝人家产品的文件系统     

> dd if=/dev/mtdblock3 of=mount-point  

其中```mount-point```，为挂载目录，不要```dd```到人家的```flash```上，这一点切记。

* 根据文件系统的格式（```jffs2，cramfs，squashfs```等等）参考我前面一篇文章[http://zhuqingcode.github.io/linux/2014/01/09/mount-jffs2-pc.html](http://zhuqingcode.github.io/linux/2014/01/09/mount-jffs2-pc.html)就能一探其究竟了。  

当然，这里只是介绍了最最简单的方式，实际操作过程中获取还有更多要考虑的情况，例如没有```dd```命令该怎么办等等，这个需要你自己捣腾。



