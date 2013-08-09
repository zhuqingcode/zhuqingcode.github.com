---
layout: post
title: "opensuse 11.3开启samba"
description: ""
category: linux
tags: [linux]
---
{% include JB/setup %}

由于自己在开发过程中一直使用SUSE，刚开始使用的是SUSE 10.1 后来感觉有点落伍了，于是装了个openSUSE 11.3，SUSE不知道什么时候改名为openSUSE的，不管啦，反正我还是比较喜欢openSUSE，很酷！
可是我要通过windows去访问虚拟机下的内容，包括用sourceinsight看内核源代码，所以不得不用samba服务了。但是怎么在openSUSE 11.3上开启samba服务呢？这个问题困惑了我好久，上网搜了一下，没有一篇说的令我满意的，尽他妈瞎扯淡！于是，我自己捣鼓了许久，终于搞定了，所以想写篇博客儿记录一下！  

* 首先，进入虚拟机下的openSUSE 11.3，如图：  

![opensuse0](/images/opensuse0.jpg)  

* 关闭防火墙

点击左下角的“Computer”，再点击“YaST”，如图：  

![opensuse1](/images/opensuse1.jpg)   

进入如下界面：  

![opensuse2](/images/opensuse2.jpg)  

* 点击“Security and Users”里面的Firewall，如图：  

![opensuse3](/images/opensuse3.jpg)  

进入如下的界面：  

![opensuse4](/images/opensuse4.jpg)  

点击上图中的”Stop Firewall Now“,然后点击”Next“，关闭防火墙。 

* 开启samba服务  

点击下图中的”Samba Server“  

![opensuse5](/images/opensuse5.jpg)  

进入如下界面：  

![opensuse6](/images/opensuse6.jpg)  

这里添加相应的目录后，点击”OK“。  

* .添加samba密码：  


打开一个终端，键入命令，如图：  

![opensuse7](/images/opensuse7.jpg)  

注意：上面的root表示当前用户是root，如果不是root用户，把”root“换成对应的用户名即可，然后输入密码即可。  

* 访问samba：  

在windows下开启”运行“对话框，如图，输入”//192.168.4.185“这是虚拟机的ip，  

![opensuse8](/images/opensuse8.jpg)  

确定后，输入用户名和密码即可。  

# 完

