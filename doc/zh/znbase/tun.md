# **性能调优**

## **系统调优**

### **常用系统调优命令**

**- lsof**

  列出打开文件（lists openfiles）,可查看网络、进程、命令等信息

**- strace**

  跟踪系统调用性能。例如：

>   strace -p 58893 -Ttty 2>trace.log  
>
>   #1 11:30:31.435796 gettimeofday({1590982231, 435808}, NULL) = 0 <0.000012>
>
>   #2 11:30:31.435913 pread(244, "#$\356W\0\2\237\234\0\2\237\233\0\2\237\235\0\0\2\272\264\36\276sE\277\0\0\0\0\0\0"..., 16384, 2816933888) = 16384 <0.019401>
>
>   #3 11:30:31.455361 gettimeofday({1590982231, 455378}, NULL) = 0 <0.000012>
>

  上面例子可见第三行pread系统读调用花了19毫秒，存在系统性能问题，结合lsof命令可知文件句柄244对应是数据库的数据文件，由此得出读取表缓慢的性能结论；

**- dmesg**

  观察操作系统内核日志，例如：

>   dmesg –T >trace.log
>
>   #1 [Fri Apr 19 11:14:27 2019] Out of memory: Kill process 976 (WTJourn.Flusher) score 137or sacrifice child
>
>   #2 [Fri Apr 19 11:14:27 2019] Killed process 976 (WTJourn.Flusher) total-vm:3718480kB, anon-rss:2305768kB, file-rss:0kB, shmem-rss:0kB
>
>   #3 [Fri Apr 19 11:14:27 2019] mongod invoked oom-killer: gfp_mask=0x201da, order=0, oom_score_adj=0
>
>   #4 [Fri Apr 19 11:14:27 2019] mongod cpuset=/ mems_allowed=0-1
>

  上面例子可见第一行 Out of memory，进程号976因为内存不足被操作系统杀掉

**- vmstat 1**

  对系统的进程情况、内存使用情况、交换页和I/O块使用情况、中断以及CPU使用情况进行统计并报告相应的信息

**- mpstat -P ALL 1**

  输出每一个 CPU 的运行状况，为多处理器系统中的 CPU 利用率提供统计信息

**- pidstat 1**

  用于监控全部或指定进程的cpu、内存、线程、设备IO等系统资源的占用情况，例如：

>   pidstat -p 23514 -w 1 10
>
>   1519   PID  cswch/s nvcswch/s Command
>
>   1519  23514   0.00   0.00 drdb
>
>   1519  23514   0.00   0.00 drdb
>
>   1519  23514   0.00   0.00 drdb
>

  说明：

  ​	cswch/s: 每秒任务主动(自愿的)切换上下文的次数，当某一任务处于阻塞等待时，将主动让出自己的CPU资源。

  ​	nvcswch/s: 每秒任务被动(不自愿的)切换上下文的次数，CPU分配给某一任务的时间片已经用完，因此将强迫该进程让出CPU的执行权。

**-  iostat -xz 1**

  iostat主要用来观察io设备的负载情况的

**- free –m**

  观察可用内存

**- sar -n DEV 1**

  报告网络设备状态统计信息

**- sar -n TCP,ETCP 1**

  报告网络设备使用TCP协议的出错状态统计信息

**- pstack**

  可显示每个进程的栈跟踪，可结合软件源码，跟踪软件运行情况

**- iotop**

  显示系统中所有运行进程并将进程根据I/O统计信息排序

### OS内核调优

#### **CPU调优**

- CPU C-State 设置为C0或C1. 使CPU全力运行，在系统启动行中加入 processor.max_cstate=0； 重建grub2；重启机器

  > vim /etc/sysconfig/grub
>
  > GRUB_CMDLINE_LINUX=”… processor.max_cstate=0“
>
  > grub2-mkconfig -o /boot/grub2/grub.cfg

 

- CPU 取消powersaving 设置，在启动行中加入 intel_idle.max_cstate=0 processor.max_cstate=1；重建grub2; 重启机器。

   > vim /etc/sysconfig/grub
>
  > GRUB_CMDLINE_LINUX=”…intel_idle.max_cstate=0 processor.max_cstate=1 “
>
  > grub2-mkconfig -o /boot/grub2/grub.cfg

 

- cpufreq 是一个动态调整 CPU 频率的模块，可支持五种模式。为保证服务性能应选用 performance 模式，将 CPU 频率固定工作在其支持的最高运行频率上，不进行动态调节，操作命令为 :

  > cpupower frequency-set --governor performance

- 推荐打开CPU超频

#### **关闭交换空间**

1， 查看是否启用swap：swapon -s

> ​	Filename  Type  Size  Used  Priority
>
> ​	/dev/sda2 partition 16777212  0    -1
>

2,关闭swap

> ​	swapoff /dev/sda2
>

3,永久关闭swap

>    vi /etc/fstab
>
>    #UUID=d639aaed-b343-4346-8157-4e3cca4669fd swap swap  defaults    0 0

然后reboot

#### 关闭防火墙和SELinux

> ​	systemctl stop firewalld.service
>
> ​	systemctl disable firewalld.service
>
> ​	vi /etc/selinux/config 确保以下内容
>
> ​	SELINUX=disabled
>

#### 网络调优

> ​	vi /etc/sysctl.conf
>
> ​	\# The default setting of the socket receive buffer in bytes.
>
> ​	net.core.rmem_max = 4194304
>
> ​	\# The maximum receive socket buffer size in bytes
>
> ​	net.core.wmem_default = 262144
>
> ​	\# The default setting (in bytes) of the socket send buffer.
>
> ​	net.core.wmem_max = 4194304
>
> ​	\# The maximum send socket buffer size in bytes.
>
> ​	net.core.somaxconn = 4096
>
> ​	net.ipv4.tcp_max_syn_backlog = 4096
>
> ​	net.ipv4.tcp_keepalive_intvl = 20
>
> ​	net.ipv4.tcp_keepalive_probes = 3
>
> ​	net.ipv4.tcp_keepalive_time = 60
>
> ​	net.ipv4.tcp_mem = 8388608 12582912 16777216
>
> ​	net.ipv4.tcp_fin_timeout = 5
>
> ​	net.ipv4.tcp_synack_retries = 2
>
> ​	net.ipv4.tcp_syncookies = 1
>
> ​	\# 开启SYN Cookies。当出现SYN等待队列溢出时，启用cookie来处理，可防范少量的SYN攻击
>
> ​	net.ipv4.tcp_timestamps = 1
>
> ​	\# 减少time_wait
>
> ​	net.ipv4.tcp_tw_recycle = 0
>
> ​	\# 如果=1则开启TCP连接中TIME-WAIT套接字的快速回收，但是NAT环境可能导致连接失败，建议服务端关闭它
>
> ​	net.ipv4.tcp_tw_reuse = 1
>
> ​	\# 开启重用。允许将TIME-WAIT套接字重新用于新的TCP连接
>
> ​	net.ipv4.tcp_max_tw_buckets = 262144
>
> ​	net.ipv4.tcp_rmem = 8192 87380 16777216
>
> ​	net.ipv4.tcp_wmem = 8192 65536 16777216
>
> ​	net.nf_conntrack_max = 1200000
>
> ​	net.netfilter.nf_conntrack_max = 1200000
>
> ​	vm.dirty_background_bytes = 409600000
>

#### 文件系统调优

- 使用deadline/noop这两种I/O调度器；

- 尽量使用xfs文件系统

- 文件系统mount参数中增加：noatime, nodiratime, nobarrier几个选项(nobarrier是xfs文件系统特有的)；

- 文件系统内核参数调整：

  > vi /etc/sysctl.conf
>
  > fs.aio-max-nr = 1048576
>
  > fs.file-max = 76724600

#### 内存调优

> ​	\#系统脏页到达这个值，系统后台刷脏页调度进程 pdflush（或其他）自动将(dirty_expire_centisecs/100）秒前的脏页刷到磁盘
>
> ​     vm.dirty_background_bytes = 409600000
>
> ​	\#内核的flusher线程会周期性地唤醒，然后将老的脏数据写到磁盘上。 dirty_writeback_centisecs定义了唤醒的间隔，单位是百分之一秒。如果设置为0，则禁止周期性地唤醒回写线程
>
> ​	vm.dirty_expire_centisecs = 100
>
> ​	\#如果系统进程刷脏页太慢，使得系统脏页超过内存 95 % 时，则用户进程如果有写磁盘的操作（如fsync, fdatasync等调用），则需要主动把系统脏页刷出
>
> ​	vm.dirty_ratio = 95
>
> ​	\#系统保留给内核用的内存
>
> ​	vm.min_free_kbytes = 2097152
>
> ​	\#0. 启发式策略。合理的overcommit会被接受，不合理的overcommit会被拒绝。 
>
> ​	\#1. 任何overcommit都会被接受。 
>
> ​	\#2. 当系统分配的内存超过swap+N%*物理RAM(N%由vm.overcommit_ratio决定)时，会拒绝commit。
>
> ​	vm.overcommit_memory = 0
>
> ​	\# CommitLimit是一个内存分配上限,CommitLimit = 物理内存 * overcommit_ratio(默认50，即50%) + swap大小
>
> ​	vm.overcommit_ratio = 90
>

## **数据库调优** 

### **节点参数调优**

--attrs，标记node的特点，比如ram大小，硬盘种类，大小等。主要跟replication zone配合使用，zone能通过这些特点来进行库、表、行级别的数据分离存储。比如限制某个表的数据只能存储在attrs硬盘为ssd的store上。

--cache，分给rocksdb的内存大小，越多读性能越好，建议25%的内存容量。

--space，标明node的地域位置，主要配合replication zone使用限制数据存储地域，也是特性follow workload必要参数，只有设置了它，lease才能自动移往负载来源node以减少时延（当node靠得很近时不设置）。

--max-sql-memory，用于sql session和其他sql运行时分配的缓存，更大的缓存可以允许更多的并发连接，建议25%的内存。

--store，一个store就是一个rocksdb实例，使用多个store，并给每个store分配一个独立的硬盘，在cpu等其他因素充裕的情况下可以提高并发。

### Replication Zone调优

​	通过zone，可以配置集群中replica的副本数量，限定数据存储地点，配置垃圾回收（较旧的MVCC数据）时间等，配置粒度从cluster，database，table，到row。主要配置参数如下：

- range_min_bytes，range的最小大小。

- range_max_bytes，range最大大小，默认64M。

- ttlseconds，垃圾回收时间，回收旧的MVCC数据，默认是25小时，超过25小时的数据会被异步清理。

- num_replicas，副本数量，默认为3。

- constraints，一串JSON数据，限制replica位置的参数，前面设置的node参数如locality和attrs，都是这里的限制条件，比如：

  constraints: {"+region=us-west1": 2, "+region=us-central1": 1}限制第二份replica只能存在us-west1，第一份replica只能存在us-central1。

### **集群参数调优**

​	这些设置是集群范围内的设置，并且都是比较内核级别的设置，可能会造成很大影响，因而需要谨慎：

- kv.allocator.lease_rebalancing_aggressiveness，是否更激进的根据负载平衡lease分布，默认为1，0～1之间更保守，>1更激进。一般来讲，当出现某些node负载一直高于其他node时应该设置更高的系数。
- kv.allocator.load_based_lease_rebalancing.enabled，是否根据负载和时延来平衡lease，一般来讲建议开启。
- kv.allocator.range_rebalance_threshold，当该node的负载等因素大于集群平均值多少时判断需要平衡，默认为5%，如果因为平衡影响了性能可以将其设置的高一些。
- kv.range.backpressure_range_size_multiplier，range在因为write阻塞前能够增长的最大range_max_bytes倍数大小而不用splitting，默认是2倍，即如果没有碰到write block那么小于128M的range依然不用被splitting，可以减少因为splitting产生的压力。
- rocksdb.min_wal_sync_interval，每多少秒刷新RocksDB log到硬盘，默认为0，这个不应该修改。
- server.consistency_check.interval，多久检测一次range的每个replica一致性，默认是24h，这个弄在每天负载最低的时候检测最好。
- server.time_until_store_dead，上一次store活跃的时间间隔（发出新的gossip信息），超过则其他store认为该store已经挂了，默认为5min。
- sql.defaults.distsql，是否开启分布式SQL，有不开启，根据情况选择是否使用，和开启三种，默认是第二种。
- sql.distsql.distribute_index_joins，是否开启分布式hash join，建议开启。
- ·sql.distsql.merge_joins.enabled，是否使用merge join，当join的参数有序时会使用merge join，建议开启。