---
layout: post
title: "Linux内核编程之完成接口completion的简单使用"
description: ""
category: linux
tags: [linux,completion]
---

这里对*完成接口completion*如何实现不做深入解释，有兴趣的可以去看Linux源代码，主要在  

- include/linux/completion.h  

这个头文件里。这里只对如何使用结合demo稍作解释。下面直接给出驱动源代码：  

	#include <linux/module.h>
	#include <linux/moduleparam.h>
	#include <linux/init.h>  
	#include <linux/timer.h> // for timer_list API
	#include <linux/param.h> // for HZ 
	#include <linux/jiffies.h> // for jiffies 
	#include <linux/interrupt.h>
	#include <linux/completion.h>
	#include <linux/miscdevice.h>
	#include <linux/uaccess.h>
	#include <linux/fs.h>
	#include <linux/errno.h>
	
	DECLARE_COMPLETION(completion_demo);
	
	static char completion_str[16] = {0x0};
	
	static int completion_open(struct inode *inode, struct file *file) {
		printk("completion opened.\n");
		return 0;
	}
	
	static int completion_release(struct inode *inode, struct file *file) {
		printk("completion released.\n");
		return 0;
	}
	
	static ssize_t completion_read(struct file *file, char __user *buffer, size_t count, loff_t *pos) {
		ssize_t ret;
		printk("completion read.\n");
		wait_for_completion_interruptible(&completion_demo);
		if (ret == 0) {
			return count;
		} else {
			return -EFAULT;
		}
	}
	
	static ssize_t completion_write(struct file *file, const char __user *buffer, size_t count, loff_t *pos) {
		ssize_t ret;
		printk("completion write.\n");
		ret = copy_from_user(completion_str, buffer, count);
		if (ret == 0) {
			complete(&completion_demo);
			return count;
		} else {
			return -EFAULT;
		}
	}
	
	static struct file_operations completion_fops =
	{ 
	  	.owner = THIS_MODULE, 
	  	.open  = completion_open,
	  	.release = completion_release,
	  	.read = completion_read,
	  	.write = completion_write,
	}; 
	
	static struct miscdevice completion_dev = {
		.minor = MISC_DYNAMIC_MINOR,
		.name = "completion",
		.fops = &completion_fops,
	};
	 
	static int __init completion_demo_init(void)  
	{  
		int ret;
		printk("init completion demo.\n");
		ret = misc_register(&completion_dev);
	
		if (ret) { 
		  	printk("misc_register error.\n"); 
		  	return ret; 
		}
	
		return 0;    
	}  
	  
	static void __exit completion_demo_exit(void)  
	{  
		printk("exit completion demo.\n");
		misc_deregister(&completion_dev);
	}  
	MODULE_LICENSE("GPL");
	MODULE_AUTHOR("zhuqinggooogle@gmail.com");  
	module_init(completion_demo_init);  
	module_exit(completion_demo_exit); 

这里主要模拟这种情况：  

- 两个进程，一个进程写，另一个进程读。但是，必须保证在一个进程写完后，另一个进程才能读  

为此，我写了个简单的上层demo，下面是源代码：  

	#include <stdio.h>
	#include <stdlib.h>
	#include <unistd.h>
	#include <sys/types.h>
	#include <fcntl.h> 
	
	char completion_demo[] = "completion demo";
	
	int main(int argc, char **argv) {
		int fd = -1;
		char buffer[16] = {0x0};
		int ret;
		pid_t pid;
		
		pid = fork();
		if (pid < 0) {
			perror("fork error.\n");
			exit(-1);
		} else if (0 == pid) {
			printf("----child process----\n");
			fd = open("/dev/completion", O_RDWR);
			if (fd < 0) {
				perror("open error\n");
				exit(-1);
			}
			ret = read(fd, buffer, 16);
			if (ret > 0) {
				printf("%s\n", buffer);
				close(fd);
				exit(0);
			}
			close(fd);
		} else if (pid > 0) {
			printf("----father process----\n");
			fd = open("/dev/completion", O_RDWR);
			if (fd < 0) {
				perror("open error\n");
				exit(-1);
			}
			printf("sleep for 10s just for delaying write operation.\n");
			sleep(10);
			ret = write(fd, completion_demo, sizeof(completion_demo));
			if (ret > 0) {
				printf("write ok.\n");
				close(fd);
				exit(0);
			}
			close(fd);
		}
	}

这里，父进程写，子进程读。父进程里休眠的10秒来模拟写延时的情况，子进程先读，读不到阻塞，然后父进程完成写操作后唤醒子进程。  

我们编译驱动demo和上层demo，下面是驱动demo的*Makefile*：  

	LINUXROOT := /home/Hi3520D_SDK_V1.0.2.0/osdrv/kernel/linux-3.0.y
	PWD := $(shell pwd)
	obj-m := completion_demo.o
	completion_demo-y += completion.o
	
	default:
	        @make -C $(LINUXROOT) M=$(PWD) modules 
	clean: 
	        @rm *.o *.ko -rf
	        @make -C $(LINUXROOT) M=$(PWD) clean  

编译成功后，加载到开发板是看看：  

	/mnt/3520d $ insmod completion_demo.ko 
	init completion demo.  

然后再去运行上层demo：  

	/mnt/3520d $ ./demo 
	----father procecompletion opened.
	ss----
	sleep focompletion opened.
	r 10s just for dcompletion read.
	elaying write operation.
	----child process----
	completion write.
	write ok.
	completion released.
	/mnt/3520d $ completion released.
	completion demo  

看上去打印好乱，其实这是因为驱动demo里和上层demo里都有打印，打印混乱的结果。如果没有混乱应该是下面的情况：  

	/mnt/3520d $ ./demo   
	----father process----  
	completion opened.  
	sleep for 10s just for delaying write operation.  
	----child process----  
	completion opened.  
	completion read.  
	completion write.  
	write ok.  
	completion released.
	completion demo 
	/mnt/3520d $ completion released.     

