---
layout: post
title: "Linux内核数据结构之spinlock"
description: ""
category: linux
tags: [linux,spinlock]
---

从我接触Linux已经有大约四年时间了，内核源代码也尝试着看过，琢磨过。*Linus Benedict Torvalds* 也说过，学习Linux最好的方法就是 *read the fucking source code*。 但是， *看了忘，忘了看，看了再忘，忘了再看* 这个循环始终困扰着我，于是乎打算把看过的一些Linux源码中我觉得比较重要的东西想借助自己的博客记录下来，只希望能通过此举加深印象。  

今天就来简单记录一下自旋锁 *spinlock* 的数据类型。注意：以下代码取自Linux *3.0.8*， 如有出入， 可能是由于源代码版本的问题。  

*include/linux/*目录下关于 *spinlock* 的头文件有6个：  

> 1.spinlock.h  
> 2.spinlock\_api\_smp.h  
> 3.spinlock\_api\_up.h  
> 4.spinlock\_types.h  
> 5.spinlock\_types\_up.h  
> 6.spinlock\_up.h  
 
初步看一下名字，第六感告诉我们这肯定和 *SMP* 和 *UP*，也就是多CPU和单CPU有关系。我们看一下 *spinlock\_types.h* 中关于 *spinlock* 的定义：  

	typedef struct spinlock {
		union {
			struct raw_spinlock rlock;
	
	#ifdef CONFIG_DEBUG_LOCK_ALLOC
	# define LOCK_PADSIZE (offsetof(struct raw_spinlock, dep_map))
			struct {
				u8 __padding[LOCK_PADSIZE];
				struct lockdep_map dep_map;
			};
	#endif
		};
	} spinlock_t;  

其中，牵扯到 *struct raw_spinlock rlock;*， 再来看一下它的定义：  

	typedef struct raw_spinlock {
		arch_spinlock_t raw_lock;
	#ifdef CONFIG_GENERIC_LOCKBREAK
		unsigned int break_lock;
	#endif
	#ifdef CONFIG_DEBUG_SPINLOCK
		unsigned int magic, owner_cpu;
		void *owner;
	#endif
	#ifdef CONFIG_DEBUG_LOCK_ALLOC
		struct lockdep_map dep_map;
	#endif
	} raw_spinlock_t;  

其中，又牵扯到 *arch_spinlock_t raw_lock;*， 如果你认真跟踪源代码，你会发现有两个地方牵扯到了其定义，也就是 *spinlock\_types.h* 开头的一段代码包含进来的两个头文件：

	#if defined(CONFIG_SMP)
	# include <asm/spinlock_types.h>
	#else
	# include <linux/spinlock_types_up.h>
	#endif  

*asm/spinlock_types.h* 跟平台有关系，这里看一下arm平台的:  

如果是 *SMP* 系统：  

	typedef struct {
		volatile unsigned int lock;
	} arch_spinlock_t;

如果是 *UP* 系统：  

	#ifdef CONFIG_DEBUG_SPINLOCK

	typedef struct {
		volatile unsigned int slock;
	} arch_spinlock_t;
	
	#define __ARCH_SPIN_LOCK_UNLOCKED { 1 }
	
	#else
	
	typedef struct { } arch_spinlock_t;
	
	#define __ARCH_SPIN_LOCK_UNLOCKED { }
	
	#endif  

从这里看出，如果定义了 *CONFIG\_DEBUG\_SPINLOCK* 就是一个*volatile unsigned int*的变量；相反，则是一个空结构体。这也间接证明一个问题，*UP* 系统上不存在真正意义上的自旋锁。