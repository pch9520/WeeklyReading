<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [深入理解Linux内核](#%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3linux%E5%86%85%E6%A0%B8)
  - [3 进程](#3-%E8%BF%9B%E7%A8%8B)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 深入理解Linux内核

## 3 进程

X86汇编语言中CPU上的通用寄存器有：
1. eax，是很多加法乘法指令的缺省寄存器；
2. ebx，是基地址寄存器，在内存寻址时存放基地址；
3. ecx，是计数器，是重复（REP）前缀指令和LOOP指令的内定计数器；
4. edx，总是被用来存放整数除法产生的余数；
5. esi/edi，是源/目标索引寄存器；
6. ebp，是基址指针，它最经常被用作高级语言函数调用的“框架指针”，在破解的时候，经常可以看见一个标准的函数起始代码；
7. esp，专门用作堆栈指针，被形象地称为栈顶指针，堆栈的顶部是地址小的区域，压入堆栈的数据越多，esp就越来越小