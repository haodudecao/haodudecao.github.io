---
layout: post
title: "由 df 命令到 tmpfs "
subtitle: "Linux-df-tmpfs"
author: "HaoDu"
catalog: true
tags:
  - Linux
  - Command

---

# df 命令
> 经常使用 df 命令来查看硬盘使用情况,却不知道每行的详细含义

# 常用命令

```
df -h #以人类可读方式显示
[root@iZbp16gi5od1u0wwphfx4tZ api]# df -h
文件系统        容量  已用  可用 已用% 挂载点
/dev/vda1        79G   39G   37G   52% /
devtmpfs        1.8G     0  1.8G    0% /dev
tmpfs           1.8G     0  1.8G    0% /dev/shm
tmpfs           1.8G  932K  1.8G    1% /run
tmpfs           1.8G     0  1.8G    0% /sys/fs/cgroup
tmpfs           365M     0  365M    0% /run/user/0

df --total #最后附加一行总量
[root@iZbp16gi5od1u0wwphfx4tZ api]# df --total
文件系统          1K-块     已用     可用 已用% 挂载点
/dev/vda1      82435124 40471192 38382652   52% /
devtmpfs        1856428        0  1856428    0% /dev
tmpfs           1866884        0  1866884    0% /dev/shm
tmpfs           1866884      900  1865984    1% /run
tmpfs           1866884        0  1866884    0% /sys/fs/cgroup
tmpfs            373380        0   373380    0% /run/user/0
total          90265584 40472092 46212212   47% -
```

## tmpfs 是什么
我们通过df可以看到tmpfs是挂载到/dev/下的shm目录，tmpfs是什么呢? 其实是一个临时文件系统，驻留在内存中，所以/dev/shm/这个目录不在硬盘上，而是在内存里。因为是在内存里，所以读写非常快，可以提供较高的访问速度。linux下，**tmpfs默认最大为内存的一半大小**



## 实际应用：
最后在说下tmpfs的一些应用，tmpfs用途还是较广的，Linux中可以把一些程序的临时文件放置在tmpfs中，利用tmpfs比硬盘速度快的特点来提升系统性能。比如可以用来放squid程序的缓存文件。当然，还有其他的用途，我们应该想到，tmpfs的挂载点既然能当作普通磁盘使用，也就能当作nfs的共享目录使用。但是用tmpfs做 nfs 共享需要考虑网卡速度和tmpfs读写速度二者孰为瓶颈，如果网卡速度是瓶颈，那么直接用nfs创建一个普通磁盘目录即可。因为tmpfs重启后丢失，使用tmpfs做nfs前，须先从磁盘或用rsync从其他服务器拷贝到tmpfs里。更新文件时，先更新磁盘上的文件，或更新rsync的源。然后设置定时进程，每分钟从磁盘或rsync里将程序同步到tmpfs即可。
