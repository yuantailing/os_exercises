#lec9 虚存置换算法spoc练习

## 个人思考题
1. 置换算法的功能？

2. 全局和局部置换算法的不同？

3. 最优算法、先进先出算法和LRU算法的思路？

4. 时钟置换算法的思路？

5. LFU算法的思路？

6. 什么是Belady现象？

7. 几种局部置换算法的相关性：什么地方是相似的？什么地方是不同的？为什么有这种相似或不同？

8. 什么是工作集？

9. 什么是常驻集？

10. 工作集算法的思路？

11. 缺页率算法的思路？

12. 什么是虚拟内存管理的抖动现象？

13. 操作系统负载控制的最佳状态是什么状态？

## 小组思考题目

----
(1)（spoc）请证明为何LRU算法不会出现belady现象

> 证明：总页面数n时，页命中当且仅当本次访问与上次访问这页之间，隔了<n个不同页面。

> 总页面数a若命中，则隔了<a个不同页面。对于任意b>a，隔了<b个不同页面，所以总页面数为b也命中。所以不会出现belady现象。

(2)（spoc）根据你的`学号 mod 4`的结果值，确定选择四种替换算法（0：LRU置换算法，1:改进的clock 页置换算法，2：工作集页置换算法，3：缺页率置换算法）中的一种来设计一个应用程序（可基于python, ruby, C, C++，LISP等）模拟实现，并给出测试。请参考如python代码或独自实现。
 - [页置换算法实现的参考实例](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab3/page-replacement-policy.py)

> 与矣晓沅（2012011364）同组，实现了LRU算法。

```
#include <iostream>
#include <cstdlib>
#include <stack>
using namespace std;

//LRU算法，采用活动页面栈方式实现


//访问页面栈
class PageStack {
    public:
        PageStack(int memsize);
        ~PageStack();
        void visit(int pagenum); //访问某一页，按页号进行
        void showMemory(); //查看内存状态

    private:
        int* Memory;
        int MemSize;
        int currentpagenum;
        int push(int pagenum);
};

PageStack::PageStack(int memsize) {
    Memory = new int[memsize];
    currentpagenum = 0;
    MemSize = memsize;
}

PageStack::~PageStack() {
    delete Memory;

}

void PageStack::showMemory() {
    for (int i=currentpagenum-1; i>=0; i--) {
        cout << "|" << Memory[i] << "|" << endl;
    }
    cout << "_________________________" << endl;
}

int PageStack::push(int pagenum) {
    if (currentpagenum<MemSize) {
        Memory[currentpagenum++] = pagenum;
    } else { //删除栈底元素
        for (int i=0; i<MemSize-1; i++) {
            Memory[i] = Memory[i+1];
        }
        Memory[MemSize-1] = pagenum;

    }
}

void PageStack::visit(int pagenum) {
    //先查找栈中是否有该元素
    //为简单起见，直接线性查找
    bool has = 0;
    int index = 0;
    for (int i=0; i<currentpagenum; i++) {
        if (Memory[i] == pagenum) {
            has = 1;
            index = i;
            break;
        }
    }

    if (has) { //若page存在，则放到栈顶
        int visitpage = Memory[index];
        for (int i=index; i<currentpagenum-1; i++) {
            Memory[i] = Memory[i+1];
        }
        Memory[currentpagenum-1] = pagenum;

    } else { //否则，直接push
        cout << "page fault" << endl;
        push(pagenum);
    }
}
```

## 扩展思考题
（1）了解LIRS页置换算法的设计思路，尝试用高级语言实现其基本思路。此算法是江松博士（导师：张晓东博士）设计完成的，非常不错！

参考信息：

 - [LIRS conf paper](http://www.ece.eng.wayne.edu/~sjiang/pubs/papers/jiang02_LIRS.pdf)
 - [LIRS journal paper](http://www.ece.eng.wayne.edu/~sjiang/pubs/papers/jiang05_LIRS.pdf)
 - [LIRS-replacement ppt1](http://dragonstar.ict.ac.cn/course_09/XD_Zhang/(6)-LIRS-replacement.pdf)
 - [LIRS-replacement ppt2](http://www.ece.eng.wayne.edu/~sjiang/Projects/LIRS/sig02.ppt)
