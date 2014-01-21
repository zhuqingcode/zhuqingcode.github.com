---
layout: post
title: "粗略估计linux系统时钟的滴答数(HZ)"
description: ""
category: linux
tags: [linux,timer,HZ]
---
{% include JB/setup %}

使用过linux内核定时器的同学肯定听过*系统时钟*，*滴答数*，*HZ*等一些专业术语，如果还不熟悉，自己最好先大致了解一下，以备不时之用。好了，废话少说，切入主题！  

那么，怎么来简单而又快速的查看系统的*滴答数(HZ)*呢？很简单，只用一条shell命令即可，当然这里只是粗略估计。好了，我们来看一下：  

	/root $ cat /proc/interrupts;sleep 1;cat /proc/interrupts
	           CPU0       
	 35:      85054       GIC  System Timer Tick, Free Timer
	 40:        837       GIC  uart-pl011
	 47:          0       GIC  AIO Interrupt
	 52:          0       GIC  ahci
	 53:          0       GIC  ehci_hcd:usb1
	 54:          1       GIC  ohci_hcd:usb2
	 56:          0       GIC  hieth
	 61:          0       GIC  L2 chk0
	 62:          0       GIC  L2 chk1
	 63:          0       GIC  L2 com
	 65:          0       GIC  VOIE
	 70:          0       GIC  tde_osr_isr
	 71:          0       GIC  vcmp_isr
	 74:          0       GIC  x5_jpeg
	Err:          0
	           CPU0       
	 35:      85156       GIC  System Timer Tick, Free Timer
	 40:        909       GIC  uart-pl011
	 47:          0       GIC  AIO Interrupt
	 52:          0       GIC  ahci
	 53:          0       GIC  ehci_hcd:usb1
	 54:          1       GIC  ohci_hcd:usb2
	 56:          0       GIC  hieth
	 61:          0       GIC  L2 chk0
	 62:          0       GIC  L2 chk1
	 63:          0       GIC  L2 com
	 65:          0       GIC  VOIE
	 70:          0       GIC  tde_osr_isr
	 71:          0       GIC  vcmp_isr
	 74:          0       GIC  x5_jpeg
	Err:          0
	/root $   

就这条命令就行了。当然，你的linux内核配置中得选上*proc*伪文件系统！接下来就做个小学减法即可：  

>85156-85054 = 102 约等于100  

*滴答数(HZ)*一般是100，250，1000等这些数，显然这里取100。  

#完