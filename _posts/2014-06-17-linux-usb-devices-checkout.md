---
layout: post
title: "Linux如何查看USB设备的VID(Vendor ID)、PID(Product ID)"
description: ""
category: linux
tags: [linux,usb]
---

在Linux下经常会用到USB设备，例如U盘、USB WIFI模块、3G模块（通过miniPCI-E转接板）。用到这种需要接到系统USB接口上的设备，通常你都会想“插上去能不能被Linux系统识别？”。其实，查看其能否被识别其实很简单，只需一条shell命令即可：  

	cat /proc/bus/usb/devices  

我们来看一下：  

	T:  Bus=02 Lev=00 Prnt=00 Port=00 Cnt=00 Dev#=  1 Spd=12  MxCh= 2
	B:  Alloc=  0/900 us ( 0%), #Int=  0, #Iso=  0
	D:  Ver= 1.10 Cls=09(hub  ) Sub=00 Prot=00 MxPS=64 #Cfgs=  1
	P:  Vendor=0000 ProdID=0000 Rev= 2.06
	S:  Manufacturer=Linux 2.6.16.21-0.25-default uhci_hcd
	S:  Product=UHCI Host Controller
	S:  SerialNumber=0000:00:07.2
	C:* #Ifs= 1 Cfg#= 1 Atr=c0 MxPwr=  0mA
	I:  If#= 0 Alt= 0 #EPs= 1 Cls=09(hub  ) Sub=00 Prot=00 Driver=hub
	E:  Ad=81(I) Atr=03(Int.) MxPS=   2 Ivl=255ms
	
	T:  Bus=01 Lev=00 Prnt=00 Port=00 Cnt=00 Dev#=  1 Spd=480 MxCh= 6
	B:  Alloc=  0/800 us ( 0%), #Int=  0, #Iso=  0
	D:  Ver= 2.00 Cls=09(hub  ) Sub=00 Prot=01 MxPS=64 #Cfgs=  1
	P:  Vendor=0000 ProdID=0000 Rev= 2.06
	S:  Manufacturer=Linux 2.6.16.21-0.25-default ehci_hcd
	S:  Product=EHCI Host Controller
	S:  SerialNumber=0000:02:02.0
	C:* #Ifs= 1 Cfg#= 1 Atr=c0 MxPwr=  0mA
	I:  If#= 0 Alt= 0 #EPs= 1 Cls=09(hub  ) Sub=00 Prot=00 Driver=hub
	E:  Ad=81(I) Atr=03(Int.) MxPS=   2 Ivl=256ms  

这里很明显可以清楚的看到：  

- Vendor=？ ProdID=？ 
 
的字样。这就是制造商的VID(Vendor ID)、PID(Product ID)。  

但是，如果想查看  

- /proc/bus/usb/devices  

这个文件有一个前提：你必须事先挂在好了usbfs(文件系统)。要挂在其实也很简单，一条命令即可：  

- mount -t usbfs none /proc/bus/usb  


