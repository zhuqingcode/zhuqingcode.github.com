---
layout: post
title: "挂载(mount)jffs2格式镜像文件到宿主linux"
description: ""
category: linux
tags: [jffs2,mount,linux]
---
{% include JB/setup %}  

jffs2文件系统对于做嵌入式linux开发的人来说肯定再熟悉不过了，而PC（宿主）机上一般不用这个文件系统。现在问题来了，那么如果你拿到一个jffs2的问题系统镜像，如果你对其中的内容很感兴趣，你想查看其内容该怎么办呢？不要告诉我直接*cat*它，这样会被人家鄙视的！下面就来简单介绍如何办！  

>1.首先，想要识别jffs2文件系统，宿主机linux首先要能识别这个文件系统。好了，看一下自己的宿主linux能否识别：  
 
	linux-geek:/home/Hi3520D_SDK_V1.0.2.0/osdrv/busybox # cat /proc/filesystems 
	nodev   sysfs
	nodev   rootfs
	nodev   bdev
	nodev   proc
	nodev   debugfs
	nodev   securityfs
	nodev   sockfs
	nodev   pipefs
	nodev   futexfs
	nodev   tmpfs
	nodev   inotifyfs
	nodev   eventpollfs
	nodev   devpts
	        ext2
	nodev   ramfs
	nodev   hugetlbfs
	        minix
	        iso9660
	nodev   mqueue
	        reiserfs
	nodev   usbfs
	nodev   vmhgfs
	nodev   vmblock
	nodev   rpc_pipefs
	nodev   nfsd
	        vfat

其中，没有看到jffs2的影子！那么，我们就应该去加载相应的模块：  

	linux-geek:/home/Hi3520D_SDK_V1.0.2.0/osdrv/busybox # modprobe jffs2  

再去看一下是否支持了：  

linux-geek:/home/Hi3520D_SDK_V1.0.2.0/osdrv/busybox # !cat 

	cat /proc/filesystems 
	nodev   sysfs
	nodev   rootfs
	nodev   bdev
	nodev   proc
	nodev   debugfs
	nodev   securityfs
	nodev   sockfs
	nodev   pipefs
	nodev   futexfs
	nodev   tmpfs
	nodev   inotifyfs
	nodev   eventpollfs
	nodev   devpts
	        ext2
	nodev   ramfs
	nodev   hugetlbfs
	        minix
	        iso9660
	nodev   mqueue
	        reiserfs
	nodev   usbfs
	nodev   vmhgfs
	nodev   vmblock
	nodev   rpc_pipefs
	nodev   nfsd
	        vfat
	nodev   jffs2  

好了，支持了！  

>2.加载*mtdram*模块、*mtdblock*模块：  

	linux-geek:/home/Hi3520D_SDK_V1.0.2.0/osdrv/busybox # modprobe mtdblock  
	linux-geek:/home/Hi3520D_SDK_V1.0.2.0/osdrv/busybox # modprobe mtdram total_size=12288  

上述的*total_size*后面跟的参数单位是KB，并且其大小要大于jffs2镜像文件的大小，要不然等到拷贝数据的时候会出现大小不够用的情况！  

这个时候再看一下*dev*目录下是否存在了*mtdblock0*：  

	linux-geek:/home/Hi3520D_SDK_V1.0.2.0/osdrv/busybox # ls /dev/mtdblock?
	/dev/mtdblock0
好了，有了！  

>3.拷贝jffs2镜像文件到*/dev/mtdblock0*：  

	linux-geek:/home/Hi3520D_SDK_V1.0.2.0/osdrv/busybox # dd if=jffs2-rootfs.img of=/dev/mtdblock0 
	21343+1 records in
	21343+1 records out
	10927812 bytes (11 MB) copied, 0.41383 seconds, 26.4 MB/s
	linux-geek:/home/Hi3520D_SDK_V1.0.2.0/osdrv/busybox #   

至于命令*dd*这里不作解释，自己google，丰衣足食！  

>4.挂载(mount)*/dev/mtdblock0*：  

	linux-geek:/home/Hi3520D_SDK_V1.0.2.0/osdrv/busybox # mount -t jffs2 /dev/mtdblock0 /mnt  

如果挂载成功，再来看看mnt目录下是不是出现了我们所熟悉的目录结构：  

	linux-geek:/home/Hi3520D_SDK_V1.0.2.0/osdrv/busybox # ls /mnt -l
	total 3
	drwxr-xr-x 2 root root    0 Dec 25 01:52 bin
	drwxr-xr-x 2 root root    0 Dec 25 01:52 dev
	drwxr-xr-x 5 root root    0 Jan  8 15:33 etc
	drwxr-xr-x 3 root root    0 Jan  2 19:34 home
	lrwxrwxrwx 1 root root    9 Jan  2 16:01 init -> sbin/init
	drwxr-xr-x 2 root root    0 Jan  2 15:48 lib
	lrwxrwxrwx 1 root root   11 Dec 25 01:52 linuxrc -> bin/busybox
	drwxr-xr-x 2 root root    0 Jan  2 16:02 lost+found
	-rwxr-xr-x 1 root root 1341 Jan  2 16:02 mkimg.rootfs
	-rwxr-xr-x 1 root root  431 Jan  2 16:02 mknod_console
	drwxr-xr-x 2 root root    0 Dec 25 01:52 mnt
	drwxr-xr-x 2 root root    0 Jan  2 16:03 nfsroot
	drwxr-xr-x 2 root root    0 Jan  2 16:03 opt
	drwxr-xr-x 2 root root    0 Dec 25 01:52 proc
	drwxr-xr-x 2 root root    0 Jan  2 16:08 root
	drwxr-xr-x 2 root root    0 Dec 25 01:52 sbin
	drwxr-xr-x 2 root root    0 Jan  2 16:05 share
	drwxr-xr-x 2 root root    0 Jan  2 16:05 sys
	drwxr-xr-x 2 root root    0 Dec 25 01:52 tmp
	drwxr-xr-x 4 root root    0 Jan  8 15:27 usr
	drwxr-xr-x 3 root root    0 Jan  8 15:28 var  

看到这里，你会恍然大悟了。相信你会举一反三了，其他文件系统(cramfs等等)以此类推！如果没有，请留下评论！  

#完