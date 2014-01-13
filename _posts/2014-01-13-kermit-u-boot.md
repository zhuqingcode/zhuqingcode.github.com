---
layout: post
title: "linux下利用kermit烧写u-boot"
description: ""
category: linux
tags: [linux,kermit,u-boot]
---
{% include JB/setup %}  

做嵌入式软件的猿人肯定听说过u-boot，关于其定义及功能这里不作解释，详见维基百科[*u-boot*][1]以及自己google。  

关于*u-boot*如何烧写的问题，这里指的不是裸板烧写，而是在先进入*u-boot*环境的情况下烧写新的*u-boot*，用于测试或者其他目的。这里我常用烧到内存，然后利用命令*go*运行，这样子可以快速测试！在网络畅通的情况下一般用tftp协议烧写，这种方法简单，来的快！但是在实际情况应用中如果没有网络只有串口那该怎么办呢？能不能烧呢？答案是肯定的！下面就来做简单介绍！  

首先，我们进入*u-boot*环境：  

	U-Boot 2010.06-svn123 (Sep 16 2013 - 15:21:32)
	
	DRAM:  256 MiB
	Check spi flash controller v350... Found
	Spi(cs1) ID: 0xC2 0x20 0x18 0xC2 0x20 0x18
	Spi(cs1): Block:64KB Chip:16MB Name:"MX25L128XX"
	envcrc 0x8e7cb057
	ENV_SIZE = 0x3fffc
	In:    serial
	Out:   serial
	Err:   serial
	Hisilicon ETH net controler
	Press CTRL-C to abort autoboot in 0 secondshisilicon # 
	hisilicon # 
	hisilicon #   

这里有个小插曲，为了节省开发成本，我司买了个人家的成品来做开发板(海思3520D)要我拿来当作开发板用。没有网口，只有串口！这样子，我只能通过串口来烧写和测试*u-boot*了！  

这里，主要用到*loadb*这个命令，在u-boot下help看一下：  

	hisilicon # help loadb
	loadb - load binary file over serial line (kermit mode)  

由于要用到*kermit*，所以索性在linux使用USB转串口来进行测试工作。关于如何在linux下使用USB转串口，这里不作解释。直接来看一下是否识别了USB转串口：  

	linux-geek:~ # ls /dev/ttyUSB?
	/dev/ttyUSB0  

好了，有了，那么就开始吧！  

>1.启动*kermit*，如果你的linux上没有kermit这个软件，请自行安装。  

	linux-geek:~ # kermit
	?No keywords match - hardshake
	Command stack:
	  1. File  : /root/.kermrc (line 4)
	  0. Prompt: (top level)
	C-Kermit 8.0.211, 10 Apr 2004, for Linux
	 Copyright (C) 1985, 2004,
	  Trustees of Columbia University in the City of New York.
	Type ? or HELP for help.
	(/root/) C-Kermit>  

看到了吧？注意这一行：  
	
	  1. File  : /root/.kermrc (line 4)  

这表示kermit首先要读取此目录下的配置文件，配置USB转串口的参数：波特率等等，所以在启动之前，要先创建一个*.kermit*文件，这里看一下我的*.kermit*文件：  

	linux-geek:~ # cat ./.kermrc 
	set line /dev/ttyUSB0                                                                                                                
	set speed 115200
	set carrier-watch off 
	set hardshake none
	set flow-control none
	robust
	set file type bin 
	set file name lit 
	set rec pack 1000
	set send pack 1000
	set window 5

具体的不解释。  

>2.启动*kermit*后，键入『c』进行连接串口：  

	(/root/) C-Kermit>c
	Connecting to /dev/ttyUSB0, speed 115200
	 Escape character: Ctrl-\ (ASCII 28, FS): enabled
	Type the escape character followed by C to get back,
	or followed by ? to see other options.
	----------------------------------------------------  

这样子就连接了USB转串口。好了我们重启设备进入*u-boot*环境。  

>3.烧写*u-boot*，键入*loadb 81000000*，回车：  


	U-Boot 2010.06-svn123 (Sep 16 2013 - 15:21:32)
	
	DRAM:  256 MiB
	Check spi flash controller v350... Found
	Spi(cs1) ID: 0xC2 0x20 0x18 0xC2 0x20 0x18
	Spi(cs1): Block:64KB Chip:16MB Name:"MX25L128XX"
	envcrc 0x8e7cb057
	ENV_SIZE = 0x3fffc
	In:    serial
	Out:   serial
	Err:   serial
	Hisilicon ETH net controler
	Press CTRL-C to abort autoboot in 0 secondshisilicon # 
	hisilicon # 
	hisilicon # 
	hisilicon # loadb 81000000  
	## Ready for binary (kermit) download to 0x81000000 at 115200 bps...
后面的*81000000*是内存地址，请根据实际情况自行修改。这表示把*u-boot*下载到这个地址。  

此时，我们再返回到*kermit*环境下，键入『Ctrl+\』，『c』：  

	(Back at linux-geek.site)
	----------------------------------------------------
	(/root/) C-Kermit>

这就回来了。此时，我们键入：send path/to/u-boot's name  

	(/root/) C-Kermit>send /tftpboot/u-boot-256k.bin   

这里表示通过串口发送/tftpboot/u-boot-256k.bin这个文件。  

然后，回车，你就见到如下界面：  

	C-Kermit 8.0.211, 10 Apr 2004, linux-geek.site [192.168.4.165]
	
	   Current Directory: /root
	Communication Device: /dev/ttyUSB0
	 Communication Speed: 115200
	              Parity: none
	         RTT/Timeout: 01 / 02
	             SENDING: /tftpboot/u-boot-256k.bin => u-boot-256k.bin
	           File Type: BINARY
	           File Size: 226572
	        Percent Done: 3   /-
	                          ...10...20...30...40...50...60...70...80...90..100
	 Estimated Time Left: 00:00:36
	  Transfer Rate, CPS: 6053
	        Window Slots: 1 of 1
	         Packet Type: D
	        Packet Count: 17
	       Packet Length: 999
	         Error Count: 0
	          Last Error:
	        Last Message:
	
	X to cancel file, Z to cancel group, <CR> to resend last packet,
	E to send Error packet, ^C to quit immediately, ^L to refresh screen.  

这表示正在发送/tftpboot/u-boot-256k.bin这个文件。完成之后，就自动回到*kermit*命令下：  

	(/root/) C-Kermit>send /tftpboot/u-boot-256k.bin 
	(/root/) C-Kermit>  

此时，键入『c』，重新连接USB转串口：  

	(/root/) C-Kermit>c
	Connecting to /dev/ttyUSB0, speed 115200
	 Escape character: Ctrl-\ (ASCII 28, FS): enabled
	Type the escape character followed by C to get back,
	or followed by ? to see other options.
	----------------------------------------------------
	## Total Size      = 0x0003750c = 226572 Bytes
	## Start Addr      = 0x81000000  

这就回到了之前的*u-boot*环境下，键入『go 81000000』，回车：  

	hisilicon # go 81000000
	## Starting application at 0x81000000 ...
	
	
	U-Boot 2010.06 (Dec 30 2013 - 15:15:59)
	
	Check spi flash controller v350... Found
	Spi(cs1) ID: 0xC2 0x20 0x18 0xC2 0x20 0x18
	Spi(cs1): Block:64KB Chip:16MB Name:"MX25L128XX"
	*** Warning - bad CRC, using default environment
	
	In:    serial
	Out:   serial
	Err:   serial
	hisilicon #   

这就重新运行了刚才我们烧写的那个*u-boot*文件。  

回顾一下整个过程，看起来有点乱，等到实际操作的时候还是挺简单的。只不过把一个实际操作过程用文字来描述的时候就显得乱了，看来要恶补语文了！  

#完
[1]:http://en.wikipedia.org/wiki/Das_U-Boot
