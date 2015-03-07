# lab0 SPOC思考题

## 个人思考题

---

能否读懂ucore中的AT&T格式的X86-32汇编语言？请列出你不理解的汇编语言。
- [x]  

>  能。

虽然学过计算机原理和x86汇编（根据THU-CS的课程设置），但对ucore中涉及的哪些硬件设计或功能细节不够了解？
- [x]  

> 保护模式、进程调度、文件系统等。


哪些困难（请分优先级）会阻碍你自主完成lab实验？
- [x]  

> 不可抗力
> 断电 / 断网
> 对操作系统理解不够深
> 汇编语言问题
> C语言编程问题


如何把一个在gdb中或执行过程中出现的物理/线性地址与你写的代码源码位置对应起来？
- [x]  

> 一、gdb中可以看到物理地址属于哪一个函数符号，先将函数符号与源代码中的函数名对应起来。然后根据大致位置和上下文特点，根据经验可以分析出对应代码的哪一行。
> 二、编译生成时，可以打开调试信息开关，可以生成更多调试信息，包含源代码行数信息。

了解函数调用栈对lab实验有何帮助？
- [x]  

> 便于获知程序的控制流，定位系统当前状态、出问题的位置。
> 可以发现递归导致的死循环。
> 用于分析栈溢出导致的错误。
> 方便调试程序。

你希望从lab中学到什么知识？
- [x]  

> Linux使用、版本控制、代码风格、框架设计、设计思想、C语言技巧、调试技巧等。

---

## 小组讨论题
（小组：袁泰凌、卢嘉铭）

---

搭建好实验环境，请描述碰到的困难和解决的过程。
- [x]  

> 顺利地安装了虚拟机、linux、gcc、qemu。

熟悉基本的git命令行操作命令，从github上
的 http://www.github.com/chyyuu/ucore_lab 下载
ucore lab实验
- [x]  

> 已经熟悉git clone、pull、push、add、commit、log、reset等命令。
> git clone http://www.github.com/chyyuu/ucore_lab

尝试用qemu+gdb（or ECLIPSE-CDT）调试lab1
- [x]   

> 重新生成make clean、make、make debug进行调试。

对于如下的代码段，请说明”：“后面的数字是什么含义
```
 /* Gate descriptors for interrupts and traps */
 struct gatedesc {
    unsigned gd_off_15_0 : 16;        // low 16 bits of offset in segment
    unsigned gd_ss : 16;            // segment selector
    unsigned gd_args : 5;            // # args, 0 for interrupt/trap gates
    unsigned gd_rsv1 : 3;            // reserved(should be zero I guess)
    unsigned gd_type : 4;            // type(STS_{TG,IG32,TG32})
    unsigned gd_s : 1;                // must be 0 (system)
    unsigned gd_dpl : 2;            // descriptor(meaning new) privilege level
    unsigned gd_p : 1;                // Present
    unsigned gd_off_31_16 : 16;        // high bits of offset in segment
 };
 ```

- [x]  

> 位域长度。

对于如下的代码段，
```
#define SETGATE(gate, istrap, sel, off, dpl) {            \
    (gate).gd_off_15_0 = (uint32_t)(off) & 0xffff;        \
    (gate).gd_ss = (sel);                                \
    (gate).gd_args = 0;                                    \
    (gate).gd_rsv1 = 0;                                    \
    (gate).gd_type = (istrap) ? STS_TG32 : STS_IG32;    \
    (gate).gd_s = 0;                                    \
    (gate).gd_dpl = (dpl);                                \
    (gate).gd_p = 1;                                    \
    (gate).gd_off_31_16 = (uint32_t)(off) >> 16;        \
}
```
如果在其他代码段中有如下语句，
```
unsigned intr;
intr=8;
SETGATE(intr, 0,1,2,3);
```
请问执行上述指令后， intr的值是多少？

- [x]  

> 65538

请分析 [list.h](https://github.com/chyyuu/ucore_lab/blob/master/labcodes/lab2/libs/list.h)内容中大致的含义，并能include这个文件，利用其结构和功能编写一个数据结构链表操作的小C程序
- [x]  

> 定义了链表类，及其初始化、插入、查询的操作，用了面向对象的设计思想。

```
#include <stdio.h>
#include "list.h"

struct linked_integers {
    unsigned int data;
    list_entry_t integer_link;
};

typedef struct linked_integers linked_integers_t;

#define to_struct(prt, type, member) \
    ((type *)((char *)(prt) - offsetof(type, member)))
#define offsetof(type, member) \
    ((int)(&((type *)0)->member))

int main() {
    linked_integers_t pool[11];
    linked_integers_t *root = &pool[10];
    int i;
    list_init(&root->integer_link);
    for (i = 0; i < 10; i++) {
        linked_integers_t *next = &pool[i];
        next->data = i;
        list_init(&next->integer_link);
        list_add_after(&root->integer_link, &next->integer_link);
        root = next;
        printf("Integer add: %d\n", next->data);
    }
    for (i = 0; i < 10; i++) {
        list_entry_t *prev;
        printf("Inverted ordered output: %d\n", root->data);
        prev = list_prev(&root->integer_link);
        root = to_struct(prev, linked_integers_t, integer_link);
    }
    return 0;
}
```

> 这个程序把0~9这10个数字依次插入链表，然后循链表顺序倒序输出。

---

## 开放思考题

---

是否愿意挑战大实验（大实验内容来源于你的想法或老师列好的题目，需要与老师协商确定，需完成基本lab，但可不参加闭卷考试），如果有，可直接给老师email或课后面谈。
- [x]  

> 否。

---
