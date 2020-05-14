#### 系统命令

###### 性能指标命令 top

```shell
$ top
top - 11:46:39 up 26 days,  2:28,  1 user,  load average: 0.00, 0.01, 0.05
Tasks: 118 total,   1 running, 117 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.1 sy,  0.0 ni, 99.9 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  8009832 total,  4907536 free,  1499476 used,  1602820 buff/cache
KiB Swap:        0 total,        0 free,        0 used.  6198256 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
  659 root      20   0  298700   6104   4772 S   0.3  0.1  25:34.57 vmtoolsd 
    1 root      20   0  127996   6612   4132 S   0.0  0.1   0:21.62 systemd 
    2 root      20   0       0      0      0 S   0.0  0.0   0:00.60 kthreadd 
    3 root      20   0       0      0      0 S   0.0  0.0   0:00.49 ksoftirqd/0 
    5 root       0 -20       0      0      0 S   0.0  0.0   0:00.00 kworker/0:0H 
    7 root      rt   0       0      0      0 S   0.0  0.0   0:00.09 migration/0 
    8 root      20   0       0      0      0 S   0.0  0.0   0:00.00 rcu_bh 
```

###### 内存指标命令 free

```shell
$ free -m
              total        used        free      shared  buff/cache   available
Mem:           7822        1464        4792          16        1565        6052
Swap:             0           0           0
```

###### 磁盘空间命令（查看当前目录下N个层次的占用空间大小） du

```shell
$ du -h --max-depth=2
5.4M	./tqview/.git
2.0M	./tqview/src
109M	./tqview/target
1.5G	./tqview
158M	./logs
5.3M	./resource-portal/.git
1.5M	./resource-portal/src
1.6M	./resource-portal/target
13M		./resource-portal
1.6G	.
```

###### 磁盘空间命令（查看挂载磁盘分区的大小和可用大小） df

```shell
$ df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   49G  3.7G   46G   8% /
devtmpfs                 3.9G     0  3.9G   0% /dev
tmpfs                    3.9G     0  3.9G   0% /dev/shm
tmpfs                    3.9G   17M  3.9G   1% /run
tmpfs                    3.9G     0  3.9G   0% /sys/fs/cgroup
/dev/sda1               1014M  226M  789M  23% /boot
tmpfs                    783M     0  783M   0% /run/user/1000
```

