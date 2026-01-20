---
title: systemd入门教程
date: 2018-03-29 09:57:00
categories: ["系统运维"]
---

最近在处理数字货币行情数据，使用websocket接收行情数据写入磁盘，发现运行时间久了偶尔出现socket close的异常，重试多次无效后程序异常退出，所以考虑将python程序作为linux 守护进程运行，了解了supervisord和systemd，决定使用更强大的systemd。

# 基本概念

systemd 是一个 Linux 系统基础组件的集合，提供了一个系统和服务管理器，运行为 PID 1 并负责启动其它程序。功能包括：支持并行化任务；同时采用 socket 式与 D-Bus 总线式激活服务；按需启动守护进程（daemon）；利用 Linux 的 cgroups 监视进程；支持快照和系统恢复；维护挂载点和自动挂载点；各服务间基于依赖关系进行精密控制。systemd 支持 SysV 和 LSB 初始脚本，可以替代 sysvinit。除此之外，功能还包括日志进程、控制基础系统配置，维护登陆用户列表以及系统账户、运行时目录和设置，可以运行容器和虚拟机，可以简单的管理网络配置、网络时间同步、日志转发和名称解析等。

systemd架构图

![](images/15222891502855.jpg)


## 常用工具 

* systemd-analyze: 查看启动耗时
* systemctl: 用来查看系统状态、启动停止服务，在systemctl参数中添加-H <用户名>@<主机名>可以实现对其他机器的远程控制。该过程使用ssh链接
* journalctl: Systemd统一管理所有Unit的启动日志

## unit

一个单元配置文件可以描述如下内容之一：系统服务(.service)、挂载点(.mount)、sockets(.sockets) 、系统设备(.device)、交换分区(.swap)、文件路径(.path)、启动目标(.target)、由 systemd 管理的计时器(.timer)

## 如何编写服务脚本

```

[Unit]

Description=Network Manager

Wants=network.target

Before=network.target network.service

                                                                                                                                                                               

[Service]

Type=dbus

BusName=org.freedesktop.NetworkManager

ExecStart=/usr/sbin/NetworkManager --no-daemon

# NM doesn't want systemd to kill its children for it

KillMode=process

 

[Install]

WantedBy=multi-user.target

Alias=dbus-org.freedesktop.NetworkManager.service

Also=NetworkManager-dispatcher.service
```

### 整个文件分三个部分，[Unit]·[Service]·[Install]

* [Unit]：记录unit文件的通用信息。

* [Service]：记录Service的信息

* [Install]：安装信息。

#### Unit主要包含以下内容：

●  Description：对本service的描述。

●  Before, After：定义启动顺序，Before=xxx.service，代表本服务在xxx.service启动之前启动。After=xxx.service,代表本服务在xxx之后启动。

●  Requires: 这个单元启动了，那么它“需要”的单元也会被启动; 它“需要”的单元被停止了，它自己也活不了。但是请注意，这个设定并不能控制某单元与它“需要”的单元的启动顺序（启动顺序是另外控制的），即 Systemd 不是先启动 Requires 再启动本单元，而是在本单元被激活时，并行启动两者。于是会产生争分夺秒的问题，如果 Requires 先启动成功，那么皆大欢喜; 如果 Requires 启动得慢，那本单元就会失败（Systemd 没有自动重试）。所以为了系统的健壮性，不建议使用这个标记，而建议使用 Wants 标记。可以使用多个 Requires。

●  RequiresOverridable：跟 Requires 很像。但是如果这条服务是由用户手动启动的，那么 RequiresOverridable 后面的服务即使启动不成功也不报错。跟 Requires 比增加了一定容错性，但是你要确定你的服务是有等待功能的。另外，如果不由用户手动启动而是随系统开机启动，那么依然会有 Requires 面临的问题。

●  Requisite：强势版本的 Requires。要是这里需要的服务启动不成功，那本单元文件不管能不能检测等不能等待都立刻就会失败。

●  Wants：推荐使用。本单元启动了，它“想要”的单元也会被启动。但是启动不成功，对本单元没有影响。

●  Conflicts：一个单元的启动会停止与它“冲突”的单元，反之亦然。

#### Service主要包含以下内容：

●  Type：service的种类，包含下列几种类型：

    ----simple 默认，这是最简单的服务类型。意思就是说启动的程序就是主体程序，这个程序要是退出那么一切都退出。

    -----forking 标准 Unix Daemon 使用的启动方式。启动程序后会调用 fork() 函数，把必要的通信频道都设置好之后父进程退出，留下守护精灵的子进程

    -----oneshot种服务类型就是启动，完成，没进程了。

notify,idle类型比较少见，不介绍。

●  ExecStart：服务启动时执行的命令，通常此命令就是服务的主体。

    ------如果你服务的类型不是 oneshot，那么它只可以接受一个命令，参数不限。

    ------多个命令用分号隔开，多行用 \ 跨行。

●  ExecStartPre, ExecStartPost：ExecStart执行前后所调用的命令。

●  ExecStop：定义停止服务时所执行的命令，定义服务退出前所做的处理。如果没有指定，使用systemctl stop xxx命令时，服务将立即被终结而不做处理。

●  Restart：定义服务何种情况下重启（启动失败，启动超时，进程被终结）。可选选项：no, on-success, on-failure,on-watchdog, on-abort

●  SuccessExitStatus：参考ExecStart中返回值，定义何种情况算是启动成功。

    eg：SuccessExitStatus=1 2 8 SIGKILL

#### Install主要包含以下内容：

●  WantedBy：何种情况下，服务被启用。

    eg：WantedBy=multi-user.target（多用户环境下启用）

●  Alias：别名

# 例子

新建文件 /etc/systemd/system/huobi.service

内容如下:

```
[Unit]
Description=huobi downloader
After=rc-local.service

[Service]
Type=simple
User=root
Group=root
WorkingDirectory=/usr/lib/code/atom
ExecStart=/home/ubuntu/anaconda3/bin/python3.6 exchange/downloader.py huobi /data2/huobi/huobi.log
Restart=always

[Install]
WantedBy=multi-user.target
```

观察系统日志，发现程序异常退出后systemd重启的了服务

```
root@ip-172-31-4-62:/usr/lib/code/atom#  journalctl -u huobi.service
-- Logs begin at Mon 2018-03-26 17:51:39 CST, end at Thu 2018-03-29 16:39:27 CST. --
Mar 29 10:58:46 ip-172-31-4-62 systemd[1]: Started huobi downloader.
Mar 29 11:02:18 ip-172-31-4-62 systemd[1]: huobi.service: Main process exited, code=killed, status=9/KILL
Mar 29 11:02:18 ip-172-31-4-62 systemd[1]: huobi.service: Unit entered failed state.
Mar 29 11:02:18 ip-172-31-4-62 systemd[1]: huobi.service: Failed with result 'signal'.
Mar 29 11:02:18 ip-172-31-4-62 systemd[1]: huobi.service: Service hold-off time over, scheduling restart.
Mar 29 11:02:18 ip-172-31-4-62 systemd[1]: Stopped huobi downloader.
Mar 29 11:02:18 ip-172-31-4-62 systemd[1]: Started huobi downloader.
Mar 29 11:29:58 ip-172-31-4-62 python3.6[9124]: Traceback (most recent call last):
Mar 29 11:29:58 ip-172-31-4-62 python3.6[9124]:   File "./exchange/ex_huobi.py", line 66, in dispatch
Mar 29 11:29:58 ip-172-31-4-62 python3.6[9124]:     raw_data = await ws.recv()
Mar 29 11:29:58 ip-172-31-4-62 python3.6[9124]:   File "/home/ubuntu/anaconda3/lib/python3.6/site-packages/websockets/protocol.py", line 323, i
Mar 29 11:29:58 ip-172-31-4-62 python3.6[9124]:     raise ConnectionClosed(self.close_code, self.close_reason)
Mar 29 11:29:58 ip-172-31-4-62 python3.6[9124]: websockets.exceptions.ConnectionClosed: WebSocket connection is closed: code = 1006 (connection
Mar 29 11:29:58 ip-172-31-4-62 systemd[1]: huobi.service: Service hold-off time over, scheduling restart.
Mar 29 11:29:58 ip-172-31-4-62 systemd[1]: Stopped huobi downloader.
Mar 29 11:29:58 ip-172-31-4-62 systemd[1]: Started huobi downloader.
Mar 29 13:30:44 ip-172-31-4-62 python3.6[19180]: Traceback (most recent call last):
Mar 29 13:30:44 ip-172-31-4-62 python3.6[19180]:   File "./exchange/ex_huobi.py", line 66, in dispatch
Mar 29 13:30:44 ip-172-31-4-62 python3.6[19180]:     raw_data = await ws.recv()
Mar 29 13:30:44 ip-172-31-4-62 python3.6[19180]:   File "/home/ubuntu/anaconda3/lib/python3.6/site-packages/websockets/protocol.py", line 323,
Mar 29 13:30:44 ip-172-31-4-62 python3.6[19180]:     raise ConnectionClosed(self.close_code, self.close_reason)
Mar 29 13:30:44 ip-172-31-4-62 python3.6[19180]: websockets.exceptions.ConnectionClosed: WebSocket connection is closed: code = 1006 (connectio
Mar 29 13:30:45 ip-172-31-4-62 systemd[1]: huobi.service: Service hold-off time over, scheduling restart.
Mar 29 13:30:45 ip-172-31-4-62 systemd[1]: Stopped huobi downloader.
Mar 29 13:30:45 ip-172-31-4-62 systemd[1]: Started huobi downloader.
```

# 参考

https://wiki.archlinux.org/index.php/systemd_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)

https://www.hi-linux.com/posts/3761.html

https://blog.csdn.net/fu_wayne/article/details/38018825


