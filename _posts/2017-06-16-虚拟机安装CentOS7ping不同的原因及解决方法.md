---
layout: post
title:  "虚拟机安装CentOS７ping不同的原因及解决方法"
date:   2017-06-06
categories: Linux
---


###这是配置好的CentOS7，刚开始在Vmware里装CentOS7后是没有ip的，原因是CentOS7默认不启动网卡的，(网卡不启用还ping个毛)

![](https://coding.net/u/TryBin/p/image/git/raw/master/CentOS%25EF%25BC%2597ping%25E4%25B8%258D%25E5%2590%258C/2.png)
进入 /etc/sysconfig/network-scipts 文件夹下

![](https://coding.net/u/TryBin/p/image/git/raw/master/CentOS%25EF%25BC%2597ping%25E4%25B8%258D%25E5%2590%258C/3.png)
查看 ifcfg-eno16777736 网卡配置文件(centos7改动了很多！！！跟书本教材的老版本有很多不一样的)

![](https://coding.net/u/TryBin/p/image/git/raw/master/CentOS%25EF%25BC%2597ping%25E4%25B8%258D%25E5%2590%258C/4.png)
将onboot修改为=yes 问题就解决了!

![](https://coding.net/u/TryBin/p/image/git/raw/master/CentOS%25EF%25BC%2597ping%25E4%25B8%258D%25E5%2590%258C/5.png)
版权声明：本文为博主原创文章，未经博主允许不得转载。

