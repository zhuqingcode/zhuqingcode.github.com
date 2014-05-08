---
layout: post
title: "多级目录下gitignore"
description: ""
category: git 
tags: [git,.gitignore]
---
{% include JB/setup %}  

假设有这么一种情况：项目中要用到git进行代码跟踪，这个项目有很大，涉及到多级目录。例如：  

	project------dir0-----file0
	   |          |-------file1
	   |          `-------fileN
       |
	   |---------dir1-----file0
	   |          |-------file1
       |          `-------fileN
       |
	   `---------dir2-----file0
				  |-------file1
                  `-------fileN  
	    

用到git，肯定会遇到要忽略一些不必要跟踪的文件这种情况，大多数情况下，用于忽略文件的**.gitignore**，都会放在**project**目录下。但是有些忽略的文件是在**dir0**、**dir1**、**dir2**目录下的，这时候**.gitignore**该怎么写呢？也许你会想到在**project**目录下的**.gitignore**中添加些相对路径以达到目的，这是一种不推荐的做法。推荐的做法是在**dir0**、**dir1**、**dir2**下也添加一个**.gitignore**文件，所以项目结构就变成这样子：  


	project------dir0-----file0
	   |          |-------file1
	   |          |-------fileN
       |          `-------.gitignore
	   |---------dir1-----file0
	   |          |-------file1
       |          |-------fileN
       |          `-------.gitignore
	   |---------dir2-----file0
	   |		  |-------file1
       |          |-------fileN  
	   |          `-------.gitignore
	   |---------git仓库	
	   |
       `---------.gitignore  

这也是一种聪明的做法！  

#完