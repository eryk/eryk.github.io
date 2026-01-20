---
title: linux用户进程执行过程被系统kill掉了
date: 2017-09-22 15:16:00
categories: ["其他"]
---

系统日志显示

```
Sep 22 12:40:44 gpu01 systemd[1]: Stopping User Manager for UID 1001...
Sep 22 12:40:44 gpu01 systemd[1453]: Stopped target Default.
Sep 22 12:40:44 gpu01 systemd[1453]: Stopped target Basic System.
Sep 22 12:40:44 gpu01 systemd[1453]: Stopped target Timers.
Sep 22 12:40:44 gpu01 systemd[1453]: Stopped target Sockets.
Sep 22 12:40:44 gpu01 systemd[1453]: Stopped target Paths.
Sep 22 12:40:44 gpu01 systemd[1453]: Reached target Shutdown.
Sep 22 12:40:44 gpu01 systemd[1453]: Starting Exit the Session...
Sep 22 12:40:44 gpu01 systemd[1453]: Received SIGRTMIN+24 from PID 28725 (kill).
Sep 22 12:40:44 gpu01 systemd[1]: Stopped User Manager for UID 1001.
Sep 22 12:40:44 gpu01 systemd[1]: Removed slice User Slice of xxx.
```

google到如下解释：
https://serverfault.com/questions/774491/what-is-sigrtmin24-in-syslog

当用户session都退出之后，之前后台执行的进程就会被kill掉

所以程序运行最好用nohup xxx &

这还有一篇文章介绍nohup 和 & 的区别，核心意思是说：

> 用nohup运行命令可以使命令永久的执行下去，和用户终端没有关系，例如我们断开SSH连接都不会影响他的运行，注意了nohup没有后台运行的意思；&才是后台运行
> 
> &是指在后台运行，但当用户推出(挂起)的时候，命令自动也跟着退出

http://blog.csdn.net/zhang_red/article/details/52789691

