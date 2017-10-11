---
layout: post
title:  "Linux下配置JAVA开发环境-JDK+Eclipse+Tomcat"
date:   2017-06-06
categories: Linux
---

###  1.准备工作
 （我的电脑环境:ArchLinux）
 
 - 下载jdk(版本 8u131)
 链接: http://pan.baidu.com/s/1dE9etc1 密码: tbc6
 - 下载Eclipse IDE for Java EE Developers
  链接: http://pan.baidu.com/s/1o7ERBei 密码: 6ud6
 - 下载Tomcat
 链接: http://pan.baidu.com/s/1boT1h6J 密码: 75xf
 - 开始配置jdk
 1.解压jdk
 

```
tar -zxvf jdk-8u131-linux-x64.tar.gz 
```
2.将jdk目录移动到/opt/下

```
sudo cp -r jdk1.8.0_131/ /opt
```
3.修改/etc/profile文件，在profile文件末尾添加一下代码

```
export JAVA_HOME=/opt/jdk1.8.0_131
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JRE_HOME/lib
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
```
然后 执行  source /etc/profile 命令使配置生效(不执行此命令，需要重启才能生效)
执行 java -version  和javac  检查是否配置正确

```
[sky@sky-pc Downloads]$ java -version
java version "1.8.0_131"
Java(TM) SE Runtime Environment (build 1.8.0_131-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.131-b11, mixed mode)

```

 - Eclipse安装
Eclipse安装比较简单，去官网下载Eclipse IDE for Java EE Developers 版本，解压后 执行eclipse即可，如果前面刚配置完jdk，则需要重启才能运行.
 - Tomcat（版本9.0.0.M21）安装
 1.解压tomcat的包
 

```
 tar -zxvf apache-tomcat-9.0.0.M21.tar.gz 

```
2.移动tomcat目录到/opt下

```
sudo cp -r apache-tomcat-9.0.0.M21 /opt/
```

3.修改tomcat启动文件
可能会需要修改权限 :sudo chmod 755 -R  /opt/apache-tomcat-9.0.0.M21
在starup.sh启动文件末尾添加以下代码
```
JAVA_HOME=/opt/jdk1.8.0_131
JRE_HOME=$JAVA_HOME/jre
CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JRE_HOME/lib
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
TOMCAT_HOME=/opt/apache-tomcat-9.0.0.M21

```
执行 sudo /opt/apache-tomcat-9.0.0.M21/bin/startup.sh 
会看到控制台有以下输出
Using CATALINA_BASE:   /opt/apache-tomcat-9.0.0.M21
Using CATALINA_HOME:   /opt/apache-tomcat-9.0.0.M21
Using CATALINA_TMPDIR: /opt/apache-tomcat-9.0.0.M21/temp
Using JRE_HOME:        /opt/jdk1.8.0_131
Using CLASSPATH:       /opt/apache-tomcat-9.0.0.M21/bin/bootstrap.jar:/opt/apache-tomcat-9.0.0.M21/bin/tomcat-juli.jar
Tomcat started.

浏览器访问 http://localhost:8080/ 会看到以下页面，配置成功
![这里写图片描述](http://img.blog.csdn.net/20170614133659214?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
为了以后方便启动tomcat 可设置软链接

```
sudo ln  -s /opt/apache-tomcat-9.0.0.M21/bin/startup.sh  /usr/bin/tomcat-start
sudo ln  -s /opt/apache-tomcat-9.0.0.M21/bin/shutdown.sh  /usr/bin/tomcat-stop
```
以后直接使用 sudo tomcat-start 和sudo tomcat-stop即可，
(只为了方便使用,一般用eclipse做javaweb开发 会在里面另行设置tomcat服务器的)