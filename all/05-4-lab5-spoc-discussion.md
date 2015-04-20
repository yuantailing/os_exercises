# lab5 spoc 思考题

- 有"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的ucore_code和os_exercises的git repo上。


## 个人思考题

### 总体介绍

 - 第一个用户进程创建有什么特殊的？
 - 系统调用的参数传递过程？
 - getpid的返回值放在什么地方了？

### 进程的内存布局

 - 尝试在进程运行过程中获取内核堆栈和用户堆栈的调用栈？
 - 尝试在进程运行过程中获取内核空间中各进程相同的页表项（代码段）和不同的页表项（内核堆栈）？

### 执行ELF格式的二进制代码-do_execve的实现

 - 在do_execve中进程清空父进程时，当前进程是哪一个？在什么时候开始使用新加载进程的地址空间？
 - 新加载进程的第一级页表的建立代码在哪？

### 执行ELF格式的二进制代码-load_icode的实现

 - 第一个内核线程和第一个用户进程的创建有什么不同？
 - 尝试跟踪分析新创建的用户进程的开始执行过程？

### 进程复制

 - 为什么新进程的内核堆栈可以先于进程地址空间复制进行创建？
 - 进程复制的代码在哪？复制了哪些内容？
 - 进程复制过程中有哪些修改？为什么要修改？

### 内存管理的copy-on-write机制
 - 什么是写时复制？
 - 写时复制的页表在什么时候进行复制？共享地址空间和写时复制有什么不同？

## 小组练习与思考题

### (1)(spoc) 在真实机器的u盘上启动并运行ucore lab,

请准备一个空闲u盘，然后请参考如下网址完成练习

https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab1/lab1-boot-with-grub2-in-udisk.md

(报告可课后完成)请理解grub multiboot spec的含义，并分析ucore_lab是如何实现符合grub multiboot spec的，并形成spoc练习报告。

### (2)(spoc) 理解用户程的生命周期。

> 需写练习报告和简单编码，完成后放到git server 对应的git repo中

### 练习用的[lab5 spoc exercise project source code](https://github.com/chyyuu/ucore_lab/tree/master/related_info/lab5/lab5-spoc-discuss)

#### 掌握知识点
1. 用户进程的启动、运行、就绪、等待、退出
2. 用户进程的管理与简单调度
3. 用户进程的上下文切换过程
4. 用户进程的特权级切换过程
5. 用户进程的创建过程并完成资源占用
6. 用户进程的退出过程并完成资源回收

> 注意，请关注：内核如何创建用户进程的？用户进程是如何在用户态开始执行的？用户态的堆栈是保存在哪里的？

阅读代码，在现有基础上再增加一个用户进程A，并通过增加cprintf函数到ucore代码中，
能够把个人思考题和上述知识点中的内容展示出来：即在ucore运行过程中通过`cprintf`函数来完整地展现出来进程A相关的动态执行和内部数据/状态变化的细节。(约全面细致约好)

请完成如下练习，完成代码填写，并形成spoc练习报告

> 自己的输出结果
> ```
I am the child.
ebp:0xc03adec8 eip:0xc0101f62 args:0x00000003 0xafffff98 0xc03adfb4 0x00000000 
    kern/debug/kdebug.c:350: print_stackframe+21
ebp:0xc03adef8 eip:0xc010c54e args:0xc03adf24 0x0000000a 0x00000000 0x00000000 
    kern/syscall/syscall.c:24: sys_wait+10
ebp:0xc03adf48 eip:0xc010c6a2 args:0x00000000 0x00000000 0x00000000 0x00000000 
    kern/syscall/syscall.c:95: syscall+115
ebp:0xc03adf78 eip:0xc0103d39 args:0xc03adfb4 0x00000000 0x00800020 0x0000001b 
    kern/trap/trap.c:216: trap_dispatch+299
ebp:0xc03adfa8 eip:0xc0103eae args:0xc03adfb4 0x00800306 0x00000003 0xafffff44 
    kern/trap/trap.c:287: trap+74
ebp:0xafffff44 eip:0xc0103f03 args:0x00000003 0x00000003 0xafffff98 0xafffff78 
    kern/trap/trapentry.S:24: <unknown>+0
ebp:0xafffff58 eip:0x00800232 args:0x00000003 0xafffff98 0xafffff84 0x0000001f 
    user/libs/syscall.c:45: sys_wait+31
ebp:0xafffff78 eip:0x00800306 args:0x00000003 0xafffff98 0x00000000 0x00000000 
    user/libs/ulib.c:25: waitpid+23
ebp:0xafffffa8 eip:0x0080109a args:0x00000000 0x00000000 0x00000000 0x00000000 
    user/exit.c:28: main+264
ebp:0xafffffd8 eip:0x0080034d args:0x00000000 0x00000000 0x00000000 0x00000000 
    user/libs/umain.c:7: umain+10
-------------------- BEGIN --------------------
PDE(001) 00000000-00400000 00400000 urw
  |-- PTE(00004) 00200000-00204000 00004000 urw
PDE(001) 00800000-00c00000 00400000 urw
  |-- PTE(00002) 00800000-00802000 00002000 ur-
  |-- PTE(00001) 00802000-00803000 00001000 urw
PDE(001) afc00000-b0000000 00400000 urw
  |-- PTE(00004) afffc000-b0000000 00004000 urw
PDE(0e0) c0000000-f8000000 38000000 urw
  |-- PTE(38000) c0000000-f8000000 38000000 -rw
PDE(001) fac00000-fb000000 00400000 -rw
  |-- PTE(00001) fac00000-fac01000 00001000 urw
  |-- PTE(00001) fac02000-fac03000 00001000 urw
  |-- PTE(00001) faebf000-faec0000 00001000 urw
  |-- PTE(000e0) faf00000-fafe0000 000e0000 urw
  |-- PTE(00001) fafeb000-fafec000 00001000 -rw
--------------------- END ---------------------
```

> 代码在github上的链接
> [https://github.com/yuantailing/ucore_lab/tree/master/related_info/lab5/lab5-spoc-discuss](https://github.com/yuantailing/ucore_lab/tree/master/related_info/lab5/lab5-spoc-discuss)
