# lab2 SPOC思考题

NOTICE
- 有"w4l1"标记的题是助教要提交到学堂在线上的。
- 有"w4l1"和"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的git repo上。
- 有"hard"标记的题有一定难度，鼓励实现。
- 有"easy"标记的题很容易实现，鼓励实现。
- 有"midd"标记的题是一般水平，鼓励实现。

## 个人思考题
---

x86保护模式中权限管理无处不在，下面哪些时候要检查访问权限()  (w4l1)
- [x] 内存寻址过程中
- [x] 代码跳转过程中
- [x] 中断处理过程中
- [ ] ALU计算过程中
 
> 前三个需要。这里假定ALU完成计算所需数据都已经在CPU内部了。


请描述ucore OS建立页机制的准备工作包括哪些步骤？ (w4l1) 
```
  + 采分点：说明了ucore OS在让页机制正常工作的主要准备工作
  - 答案没有涉及如下3点；（0分）
  - 描述了对GDT的初始化,完成了段机制（1分）
  - 除第二点外进一步描述了对物理内存的探测和空闲物理内存的管理。（2分）
  - 除上述两点外，进一步描述了页表建立初始过程和设置CR0控寄存器某位来使能页（3分）

 ```
- [x]  

> 1. 用ucore基于连续内存的内存分配算法与机制，分配4K作为页目录表
  2. 初始化页
  3. 建立页表，将0xC0000000~0xF8000000映射到0x00000000~0x38000000
  4. 建立页表，将0x00000000~0x00100000映射到0x00000000~0x00100000
  5. 将CR0的第31位置1，启动页机制；将CR3置为页目录起始地址
  6. 更新GDT
  7. 取消0x00000000~0x00100000到0x00000000~0x00100000的映射

---

## 小组思考题
---

（1）（spoc）请用lab1实验的基准代码（即没有修改的需要填空的源代码）来做如下实验： 执行`make qemu`，会得到一个输出结果，请给出合理的解释：为何qemu退出了？【提示】需要对qemu增加一些用于基于执行过的参数，重点是分析其执行的指令和产生的中断或异常。 

- 因为没有设置中断处理向量，所以在发生中断时出错退出了。

> 

（2）(spoc)假定你已经完成了lab1的实验,接下来是对lab1的中断处理的回顾：请把你的学号对37(十进制)取模，得到一个数x（x的范围是-1<x<37），然后在你的答案的基础上，修init.c中的kern_init函数，在大约36行处，即

```
    intr_enable();              // enable irq interrupt
```
语句之后，加入如下语句(把x替换为你学号 mod 37得的值)：
```
    asm volatile ("int $x");
```    
然后，请回答加入这条语句后，执行`make qemu`的输出结果与你没有加入这条语句后执行`make qemu`的输出结果的差异，并解释为什么有差异或没差异？ 

- [x]  

> 加入这句之前，输出结果是

```
(THU.CST) os is loading ...

Special kernel symbols:
  entry  0x00100000 (phys)
  etext  0x00103419 (phys)
  edata  0x0010ea16 (phys)
  end    0x0010fd20 (phys)
Kernel executable memory footprint: 64KB
ebp:0x00007b08 eip:0x001009a6 args:0x00010094 0x00000000 0x00007b38 0x00100092 
    kern/debug/kdebug.c:306: print_stackframe+21
ebp:0x00007b18 eip:0x00100c8f args:0x00000000 0x00000000 0x00000000 0x00007b88 
    kern/debug/kmonitor.c:125: mon_backtrace+10
ebp:0x00007b38 eip:0x00100092 args:0x00000000 0x00007b60 0xffff0000 0x00007b64 
    kern/init/init.c:48: grade_backtrace2+33
ebp:0x00007b58 eip:0x001000bb args:0x00000000 0xffff0000 0x00007b84 0x00000029 
    kern/init/init.c:53: grade_backtrace1+38
ebp:0x00007b78 eip:0x001000d9 args:0x00000000 0x00100000 0xffff0000 0x0000001d 
    kern/init/init.c:58: grade_backtrace0+23
ebp:0x00007b98 eip:0x001000fe args:0x0010343c 0x00103420 0x0000130a 0x00000000 
    kern/init/init.c:63: grade_backtrace+34
ebp:0x00007bc8 eip:0x00100055 args:0x00000000 0x00000000 0x00000000 0x00010094 
    kern/init/init.c:28: kern_init+84
ebp:0x00007bf8 eip:0x00007d68 args:0xc031fcfa 0xc08ed88e 0x64e4d08e 0xfa7502a8 
    <unknow>: -- 0x00007d67 --
ebp:0x00000000 eip:0x00007c4f args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53 
    <unknow>: -- 0x00007c4e --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000 
    <unknow>: -- 0xf000ff52 --
ebp:0x00000000 eip:0x00000000 args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53 
    <unknow>: -- 0xffffffff --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000 
    <unknow>: -- 0xf000ff52 --
ebp:0x00000000 eip:0x00000000 args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53 
    <unknow>: -- 0xffffffff --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000 
    <unknow>: -- 0xf000ff52 --
ebp:0x00000000 eip:0x00000000 args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53 
    <unknow>: -- 0xffffffff --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000 
    <unknow>: -- 0xf000ff52 --
ebp:0x00000000 eip:0x00000000 args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53 
    <unknow>: -- 0xffffffff --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000 
    <unknow>: -- 0xf000ff52 --
ebp:0x00000000 eip:0x00000000 args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53 
    <unknow>: -- 0xffffffff --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000 
    <unknow>: -- 0xf000ff52 --
++ setup timer interrupts
100 ticks
End of Test.
kernel panic at kern/trap/trap.c:18:
    EOT: kernel seems ok.
Welcome to the kernel debug monitor!!
Type 'help' for a list of commands.
```

> 加入这句之后，输出结果是

```
(THU.CST) os is loading ...

Special kernel symbols:
  entry  0x00100000 (phys)
  etext  0x0010341b (phys)
  edata  0x0010ea16 (phys)
  end    0x0010fd20 (phys)
Kernel executable memory footprint: 64KB
ebp:0x00007b08 eip:0x001009a8 args:0x00010094 0x00000000 0x00007b38 0x00100094 
    kern/debug/kdebug.c:306: print_stackframe+21
ebp:0x00007b18 eip:0x00100c91 args:0x00000000 0x00000000 0x00000000 0x00007b88 
    kern/debug/kmonitor.c:125: mon_backtrace+10
ebp:0x00007b38 eip:0x00100094 args:0x00000000 0x00007b60 0xffff0000 0x00007b64 
    kern/init/init.c:48: grade_backtrace2+33
ebp:0x00007b58 eip:0x001000bd args:0x00000000 0xffff0000 0x00007b84 0x00000029 
    kern/init/init.c:53: grade_backtrace1+38
ebp:0x00007b78 eip:0x001000db args:0x00000000 0x00100000 0xffff0000 0x0000001d 
    kern/init/init.c:58: grade_backtrace0+23
ebp:0x00007b98 eip:0x00100100 args:0x0010343c 0x00103420 0x0000130a 0x00000000 
    kern/init/init.c:63: grade_backtrace+34
ebp:0x00007bc8 eip:0x00100055 args:0x00000000 0x00000000 0x00000000 0x00010094 
    kern/init/init.c:28: kern_init+84
ebp:0x00007bf8 eip:0x00007d68 args:0xc031fcfa 0xc08ed88e 0x64e4d08e 0xfa7502a8 
    <unknow>: -- 0x00007d67 --
ebp:0x00000000 eip:0x00007c4f args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53 
    <unknow>: -- 0x00007c4e --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000 
    <unknow>: -- 0xf000ff52 --
ebp:0x00000000 eip:0x00000000 args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53 
    <unknow>: -- 0xffffffff --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000 
    <unknow>: -- 0xf000ff52 --
ebp:0x00000000 eip:0x00000000 args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53 
    <unknow>: -- 0xffffffff --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000 
    <unknow>: -- 0xf000ff52 --
ebp:0x00000000 eip:0x00000000 args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53 
    <unknow>: -- 0xffffffff --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000 
    <unknow>: -- 0xf000ff52 --
ebp:0x00000000 eip:0x00000000 args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53 
    <unknow>: -- 0xffffffff --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000 
    <unknow>: -- 0xf000ff52 --
ebp:0x00000000 eip:0x00000000 args:0xf000e2c3 0xf000ff53 0xf000ff53 0xf000ff53 
    <unknow>: -- 0xffffffff --
ebp:0xf000ff53 eip:0xf000ff53 args:0x00000000 0x00000000 0x00000000 0x00000000 
    <unknow>: -- 0xf000ff52 --
++ setup timer interrupts
trapframe at 0x7b5c
  edi  0x00000001
  esi  0x00000000
  ebp  0x00007bc8
  oesp 0x00007b7c
  ebx  0x00010094
  edx  0x000000a1
  ecx  0x00000000
  eax  0x000000ff
  ds   0x----0010
  es   0x----0010
  fs   0x----0023
  gs   0x----0023
  trap 0x00000006 Invalid Opcode
  err  0x00000000
  eip  0x00100070
  cs   0x----0008
  flag 0x00000207 CF,PF,IF,IOPL=0
kernel panic at kern/trap/trap.c:181:
    unexpected trap in kernel.

Welcome to the kernel debug monitor!!
Type 'help' for a list of commands.
```

> 因为在内核态发生了不允许出现的的中断（中断号2012012017 mod 37 == 6，译作Invalid Opcode），根据trap/trap.c里中断处理程序中的

```
        if ((tf->tf_cs & 3) == 0) {
            print_trapframe(tf);
            panic("unexpected trap in kernel.\n");
        }
```

> 先打印了trapframe的内容，然后出发panic中止了操作系统。

（3）对于lab2的输出信息，请说明数字的含义
```
e820map:
  memory: 0009fc00, [00000000, 0009fbff], type = 1.
  memory: 00000400, [0009fc00, 0009ffff], type = 2.
  memory: 00010000, [000f0000, 000fffff], type = 2.
  memory: 07ee0000, [00100000, 07fdffff], type = 1.
  memory: 00020000, [07fe0000, 07ffffff], type = 2.
  memory: 00040000, [fffc0000, ffffffff], type = 2.
```
修改lab2，让其显示` type="some string"` 让人能够读懂，而不是不好理解的数字1,2  (easy) 
- [x]  

> 

（4）(spoc)有一台只有页机制的简化80386的32bit计算机，有地址范围位0~256MB的物理内存空间（physical memory），可表示大小为256MB，范围为0xC0000000~0xD0000000的虚拟地址空间（virtual address space）,页大小（page size）为4KB，采用二级页表，一个页目录项（page directory entry ，PDE）大小为4B,一个页表项（page-table entries PTEs）大小为4B，1个页目录表大小为4KB，1个页表大小为4KB。
```
PTE格式（32 bit） :
  PFN19 ... PFN0|NOUSE9 ... NOUSE0|WRITABLE|VALID
PDE格式（32 bit） :
  PT19 ... PT0|NOUSE9 ... NOUSE0|WRITABLE|VALID
 
其中：
NOUSE9 ... NOUSE0为保留位，要求固定为0
WRITABLE：1表示可写，0表示只读
VLAID：1表示有效，0表示无效
```

假设ucore OS已经为此机器设置好了针对如下虚拟地址<-->物理地址映射的二级页表，设置了页目录基址寄存器（page directory base register，PDBR）保存了页目录表的物理地址（按页对齐），其值为0。已经建立好了从0x1000~41000的二级页表，且页目录表的index为0~63的页目录项的(PT19 ... PT0)的值=(index+1)。
请写出一个translation程序（可基于python, ruby, C, C++，LISP等），输入是一个虚拟地址和一个物理地址，能够自动计算出对应的页目录项的index值,页目录项内容的值，页表项的index值，页表项内容的值。即(pde_idx, pde_ctx, pte_idx, pte_cxt)

请用如下值来验证你写的程序的正确性：
```
va 0xc2265b1f, pa 0x0d8f1b1f
va 0xcc386bbc, pa 0x0414cbbc
va 0xc7ed4d57, pa 0x07311d57
va 0xca6cecc0, pa 0x0c9e9cc0
va 0xc18072e8, pa 0x007412e8
va 0xcd5f4b3a, pa 0x06ec9b3a
va 0xcc324c99, pa 0x0008ac99
va 0xc7204e52, pa 0x0b8b6e52
va 0xc3a90293, pa 0x0f1fd293
va 0xce6c3f32, pa 0x007d4f32
```

参考的输出格式为：
```
va 0xcd82c07c, pa 0x0c20907c, pde_idx 0x00000336, pde_ctx  0x00037003, pte_idx 0x0000002c, pte_ctx  0x0000c20b
```

- [x]  

> 运行结果

```
va 0xc2265b1f, pa 0x0d8f1b1f, pde_idx 0x00000308, pde_ctx 0x00009003, pte_idx 0x00000265, pte_ctx 0x0d8f1003
va 0xcc386bbc, pa 0x0414cbbc, pde_idx 0x00000330, pde_ctx 0x00031003, pte_idx 0x00000386, pte_ctx 0x0414c003
va 0xc7ed4d57, pa 0x07311d57, pde_idx 0x0000031f, pde_ctx 0x00020003, pte_idx 0x000002d4, pte_ctx 0x07311003
va 0xca6cecc0, pa 0x0c9e9cc0, pde_idx 0x00000329, pde_ctx 0x0002a003, pte_idx 0x000002ce, pte_ctx 0x0c9e9003
va 0xc18072e8, pa 0x007412e8, pde_idx 0x00000306, pde_ctx 0x00007003, pte_idx 0x00000007, pte_ctx 0x00741003
va 0xcd5f4b3a, pa 0x06ec9b3a, pde_idx 0x00000335, pde_ctx 0x00036003, pte_idx 0x000001f4, pte_ctx 0x06ec9003
va 0xcc324c99, pa 0x0008ac99, pde_idx 0x00000330, pde_ctx 0x00031003, pte_idx 0x00000324, pte_ctx 0x0008a003
va 0xc7204e52, pa 0x0b8b6e52, pde_idx 0x0000031c, pde_ctx 0x0001d003, pte_idx 0x00000204, pte_ctx 0x0b8b6003
va 0xc3a90293, pa 0x0f1fd293, pde_idx 0x0000030e, pde_ctx 0x0000f003, pte_idx 0x00000290, pte_ctx 0x0f1fd003
va 0xce6c3f32, pa 0x007d4f32, pde_idx 0x00000339, pde_ctx 0x0003a003, pte_idx 0x000002c3, pte_ctx 0x007d4003
```

> C++代码

```
#include <cstdio>
using namespace std;

int main() {
    unsigned va, pa;
    scanf("%x%x", &va, &pa);
    unsigned pde_idx = (va >> 22) & 0x000003ff;
    unsigned pde_ctx = ((pde_idx & 0x0000003f) + 1) << 12 | 0x00000003;
    unsigned pte_idx = (va >> 12) & 0x000003ff;
    unsigned pte_ctx = (pa & 0xfffff000) | 0x00000003;
    printf("va 0x%08x, pa 0x%08x, pde_idx 0x%08x, pde_ctx 0x%08x, pte_idx 0x%08x, pte_ctx 0x%08x\n",
           va, pa, pde_idx, pde_ctx, pte_idx, pte_ctx);
    return 0;
}
```

---

## 开放思考题

---

（1）请简要分析Intel的x64 64bit体系结构下的分页机制是如何实现的 
```
  + 采分点：说明Intel x64架构的分页机制的大致特点和页表执行过程
  - 答案没有涉及如下3点；（0分）
  - 正确描述了x64支持的物理内存大小限制（1分）
  - 正确描述了x64下的多级页表的级数和多级页表的结构（2分）
  - 除上述两点外，进一步描述了在多级页表下的虚拟地址-->物理地址的映射过程（3分）
 ```
- [x]  

>  

（2）Intel8086不支持页机制，但有hacker设计过包含未做任何改动的8086CPU的分页系统。猜想一下，hacker是如何做到这一点的？提示：想想MMU的逻辑位置

- [x]  

> 

---
