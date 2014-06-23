---
layout: post
title: "嵌入式Linux下如何利用NFSROOT启动开发板"
description: ""
category: linux
tags: [linux,nfsroot]
---  

做嵌入式开发的朋友肯定听过或者使用过NFSROOT来启动开发板，这样子有几个好处：  

- 避免了重复制作根文件系统(rootfs)镜像以及烧写文件系统
- 节省了开发时间  

利用NFSROOT来启动开发板对开发固然有好处，但是要怎么开启NFSROOT？要具备以下几个条件：  

1. 宿主机(一般虚拟机下Linux系统)要开启NFS server服务  
2. 开发板使用的Linux内核要支持NFSROOT
3. 开发板的网口要可以使用即网络要畅通  

以下就着重介绍上述第二点。  

其实，说起来也很简单，只要在内核配置中选中有关NFSROOT有关的选项，重新编译内核然后把内核镜像烧写到flash中的内核分区或者利用*u-boot tftp*下载到内存中利用*bootm*启动；另外，配置相关的u-boot启动参数就基本上OK了。接下来说说如何在内核中开启NFSROOT的功能：  

	File systems--->Network File Systems--->  
	<*>   NFS client support                                                                                             
	  [*]     NFS client support for NFS version 3                                                                          
	  [*]       NFS client support for the NFSv3 ACL protocol extension                                                                                                         
	  [*]     NFS client support for NFS version 4                                                                                                          
	  [ ]       NFS client support for NFSv4.1 (EXPERIMENTAL)                                                                                              
    [*]   Root file system on NFS  

这样子基本上可以了。接下来就是重新编译内核以及烧写的过程，这里不再赘述。  

然后，就是在*u-boot*中修改相关的启动参数，一个标准的参数如下：

setenv bootargs 'mem=64M console=ttyAMA0,115200 root=/dev/nfs rw nfsroot=192.168.4.165:/home/NFSROOT/hi3520d-rootfs-s-b ip=192.168.4.99:192.168.4.1:255.255.255.0 mtdparts=hi_sfc:256K(boot),256K(env),1792K(kernel),-(rootfs)'  

特别注意这么几个参数：  

- root=/dev/nfs rw 
- nfsroot=192.168.4.165:/home/NFSROOT/hi3520d-rootfs-s-b ip=192.168.4.99:192.168.4.1:255.255.255.0  

*root*后一定要写成*/dev/nfs*，至于*rw*就随你便，不过最好是*rw*；*nfsroot*后加NFS服务器(这里是虚拟机下的Linux)的*ip*地址+roorfs目录；*ip*后是开发板将要使用的地址，如果你选用静态ip的话，你需要这么来写。

其余参数的具体含义就不赘述了。修改好，保存一下。最后就是启动的过程了，如果不出意外，应该可以顺利的启动起来。
  
