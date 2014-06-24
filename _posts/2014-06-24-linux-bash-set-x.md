---
layout: post
title: "如何将Bash脚本中的执行语句打印出来以起到调试作用"
description: ""
category: shell 
tags: [linux,bash]
---

我们在调试Linux驱动的时候经常会用到*printk*函数打印一些消息到控制台，这样能帮我们判断程序是怎么执行下来的，以起到调试的作用。但是如何在*Bash*脚本中如何打印出执行的语句呢？下面举个例子做简单的介绍。  
	
	linux-geek:~/3520d/osdrv/busybox # cat make-rootfs.sh 
	#!/bin/bash
	if [ $# != 1 ];then
	        echo "Please input flash block size:0x10000 or 0x40000"
	        exit
	fi
	cd ./rootfs_dev/home && \
	rm -rf mpp.tar.lzma && 
	tar -cv mpp | lzma -z > mpp.tar.lzma && \
	mv mpp ../../backup && \
	cd - && \
	mkfs.jffs2 -d ./rootfs_dev -l -e $1 -o jffs2-rootfs.img && \
	mkfs.cramfs ./rootfs_dev cramfs-rootfs.img && \
	cp jffs2-rootfs.img /tftpboot && \
	cp cramfs-rootfs.img /tftpboot && \
	mv ./backup/mpp ./rootfs_dev/home  

假设，我们手上有上面一个简单的脚本程序，它的作用是做*jffs2*、*cramfs*的文件系统镜像。我们去执行一下试试看：  

	linux-geek:~/3520d/osdrv/busybox # ./make-rootfs.sh 0x40000
	mpp/
	mpp/ko/
	mpp/ko/hi3520D_vda.ko
	mpp/ko/hi3520D_viu.ko
	mpp/ko/hi3520D_vou.ko
	mpp/ko/hi3520D_adec.ko
	mpp/ko/hi3520D_aenc.ko
	mpp/ko/jpeg.ko
	mpp/ko/hi3520D_vdec.ko
	mpp/ko/hi3520D_venc.ko
	mpp/ko/crgctrl_8D1_hi3520D.sh
	mpp/ko/hi3520D_vfmw.ko
	mpp/ko/crgctrl_2X720P_hi3520D.sh
	mpp/ko/crgctrl_1X1080P_hi3520D.sh
	mpp/ko/hi3520D_base.ko
	mpp/ko/hi3520D_group.ko
	mpp/ko/hi3520D_vpss.ko
	mpp/ko/pinmux_8D1_hi3520D.sh
	mpp/ko/load3520D
	mpp/ko/sysctl_hi3520D.sh
	mpp/ko/hi3520D_chnl.ko
	mpp/ko/hi3520D_ai.ko
	mpp/ko/hi3520D_ao.ko
	mpp/ko/hi3520D_rc.ko
	mpp/ko/extdrv/
	mpp/ko/extdrv/hi_ext_wdt.ko
	mpp/ko/extdrv/gpioi2c.ko
	mpp/ko/extdrv/hi_ir.ko
	mpp/ko/extdrv/tw2968.ko
	mpp/ko/extdrv/gpio.ko
	mpp/ko/extdrv/rtc-isl1208.ko
	mpp/ko/extdrv/wdt.ko
	mpp/ko/extdrv/wifi-skw17/
	mpp/ko/extdrv/wifi-skw17/ath9k_common.ko
	mpp/ko/extdrv/wifi-skw17/ath9k_hw.ko
	mpp/ko/extdrv/wifi-skw17/ath9k_htc.ko
	mpp/ko/extdrv/wifi-skw17/compat.ko
	mpp/ko/extdrv/wifi-skw17/wifi-insmod.sh
	mpp/ko/extdrv/wifi-skw17/ath.ko
	mpp/ko/extdrv/wifi-skw17/mac80211.ko
	mpp/ko/extdrv/wifi-skw17/cfg80211.ko
	mpp/ko/extdrv/wifi-skw17/ath9k.ko
	mpp/ko/hi3520D_jpege.ko
	mpp/ko/crgctrl_1XHD_4XD1_hi3520D.sh
	mpp/ko/load3520D_socket
	mpp/ko/mmz.ko
	mpp/ko/hi3520D_h264e.ko
	mpp/ko/vcmp.ko
	mpp/ko/hiuser.ko
	mpp/ko/hifb.ko
	mpp/ko/hi3520D_dsu.ko
	mpp/ko/hi3520D_ive.ko
	mpp/ko/hi3520D_sio.ko
	mpp/ko/hi3520D_tde.ko
	mpp/ko/hi3520D_region.ko
	mpp/ko/hi3520D_sys.ko
	/root/3520d/osdrv/busybox  

上述的打印主要是命令*tar*的打印。我们在脚本中加入  

- set -x 

试试看：  

	#!/bin/bash
	set -x
	if [ $# != 1 ];then
	        echo "Please input flash block size:0x10000 or 0x40000"
	        exit
	fi
	cd ./rootfs_dev/home && \
	rm -rf mpp.tar.lzma && 
	tar -cv mpp | lzma -z > mpp.tar.lzma && \
	mv mpp ../../backup && \
	cd - && \
	mkfs.jffs2 -d ./rootfs_dev -l -e $1 -o jffs2-rootfs.img && \
	mkfs.cramfs ./rootfs_dev cramfs-rootfs.img && \
	cp jffs2-rootfs.img /tftpboot && \
	cp cramfs-rootfs.img /tftpboot && \
	mv ./backup/mpp ./rootfs_dev/home  

再去执行一下看看：  

	+ '[' 1 '!=' 1 ']'
	+ cd ./rootfs_dev/home
	+ rm -rf mpp.tar.lzma
	+ tar -cv mpp
	mpp/
	mpp/ko/
	mpp/ko/hi3520D_vda.ko
	+ lzma -z
	mpp/ko/hi3520D_viu.ko
	mpp/ko/hi3520D_vou.ko
	mpp/ko/hi3520D_adec.ko
	mpp/ko/hi3520D_aenc.ko
	mpp/ko/jpeg.ko
	mpp/ko/hi3520D_vdec.ko
	mpp/ko/hi3520D_venc.ko
	mpp/ko/crgctrl_8D1_hi3520D.sh
	mpp/ko/hi3520D_vfmw.ko
	mpp/ko/crgctrl_2X720P_hi3520D.sh
	mpp/ko/crgctrl_1X1080P_hi3520D.sh
	mpp/ko/hi3520D_base.ko
	mpp/ko/hi3520D_group.ko
	mpp/ko/hi3520D_vpss.ko
	mpp/ko/pinmux_8D1_hi3520D.sh
	mpp/ko/load3520D
	mpp/ko/sysctl_hi3520D.sh
	mpp/ko/hi3520D_chnl.ko
	mpp/ko/hi3520D_ai.ko
	mpp/ko/hi3520D_ao.ko
	mpp/ko/hi3520D_rc.ko
	mpp/ko/extdrv/
	mpp/ko/extdrv/hi_ext_wdt.ko
	mpp/ko/extdrv/gpioi2c.ko
	mpp/ko/extdrv/hi_ir.ko
	mpp/ko/extdrv/tw2968.ko
	mpp/ko/extdrv/gpio.ko
	mpp/ko/extdrv/rtc-isl1208.ko
	mpp/ko/extdrv/wdt.ko
	mpp/ko/extdrv/wifi-skw17/
	mpp/ko/extdrv/wifi-skw17/ath9k_common.ko
	mpp/ko/extdrv/wifi-skw17/ath9k_hw.ko
	mpp/ko/extdrv/wifi-skw17/ath9k_htc.ko
	mpp/ko/extdrv/wifi-skw17/compat.ko
	mpp/ko/extdrv/wifi-skw17/wifi-insmod.sh
	mpp/ko/extdrv/wifi-skw17/ath.ko
	mpp/ko/extdrv/wifi-skw17/mac80211.ko
	mpp/ko/extdrv/wifi-skw17/cfg80211.ko
	mpp/ko/extdrv/wifi-skw17/ath9k.ko
	mpp/ko/hi3520D_jpege.ko
	mpp/ko/crgctrl_1XHD_4XD1_hi3520D.sh
	mpp/ko/load3520D_socket
	mpp/ko/mmz.ko
	mpp/ko/hi3520D_h264e.ko
	mpp/ko/vcmp.ko
	mpp/ko/hiuser.ko
	mpp/ko/hifb.ko
	mpp/ko/hi3520D_dsu.ko
	mpp/ko/hi3520D_ive.ko
	mpp/ko/hi3520D_sio.ko
	mpp/ko/hi3520D_tde.ko
	mpp/ko/hi3520D_region.ko
	mpp/ko/hi3520D_sys.ko
	+ mv mpp ../../backup
	+ cd -
	/root/3520d/osdrv/busybox
	+ mkfs.jffs2 -d ./rootfs_dev -l -e 0x40000 -o jffs2-rootfs.img
	+ mkfs.cramfs ./rootfs_dev cramfs-rootfs.img
	+ cp jffs2-rootfs.img /tftpboot
	+ cp cramfs-rootfs.img /tftpboot
	+ mv ./backup/mpp ./rootfs_dev/home  

仔细看，你会发现其中好多语句前面多了个*+*号，这正是脚本中的执行语句，说到这里你肯定就明白了只要在脚本的开头处加上个  

### set -x  

就可以打印出脚本里的执行语句即调试脚本了。  

