#lec 3 SPOC Discussion

## 第三讲 启动、中断、异常和系统调用-思考题

## 3.1 BIOS
 1. 比较UEFI和BIOS的区别。

 > UEFI是BIOS的一种升级替代方案。关于BIOS和UEFI二者的比较，如果仅从系统启动原理方面来做比较，UEFI之所以比BIOS强大，是因为UEFI本身已经相当于一个微型操作系统，其带来的便利之处在于：

 > 首先，UEFI已具备文件系统的支持，它能够直接读取FAT分区中的文件。Fat32、NTFS都是常见的文件系统类型。

 > 其次，可开发出直接在UEFI下运行的应用程序，这类程序文件通常以efi结尾。既然UEFI可以直接识别FAT分区中的文件，又有可直接在其中运行的应用程序。

 > BIOS下启动操作系统之前，必须从硬盘上指定扇区读取系统启动代码（包含在主引导记录中），然后从活动分区中引导启动操作系统。而在UEFI下，这些统统都不需要，不再需要主引导记录，不再需要活动分区，不需要任何工具，只要复制安装文件到一个FAT32（主）分区/U盘中，然后从这个分区/U盘启动。

 1. 描述PXE的大致启动流程。

 > 客户端个人电脑开机后， 在 TCP/IP Bootrom 获得控制权之前先做自我测试。

 > Bootprom 送出 BOOTP/DHCP 要求以取得 IP。

 > 如果服务器收到个人电脑所送出的要求，就会送回BOOTP/DHCP回应，内容包括客户端的IP地址，预设网关，及开机映像文件。否则，服务器会忽略这个要求。

 > Bootprom 由 TFTP 通讯协议从服务器下载开机映像文件。

 > 个人电脑通过这个开机映像文件开机， 这个开机文件可以只是单纯的开机程式也可以是操作系统。

 > 开机映像文件将包含kernel loader及压缩过的kernel，此kernel将支持NTFS root系统。

 > 远程客户端根据下载的文件启动机器。
 
## 3.2 系统启动流程
 1. 了解NTLDR的启动流程。
 
 ```
1、电源自检程序开始运行
2、主引导记录被装入内存，并且程序开始执行
3、活动分区的引导扇区被装入内存
4、NTLDR从引导扇区被装入并初始化
5、将处理器的实模式改为32位平滑内存模式
6、NTLDR开始运行适当的小文件系统驱动程序。小文件系统驱动程序是建立在NTLDR内部的，它能读FAT或NTFS。
7、NTLDR读boot.ini文件
8、NTLDR装载所选操作系统
9.Ntdetect搜索计算机硬件并将列表传送给NTLDR，以便将这些信息写进\\HKE Y_LOCAL_MACHINE\HARDWARE中。
10.然后NTLDR装载Ntoskrnl.exe，Hal.dll和系统信息集合。
11.Ntldr搜索系统信息集合，并装载设备驱动配置以便设备在启动时开始工作
12.Ntldr把控制权交给Ntoskrnl.exe，这时,启动程序结束,装载阶段开始
 ```

 > 参考资料：http://baike.baidu.com/link?url=RKUFAW3zdDOU7Eeuwnj1CQcH9cKUayXI36KYtdyLBYySHeX0huEjl8CAiKtKRW2Liqsk5ZAnDG71tUW-nQe-p_
 
 1. 了解GRUB的启动流程。
 
 > 装载stage1

 > 装载stage1.5

 > 装载stage2

 > 读取/boot/grub.conf文件并显示启动菜单

 > 装载所选的kernel和initrd文件到内存中

 > 参考资料：http://wenku.baidu.com/link?url=mMdriP6cVJoPu_kyguFMofHMkEai9S2w7lrm3GaiUi2WhS6WFDUW01ptUoikTVy62Uoi3BRJ6zc8ANtXYGKtLfEzBggghWmJZfUP5FbyvhG

 1. 比较NTLDR和GRUB的功能有差异。

 > GNU GRUB（GRand Unified Bootloader）是一个来自GNU项目的多操作系统启动程序，而引导扇区NTLDR（NT Loader）是Windows NT/windows 2000/windows XP/windows server 2003的引导程序。
 
 1. 了解u-boot的功能。
 
 > 系统引导支持NFS挂载、RAMDISK(压缩或非压缩)形式的根文件系统；支持NFS挂载、从FLASH中引导压缩或非压缩系统内核；

 > 基本辅助功能强大的操作系统接口功能；可灵活设置、传递多个关键参数给操作系统，适合系统在不同开发阶段的调试要求与产品发布，尤以Linux支持最为强劲；支持目标板环境参数多种存储方式，如FLASH、NVRAM、EEPROM；

 > CRC32校验可校验FLASH中内核、RAMDISK镜像文件是否完好；

 > 设备驱动串口、SDRAM、FLASH、以太网、LCD、NVRAM、EEPROM、键盘、USB、PCMCIA、PCI、RTC等驱动支持；

 > 上电自检功能SDRAM、FLASH大小自动检测；SDRAM故障检测；CPU型号；

 > 特殊功能XIP内核引导；

 > 参考资料：http://baike.baidu.com/link?url=UfBPpWF2eZrlLX_VGKL83Kw10e1ybo51O16R_hMaX9Pb3FVmRxTOpSlw6lblvCs0NfbKejAsrIxHzwy7GaJ4_q#3

## 3.3 中断、异常和系统调用比较
 1. 举例说明Linux中有哪些中断，哪些异常？
 
 > 中断：键盘输入。

 > 异常：整数除以零，页缺失。
 
 1. Linux的系统调用有哪些？大致的功能分类有哪些？  (w2l1)

 > Linux的系统调用大致功能分为八类：进程控制、文件系统控制、系统控制、内存管理、网络管理、socket控制、用户管理、进程间通信。
 
 > 控制硬件——系统调用往往作为硬件资源和用户空间的抽象接口，比如读写文件时用到的write/read调用。
 
 > 设置系统状态或读取内核数据——因为系统调用是用户空间和内核的唯一通讯手段，所以用户设置系统状态，比如开/关某项内核服务（设置某个内核变量），或读取内核数据都必须通过系统调用。比如getpgid、getpriority、setpriority、sethostname。
 
 > 进程管理——一系统调用接口是用来保证系统中进程能以多任务在虚拟内存环境下得以运行。比如 fork、clone、execve、exit等。
 
 > 列举如下：

 ```
一、进程控制：
fork	创建一个新进程
clone	按指定条件创建子进程
execve	运行可执行文件
exit	中止进程
_exit	立即中止当前进程
getdtablesize	进程所能打开的最大文件数
getpgid	获取指定进程组标识号
setpgid	设置指定进程组标志号
getpgrp	获取当前进程组标识号
setpgrp	设置当前进程组标志号
getpid	获取进程标识号
getppid	获取父进程标识号
getpriority	获取调度优先级
setpriority	设置调度优先级
modify_ldt	读写进程的本地描述表
nanosleep	使进程睡眠指定的时间
nice	改变分时进程的优先级
pause	挂起进程，等待信号
personality	设置进程运行域
prctl	对进程进行特定操作
ptrace	进程跟踪
sched_get_priority_max	取得静态优先级的上限
sched_get_priority_min	取得静态优先级的下限
sched_getparam	取得进程的调度参数
sched_getscheduler	取得指定进程的调度策略
sched_rr_get_interval	取得按RR算法调度的实时进程的时间片长度
sched_setparam	设置进程的调度参数
sched_setscheduler	设置指定进程的调度策略和参数
sched_yield	进程主动让出处理器,并将自己等候调度队列队尾
vfork	创建一个子进程，以供执行新程序，常与execve等同时使用
wait	等待子进程终止
wait3	参见wait
waitpid	等待指定子进程终止
wait4	参见waitpid
capget	获取进程权限
capset	设置进程权限
getsid	获取会晤标识号
setsid	设置会晤标识号

二、文件系统控制
1、文件读写操作
fcntl	文件控制
open	打开文件
creat	创建新文件
close	关闭文件描述字
read	读文件
write	写文件
readv	从文件读入数据到缓冲数组中
writev	将缓冲数组里的数据写入文件
pread	对文件随机读
pwrite	对文件随机写
lseek	移动文件指针
_llseek	在64位地址空间里移动文件指针
dup	复制已打开的文件描述字
dup2	按指定条件复制文件描述字
flock	文件加/解锁
poll	I/O多路转换
truncate	截断文件
ftruncate	参见truncate
umask	设置文件权限掩码
fsync	把文件在内存中的部分写回磁盘

2、文件系统操作
access	确定文件的可存取性
chdir	改变当前工作目录
fchdir	参见chdir
chmod	改变文件方式
fchmod	参见chmod
chown	改变文件的属主或用户组
fchown	参见chown
lchown	参见chown
chroot	改变根目录
stat	取文件状态信息
lstat	参见stat
fstat	参见stat
statfs	取文件系统信息
fstatfs	参见statfs
readdir	读取目录项
getdents	读取目录项
mkdir	创建目录
mknod	创建索引节点
rmdir	删除目录
rename	文件改名
link	创建链接
symlink	创建符号链接
unlink	删除链接
readlink	读符号链接的值
mount	安装文件系统
umount	卸下文件系统
ustat	取文件系统信息
utime	改变文件的访问修改时间
utimes	参见utime
quotactl	控制磁盘配额

三、系统控制
ioctl	I/O总控制函数
_sysctl	读/写系统参数
acct	启用或禁止进程记账
getrlimit	获取系统资源上限
setrlimit	设置系统资源上限
getrusage	获取系统资源使用情况
uselib	选择要使用的二进制函数库
ioperm	设置端口I/O权限
iopl	改变进程I/O权限级别
outb	低级端口操作
reboot	重新启动
swapon	打开交换文件和设备
swapoff	关闭交换文件和设备
bdflush	控制bdflush守护进程
sysfs	取核心支持的文件系统类型
sysinfo	取得系统信息
adjtimex	调整系统时钟
alarm	设置进程的闹钟
getitimer	获取计时器值
setitimer	设置计时器值
gettimeofday	取时间和时区
settimeofday	设置时间和时区
stime	设置系统日期和时间
time	取得系统时间
times	取进程运行时间
uname	获取当前UNIX系统的名称、版本和主机等信息
vhangup	挂起当前终端
nfsservctl	对NFS守护进程进行控制
vm86	进入模拟8086模式
create_module	创建可装载的模块项
delete_module	删除可装载的模块项
init_module	初始化模块
query_module	查询模块信息
*get_kernel_syms	取得核心符号,已被query_module代替

四、内存管理
brk	改变数据段空间的分配
sbrk	参见brk
mlock	内存页面加锁
munlock	内存页面解锁
mlockall	调用进程所有内存页面加锁
munlockall	调用进程所有内存页面解锁
mmap	映射虚拟内存页
munmap	去除内存页映射
mremap	重新映射虚拟内存地址
msync	将映射内存中的数据写回磁盘
mprotect	设置内存映像保护
getpagesize	获取页面大小
sync	将内存缓冲区数据写回硬盘
cacheflush	将指定缓冲区中的内容写回磁盘

五、网络管理
getdomainname	取域名
setdomainname	设置域名
gethostid	获取主机标识号
sethostid	设置主机标识号
gethostname	获取本主机名称
sethostname	设置主机名称

六、socket控制
socketcall	socket系统调用
socket	建立socket
bind	绑定socket到端口
connect	连接远程主机
accept	响应socket连接请求
send	通过socket发送信息
sendto	发送UDP信息
sendmsg	参见send
recv	通过socket接收信息
recvfrom	接收UDP信息
recvmsg	参见recv
listen	监听socket端口
select	对多路同步I/O进行轮询
shutdown	关闭socket上的连接
getsockname	取得本地socket名字
getpeername	获取通信对方的socket名字
getsockopt	取端口设置
setsockopt	设置端口参数
sendfile	在文件或端口间传输数据
socketpair	创建一对已联接的无名socket

七、用户管理
getuid	获取用户标识号
setuid	设置用户标志号
getgid	获取组标识号
setgid	设置组标志号
getegid	获取有效组标识号
setegid	设置有效组标识号
geteuid	获取有效用户标识号
seteuid	设置有效用户标识号
setregid	分别设置真实和有效的的组标识号
setreuid	分别设置真实和有效的用户标识号
getresgid	分别获取真实的,有效的和保存过的组标识号
setresgid	分别设置真实的,有效的和保存过的组标识号
getresuid	分别获取真实的,有效的和保存过的用户标识号
setresuid	分别设置真实的,有效的和保存过的用户标识号
setfsgid	设置文件系统检查时使用的组标识号
setfsuid	设置文件系统检查时使用的用户标识号
getgroups	获取后补组标志清单
setgroups	设置后补组标志清单

八、进程间通信
ipc	进程间通信总控制调用
1、信号
sigaction	设置对指定信号的处理方法
sigprocmask	根据参数对信号集中的信号执行阻塞/解除阻塞等操作
sigpending	为指定的被阻塞信号设置队列
sigsuspend	挂起进程等待特定信号
signal	参见signal
kill	向进程或进程组发信号
*sigblock	向被阻塞信号掩码中添加信号,已被sigprocmask代替
*siggetmask	取得现有阻塞信号掩码,已被sigprocmask代替
*sigsetmask	用给定信号掩码替换现有阻塞信号掩码,已被sigprocmask代替
*sigmask	将给定的信号转化为掩码,已被sigprocmask代替
*sigpause	作用同sigsuspend,已被sigsuspend代替
sigvec	为兼容BSD而设的信号处理函数,作用类似sigaction
ssetmask	ANSI C的信号处理函数,作用类似sigaction

2、消息
msgctl	消息控制操作
msgget	获取消息队列
msgsnd	发消息
msgrcv	取消息

3、管道
pipe	创建管道

4、信号量
semctl	信号量控制
semget	获取一组信号量
semop	信号量操作

5、共享内存
shmctl	控制共享内存
shmget	获取共享内存
shmat	连接共享内存
shmdt	拆卸共享内存
 ```

 > 参考资料：http://www.ibm.com/developerworks/cn/linux/kernel/syscall/part1/appendix.html、http://blog.csdn.net/orange_os/article/details/7485069。

 ```
  + 采分点：说明了Linux的大致数量（上百个），说明了Linux系统调用的主要分类（文件操作，进程管理，内存管理等）
  - 答案没有涉及上述两个要点；（0分）
  - 答案对上述两个要点中的某一个要点进行了正确阐述（1分）
  - 答案对上述两个要点进行了正确阐述（2分）
  - 答案除了对上述两个要点都进行了正确阐述外，还进行了扩展和更丰富的说明（3分）
 ```
 
 1. 以ucore lab8的answer为例，uCore的系统调用有哪些？大致的功能分类有哪些？(w2l1)
 
 > ucore的系统调用有sys_exit,sys_fork,sys_wait,sys_exec,sys_yield,sys_kill,sys_getpid,sys_putc,sys_pgdir,sys_gettime,sys_lab6_set_priority,sys_sleep,sys_open,sys_close,sys_read,sys_write,sys_seek,sys_fstat,sys_fsync,sys_getcwd,sys_getdirentry,sys_dup等。

 > 大致的功能分类有文件操作，进程管理，内存管理等。

 > 这些系统调用的向量存放在labcodes_answer\lab8_result\kern\syscall\syscall.c的名为syscalls的函数指针数组里。
 
 ```
  + 采分点：说明了ucore的大致数量（二十几个），说明了ucore系统调用的主要分类（文件操作，进程管理，内存管理等）
  - 答案没有涉及上述两个要点；（0分）
  - 答案对上述两个要点中的某一个要点进行了正确阐述（1分）
  - 答案对上述两个要点进行了正确阐述（2分）
  - 答案除了对上述两个要点都进行了正确阐述外，还进行了扩展和更丰富的说明（3分）
 ```
 
## 3.4 linux系统调用分析
 1. 通过分析[lab1_ex0](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab1/lab1-ex0.md)了解Linux应用的系统调用编写和含义。(w2l1)

 > `objdump`的大致用途：-d指令反汇编可执行文件中的需要执行指令的那些段。-f指令显示文件头信息。

 > `nm`的大致用途：报告可执行程序的信息，如库或对象名、符号名称、符号类型等、值、大小等。

 > `file`的大致用途：检测文件类型。

 > 系统调用是由操作系统实现提供的所有系统调用所构成的集合即程序接口或应用编程接口(API)。

 > 编写系统调用时，通过系统调用号判断进入相应的函数。具体的系统调用函数主要是一些底层的硬件操作，很多都需要用汇编来编写。

 ```
  + 采分点：说明了objdump，nm，file的大致用途，说明了系统调用的具体含义
  - 答案没有涉及上述两个要点；（0分）
  - 答案对上述两个要点中的某一个要点进行了正确阐述（1分）
  - 答案对上述两个要点进行了正确阐述（2分）
  - 答案除了对上述两个要点都进行了正确阐述外，还进行了扩展和更丰富的说明（3分）
 
 ```

 1. 通过调试[lab1_ex1](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab1/lab1-ex1.md)了解Linux应用的系统调用执行过程。(w2l1)

 > 用`strace`命令显示了运行该打印hello world的程序用到的所有系统调用简略信息，包括每种系统调用发生的次数、用时、用时比例等：
 
 ```
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 58.35    0.000346         346         1           write
 10.62    0.000063           8         8           mmap
  8.09    0.000048          12         4           mprotect
  7.08    0.000042          42         1           execve
  5.06    0.000030          30         1           munmap
  4.22    0.000025          13         2           open
  3.54    0.000021           7         3         3 access
  1.18    0.000007           2         3           fstat
  0.84    0.000005           5         1           read
  0.51    0.000003           2         2           close
  0.34    0.000002           2         1           arch_prctl
  0.17    0.000001           1         1           brk
------ ----------- ----------- --------- --------- ----------------
100.00    0.000593                    28         3 total
 ```
 
 > `more /proc/interrupts`展示了系统调用的顺序。首先是动态加载和分配空间（brk、mmap2），然后是搜索检查标准库（open、read、fstat），接着加载标准库并初始化（mmap、arch_prctl、mprotect），最后执行write函数并退出。
 
 ```
  + 采分点：说明了strace的大致用途，说明了系统调用的具体执行过程（包括应用，CPU硬件，操作系统的执行过程）
  - 答案没有涉及上述两个要点；（0分）
  - 答案对上述两个要点中的某一个要点进行了正确阐述（1分）
  - 答案对上述两个要点进行了正确阐述（2分）
  - 答案除了对上述两个要点都进行了正确阐述外，还进行了扩展和更丰富的说明（3分）
 ```
 
## 3.5 ucore系统调用分析
 1. ucore的系统调用中参数传递代码分析。
 1. ucore的系统调用中返回结果的传递代码分析。
 1. 以ucore lab8的answer为例，分析ucore 应用的系统调用编写和含义。
 1. 以ucore lab8的answer为例，尝试修改并运行ucore OS kernel代码，使其具有类似Linux应用工具`strace`的功能，即能够显示出应用程序发出的系统调用，从而可以分析ucore应用的系统调用执行过程。
 
## 3.6 请分析函数调用和系统调用的区别
 1. 请从代码编写和执行过程来说明。
   1. 说明`int`、`iret`、`call`和`ret`的指令准确功能

   > `int`：先预备好系统调用的参数和系统调用号，用int指令触发系统调用。跳转到中断处理程序，并进入内核态。
   > `iret`：在`ret`指令的同时，完成从内核态到用户态的切换。
   > `call`：段指针寄存器的值、指令指针寄存器压栈，跳转到子函数处执行。
   > `ret`：子程序返回，恢复父函数段指针寄存器的值，恢复栈顶指针寄存器的值（减2倍指针长度），返回到父函数的调用处执行。

