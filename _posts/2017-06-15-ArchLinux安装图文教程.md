---
layout: post
title:  "ArchLinux安装图文教程"
date:   2017-06-15
categories: Linux
---
# 主要为以下步骤：
1.下载ArchLinux安装镜像并 制作U盘启动工具

2.开机

3.进行联网

4.编辑镜像站文件

5.开始分区(UEFI+GPT)

6.格式化分区，并挂载

7.开始安装基本操作系统

8.配置基础系统

9.引导系统

10.用户管理

11.网络配置

12.安装桌面环境

13.安装完后的工作

***
# 开始：
## 1.下载ArchLinux安装镜像并 制作U盘启动工具
（本次使用archlinux-2017.06.01-x86_64.iso）
下载地址：https://www.archlinux.org/download/

![](http://img.blog.csdn.net/20170720103128599?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center) 
下载Ultra ISO将镜像写入U盘

### （1）打开iso文件
![](http://img.blog.csdn.net/20170720103235713?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
### （2）写入硬盘镜像

![](http://img.blog.csdn.net/20170720103326638?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


选择你要写入的硬盘驱动器（你的u盘）
写入方式改为：RAW
![](http://img.blog.csdn.net/20170720103419268?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

单击写入
## 2.开机
### 1.开机进入U盘启动（UEFI引导）
![](http://img.blog.csdn.net/20170720103531719?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
进入系统后界面如下：
![](http://img.blog.csdn.net/20170720103600323?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
## 3.进行联网
执行:

```
# wifi-menu
```

连接wifi
或者：

```
# pppoe-setup
```

进行配置或者：

```
# systemctl start adsl
```

进行 adsl连接
连接完后，执行：

```
# ping www.baidu.com
```

或其他网址测试网路是否通

同步时间
执行：

```
# timedatectl set-ntp true
```

## 4.编辑镜像站文件
由于镜像站文件中有太多国外网址，网速慢，所以在镜像站文件开头添加国内镜像站
执行：

```
# nano /etc/pacman.d/mirrorlist
```

执行后如下图所示
![](http://img.blog.csdn.net/20170720103721219?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
注释掉第一个镜像站，在前面加2个##,将 第二个镜像站：mirrors.xxxxxx.com/……的xxxxxx改为163
也可以手动注释掉或者删除掉非中国的镜像站
修改后如下图所示：
![](http://img.blog.csdn.net/20170720103801590?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
执行ctrl+x退出，提示 是否保存，输入y，回车 保存
## 5.开始分区(UEFI+GPT)
本次将为sda硬盘重新建立分区表，重新建立分区，数据会全部丢失.
分区方案：
sda1---------------200M------------------------/boot/EFi
sda2---------------200M------------------------/boot
sda3---------------100G------------------------/
先查看下电脑硬盘设备，执行lsblk,如下图所示：(不同电脑设备不同，有可能会是 /dev/sdb……）
(有parted、fdisk两种分区方法，本次采用fdisk进行分区)

![](http://img.blog.csdn.net/20170720103833519?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
## 用fdisk进行分区
（1）建立GPT分区表
执行：

```
# fdisk /dev/sda
```

 
不同电脑设备不同，有可能会是 /dev/sdb……）
进入fdisk交互界面：

输入:g 建立gpt分区表:

（2）建立分区
输入：n 添加一个分区

回车：

提示让输入开始扇区(一个扇区512B，按自己要分区容量大小进行计算)
输入2048,回车

让输入结束扇区，由于一个扇区512B，要创建200M的分区,应该输入：+200M；

建立第二个分区：
输入n;
回车
输入开始扇区: 回车 （默认开始扇区即可）
输入结束扇区:+200M

建立第三个分区：
输入n;
回车
输入开始扇区:回车 （默认开始扇区即可）
输入结束扇区:直接回车(默认大那个数字)

输入:w 保存并退出；
执行:lsblk 如下图所示：

## 6.格式化分区，并挂载
### （1）格式化分区
执行:

```
# mkfs.fat -F32 /dev/sda1
```

 

(格式化ESP分区)

```
# mkfs.ext4 /dev/sda2 
```

(格式化boot分区)

```
# mkfs.ext4 /dev/sda3
```

(格式化根分区)
执行完如下图所示：

![](http://img.blog.csdn.net/20170720105045302?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

### （2）挂载：

```
# mount /dev/sda3 /mnt
# mkdir /mnt/boot
# mount /dev/sda2 /mnt/boot
# mkdir /mnt/boot/EFI
# mount /dev/sda1 /mnt/boot/EFI
```

执行:

```
# lsblk 
```

如下图所示

![](http://img.blog.csdn.net/20170720105130254?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
## 7.开始安装基本操作系统
执行： 

```
# pacstrap -i /mnt base base-devel
```

后开始安装
## 8.配置基础系统
### （1）配置fstab
执行：

```
# genfstab -U /mnt >> /mnt/etc/fstab
```

最好再执行：

```
# cat /mnt/etc/fastab
```

检查一下

![](http://img.blog.csdn.net/20170720105236753?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
### （2）切换到新系统
执行：

```
# arch-chroot /mnt /bin/bash
```

![](http://img.blog.csdn.net/20170720105313935?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
### （3）进行本地语言设置
执行：

```
# nano /etc/locale.gen
```

反注释（删掉前面的#）
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
这两个，退出保存
执行：

```
# locale-gen
```

![](http://img.blog.csdn.net/20170720105404689?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
执行：

```
# echo LANG=en_US.UTF-8 > /etc/locale.conf
```

![](http://img.blog.csdn.net/20170720105603582?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
### （4）设置时区
执行：

```
# ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

![](http://img.blog.csdn.net/20170720105908589?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

也可以执行：

```
# tzselect 
```

按照提示选择时区
执行:

```
# hwclock --systohc --utc
```

设置硬件时间

![](http://img.blog.csdn.net/20170720110050785?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

## 9.引导系统
GRUB进行UEFI引导
执行：

```
# pacman -S dosfstools grub efibootmgr
```

安装引导工具

![](http://img.blog.csdn.net/20170720110143861?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

执行：

```
# grub-install --target=x86_64-efi --efi-directory=/boot/EFI --recheck
```

进行安装grub

![](http://img.blog.csdn.net/20170720110214651?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

执行： 

```
# grub-mkconfig -o /boot/grub/grub.cfg
```

进行配置grub

![](http://img.blog.csdn.net/20170720110353531?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

## 10.用户管理
### （1）设置root密码
执行:

```
# passwd
```

![](http://img.blog.csdn.net/20170720110429112?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

### （2）添加用户
执行：

```
# useradd -m -g users -s /bin/bash 用户名
```

（务必添加一个 用户 ，否则后面sddm显示管理器登录的时候无法登录，sddm不会列出root用户）
执行：

```
# passwd 用户名
```

为刚才添加的用户设置密码
执行：

```
# nano /etc/sudoers
```

在 root ALL=(ALL) ALL 下面添加
用户名 ALL=(ALL) ALL
为你刚才创建的用户 添加sudo权限
###（3）退出chroot重启
（笔记本请直接跳到下面网络配置，安装无线网络相关模块）
(也可以不重启，直接进行下面的网络配置和桌面环境配置）
执行：

```
# exit
```

退出chroot
执行：

```
# reboot
```

重启电脑
## 11.网络配置
开机进入电脑
###（1）有线连接

```
# systemctl enable dhcpcd
```


root下执行不了此命令，可以省略，执行完下面的命令一会重启会自动启动dhcpcd服务）
启动dhcpcd



```
# systemctl enable dhcpcd
```

开机自动启动dhcp服务

![](http://img.blog.csdn.net/20170720110504241?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

###（2）无线连接：

```
# pacman -S iw wpa_supplicant dialog
```

###（3）ADSL 宽带连接：

```
# pacman -S rp-pppoe# pppoe-setup # systemctl start adsl
```

（chroot下执行不了此命令）# systemctl enable adsl
##　12.安装桌面环境
### （1）安装显卡驱动
确定显卡型号
执行:

```
# lspci | grep VGA
```

执行：

```
# pacman -S 驱动包
```

官方仓库提供的驱动包:
通用----------------------------------xf86-video-vesa
intel----------------------------------xf86-video-intel
Geforce7+--------------------------xf86-video-nouveau
Geforce6/7-------------------------xf86-video-304xx

![](http://img.blog.csdn.net/20170720110557828?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
###（2）安装X窗口系统
执行：

```
# pacman -S xorg
```

安装X窗口系统
![](http://img.blog.csdn.net/20170720110626440?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
执行：

```
# pacman -S xf86-input-synaptics
```

（触摸板驱动，笔记版可装，台式机就不用了）执行

```
# pacman -S ttf-dejavu wqy-microhei
```

安装字体：Dejavu 和 微米黑字体（不安装的话 后面进入桌面环境设置系统语言为简体中文的时候会出现字体显示不全的问题）
![]()http://img.blog.csdn.net/20170720110819958?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center
###（3）安装kde-plasma桌面环境
安装　Gnome桌面环境的直接跳到第(4)步
（kde和gnome桌面环境自带了大部分的驱动 ，安装其他桌面环境可能需要额外配置一些驱动，比如声卡）
想安装其他桌面环境 参照官方wiki：https://wiki.archlinux.org/index.php/Desktop_environment_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)
执行：

```
# pacman -S plasma
```

安装plasma
![](http://img.blog.csdn.net/20170720110853257?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
执行：

```
# pacman -S konsole
```

安装 kde下的控制台终端
![](http://img.blog.csdn.net/20170720110939107?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
执行：

```
# pacman -S dolphin
```

安装kde下的文件管理器
（可以直接执行:

```
# pacman -S kde-applications
```

安装kde套件，包含了常用的系统工具）
安装完后
执行：

```
# systemctl enable sddm
```

启用 sddm显示管理器
![](http://img.blog.csdn.net/20170720111007519?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
执行：

```
# systemctl enable NetworkManager
```

启用网络管理
![](http://img.blog.csdn.net/20170720111036741?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
执行:

```
# pacman -S plasma-nm
```

安装 网络管理的前端工具（图形界面）
执行：

```
# reboot
```

重启

进入系统后界面如下：
![](http://img.blog.csdn.net/20170720111136441?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
（4）安装Gnome桌面环境
执行：

```
# pacman -S gnome
```

安装gnome桌面
执行：

```
# pacman -S gnome-tweak-tool
```

安装gnome桌面优化工具
执行：

```
# pacman -S alacarte
```

安装gnome桌面菜单编辑器
执行：

```
# systemctl enable gdm
```

启用gnome窗口管理器服务
执行：

```
# systemctl enable NetworkManager
```

启用网络管理器服务
执行：

```
# reboot
```


## 13.安装完后的工作
### （1）添加archlinuxcn源（里面包含了很多中国人常用而官方仓库又没有的软件）
执行：

```
# nano /etc/pacman.conf
```

在 /etc/pacman.conf 文件末尾添加两行：

```
[archlinuxcn]
SigLevel=Never
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
```

（2）安装中文输入法
执行：

```
# pacman -S fcitx-im fcitx-configtool
```

安装输入法引擎
（官方仓库里的输入法：
fcitx-cloudpinyin
fcitx-googlepinyin 
fcitx-libpinyin
fcitx-sunpinyin）
执行：

```
# nano ~/.xprofile
```

添加一下内容

```
export GTK_IM_MODULE=fcitx

export QT_IM_MODULE=fcitx

export XMODIFIERS="@im=fcitx"
```

执行：

```
# pacman -S fcitx-sogoupinyin
```

安装搜狗输入法
### （3）安装网易云音乐
执行：

```
# pacman -S netease-cloud-music
```

安装网易云音乐
### （4）安装yaourt使用aur
执行：

```
# pacman -S yaourt
```

安装yarourt
以后可以使用yaourt 安装aur中的软件了 ，yaourt跟pacman使用方法一样
安装kde下的文件管理器
（5）安装浏览器
执行：

```
# pacman -S google-chrome
```

安装google浏览器（没法在线观看视频）
执行：

```
# pacman -S firefox
```

安装火狐浏览器
（执行: # pacman -S flashplugin 安装flas插件，否则无法在线观看视频，chrome浏览器不支持flash）
###（6）其他常用软件
可在https://wiki.archlinux.org/index.php/List_of_applications_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)
进行查找

（7）桌面美化
Kde-Plasma桌面：
![](https://coding.net/u/TryBin/p/image/git/raw/master/ArchLinux%25E5%25AE%2589%25E8%25A3%2585/arch-plasma.png)
![](https://github.com/hexusr/pictures/blob/master/plasma.png?raw=true)
Gnome桌面：

![](http://img.blog.csdn.net/20170619153152180?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

可自行安装一些主题，请自己探索。



