### 起因
测试同学反映说开发机器的mysql服务最近经常奔溃，遂有了下面一番调查。

### 调查
#### mysql错误日志
```
vim /data/mysql/mysql-error.log
```
日志如下：
```
161021 10:43:30 mysqld_safe Number of processes running now: 0

161021 10:43:30 mysqld_safe mysqld restarted

2016-10-21 10:43:31 0 [Warning] 'THREAD_CONCURRENCY' is deprecated and will be removed in a future release.

2016-10-21 10:43:31 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).

2016-10-21 10:43:31 0 [Note] /usr/local/mysql/bin/mysqld (mysqld 5.6.24-log) starting as process 28801 ...

2016-10-21 10:43:31 28801 [Note] Plugin 'FEDERATED' is disabled.

2016-10-21 10:43:31 28801 [Note] InnoDB: Using atomics to ref count buffer pool pages

2016-10-21 10:43:31 28801 [Note] InnoDB: The InnoDB memory heap is disabled

2016-10-21 10:43:31 28801 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins

2016-10-21 10:43:31 28801 [Note] InnoDB: Memory barrier is not used

2016-10-21 10:43:31 28801 [Note] InnoDB: Compressed tables use zlib 1.2.3

2016-10-21 10:43:31 28801 [Note] InnoDB: Using Linux native AIO

2016-10-21 10:43:31 28801 [Note] InnoDB: Using CPU crc32 instructions

2016-10-21 10:43:31 28801 [Note] InnoDB: Initializing buffer pool, size = 2.0G

2016-10-21 10:43:31 28801 [Note] InnoDB: Completed initialization of buffer pool

2016-10-21 10:43:31 28801 [Note] InnoDB: Highest supported file format is Barracuda.

2016-10-21 10:43:31 28801 [Note] InnoDB: Log scan progressed past the checkpoint lsn 13238044364

2016-10-21 10:43:31 28801 [Note] InnoDB: Database was not shutdown normally!

2016-10-21 10:43:31 28801 [Note] InnoDB: Starting crash recovery.

2016-10-21 10:43:31 28801 [Note] InnoDB: Reading tablespace information from the .ibd files...

2016-10-21 10:43:32 28801 [Note] InnoDB: Restoring possible half-written data pages

2016-10-21 10:43:32 28801 [Note] InnoDB: from the doublewrite buffer...

InnoDB: Doing recovery: scanned up to log sequence number 13238046090

161021 10:43:35 mysqld_safe mysqld from pid file /data/mysql/data/localhost.pid ended
```
说明mysql服务挂了之后重启，重启之后又挂了。

#### 查看系统日志
```
grep mysqld /var/log/messages
```
日志如下：
```
Oct 21 10:43:29 localhost kernel: [26312]    27 26312     2868      126   0       0             0 mysqld_safe

Oct 21 10:43:29 localhost kernel: [28266]    27 28266  1167978   208669   5       0             0 mysqld

Oct 21 10:43:29 localhost kernel: Out of memory: Kill process 28266 (mysqld) score 51 or sacrifice child

Oct 21 10:43:29 localhost kernel: Killed process 28266, UID 27, (mysqld) total-vm:4671912kB, anon-rss:834284kB, file-rss:392kB

Oct 21 10:43:29 localhost kernel: [26312]    27 26312     2868      126   0       0             0 mysqld_safe

Oct 21 10:43:29 localhost kernel: [28332]    27 28266  1167978   208669   0       0             0 mysqld

Oct 21 10:43:29 localhost kernel: Out of memory: Kill process 28332 (mysqld) score 51 or sacrifice child

Oct 21 10:43:29 localhost kernel: Killed process 28332, UID 27, (mysqld) total-vm:4671912kB, anon-rss:834284kB, file-rss:392kB
```
重点在这里：
```
Oct 21 10:43:29 localhost kernel: Out of memory: Kill process 28266 (mysqld) score 51 or sacrifice child
```
mysql因为OOM被内核kill掉了，这是linux自我保护进行内存清理动作。

#### OOM问题
Out of memory 问题，这通常是因为某时刻应用程序大量请求内存导致系统内存不足造成的，这通常会触发 Linux 内核里的 Out of Memory (OOM) killer，OOM killer 会杀掉某个进程以腾出内存留给系统用，不致于让系统立刻崩溃。

OOM killer根据 points 的高低来决定杀哪个进程，这个 points 可以根据 adj 调节，root 权限的进程通常被认为很重要，不应该被轻易杀掉，所以打分的时候可以得到 3% 的优惠（adj -= 30; 分数越低越不容易被杀掉）。我们可以在用户空间通过操作每个进程的 oom_adj 内核参数来决定哪些进程不这么容易被 OOM killer 选中杀掉。比如，如果不想 MySQL 进程被轻易杀掉的话可以找到 MySQL 运行的进程号后，调整 oom_score_adj 为 -15（注意 points 越小越不容易被杀）

```
# ps aux | grep mysqld
mysql    2196  1.6  2.1 623800 44876 ?        Ssl  09:42   0:00 /usr/sbin/mysqld

# cat /proc/2196/oom_score_adj
0
# echo -15 > /proc/2196/oom_score_adj
```

### 后续
其实发生OOM大部分是因为程序导致内存泄漏，这次的原因是测试同学写了个脚本去发邮件，由于程序问题，导致一直在请求mail程序，导致内存不够，触发了oom killer杀掉了低分的进程。
