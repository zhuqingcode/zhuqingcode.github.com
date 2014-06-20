---
layout: post
title: "Linux内核编程之tasklet的简单使用"
description: ""
category: linux
tags: [linux,tasklet]
---

*tasklet*主要用在中断的下半段(bh)，主要用于处理一些可以延时处理的操作，这样子可以使中断ISR早点结束，把一些扫尾工作交给*tasklet*。说到底，*tasklet*也是一种延时机制，跟*work_queue*有点像。同上一篇，这里对*tasklet*的机制不做深入分析，只对如何使用稍作介绍。

为了能说明清楚这里给出一个demo：  
	
	#include <linux/module.h>  
	#include <linux/init.h>  
	#include <linux/timer.h> // for timer_list API
	#include <linux/param.h> // for HZ 
	#include <linux/jiffies.h> // for jiffies 
	#include <linux/interrupt.h>
	  
	static struct timer_list timer;
	
	void tasklet_demo_handle(unsigned long data) {
		printk("%s\n", __func__);
		printk("%d\n", data);
	}
	
	DECLARE_TASKLET(tasklet_demo,  tasklet_demo_handle,  0);
	
	static void ISR_handler(unsigned long time) {
		tasklet_schedule(&tasklet_demo);
		return;
	}	
	 
	static int __init tasklet_demo_init(void)  
	{  
		printk("init tasklet demo.\n");
		init_timer(&timer) ;
		timer.function = ISR_handler;
		timer.expires = jiffies + HZ * 10; 
		add_timer(&timer);
	   	return 0;    
	}  
	  
	static void __exit tasklet_demo_exit(void)  
	{  
		printk("exit tasklet demo.\n");
		del_timer_sync(&timer);
	}  
	MODULE_LICENSE("GPL");
	MODULE_AUTHOR("zhuqinggooogle@gmail.com");  
	module_init(tasklet_demo_init);  
	module_exit(tasklet_demo_exit);   

这里用内核定时器模拟一个中断，在中断例程*ISR_handler*里提交一个*tasklet*，值得注意的是：

- DECLARE_TASKLET(tasklet_demo,  tasklet_demo_handle,  0); 

第三个参数必须是常量，要不然编译会报类似  

	error: initializer element is not constant
	error: (near initialization for 'tasklet_demo.data')  

这个错误。

我们编译一下，*insmod*到系统看一下：  

	/mnt/3520d $ insmod tasklet_demo.ko 
	init tasklet demo.  

大约过10s后，会打印：  

	/mnt/3520d $ tasklet_demo_handle
	0  


