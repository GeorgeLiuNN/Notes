## 简介

* 内核启动过程，实模式到启动模式；init/main.c 中的 start_kernel() 函数；该函数首先初始化 CPU 子系统，然后运行内存管理和进程管理系统，接着启动外部总线和 I/O 设备，最后拉起 init 父进程

## 参考

* 深入Linux内核架构

* [知乎专栏](https://zhuanlan.zhihu.com/p/93289632)

## 性能

[Linux Performance](https://www.brendangregg.com/linuxperf.html)

[性能分析利器之perf浅析](http://walkerdu.com/2018/09/13/perf-event/)

## 虚拟内存

* 页表用于向每个进程提供一致的虚拟地址空间，页表将虚拟内存映射到物理内存页，因而支持共享内存和页交换功能

* Linux 为每个进程维护了一个单独的虚拟内存地址空间，包括代码、数据、堆、共享库以及栈段

* 内核为系统中的每个进程维护一个单独的任务结构task_struct。任务结构中的元素包括或者指向内核运行该进程所需的所有信息，例如，PID、指向用户栈的指针、可执行目标文件的名字，以及程序计数器。

* 内存映射，Linux通常将一个虚拟内存区域与一个磁盘上的对象关联起来，已初始化这个虚拟内存区域的内容

* swap

  * 交换文件/交换空间/交换区域，在任何时候，交换空间都限制着当前运行着的进程所能分配的虚拟页面的总数

  * ```
    company@company-desktop:~/wms/src/cuda$ free
                  total        used        free      shared  buff/cache   available
    Mem:        8046996     2043848     2081496       22056     3921652     5777552
    Swap:       4023488           0     4023488
    ```

    ```
    company@company-desktop:~/wms/src/cuda$ cat /proc/swaps 
    Filename                                Type            Size    Used    Priority
    /dev/zram0                              partition       1005872 0       5
    /dev/zram1                              partition       1005872 0       5
    /dev/zram2                              partition       1005872 0       5
    /dev/zram3                              partition       1005872 0       5
    ```

    ```
    company@company-desktop:~/wms/src/cuda$ sudo fdisk -l
    [sudo] password for company: 
    Disk /dev/zram0: 982.3 MiB, 1030017024 bytes, 251469 sectors
    Units: sectors of 1 * 4096 = 4096 bytes
    Sector size (logical/physical): 4096 bytes / 4096 bytes
    I/O size (minimum/optimal): 4096 bytes / 4096 bytes
    
    
    Disk /dev/zram1: 982.3 MiB, 1030017024 bytes, 251469 sectors
    Units: sectors of 1 * 4096 = 4096 bytes
    Sector size (logical/physical): 4096 bytes / 4096 bytes
    I/O size (minimum/optimal): 4096 bytes / 4096 bytes
    
    
    Disk /dev/zram2: 982.3 MiB, 1030017024 bytes, 251469 sectors
    Units: sectors of 1 * 4096 = 4096 bytes
    Sector size (logical/physical): 4096 bytes / 4096 bytes
    I/O size (minimum/optimal): 4096 bytes / 4096 bytes
    
    
    Disk /dev/zram3: 982.3 MiB, 1030017024 bytes, 251469 sectors
    Units: sectors of 1 * 4096 = 4096 bytes
    Sector size (logical/physical): 4096 bytes / 4096 bytes
    I/O size (minimum/optimal): 4096 bytes / 4096 bytes
    ```

    

## 物理内存

![mmc](https://github.com/GeorgeLiuNN/George/blob/main/mmc.jpg)

* NUMA Non-Uniform Memory Access 非一致内存访问
* struct page
  * 不可移动页，在内存中固定位置，内核分配的大多数内存属于该类别
  * 可回收页，不能直接移动，但可以删除，映射自文件的数据属于该类别
  * 可移动页，用户空间应用程序的页属于该类别

* struct zone 内存域
  * ZONE_DMA 16MB
  * ZONE_DMA32 4GB
  * ZONE_NORMAL 可直接映射到内核段的普通内存域
  * ZONE_HIGHMEM 超出内核段的物理内存，物理内存比可以映射到内核地址空间中的数量要多，内核会借助于高端内存方法对其进行管理，64 位不需要使用高端地址模式
  * ZONE_MOVABLE 伪内存域，防止内存碎片
  * ZONE_NR_ZONES 结束标志常量
  * ZONE_PADDING宏生成填充字添加到数据结构中，以确保每个自旋锁都处于自身的缓存行中 
  * free_area 同名数据结构数组，用于实现伙伴系统
  * flag 描述内存域的当前状态
    * ZONE_ALL_UNRECLAIMABLE 内核页不能从物理内存换出，pinnd memory
    * ZONE_RECLAIM_LOCKED 正在回收内存域
    * ZONE_OOM_LOCKED 杀死内存消耗过大进程

* struct pglist_data 节点
  * node_zonelists 备用节点及其内存域的列表
  * 系统中所有节点的页帧是依次编号的，每个页帧的编号都是全局唯一的
  * 包含交换守护进程 swap daemon 信息

* Linux中的内存模型(Memory Model)
  * Flat Memory Model 平坦内存模型
  * Discontiguous Memory Model 不连续内存模型
  * Spares Memory Model 稀疏内存模型

## 内存分配

* 空闲链表和内存池

  * 内存分配方法好坏的判断标准：分配速度和使用效率
  * 空闲链表(free list)，链表+bitmap
  * 内存池(memory pool)，free_lists 管理不同尺寸大小的连续内存块

* Buddy系统的原理

  * 物理上，内存块按地址从小到大排列；逻辑上，内存块通过 free list 组织
  * 伙伴系统的内存管理专注于某个节点的某个内存域，但所有内存域和节点通过备用分配列表连接起来

* Buddy系统的实现

  * struct zone

    ```
    struct zone {
        /* free areas of different sizes */
        struct free_area    free_area[MAX_ORDER];
        ...
    }
    
    #define MAX_ORDER 11
    #define MAX_ORDER_NR_PAGES (1 << (MAX_ORDER - 1))
    ```

  * 复合页 compound pages

    ```
    struct free_area {
        struct list_head    free_list[MIGRATE_TYPES];
        unsigned long       nr_free;
    };
    ```

  * 可以通过"/proc/buddyinfo"查看各个zones中不同migrate type的page frame数目

  * 通过查看"/proc/pagetypeinfo"可进一步获取同一migrate type中不同order的compound page数目

  * Buddy内存的分配，只能分配2的整数幂次个页

  * Buddy内存的释放

  * 要成为buddy，除了两者的大小（即order）相同，还必须属于同一个zone。Buddy系统是以page frame为粒度分配的，这并不能直接满足Linux的应用需求，slab分配器将可以实现更小粒度的内存分配。

  * vmalloc，内核代码使用它来分配在虚拟内存中连续但物理内存中不一定来连续的内存

* slab分配器

  * 提供小内存块的分配
  * slab缓存主要针对经常分配并释放的对象，slab分配器将释放的内存块保存在一个内部列表中，并不马上返回给伙伴系统，在请求分配新实例时复用释放的内存块。这有两个优点：首先，内核不必再使用Buddy系统算法，缩短处理时间；其次，该内存可能仍然驻留在CPU的告诉缓存中，加快访问速度
  * slabtop
  * cat /proc/slabinfo

* OOM

  [如何理解Linux中的OOM(Out Of Memory Killer)机制？](https://mp.weixin.qq.com/s/pdv5MMUQ9ACpeCpyGnxb1Q)

* tmpfs

  ```
  $ df -h
  Filesystem      Size  Used Avail Use% Mounted on
  /dev/mmcblk0p1   14G   12G  1.8G  87% /
  none            1.6G     0  1.6G   0% /dev
  tmpfs           1.9G  6.7M  1.9G   1% /dev/shm
  tmpfs           1.9G   21M  1.9G   2% /run
  tmpfs           5.0M  4.0K  5.0M   1% /run/lock
  tmpfs           1.9G     0  1.9G   0% /sys/fs/cgroup
  tmpfs           383M  116K  383M   1% /run/user/1000
  
  $ free -m
                total        used        free      shared  buff/cache   available
  Mem:           3825        1130        1675          32        1019        2499
  Swap:          1912           0        1912
  
  tmpfs 是 Linux/Unix 系统上的一种基于内存的文件系统。 tmpfs 可以使用您的内存或 swap 分区来存储文件。linux 发行版中默认大小为物理内存的一半。
  tmpfs 既可以使用物理内存，也可以使用交换分区，因为 tmpfs 使用的是 “ 虚拟内存 ” 。 Linux 内核的虚拟内存同时来源于物理内存和交换分区，主要由内核中的 VM 子系统进行调度，进行内存页和 SWAP 的换入和换出操作， tmpfs 自己并不知道这些页面是在交换分区还是在物理内存中。
  /dev/shm 是给共享内存分配的，共享内存是进程间通信的一种方式。
  tmpfs 无需要建立或格式化，只需要直接使用 mount 挂载就会自动被建立: mount -t tmpfs tmpfs /mnt/tmp
  tmpfs 的特点：1. 存储空间动态变化；2. 存取速度快；3. 易失性
  ```

* zram

  [zRAM内存压缩技术原理与应用](http://www.wowotech.net/memory_management/zram.html)

  ```
  Disk /dev/zram0: 982.3 MiB, 1030017024 bytes, 251469 sectors
  Units: sectors of 1 * 4096 = 4096 bytes
  Sector size (logical/physical): 4096 bytes / 4096 bytes
  I/O size (minimum/optimal): 4096 bytes / 4096 bytes
  
  
  Disk /dev/zram1: 982.3 MiB, 1030017024 bytes, 251469 sectors
  Units: sectors of 1 * 4096 = 4096 bytes
  Sector size (logical/physical): 4096 bytes / 4096 bytes
  I/O size (minimum/optimal): 4096 bytes / 4096 bytes
  
  
  Disk /dev/zram2: 982.3 MiB, 1030017024 bytes, 251469 sectors
  Units: sectors of 1 * 4096 = 4096 bytes
  Sector size (logical/physical): 4096 bytes / 4096 bytes
  I/O size (minimum/optimal): 4096 bytes / 4096 bytes
  
  
  Disk /dev/zram3: 982.3 MiB, 1030017024 bytes, 251469 sectors
  Units: sectors of 1 * 4096 = 4096 bytes
  Sector size (logical/physical): 4096 bytes / 4096 bytes
  I/O size (minimum/optimal): 4096 bytes / 4096 bytes
  ```

  

## 缓存

[浅谈Cache Memory](http://www.wowotech.net/memory_management/458.html/comment-page-2)

