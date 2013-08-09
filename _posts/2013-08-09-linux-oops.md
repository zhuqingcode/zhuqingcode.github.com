---
layout: post
title: "根据linux Oops定位错误代码行"
description: ""
category: linux
tags: [linux]
---
{% include JB/setup %}

这几天一直在调试atmel at91sam9x25的串口，用着用着总会导致Oops，Oops内容如下：  

	[ 1023.510000] Unable to handle kernel NULL pointer dereference at virtual address 00000000
	[ 1023.520000] pgd = c0004000
	[ 1023.520000] [00000000] *pgd=00000000
	[ 1023.520000] Internal error: Oops: 17 [#1]
	[ 1023.520000] last sysfs file: /sys/devices/virtual/misc/at91flash/dev
	[ 1023.520000] Modules linked in: at91flash at91gpio at91mc323 ds18b20 at91adc
	[ 1023.520000] CPU: 0    Tainted: G        W    (2.6.39 #35)
	[ 1023.520000] PC is at atmel_tasklet_func+0x104/0x690
	[ 1023.520000] LR is at atmel_tasklet_func+0x10/0x690
	[ 1023.520000] pc : [<c01a33ac>]    lr : [<c01a32b8>]    psr: 20000013
	[ 1023.520000] sp : c7825f58  ip : 60000093  fp : 00000000
	[ 1023.520000] r10: 00000006  r9 : 00000000  r8 : 0000000a
	[ 1023.520000] r7 : 00000000  r6 : c7824000  r5 : c78a2484  r4 : c03c0cb8
	[ 1023.520000] r3 : 0000004c  r2 : 0000004c  r1 : 60000013  r0 : 00000001
	[ 1023.520000] Flags: nzCv  IRQs on  FIQs on  Mode SVC_32  ISA ARM  Segment kernel
	[ 1023.520000] Control: 0005317f  Table: 27b40000  DAC: 00000017
	[ 1023.520000] Process ksoftirqd/0 (pid: 3, stack limit = 0xc7824270)
	[ 1023.520000] Stack: (0xc7825f58 to 0xc7826000)
	[ 1023.520000] 5f40: 	                                                      00000001 c7824000
	[ 1023.520000] 5f60: 00000100 0000000a 00000000 00000006 c7825f8c 00000000 00000001 c7824000
	[ 1023.520000] 5f80: 00000100 0000000a 00000006 c0045cf8 c03b995c c00461d8 c7aa6ae0 00000000
	[ 1023.520000] 5fa0: 60000093 00000000 c7824000 c0046274 00000013 00000000 00000000 c00462e0
	[ 1023.520000] 5fc0: 00000000 c7819f70 00000000 c00570e0 00000000 00000000 00000000 00000000
	[ 1023.520000] 5fe0: c7825fe0 c7825fe0 c7819f70 c0057060 c0030b14 c0030b14 ffffffff ffffffff
	[ 1023.520000] [<c01a33ac>] (atmel_tasklet_func+0x104/0x690) from [<c0045cf8>] (tasklet_action+0x84/0xe8)
	[ 1023.520000] [<c0045cf8>] (tasklet_action+0x84/0xe8) from [<c00461d8>] (__do_softirq+0x88/0x124)
	[ 1023.520000] [<c00461d8>] (__do_softirq+0x88/0x124) from [<c00462e0>] (run_ksoftirqd+0x6c/0x128)
	[ 1023.520000] [<c00462e0>] (run_ksoftirqd+0x6c/0x128) from [<c00570e0>] (kthread+0x80/0x88)
	[ 1023.520000] [<c00570e0>] (kthread+0x80/0x88) from [<c0030b14>] (kernel_thread_exit+0x0/0x8)
	[ 1023.520000] Code: 1a000002 e59f057c e59f157c ebfa3d49 (e5973000) 
	[ 1023.710000] ---[ end trace 786b41cd25d3b661 ]---
	[ 1023.710000] Kernel panic - not syncing: Fatal exception in interrupt
	[ 1023.720000] [<c0034b10>] (unwind_backtrace+0x0/0xe0) from [<c02a8af8>] (panic+0x50/0x170)
	[ 1023.720000] [<c02a8af8>] (panic+0x50/0x170) from [<c0032e00>] (die+0x184/0x1c4)
	[ 1023.730000] [<c0032e00>] (die+0x184/0x1c4) from [<c0035aa8>] (__do_kernel_fault+0x64/0x84)
	[ 1023.740000] [<c0035aa8>] (__do_kernel_fault+0x64/0x84) from [<c0035c7c>] (do_page_fault+0x1b4/0x1c8)
	[ 1023.750000] [<c0035c7c>] (do_page_fault+0x1b4/0x1c8) from [<c002a240>] (do_DataAbort+0x30/0x98)
	[ 1023.760000] [<c002a240>] (do_DataAbort+0x30/0x98) from [<c002f86c>] (__dabt_svc+0x4c/0x60)
	[ 1023.770000] Exception stack(0xc7825f10 to 0xc7825f58)
	[ 1023.770000] 5f00:                                     00000001 60000013 0000004c 0000004c
	[ 1023.780000] 5f20: c03c0cb8 c78a2484 c7824000 00000000 0000000a 00000000 00000006 00000000
	[ 1023.790000] 5f40: 60000093 c7825f58 c01a32b8 c01a33ac 20000013 ffffffff
	[ 1023.790000] [<c002f86c>] (__dabt_svc+0x4c/0x60) from [<c01a33ac>] (atmel_tasklet_func+0x104/0x690)
	[ 1023.800000] [<c01a33ac>] (atmel_tasklet_func+0x104/0x690) from [<c0045cf8>] (tasklet_action+0x84/0xe8)
	[ 1023.810000] [<c0045cf8>] (tasklet_action+0x84/0xe8) from [<c00461d8>] (__do_softirq+0x88/0x124)
	[ 1023.820000] [<c00461d8>] (__do_softirq+0x88/0x124) from [<c00462e0>] (run_ksoftirqd+0x6c/0x128)
	[ 1023.830000] [<c00462e0>] (run_ksoftirqd+0x6c/0x128) from [<c00570e0>] (kthread+0x80/0x88)
	[ 1023.840000] [<c00570e0>] (kthread+0x80/0x88) from [<c0030b14>] (kernel_thread_exit+0x0/0x8)  

注意上述的这个地方：  

	[ 1023.520000] PC is at atmel_tasklet_func+0x104/0x690
	[ 1023.520000] LR is at atmel_tasklet_func+0x10/0x690  

下面就来显示如何定位出出错代码行:  

* 首先，编译时打开complie with debug info选项，步则如下  
* make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- menuconfig  

![linux-Oops0](/images/linux-Oops0.jpg)  

进入 Kernel hacking:  

![linux-Oops1](/images/linux-Oops1.jpg)  

选择  

	Compile the kernel with debug info  

然后，保存，退出。接着 make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi-
编译， 等编译完成。  

* 利用arm-none-linux-gnueabi-gdb 调试，如下：  

	arm-none-linux-gnueabi-gdb vmlinux  

![linux-Oops2](/images/linux-Oops2.jpg) 

对应着Oops 消息里面的这一行

	[ 1023.520000] LR is at atmel_tasklet_func+0x10/0x690  

在gdb下键入命令 ： l *atmel_tasklet_func+0x10（注意：这里的‘l’是字母“L”，由于字体的原因看起来像‘1’）  

![linux-Oops3](/images/linux-Oops3.jpg)
 
这样就找到了出错的代码行。在这里鄙视一下atmel提供的内核，竟然还有bug！
从这里可以看出是由于串口的dma导致Oops的，于是我去掉了串口的dma传输。方法如下：  

![linux-Oops4](/images/linux-Oops4.jpg)  

去掉之后还没有发现上述的Oops出现。  

# 完  

