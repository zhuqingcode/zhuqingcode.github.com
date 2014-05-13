---
layout: post
title: "./xxx: not found"
description: ""
category: linux
tags: [Makefile,static]
---

关于**./xxx: not found**，但是**xxx**确实是存在当前目录下的。不知道你是否遇到过，反正我是遇到过了，下面就举个实例说明一下。  

先来说一下现象：  

	/mnt/3520d $ ls -l | grep GPS_demo
	-rwxr-xr-x    1     17260 GPS_demo  

当前目录下是存在GPS_demo这个可执行文件的，且具备可执行性。但是，我尝试着运行它的时候却发生这样子的情况：  

	/mnt/3520d $ ./GPS_demo 
	-sh: ./GPS_demo: not found  

这是为什么呢？这个**GPS_demo**其中用到了很多标准的libc里面的库函数，但是我们来看一下系统中是否存在所依赖的库呢？  

	/mnt/3520d $ ls /lib/ -l
	/mnt/3520d $   

系统中没有其所依赖的库函数导致了这种情况，而我们**GPS_demo**是动态编译的。来看一下Makefile：  

	linux-geek:~/3520d/mpp/extdrv/GPS # cat Makefile 
	MOUNT_DIR := /home/Mount/3520d
	CC := arm-hisiv100nptl-linux-gcc
	AR := arm-hisiv100nptl-linux-ar
	STRIP := arm-hisiv100nptl-linux-strip
	CFLAGS :=  -o
	TARGET := GPS_demo
	COPYTOTEST := cp $(TARGET) $(MOUNT_DIR)
	SRC := GPS_demo.c GPS.c
	default:
	        $(CC) $(CFLAGS) $(TARGET) $(SRC) -lm
	        $(STRIP) $(TARGET)
	        $(COPYTOTEST)
	clean:
	        rm -rf $(TARGET) *.o *.a
	all:
	        $(CC) GPS.c -c -lm
	        $(AR) -r libGPS.a GPS.o
	        $(CC) GPS_demo.c $(CFLAGS) $(TARGET) -L. -lGPS -lm
	        $(STRIP) $(TARGET)
	        $(COPYTOTEST)  

那怎么解决这种情况呢？两种方法：  

1.静态编译**GPS_demo**，Makefile如下：  

	linux-geek:~/3520d/mpp/extdrv/GPS # cat Makefile 
	MOUNT_DIR := /home/Mount/3520d
	CC := arm-hisiv100nptl-linux-gcc
	AR := arm-hisiv100nptl-linux-ar
	STRIP := arm-hisiv100nptl-linux-strip
	CFLAGS := -static -o
	TARGET := GPS_demo
	COPYTOTEST := cp $(TARGET) $(MOUNT_DIR)
	SRC := GPS_demo.c GPS.c
	default:
	        $(CC) $(CFLAGS) $(TARGET) $(SRC) -lm
	        $(STRIP) $(TARGET)
	        $(COPYTOTEST)
	clean:
	        rm -rf $(TARGET) *.o *.a
	all:
	        $(CC) GPS.c -c -lm
	        $(AR) -r libGPS.a GPS.o
	        $(CC) GPS_demo.c $(CFLAGS) $(TARGET) -L. -lGPS -lm
	        $(STRIP) $(TARGET)
	        $(COPYTOTEST)
	linux-geek:~/3520d/mpp/extdrv/GPS #   

只需在CFLAGS里添加一个**-static**，即可。  

2.把**GPS_demo**所需要的库拷贝到**/lib**目录下即可。  

#完
