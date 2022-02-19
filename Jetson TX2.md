# 系统

* [Jetson TX2](https://elinux.org/Jetson_TX2)
* [JetsonHack](https://www.jetsonhacks.com/)

# 架构

* [Arm Developer](https://developer.arm.com/ip-products/processors/cortex-a)

## Jetson TX2

* SoC型号: Tegra186
* tx2 模组型号: P3310-1000
* 载板型号: P2597

## Jetson Package

* [NVIDIA Jetson Linux Driver Package Software Features](https://docs.nvidia.com/jetson/l4t/index.html#page/Tegra%20Linux%20Driver%20Package%20Development%20Guide/manifest_tx2_tx2i.html#wwpID0E0YB0HA)

* manifest

  ```
  bootloader/ 	-- Bootloader and related components directory.
  flash.sh 		-- Script to flash Bootloader and the kernel from the package.
  jetson-tx2.conf -- flash.sh configuration file for an NVIDIA Jetson TX2 Developer Kit or a Jetson TX2 					   module.
  jetson-tx2-devkit.conf 	-- flash.sh configuration file for an NVIDIA Jetson TX2 Developer Kit.
  kernel/ 		-- Kernel images and kernel modules. For more information on this directory, see Kernel 				   in this topic.
  l4t_generate_soc_bup.sh 	-- Generates kernel and Bootloader payloads based on specifications.
  p2597-0000+p3310-1000.conf 	-- flash.sh configuration for an NVIDIA Jetson TX2 Developer Kit or a Jetson 								TX2 module.
  source/					-- —
  source/nv_src_build.sh 	-- Script for building the sources.
  source_sync.sh 			-- Script to download kernel and U-Boot source.
  ```

## 优化程序性能

* 编写高效程序的三个要点：
  * 适合的算法与数据结构
  * 利于编译器优化以转换为高效可执行代码的源码
  * 多核并行处理计算
* 程序的使用方式，代码的简单性与运行速度之间的权衡
  * 算法级别上，简单算法与高效复杂算法
  * 代码级别上，低级别的优化会降低程序的可读性和模块性，易出错、难修改或扩展
* 通用优化策略
  * 不必要的函数调用
  * 不必要的条件测试
  * 不必要的内存引用
* 平台优化前提
  * 理解目标机器的计算模型
  * 利用指令集并行能力
* 优化编译器的能力和局限性
  * 编译器的优化选项：-Og/-O1/-O2/-O3
  * 编译器的安全优化，保证优化前后，程序功能的一致性；限制优化的情况：
    * 内存别名使用，指针p与指针q的是否相等
    * 函数调用
    * 函数幂等性，包含全局变量对象
    * inline内联函数，可以展开促进优化
* 表示程序性能单位：CPE(Cycles Per Element)每元素周期数
* 消除循环的低效率：代码移动，如循环中获取元素个数的大小
* 减少过程调用
* 消除不必要的内存引用
* 现代处理器
  * 指令控制单元ICU
  * 执行单元EU
* 功能单元的性能
  * 延时
  * 发射时间
  * 功能单元数目
  * 延迟、吞吐量界限
* 循环展开
* 提高并行性 - 多功能单元并发
  * 多个累积变量
  * 运算顺序重新结合
* 寄存器溢出

## DSP

### Instruction Set

* delay slots: 执行一条指令的时钟数
* the functional unit lantency 执行功能单元所需要的时钟数 
* QMPYSP .M1 A3:A0 A11:A8 A15:A12

### Pipeline Stages

* Fetch: PG/PS/PW/PR
* Decode: DP/DC
* Execute: E1/E2/E3/E4/E5

### SPLOOP

* iteration interval 循环间隔
* Prolog/Kernel/Epilog
* LBC/ILB/RILC 寄存器
* SPLOOP(D/W) 指令, invoke the loop buffer mechanism

* SPKERNEL(R) 指令，mark the end of the software pipelined loop

## CUDA

[CUDA for Tegra](https://docs.nvidia.com/cuda/cuda-for-tegra-appnote/index.html#cuda-for-tegra-abstract)

[CUDA编程方法论](https://www.zhihu.com/column/c_1139113249399345152)

### device params

```
./deviceQuery Starting...

 CUDA Device Query (Runtime API) version (CUDART static linking)

Detected 1 CUDA Capable device(s)

Device 0: "NVIDIA Tegra X2"
  CUDA Driver Version / Runtime Version          10.2 / 10.2
  CUDA Capability Major/Minor version number:    6.2
  Total amount of global memory:                 7858 MBytes (8240123904 bytes)
  ( 2) Multiprocessors, (128) CUDA Cores/MP:     256 CUDA Cores						*****
  GPU Max Clock rate:                            1300 MHz (1.30 GHz)
  Memory Clock rate:                             1300 Mhz
  Memory Bus Width:                              128-bit >= 128/8 GB/s = 16 GB/s	*****
  L2 Cache Size:                                 524288 bytes = 512KB				*****
  Maximum Texture Dimension Size (x,y,z)         1D=(131072), 2D=(131072, 65536), 3D=(16384, 16384, 16384)
  Maximum Layered 1D Texture Size, (num) layers  1D=(32768), 2048 layers
  Maximum Layered 2D Texture Size, (num) layers  2D=(32768, 32768), 2048 layers
  Total amount of constant memory:               65536 bytes = 64kB
  Total amount of shared memory per block:       49152 bytes = 48KB	*****
  Total number of registers available per block: 32768				*****
  Warp size:                                     32
  Maximum number of threads per multiprocessor:  2048				*****
  Maximum number of threads per block:           1024				*****
  Max dimension size of a thread block (x,y,z): (1024, 1024, 64)
  Max dimension size of a grid size    (x,y,z): (2147483647, 65535, 65535)
  Maximum memory pitch:                          2147483647 bytes
  Texture alignment:                             512 bytes
  Concurrent copy and kernel execution:          Yes with 1 copy engine(s)	*****
  Run time limit on kernels:                     No
  Integrated GPU sharing Host Memory:            Yes
  Support host page-locked memory mapping:       Yes
  Alignment requirement for Surfaces:            Yes
  Device has ECC support:                        Disabled
  Device supports Unified Addressing (UVA):      Yes
  Device supports Compute Preemption:            Yes
  Supports Cooperative Kernel Launch:            Yes
  Supports MultiDevice Co-op Kernel Launch:      Yes
  Device PCI Domain ID / Bus ID / location ID:   0 / 0 / 0
  Compute Mode:
     < Default (multiple host threads can use ::cudaSetDevice() with device simultaneously) >

deviceQuery, CUDA Driver = CUDART, CUDA Driver Version = 10.2, CUDA Runtime Version = 10.2, NumDevs = 1
Result = PASS
```

### computer capability

```
single float pointing 1.3 G x 128 ops/cycle/sm x 2 =  332.8 GFLOPS
double float pointing 1.3 G x 4   ops/cycle/sm x 2 =  10.4  GFLOPS
```

### [Hardware Multithreading](https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#hardware-multithreading)

* 在整个warp的生命周期内，其上下文（程序计数器、寄存器文件等）都是驻留在片上的
* 每个multiprocessor有32-bit的寄存器组、缓存和共享内存等资源，寄存器组被驻留在multiprocessor上的所有的warps所分别占有，而cache和shared memeory被thread blocks所分别占有
* multiprocessor以thread block作为加载/启动单位，以warp作为基本调度单位

### [Page-Locked Host Memory](https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#page-locked-host-memory)

* cudaHostAlloc() / cudaFreeHost()
* cudaHostRegister() ---> malloc()
* paged-locked host memory 和 device memory 的拷贝与 kernel 的执行可以并发执行
* Mapped Memory 零拷贝(减少内存拷贝和即时数据更新)
  * Accessing host memory directly from within a kernel does **not provide the same bandwidth as device memory**, but does have some advantages:
  * Since mapped page-locked memory is shared between host and device however, the application must synchronize memory accesses using streams or events (see [Asynchronous Concurrent Execution](https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#asynchronous-concurrent-execution)) to avoid any potential read-after-write, write-after-read, or write-after-write hazards.
* portable memory
* write-combination memory
* **Note:** Page-locked host memory is not cached on non I/O coherent Tegra devices. Also, cudaHostRegister() is not supported on non I/O coherent Tegra devices.

### [Asynchronous Concurrent Execution](https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#asynchronous-concurrent-execution)

* [Streams](https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#streams)

  Applications manage the concurrent operations described above through streams.

* explicite synchronization

  * cudaDeviceSynchronize()
  * cudaStreamSynchronize()
  * cudaStreamWaitEvent()
  * cudaStreamQuery()

* implicit synchronization

* Events

### [Performance Guidelines](https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#performance-guidelines)

* 最大化执行并行度

  * 应用级

    * host/device, 异步并发执行

    * 并行执行的数据共享同步的两种情况：
      第一，block内线程同步，通过__syncthreadds()和shared memory; 

      blocks间的线程同步，只能通过global memory
      第二，低效的block内线程间的同步

  * 设备级

    * 通过streams实现多个核函数映射到不同的multiprocessors，实现一个设备内多个multiprocessors的同步

  * multiprocessors级

    * 指令执行步骤：

      1. 取指：4 bit 指令代码icode + 4 bit 指令功能ifun + ...
      2. 译码：从寄存器文件读入最多两个操作数
      3. 执行：算数逻辑单元，计算内存引用地址、栈指针加减、设置条件码等
      4. 访存：读写内存
      5. 回写：最多写两个结果到寄存器
      6. 更新PC

    * 指令流水线

      测量指标：吞吐量、延时（执行一条指令所需要的时间）

      局限性：硬件单元延时的不一致划分、流水线过深收益反而降低、指令间数据的相关性

    * 延时隐藏

      thread warp 的延时定义为前后指令发射之间的时钟数，也就是吞吐量的倒数；

      计算能力为6.2的设备，a multiprocessors 能够在一个时钟内同时对4个wraps发射指令；

      例如，大部分算数指令的延时为4个时钟，对于一个multiprocessor（具有4个warp schedulers）需要16个active warps才能隐藏这个算数指令的延时；

      warp不能立即执行的最常见原因是访问等待操作数，数据可能在register（等待几个时钟） and off-chip memeory（等待几百个时钟）中；

    * Register and shared memory usage
    * 

* 最大化内存吞吐率

* 最大化指令吞吐率

* 最小化内存颠簸

* [CUDA Occupancy Calculator](https://xmartlabs.github.io/cuda-calculator/)

### Nsight System 

系统级性能分析工具

* nsys-cli

  * nsys profile --trace=osrt,cublas --stats=true exe-file

    profile : a fully formed profiling description requring and accepting no further input

    -- trace: Select the API(s) to be traced

    --stats : generate summary statistics after the connection

  * nsys stats output-file 

    命令行显示分析结果

  * nsys-ui --> inport output-file

    界面化显示分析结果

* nsys-ui

  * 连接tx2：vim /etc/ssh/sshd_config --> PermitRootLogin yes --> wq
  * root@192.168.105.38
  * Target application

### Nsight Compute

NVIDIA Nsight Compute is an interactive kernel profiler for CUDA applications

### nvprof

*  --metrics

  ```
  nvprof --query-metrics	# 查询设备的可用的测量参数
  
  nvprof --metrics achieved_occupancy,gld_throughput,gld_efficeiency,local_memory
  
  achieved_occupancy:  Ratio of the average active warps per active cycle to the maximum number of warps supported on a multiprocessor. 线程占用率分析
  
  gld_throughput:	Global memory load throughput. 内存带宽分析
  
  gld_efficiency:	Ratio of requested (请求的) global memory load throughput to required (必须的) global memory load throughput expressed as percentage. 内存加载效率
  
  local_memory_overhead:	Ratio of local memory traffic to total memory traffic between the L1 and L2 caches expressed as percentage. This is available for compute capability 3.0, 3.5 and 3.7. 寄存器溢出
  ```

### nvcc

* [4.2.7. Options for Steering GPU Code Generation](https://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html#options-for-steering-gpu-code-generation)

  * [4.2.7.1. --gpu-architecture {*arch*|all|all-major} (-arch)](https://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html#options-for-steering-gpu-code-generation-gpu-architecture)

    设置虚拟GPU架构


  * [--gpu-code *code,...* (-code)](https://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html#options-for-steering-gpu-code-generation-gpu-code)

    设置实际GPU架构


  * [--generate-code *specification* (-gencode)](https://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html#options-for-steering-gpu-code-generation-generate-code)

    This option provides a generalization of the --gpu-architecture=arch --gpu-code=code,... option combination for specifying nvcc behavior with respect to code generation.

    -m64 -gencode arch=compute_62,code=sm_62


  * [Printing Code Generation Statistics](https://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html#printing-code-generation-statistics)

  * PTX is not quite analogous to assembly. PTX is an intermediate representation of the program that can be compiled to the different, incompatible instruction set architectures (ISA) that Nvidia GPUs have been using over time. Usually, a new ISA for Nvidia GPUs comes with an updated version of PTX that can represent the new features of the ISA.

    - The `-arch` and `-code` options to `nvcc` [work slightly differently](http://docs.nvidia.com/cuda/cuda-c-best-practices-guide/index.html#building-for-maximum-compatibility) to what you describe. They are not (mutual exclusive) alternatives, rather they determine different aspects.
      `-arch` controls which PTX version is used as the intermediate representation. As such it is combined with a `compute_XX` PTX version.
      `-code` controls what code is embedded into the resulting binary - either machine code for the specified ISA if used in the `-code sm_XX` form, or PTX to be just-in-time compiled by the GPU driver if `-code compute_XX` is specified.
      As a special shortcut, specifying *only* `-arch sm_XX` will embed both the compiled code for the specified ISA *and* PTX code into the binary - this probably is the situation that you are referring to that you want to avoid.
      Finally the `-gencode` option allows you to specify multiple `-arch`/`-code` pairs, with the resulting binary containing separate code for each of the pairs.
    - You can use [`nvprune`](http://docs.nvidia.com/cuda/cuda-binary-utilities/index.html#nvprune) to remove all but the desired ISA code from a binary.
    - If unsure, you can always use [`cuobjdump`](http://docs.nvidia.com/cuda/cuda-binary-utilities/index.html#cuobjdump) to check what is in a specific binary.

    So the way to prevent any PTX code from being present in your resulting binary is to call nvcc as `nvcc -arch compute_XX -code sm_XX` (or use multiple such pairs together with `-gencode`).

* --resource-usage (-res-usage)	# 编译时打印寄存、共享内存和局部内存使用情况
  Show resource usage such as registers and memory of the GPU code.
  This option implies --nvlink-options=--verbose when --relocatable-device-code=true is set. Otherwise, it implies --ptxas-options=--verbose.
  
* --maxrregcount amount (-maxrregcount) # 编译时限制核函数使用寄存器的数目，若超过数目，带来local_memory_overhead
  Specify the maximum amount of registers that GPU functions can use.
  Until a function-specific limit, a higher value will generally increase the performance of individual GPU threads that execute this function. However, because thread registers are allocated from a global register pool on each GPU, a higher value of this option will also reduce the maximum thread block size, thereby reducing the amount of thread parallelism. Hence, a good maxrregcount value is the result of a trade-off.
  
* [2.4. Supported Phases](https://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html#supported-phases)

### [cuobjdump](https://docs.nvidia.com/cuda/cuda-binary-utilities/index.html#cuobjdump)

```
cuobjdump -sass <input file>
cuobjdump -ptx <host binary>
cuobjdump -elf <cubin file>
```

### [nvdisasm](https://docs.nvidia.com/cuda/cuda-binary-utilities/index.html#nvdisasm)



### ARM Assembly

### PTX

* [Inline PTX Assembly in CUDA](https://docs.nvidia.com/cuda/inline-ptx-assembly/index.html#abstract)

### [CUDA-GDB](https://docs.nvidia.com/cuda/cuda-gdb/index.html#abstract)

```
(gdb) help ni
Step one instruction, but proceed through subroutine calls.
Usage: nexti [N]
Argument N means step N times (or till program stops for another reason).

(gdb) help si
Step one instruction exactly.
Usage: stepi [N]
Argument N means step N times (or till program stops for another reason).
```

### jetson-stats

[jetson-stats](https://pypi.org/project/jetson-stats/)

```
sudo apt install pip
sudo -H pip install -U jetson-stats
```

## PCIe

* lspci
  * lspci -[v]** // 设备详细信息 
  * lspci -t pci // 设备树
  * lspci -d 10ee:7022 // [VenderID]:[DeviceID]
  * lspci -s 01:00.0 // bus:slot.fun
  * lspci -i [/usr/share/misc/pci.ids]  // 默认读取该文件的 pci id 数据信息
  * lspci -p [/pro/bus/pci] // 默认读取该目录pci总线信息
  * lspci -[x]** // 配置空间信息

* PCIe协议

  * Transaction Layer 事务层，构建和解析事务包(内存、I/O、配置和消息)
  * Data Link Layer 数据链路层，链路管理(错误校验、重传、DLLP)
  * Physical layer 物理层，电路电器属性(串并转换)
  
* linux pci 驱动框架

  * 基于 linux 设备驱动模型

    ```
    总线 - 设备 - 驱动
    ```

  * 总线的创建 - pci-driver.c

    ```
    struct bus_type pci_bus_type 
        - pci_bus_match 
        - pc_device_probe
    ```

  * 设备枚举 - pci-tegra.c

    ```
    int tegra_pcie_probe(srtuct platform_device *) --> INIT_DELAYED_WORK( , )
    	void pcie_delayed_detect(struct work_struct *)
            int tegra_pcie_probe_complete(struct tegra_pcie *)
                int tegra_pcie_init(struct tegra_pcie *) --> INIT_WORK( , )
                    void work_hotplug_handle(struct work_struct *work)
                    unsigned int pci_scan_child_bus(struct pci_bus *)
                        int tegra_pcie_attach(struct tegra_pcie *)
                            unsigned int pci_resan_bus(struct pci_bus *)
                            	unsigned int pci_scan_child_bus(struct pci_bus *)
    ```

* ll /sys/bus/platform/devices | grep -i "pcie"

  ```
  10003000.pcie-controller -> ../../../devices/10003000.pcie-controller/
  ```

* ll /sys/bus/platform/drivers/tegra-pcie/

  ```
  10003000.pcie-controller -> ../../../../devices/10003000.pcie-controller/
  ```

* ll /sys/devices/10003000.pcie-controller/

  ```
  total 0
  drwxr-xr-x   3 root root    0 2月  12 12:31 ./
  drwxr-xr-x 146 root root    0 2月  12 12:31 ../
  lrwxrwxrwx   1 root root    0 2月  14 16:00 driver -> ../../bus/platform/drivers/tegra-pcie/
  -rw-r--r--   1 root root 4096 2月  14 16:00 driver_override
  lrwxrwxrwx   1 root root    0 2月  14 16:00 iommu_group -> ../../kernel/iommu_groups/50/
  -r--r--r--   1 root root 4096 2月  14 16:00 modalias
  lrwxrwxrwx   1 root root    0 2月  14 16:00 of_node -> '../../firmware/devicetree/base/pcie-controller@10003000'/
  drwxr-xr-x   2 root root    0 2月  14 16:00 power/
  lrwxrwxrwx   1 root root    0 2月  14 16:00 subsystem -> ../../bus/platform/
  -rw-r--r--   1 root root 4096 2月  14 16:00 uevent
  ```

* ll /dev/ | grep -i "pcie"

  ```
  none
  ```

* tty - teletype

  [tty驱动分析](http://www.wowotech.net/tty_framework/435.html)

  * 终端terminal - tty - 控制台console

  * 串口设备serial属于tty设备

  * Linux tty framework框架 - /drivers/tty/

  * tty core - system console core - tty line disciplines

  * 终端类型 - ll /sys/dev/char

    ```
    设备号(主，次)	字符设备		备注
    				/dev/ttyx
    				/dev/ttySx
    				/dev/vc/x
    				/dev/ttyUSB0
    ```


# 排序

```
1. 冒泡排序
   O(n^2):交换 O(1):in-place 稳定
2. 快速排序
   O(nlogn):基准-分区-递归 O(nlogn):out-place
3. 堆排序
   O(nlogn):建最大/小堆（上滤）- 出堆 - 下滤 O(1):in-place（数组结构完全二叉树）
4. 选择排序
   O(n^2):每一次遍历找到最大/小数据 O(1):in-place（数组结构完全二叉树）
5. 插入排序
   O(n^2):不停交换 O(1):in-place 稳定
6. 归并排序
   O(nlogn):分组-合并 O(n):out-place（数组结构完全二叉树） 稳定
   
混肴比较
	1. 冒泡排序 	选择排序 	插入排序
	   前后交换  	跨元素交换   整体移动
	2. 快速排序 	 归并排序
	   有基准分组    平均分组
```

