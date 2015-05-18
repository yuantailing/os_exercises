# lab8 文件系统 (lec 22) spoc 思考题


- 有"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的ucore_code和os_exercises的git repo上。

## 个人思考题

### 总体介绍
 1. 文件系统中的文件、目录、索引节点和安装点这几种数据结构分别支持些什么操作？
 2. 请简要描述ucore文件系统支持的文件系统抽象

 > 文件、目录、索引节点和安装点

### ucore 文件系统架构

 1. 请简要阐述ucore 文件系统架构的四个组成部分

 > 系统调用接口、VFS、SFS和I/O接口

 2. 请简要说明进程proc_struct、文件file、inode之间的关系。 
 
 3. ucore中的进程打开文件表和系统打开文件表对应到具体的哪个数据结构上？

### Simple File System分析

 1. SFS在硬盘上的四大部分主要是什么，有何作用？
 
 > superblock, root-dir inode, freeman, data block

 2. 硬盘上的SFS是如何加载到ucore中并初始化的？
 3. 硬盘上的inode和内存中的inode的关系和区别是什么?
 4. 描述file, dir, inode在内存和磁盘上的格式和相关操作。

### Virtual File System分析

 1. file数据结构的主要内容是什么？与进程的关系是什么？
 2. inode数据结构的主要内容是什么？与file的数据结构的关系是什么？
 3. inode_ops包含哪些与文件相关的操作？
 4. VFS是如何把键盘、显示输出和磁盘文件统一到一个系统调用访问框架下的？ 

### I/O 设备接口分析

 1. device数据结构的主要内容是什么？与fs的关系是什么？与inode的关系是什么？
 2. 比较ucore中I/O接口、SFS文件系统接口和文件系统的系统调用接口的操作函数有什么异同？
 
## 小组思考题

1. (spoc) 理解文件访问的执行过程，即在ucore运行过程中通过`cprintf`函数来完整地展现出来读一个文件在ucore中的整个执行过程，(越全面细致越好)
完成代码填写，并形成spoc练习报告，需写练习报告和简单编码，完成后放到git server 对应的git repo中

> ·详细全面的跟踪过程：

```
(THU.CST) os is loading ...

Special kernel symbols:
  entry  0xc010002a (phys)
  etext  0xc01157e9 (phys)
  edata  0xc014fed0 (phys)
  end    0xc0154204 (phys)
Kernel executable memory footprint: 337KB
ebp:0xc014ef38 eip:0xc0100bd2 args:0xc010030a 0xffff6ad9 0xc014ef68 0xc01000dd 
    kern/debug/kdebug.c:350: print_stackframe+21
ebp:0xc014ef48 eip:0xc0100ec1 args:0x00000000 0x00000000 0x00000000 0xc0100360 
    kern/debug/kmonitor.c:129: mon_backtrace+10
ebp:0xc014ef68 eip:0xc01000dd args:0x00000000 0xc014ef90 0xffff0000 0xc014ef94 
    kern/init/init.c:60: grade_backtrace2+33
ebp:0xc014ef88 eip:0xc0100106 args:0x00000000 0xffff0000 0xc014efb4 0x0000002a 
    kern/init/init.c:65: grade_backtrace1+38
ebp:0xc014efa8 eip:0xc0100124 args:0x00000000 0xc010002a 0xffff0000 0x0000001d 
    kern/init/init.c:70: grade_backtrace0+23
ebp:0xc014efc8 eip:0xc0100149 args:0xc011581c 0xc0115800 0x00004334 0x00000000 
    kern/init/init.c:75: grade_backtrace+34
ebp:0xc014eff8 eip:0xc010007f args:0x00000000 0x00000000 0x0000ffff 0x40cf9a00 
    kern/init/init.c:33: kern_init+84
memory management: default_pmm_manager
e820map:
  memory: 0009fc00, [00000000, 0009fbff], type = 1.
  memory: 00000400, [0009fc00, 0009ffff], type = 2.
  memory: 00010000, [000f0000, 000fffff], type = 2.
  memory: 07efe000, [00100000, 07ffdfff], type = 1.
  memory: 00002000, [07ffe000, 07ffffff], type = 2.
  memory: 00040000, [fffc0000, ffffffff], type = 2.
check_alloc_page() succeeded!
check_pgdir() succeeded!
check_boot_pgdir() succeeded!
-------------------- BEGIN --------------------
PDE(0e0) c0000000-f8000000 38000000 urw
  |-- PTE(38000) c0000000-f8000000 38000000 -rw
PDE(001) fac00000-fb000000 00400000 -rw
  |-- PTE(000e0) faf00000-fafe0000 000e0000 urw
  |-- PTE(00001) fafeb000-fafec000 00001000 -rw
--------------------- END ---------------------
use SLOB allocator
check_slab() success
kmalloc_init() succeeded!
check_vma_struct() succeeded!
page fault at 0x00000100: K/W [no page found].
check_pgfault() succeeded!
check_vmm() succeeded.
sched class: stride_scheduler
ide 0:      10000(sectors), 'QEMU HARDDISK'.
ide 1:     262144(sectors), 'QEMU HARDDISK'.
ide 2:     262144(sectors), 'QEMU HARDDISK'.
SWAP: manager = fifo swap manager
BEGIN check_swap: count 31907, total 31907
setup Page Table for vaddr 0X1000, so alloc a page
setup Page Table vaddr 0~4MB OVER!
set up init env for check_swap begin!
page fault at 0x00001000: K/W [no page found].
page fault at 0x00002000: K/W [no page found].
page fault at 0x00003000: K/W [no page found].
page fault at 0x00004000: K/W [no page found].
set up init env for check_swap over!
write Virt Page c in fifo_check_swap
write Virt Page a in fifo_check_swap
write Virt Page d in fifo_check_swap
write Virt Page b in fifo_check_swap
write Virt Page e in fifo_check_swap
page fault at 0x00005000: K/W [no page found].
swap_out: i 0, store page in vaddr 0x1000 to disk swap entry 2
write Virt Page b in fifo_check_swap
write Virt Page a in fifo_check_swap
page fault at 0x00001000: K/W [no page found].
do pgfault: ptep c035b004, pte 200
swap_out: i 0, store page in vaddr 0x2000 to disk swap entry 3
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
swap_in: load disk swap entry 2 with swap_page in vadr 0x1000
write Virt Page b in fifo_check_swap
page fault at 0x00002000: K/W [no page found].
do pgfault: ptep c035b008, pte 300
swap_out: i 0, store page in vaddr 0x3000 to disk swap entry 4
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
swap_in: load disk swap entry 3 with swap_page in vadr 0x2000
write Virt Page c in fifo_check_swap
page fault at 0x00003000: K/W [no page found].
do pgfault: ptep c035b00c, pte 400
swap_out: i 0, store page in vaddr 0x4000 to disk swap entry 5
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
swap_in: load disk swap entry 4 with swap_page in vadr 0x3000
write Virt Page d in fifo_check_swap
page fault at 0x00004000: K/W [no page found].
do pgfault: ptep c035b010, pte 500
swap_out: i 0, store page in vaddr 0x5000 to disk swap entry 6
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
swap_in: load disk swap entry 5 with swap_page in vadr 0x4000
count is 5, total is 5
check_swap() succeeded!
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
sfs: mount: 'simple file system' (262/32506/32768)
vfs: mount disk0.
++ setup timer interrupts
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
I am No.4 philosopher_condvar
Iter 1, No.4 philosopher_condvar is thinking
I am No.3 philosopher_condvar
Iter 1, No.3 philosopher_condvar is thinking
I am No.2 philosopher_condvar
Iter 1, No.2 philosopher_condvar is thinking
I am No.1 philosopher_condvar
Iter 1, No.1 philosopher_condvar is thinking
I am No.0 philosopher_condvar
Iter 1, No.0 philosopher_condvar is thinking
I am No.4 philosopher_sema
Iter 1, No.4 philosopher_sema is thinking
I am No.3 philosopher_sema
Iter 1, No.3 philosopher_sema is thinking
I am No.2 philosopher_sema
Iter 1, No.2 philosopher_sema is thinking
I am No.1 philosopher_sema
Iter 1, No.1 philosopher_sema is thinking
I am No.0 philosopher_sema
Iter 1, No.0 philosopher_sema is thinking
kernel_execve: pid = 2, name = "sh".
called: syscall(void)
called: sysfile_open(const char *__path, uint32_t open_flags)
called: file_open(char *path, uint32_t open_flags)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: vfs_open(char *path, uint32_t open_flags, struct inode **node_store)
called: sfs_lookup(struct inode *node, char *path, struct inode **node_store)
called: sfs_lookup_once(struct sfs_fs *sfs, struct sfs_inode *sin, const char *name, struct inode **node_store, int *slot)
called: sfs_dirent_search_nolock(struct sfs_fs *sfs, struct sfs_inode *sin, const char *name, uint32_t *ino_store, int *slot, int *empty_slot)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: sfs_rbuf(struct sfs_fs *sfs, void *buf, size_t len, uint32_t blkno, off_t offset)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: syscall(void)
called: sys_open(uint32_t arg[])
called: sysfile_open(const char *__path, uint32_t open_flags)
called: file_open(char *path, uint32_t open_flags)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: vfs_open(char *path, uint32_t open_flags, struct inode **node_store)
called: syscall(void)
called: sys_open(uint32_t arg[])
called: sysfile_open(const char *__path, uint32_t open_flags)
called: file_open(char *path, uint32_t open_flags)
called: ide_read_secs(unsigned short ideno, uint32_t secno, void *dst, size_t nsecs)
called: vfs_open(char *path, uint32_t open_flags, struct inode **node_store)
called: syscall(void)
called: sys_write(uint32_t arg[])
ucalled: syscall(void)
called: sys_write(uint32_t arg[])
scalled: syscall(void)
called: sys_write(uint32_t arg[])
ecalled: syscall(void)
called: sys_write(uint32_t arg[])
rcalled: syscall(void)
called: sys_write(uint32_t arg[])
 called: syscall(void)
called: sys_write(uint32_t arg[])
scalled: syscall(void)
called: sys_write(uint32_t arg[])
hcalled: syscall(void)
called: sys_write(uint32_t arg[])
 called: syscall(void)
called: sys_write(uint32_t arg[])
icalled: syscall(void)
called: sys_write(uint32_t arg[])
scalled: syscall(void)
called: sys_write(uint32_t arg[])
 called: syscall(void)
called: sys_write(uint32_t arg[])
rcalled: syscall(void)
called: sys_write(uint32_t arg[])
ucalled: syscall(void)
called: sys_write(uint32_t arg[])
ncalled: syscall(void)
called: sys_write(uint32_t arg[])
ncalled: syscall(void)
called: sys_write(uint32_t arg[])
icalled: syscall(void)
called: sys_write(uint32_t arg[])
ncalled: syscall(void)
called: sys_write(uint32_t arg[])
gcalled: syscall(void)
called: sys_write(uint32_t arg[])
!called: syscall(void)
called: sys_write(uint32_t arg[])
!called: syscall(void)
called: sys_write(uint32_t arg[])
!called: syscall(void)
called: sys_write(uint32_t arg[])
$called: syscall(void)
called: sys_write(uint32_t arg[])
 called: syscall(void)
called: sys_read(uint32_t arg[])
```

> 对应的Git repo：[https://github.com/yuantailing/ucore_lab/tree/spoc/labcodes_answer/lab8_result](https://github.com/yuantailing/ucore_lab/tree/spoc/labcodes_answer/lab8_result)

2. （spoc） 在下面的实验代码的基础上，实现基于文件系统的pipe IPC机制

### 练习用的[lab8 spoc exercise project source code](https://github.com/chyyuu/ucore_lab/tree/master/labcodes_answer/lab8_result)
