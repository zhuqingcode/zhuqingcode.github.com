---
layout: post
title: "compat wireless missing `endif'.  Stop."
description: ""
category: linux
tags: [linux]
---
{% include JB/setup %}

项目中用的wifi模块（skw17），用的是Atheros的芯片（高通创锐讯），驱动要用*compat wireless*。但是在编译*compat wireless*不同的版本的过程中会遇到这个错误：  

	config.mk:40: *** missing `endif'.  Stop.  

看到这个错误，很显然的认为*config.mk*的第40少了个*endif*，但是我打开*config.mk*，第40行是这样子的：  

	$(foreach ver,$(COMPAT_VERSIONS),$(eval CONFIG_COMPAT_KERNEL_3_$(ver)=y))   

傻眼了，没有找到*endif*相对应的*if*的影子。于是把整个*config.mk*看了一遍没有发现哪边少个*endif*。没办法，谷歌半天，好多人遇到这样子的情况，但是没有给具体解决办法，只能归结到自己的*make*不支持这种语法了。于是，索性把这行注释掉，再编译就顺利通过了。

#完

