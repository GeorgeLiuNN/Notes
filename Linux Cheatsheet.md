# 工具

## SSH

* 应用架构
  * c/s Server-Client 服务器-客户端模式
  * b/s Brower-Server 浏览器-服务器模式
* [SSH 教程](https://wangdoc.com/ssh/)

## VSCode

* [Remote development using SSH](https://code.visualstudio.com/docs/remote/ssh#_getting-started)
* [Using C++ on Linux in VS Code](https://code.visualstudio.com/docs/cpp/config-linux)

## vcpkg

[Get started with vcpkg](https://vcpkg.io/en/getting-started.html)

[Manifest files - vcpkg.json](https://vcpkg.io/en/docs/maintainers/manifest-files.html)

```
Commands:
    vcpkg search [pat]              Search for packages available to be built
    vcpkg install <pkg>:<triplet>.  Install a package
    vcpkg remove <pkg>...           Uninstall a package
    vcpkg remove --outdated         Uninstall all out-of-date packages
    vcpkg list                      List installed packages
Options:
     --triplet=<t>                  Specify the target architecture triplet. See 'vcpkg help triplet'
                                    (default: $VCPKG_DEFAULT_TRIPLET)

```

## git

[Pro Git book](https://git-scm.com/book/zh/v2)

## Cmake

* [CMake 入门实战 | HaHack](https://www.hahack.com/codes/cmake/)
* [CMake: Public VS Private VS Interface](https://leimao.github.io/blog/CMake-Public-Private-Interface/)

```
cmake -DNATIVE_COMPILE=ON -DWITH_CUDA=1 -DCMAKE_BUILD_TYPE=Release ..
```

## Make

```
make -f Makefile -C ./ -B

$@ -- 目标文件集
$^ -- 依赖目标集，以空格分隔
$< -- 第一个依赖目标
```

## GCC

[GCC, the GNU Compiler Collection - GNU Project](https://www.gnu.org/software/gcc/)

* #pragma 

  c/c++标准预处理指令，编译指示

  * #pragma pack() 

    *control the **maximum alignment** for subsequently defined **class and union members***

  * #pragma once

* \__attribute__(())

  gcc 编译指示指令

  GCC supports attributes on functions declarations(see [Function Attribute](https://gcc.gnu.org/onlinedocs/gcc-9.4.0/gcc/Function-Attributes.html#Function-Attributes)), variable declarations (see [Variable Attributes](https://gcc.gnu.org/onlinedocs/gcc-9.4.0/gcc/Variable-Attributes.html#Variable-Attributes)), labels (see [Label Attributes](https://gcc.gnu.org/onlinedocs/gcc-9.4.0/gcc/Label-Attributes.html#Label-Attributes)), enumerators (see [Enumerator Attributes](https://gcc.gnu.org/onlinedocs/gcc-9.4.0/gcc/Enumerator-Attributes.html#Enumerator-Attributes)), statements (see [Statement Attributes](https://gcc.gnu.org/onlinedocs/gcc-9.4.0/gcc/Statement-Attributes.html#Statement-Attributes)), and types (see [Type Attributes](https://gcc.gnu.org/onlinedocs/gcc-9.4.0/gcc/Type-Attributes.html#Type-Attributes))

  * \__attribute__((noreturn))

  * \__attribute__((format (archetype, string-index, first-to-check))

  * \__attribute__((aligned(8)))

    * The `aligned` attribute specifies a minimum alignment for the variable or structure field, measured in bytes. 

    * The `aligned` attribute specifies a minimum alignment (in bytes) for variables of the specified type.

  

## GCC/GDB

```
-g		# 生成调试信息

run 	# 运行调试程序
start 	# 开始调试，指令停在main起始位置

list 	# 显示当前位置前后的源码文本
break file_name:line_num 	# 根据文件名和行号设置断点
break file_name:func 		# 根据文件名和函数名设置断点
info breakpoints 			# 显示断点信息
delete 			# 删除所有breakpoints, watchpoints和catchpoints
next 			# 单步调试
step 			# 单步进入
continue		# 继续运行
until line_num	# 运行到目标行
finsh 			# 函数执行完成

display 		# 显示变量
print xxx 		# 查看变量值
x/16fw 			# 查看内存 16 -- 单元个数，f/x -- 浮点格式/十六进制格式， w/g -- 4/8字节

gdb attach <pid>	# 附加进程

set args <params>	# 设置程序运行参数

backtrace			# 栈帧回溯
frame [n]			# 查看当前栈帧

info frame
info args
info locals
info registers

info threads	# 查询线程信息
thread <id>		# 切换线程
thread applay <id> <command>	# 对id线程执行gdb command
thread applay all <command>
set scheduler-locking off		# 关闭线程锁定，默认
set scheduler-locking on		# 锁定当前线程，执行当前线程
```

```
layout:		Changes which TUI windows are displayed.
foucus:		Changes which TUI window is currently active for scrolling. 
foucus/layout src：	显示源代码窗口
focus/layout asm：	显示汇编窗口
focus/layout regs：	显示源代码/汇编和寄存器窗口
focus/layout split：	显示源代码和汇编窗口
focus/layout next：	显示下一个layout
focus/layout prev：	显示上一个layout
Ctrl + L：	刷新窗口
Ctrl + x，	再按1：单窗口模式，显示一个窗口
Ctrl + x，	再按2：双窗口模式，显示两个窗口
Ctrl + x，	再按a：回到传统模式，即退出layout，回到执行layout之前的调试窗口。
```



## Shell

```
echo "$0"

echo "命令状态: $?"
echo "参数数目: $#"
echo "参数列表: $*"
echo "参数列表: $@"

for i in "$*"; do
    echo $i
done

for i in "$@"; do
    echo $i
done
```

```
`： 反引号，命令替换，将shell命令的标准输出插入到命令的任何位置
': 单引号，禁止转移$、\、`
": 双引号，空格字符换
```

## NFS

```
# 服务器端
sudo apt-get install nfs-kernel-server
# 添加挂载的目录及权限
echo "/home/***/linux/nfs *(rw,sync,no_root_squash)" > /etc/exports
# 重启服务
sudo service nfs-kernel-server restart
# 查询挂在目录
showmount -e


# 客户端
sudo apt install nfs-common
# 查看NFS服务器上的导出的共享目录
sudo showmount -e 192.168.105.***
# 挂载共享目录
sudo mount -t nfs 192.168.105.***:/home/***/linux/nfs /mnt

```

# 命令

## sed

```
sed -e 's/\.\([0-9][0-9]\)/\1/g' -e 's/\.\([0-9]\)/0\1/g' -e 's/^[0-9]\{3,4\}$$/&00/'
sed - stream editor for filtering and transforming text
```

## pkg-config

Return metainformation about installed libraries

## dpkg

```
dpkg -l xxx 已安装包信息

dpkg -L xxx 已安装包文件位置

dpkg -s xxx 已安装包信息

dpkg -S xxx 查询文件归属包

dpkg -i xxx.deb 安装包

dpkg -c xxx.deb 查询包安装文件

dpkg --info xxx.deb 查询包信息
```

## find

```
find ./ -iname "pattern"
```

## grep

```
grep -i [-e] "pattern" [file]
```

## fdisk

```
# Display or manipulate a disk partition table.
-l, --list                    display partitions and exit
```

## du

```
# Summarize disk usage of the set of FILEs, recursively for directories
du -sh ./ -- -s, --summarize       display only a total for each argument
```

## df

```c
# Show information about the file system on which each FILE resides, or all file systems by default.
df -hT -- -T, --print-type      print file system type
```

## groups

```
cat /etc/passwd 查看所有用户信息，用户名：密码：用户id：组id：备注：家目录：shell目录
cat /etc/groups 查找用户组信息，组名：口令：组id：组成员
usermode -a -G group-name user-name 将user-name添加到group-name组
```

## apt

```
# 列出所有已安装的包
apt list --installed

# 下载deb包
apt-get download package-name

# 查询已安装deb包信息
apt show package-name

# 查询apt缓存中deb包信息
apt-cache package-name
```

## update-alternatives

```
sudo update-alternative --config xxx 设置库默认版本
```

## readelf

```
readelf -h xxx # -h --file-header       Display the ELF file header
readelf -S xxx # -S --section-headers   Display the sections' header
     				--sections          An alias for --section-headers
readelf -s xxx # -s --syms              Display the symbol table
     				--symbols           An alias for --syms
```

## dmseg

```
dmesg - print or control the kernel ring buffer
```

## free

```c
admin@dev-server:~$ free
              total        used        free      shared  buff/cache   available
Mem:       32885836    18224016      298736        2580    14363084    14207772
Swap:       8388604        4680     8383924
# total = buff/cache + used + free
```

## /proc

```c
# proc文件系统
$ /proc/线程id/maps
$ /proc/meminfo
$ /proc/cpuinfo
$ /proc/interrupts
```

## mtrace

interpret the malloc trace log

## nm

list symbols from object files

## ldd

print shared object dependencies

## gprof

display call graph profile data

## tar

```
# 解压.tbz2文件
tar -xvf ***.tbz2 -C ./
# 清单
tar -tf ***.tbz2
```

## lspci

```
lspci is a utility for displaying information about PCI buses in the system and devices connected to them.
```

## ps

```
ps # ps - report a snapshot of the current processes.
ps axu # To see every process on the system
ps

THREAD DISPLAY
       H      Show threads as if they were processes.

       -L     Show threads, possibly with LWP and NLWP columns.

       m      Show threads after processes.

       -m     Show threads after processes.

       -T     Show threads, possibly with SPID column.
```

## pstree

```
 pstree - display a tree of processes
 pstree -p xxx    # Show PIDs.
```

## pstack

```
NAME
       pstack - print a stack trace of running processes

SYNOPSIS
       pstack pid [...]
```

## top

```
VIRT: virtual memory usage 虚拟内存
RES: resident memory usage 常驻内存
SHR: shared memory usage 共享内存
DATA: 

? or h: for help with windows
f: fields add/remove/order/sort
E/e: summary/task memory scale kb->mb->gb->tb->pb
P: cpu order
M: memory order

top -H 		# Threads-mode toggle When this toggle is On.
top -H -p [pid]

top -d 0.5	# 设置刷新时间间隔
```

# 杂项

* 环境变量设置

  ```
  1. 临时修改：export PATH=/usr/local/cuda-10.2/bin:$PATH
  
  2. 当前用户：vim ~/.bashrc
  
  3. 所有用户：vim /etc/profile
  ```

  
