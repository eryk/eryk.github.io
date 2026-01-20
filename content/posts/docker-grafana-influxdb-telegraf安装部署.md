---
title: Docker+Grafana+Influxdb+Telegraf安装部署
date: 2017-08-14 23:53:13
categories: ["系统运维"]
tags: ["Grafana,Docker,Influxdb,Telegraf"]
---

# docker启动Grafana + Influexdb
```
docker run -d \
  --name docker-statsd-influxdb-grafana \
  -p 3003:3003 \
  -p 8083:8083 \
  -p 8086:8086 \
  -p 22022:22 \
  -p 8125:8125/udp \
  samuelebistoletti/docker-statsd-influxdb-grafana:latest
```

# 设置InfluxDB

打开 http:localhost:8083，设置用户名密码
```
Username: root
Password: root
Port: 8086
```
# 设置Grafana
打开http:localhost:3003，登录用户名密码为
```
Username: root
Password: root
```
登录之后按步骤设置数据源
```
Url: http://localhost:8086
Database:	telegraf
User: telegraf
Password:	telegraf
```
# 安装并设置TeleGraf
下载地址：https://portal.influxdata.com/downloads

## 设置
/etc/telegraf/telegraf.conf
修改influxdb地址，用户名及密码，设置hostname

重启服务
> service telegraf restart

# 导入Grafana Dashboard
下载最新版本的dashboard配置：
https://grafana.com/dashboards/1443/revisions

在grafana的新建dashboard并导入配置，完成。

# 注意

docker内部已经启动了telegraf，如果不需要的话可以停掉，在多台服务器上安装并配置Telegraf写入同一Influxdb就可以实现对集群进行系统监控。

# 参考

1. [使用 Grafana、collectd 和 InfluxDB 打造现代监控系统](https://linux.cn/article-5252-1.html)
2. [docker-statsd-influxdb-grafana](https://github.com/samuelebistoletti/docker-statsd-influxdb-grafana)
3. [Send CollectD metrics to InfluxDB](https://anomaly.io/collectd-metrics-to-influxdb/)