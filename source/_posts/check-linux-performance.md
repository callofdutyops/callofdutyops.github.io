---
title: 检查Linux服务器性能
date: 2018-02-27 15:15:59
tags:
- Linux
- Tutorial
categories:
- tech
---

1. `$ uptime`：输出最近1, 5, 15分钟的负载。
2. `$ dmesg tail`：输出系统日志的最后10行，用于检查内核报警。
3. `$ vmstat 1`：输出系统核心指标，其中r, free, (si, so), (us, sy, id, wa, st)分别代表等待CPU资源进程数，系统可用内存数，(交换区写入，读取)，(用户，系统，空闲，IO等待，其他CPU时间)，参数表示刷新间隔/秒。
4. `$ mpstat -P ALL 1`：输出每个CPU的占用情况，一般若有一个CPU占用率特别高，则可能是一个单线程应用程序引起的。
5. `$ pidstat 1`：输出进程的CPU占用率。
6. `$ iostat -xz 1`：输出机器磁盘IO情况，参数-x表示输出额外信息，-z表示不输出非活动状态设备，数字表示刷新间隔。 
其中r/s, w/s, rkB/s, wkB/s分别表示每秒读写次数和每秒读写数据量，
await表示IO操作的平均等待时间，如果这个数值过大，可能是硬件设备遇到了瓶颈或者出现故障，
avgqu-sz表示向设备发出的请求平均数量，大于1表示硬件可能已经饱和，
%util表示设备利用率，这个数值表示设备的繁忙程度，经验值是如果超过60，可能会影响IO性能，100%表示已经饱和。
7. `$ free -m`：输出系统内存的使用情况，参数-m表示按照兆字节显示。
8. `$ sar -n DEV 1`：输出网络设备的吞吐率，参数-n详见man手册，数字表示刷新间隔，下同。
9. `$ sar -n TCP,ETCP 1`：输出TCP连接状态。
10. `$ top/htop`：输出系统负载总览，建议使用htop，输出更加形象。

此博文为[原文](https://medium.com/netflix-techblog/linux-performance-analysis-in-60-000-milliseconds-accc10403c55)的缩水精简版，备忘记录于此。
