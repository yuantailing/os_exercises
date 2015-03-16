# lec5 SPOC思考题


NOTICE
- 有"w3l1"标记的题是助教要提交到学堂在线上的。
- 有"w3l1"和"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的git repo上。
- 有"hard"标记的题有一定难度，鼓励实现。
- 有"easy"标记的题很容易实现，鼓励实现。
- 有"midd"标记的题是一般水平，鼓励实现。


## 个人思考题
---

请简要分析最优匹配，最差匹配，最先匹配，buddy systemm分配算法的优势和劣势，并尝试提出一种更有效的连续内存分配算法 (w3l1)
```
  + 采分点：说明四种算法的优点和缺点
  - 答案没有涉及如下3点；（0分）
  - 正确描述了二种分配算法的优势和劣势（1分）
  - 正确描述了四种分配算法的优势和劣势（2分）
  - 除上述两点外，进一步描述了一种更有效的分配算法（3分）
 ```
- [x]  

> 最优匹配
    优势：大部分分配的尺寸较小时，效果很好，可避免大的空闲分区被拆分；可减小外部碎片的大小；相对简单。
    劣势：留下外部碎片；释放分区较慢；容易产生很多无用的小碎片。

> 最差匹配
    优势：中等大小的分配较多时，效果较好；避免出现太多的小碎片。
    劣势：释放分区较慢；留下外部碎片；容易破坏大的空闲分区，因此后续难以分配大的分区。

> 最先匹配
    优势：算法简单；在高地址空间有大块的空闲分区。
    劣势：留下较多外部碎片；大块空闲分区在高址，分配大块时较慢。

> Buddy System
    优势：分配、释放都比较快。是一种限制了条件的最优匹配，避免较大的空闲分区被拆分。分配小空间、中等空间、大空间都有比较好的效果。
    劣势：留下较多内部碎片；算法相对复杂。

> 其它分配方法
    循环最先匹配
    该算法是由最先匹配算法演变而成的。在为进程分配内存空间时，不再每次从链首开始查找，而是从上次找到的空闲分区开始查找，直至找到一个能满足需求的空闲分区，并从中划出一块。
    优势：该算法能使空闲中的内存分区分布得更加均匀。
    劣势：但将会缺乏大的空闲分区。

## 小组思考题

请参考ucore lab2代码，采用`struct pmm_manager` 根据你的`学号 mod 4`的结果值，选择四种（0:最优匹配，1:最差匹配，2:最先匹配，3:buddy systemm）分配算法中的一种或多种，在应用程序层面(可以 用python,ruby,C++，C，LISP等高语言)来实现，给出你的设思路，并给出测试用例。 (spoc)

> 说明：学号 mod 4 == 1，实现最差匹配算法。

> 设计思路：分配时，查找最大的一块，切开分配；如果最大的一块也不能满足要求，则分配失败。释放时，查找相邻块，合并。

> 测试用例：在128字节的内存池里，依次分配16、48、28字节，然后释放48字节，再分配32、16、16、16字节，打印各次分配结果。

```
Offset of p0 = 0
Offset of p1 = 16
Offset of p2 = 64
Offset of p3 = 16
Offset of p4 = 92
Offset of p5 = 108
Offset of p6 = 48
```

> 其中，释放48字节后，内存池里有两块可分配的区域，一块是起始于16的48字节区域，另一块是起始于92的36字节区域。因此，在48字节区域分配p3 32byte，这块还剩16byte；在36字节区域分配p4 16byte，还剩20byte……

```
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// 最差匹配
struct PmmPool {
    int flag;
    int start;
    int len;
    PmmPool(int flag, int start, int len): flag(flag), start(start), len(len) { }
};

class PmmManager {
public:
    PmmManager(int size);
    void *alloc(int size); // 如果分配失败，返回空指针NULL
    void free(void *p);
    void *get_offset() const { return static_cast<void *>(offset); }
private:
    void merge_around(int index);
    int size;
    char *offset;
    std::vector<PmmPool> v;
};

PmmManager::PmmManager(int size): size(size), offset(new char[size]) {
    v.push_back(PmmPool(1, 0, size));
}

void *PmmManager::alloc(int size) {
    if (size < 1) return 0;
    int max_len = 0;
    int max_index = -1;
    for (int i = 0; i < (int)v.size(); i++) {
        if (v[i].flag == 1 && v[i].len > max_len) {
            max_len = v[i].len;
            max_index = i;
        }
    }
    if (max_len < size) return 0;
    v[max_index].flag = 0;
    v[max_index].len = size;
    v.push_back(PmmPool(1, v[max_index].start + size, max_len - size));
    return static_cast<void *>(offset + v[max_index].start);
}

void PmmManager::free(void *p) {
    int start = static_cast<char *>(p) - offset;
    int index = -1;
    for (int i = 0; i < (int)v.size(); i++) {
        if (v[i].start == start) {
            index = i;
            break;
        }
    }
    v[index].flag = 1;
    merge_around(index);
}

void PmmManager::merge_around(int index) {
    int start = v[index].start;
    int len = v[index].len;
    for (int i = 0; i < (int)v.size(); i++) {
        if (v[i].flag == 1 && v[i].start + v[i].len == start) {
            v[i].len = v[i].len + len;
            v[index].flag = 0;
            v[index].len = 0;
            merge_around(i);
            return;
        }
        if (v[i].flag == 1 && v[i].start == start + len) {
            v[index].len = v[i].len + len;
            v[i].flag = 0;
            v[i].len = 0;
            merge_around(index);
            return;
        }
    }
}

void show_offset(void *a, void *b, char const *name) {
    int delta = static_cast<char *>(b) - static_cast<char *>(a);
    cout << "Offset of " << name << " = " << delta << endl;
}

int main() {
    PmmManager pmm(128);
    typedef void *pvoid;
    pvoid p0, p1, p2, p3, p4, p5, p6, offset = pmm.get_offset();
    p0 = pmm.alloc(16);
    p1 = pmm.alloc(48);
    p2 = pmm.alloc(28);
    pmm.free(p1);
    p3 = pmm.alloc(32);
    p4 = pmm.alloc(16);
    p5 = pmm.alloc(16);
    p6 = pmm.alloc(16);

    show_offset(offset, p0, "p0");
    show_offset(offset, p1, "p1");
    show_offset(offset, p2, "p2");
    show_offset(offset, p3, "p3");
    show_offset(offset, p4, "p4");
    show_offset(offset, p5, "p5");
    show_offset(offset, p6, "p6");

    return 0;
}
```

```
如何表示空闲块？ 如何表示空闲块列表？ 
[(start0, size0),(start1,size1)...]
在一次malloc后，如果根据某种顺序查找符合malloc要求的空闲块？如何把一个空闲块改变成另外一个空闲块，或消除这个空闲块？如何更新空闲块列表？
在一次free后，如何把已使用块转变成空闲块，并按照某种顺序（起始地址，块大小）插入到空闲块列表中？考虑需要合并相邻空闲块，形成更大的空闲块？
如果考虑地址对齐（比如按照4字节对齐），应该如何设计？
如果考虑空闲/使用块列表组织中有部分元数据，比如表示链接信息，如何给malloc返回有效可用的空闲块地址而不破坏
元数据信息？
伙伴分配器的一个极简实现
http://coolshell.cn/tag/buddy
```

--- 

## 扩展思考题

阅读[slab分配算法](http://en.wikipedia.org/wiki/Slab_allocation)，尝试在应用程序中实现slab分配算法，给出设计方案和测试用例。

## “连续内存分配”与视频相关的课堂练习

### 5.1 计算机体系结构和内存层次
MMU的工作机理？

- [x]  

>  http://en.wikipedia.org/wiki/Memory_management_unit

L1和L2高速缓存有什么区别？

- [x]  

>  http://superuser.com/questions/196143/where-exactly-l1-l2-and-l3-caches-located-in-computer
>  Where exactly L1, L2 and L3 Caches located in computer?

>  http://en.wikipedia.org/wiki/CPU_cache
>  CPU cache

### 5.2 地址空间和地址生成
编译、链接和加载的过程了解？

- [x]  

>  

动态链接如何使用？

- [x]  

>  


### 5.3 连续内存分配
什么是内碎片、外碎片？

- [x]  

>  

为什么最先匹配会越用越慢？

- [x]  

>  

为什么最差匹配会的外碎片少？

- [x]  

>  

在几种算法中分区释放后的合并处理如何做？

- [x]  

>  

### 5.4 碎片整理
一个处于等待状态的进程被对换到外存（对换等待状态）后，等待事件出现了。操作系统需要如何响应？

- [x]  

>  

### 5.5 伙伴系统
伙伴系统的空闲块如何组织？

- [x]  

>  

伙伴系统的内存分配流程？

- [x]  

>  

伙伴系统的内存回收流程？

- [x]  

>  

struct list_entry是如何把数据元素组织成链表的？

- [x]  

>  



