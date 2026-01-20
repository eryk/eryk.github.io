---
title: 使用树莓派+kodi搭建多媒体环境
date: 2017-12-03 14:26:00
categories: ["其他"]
tags: ["树莓派,kodi"]
---

# 制作启动盘

* 下载 RASPBIAN STRETCH WITH DESKTOP，地址如下

https://www.raspberrypi.org/downloads/raspbian/

* 使用SDFormatter 格式化sd卡
* 使用win32diskimager将 .img文件写入sd卡

# 安装kodi

参考如下步骤

http://kodi.wiki/view/HOW-TO:Install_Kodi_on_Raspberry_Pi

# mac 添加nfs服务

在 /etc/exports 文件中添加如下内容，IP地址为树莓派ip地址

> /Users/eryk/Documents/movie 192.168.1.10 (rw)

然后启动nfs服务，树莓派可以从mac目录读取视频文件

```
sudo nfsd enable
sudo nfsd update
sudo nfsd status
```

# 树莓派开机mount nfs目录

使用如下命令mount nfs 目录

> sudo mount 192.168.1.4:/Users/eryk/Documents/movie /home/pi/movie

如果想在树莓派开机启动自动mount nfs目录，需要在/etc/fstab中添加如下设置
> 192.168.1.4:/Users/eryk/Documents/movie /home/pi/movie nfs defaults 0 0

# kodi设置中文

1. system–>settings–>Interface–>skin，把 fonts 改成 Arial based；
2.  system–>settings–>Interface–>Regional，改Language改成Chinese(Simple)