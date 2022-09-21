# C++-Primer-Plus-notes
阅读 *C++ Primer Plus* 时的一些笔记  
在阅读这篇笔记之前需要读者具有一定的编程基础，最好是C语言相关的基础知识，  
当然，这篇文档对于第一次接触OOP(Object Oriented Programming)的读者会有一定的帮助。
### Some Basic Knowledge of the C++  
文件 "C++ Primer Plus I.md" 主要介绍一些C++的基础知识，目录如下：
- [CH00](./C++%20Primer%20Plus.%20I.md#ch00): 介绍一些编译过程相关的基础内容
- [CH01](./C++%20Primer%20Plus.%20I.md#ch01): 面向对象编程的基本概念
- [CH02](./C++%20Primer%20Plus.%20I.md#ch02): C++中的命名空间(namespace)，简单函数的定义进行简单介绍
- [CH03](./C++%20Primer%20Plus.%20I.md#ch03): 部分内置(built-in)变量类型，"const"限定符 
- [CH04](./C++%20Primer%20Plus.%20I.md#ch04): 一些比较复杂的变量类型（数组，字符串，结构体），"new" 和 "delete" 操作符
### More Advanced Freature of C++
文件 "C++ Primer Plus II.md" 中介绍了C++中一些比较重要的特性，包括下面的内容:
- [CH05](./C++%20Primer%20Plus.%20II.md#ch05): C++中的循环语句，以及如何在循环语句的条件中读取标准输入流  
- [CH06](./C++%20Primer%20Plus.%20II.md#ch06): 条件分支，以及简单的文件读写(std::fstream, std::ifstream, std::ofstream)
- [CH07](./C++%20Primer%20Plus.%20II.md#ch07): 将数组或者指针作为参数传递，在C++中如何传递二维数组，以及函数指针
- [CH08](./C++%20Primer%20Plus.%20II.md#ch08): 进一步讨论C++中的函数：内联函数、引用变量、默认参数、函数重载以及函数模板
- [CH09](./C++%20Primer%20Plus.%20II.md#ch09): 内存模型(Memory Models)，命名空间(namespace)
### Classes in C++
文件 "C++ Primer Plus III.md" 中面向对象编程的内容:
- [CH10](./C++%20Primer%20Plus.%20III.md#ch10): C++ 类(Class)的基本内容：成员访问限定符，this指针，构造器与析构器，类的作用域等
- [CH11](./C++%20Primer%20Plus.%20III.md#ch11): 类的运算符重载，友元(friendship)，类的类型转换
- [CH12](./C++%20Primer%20Plus.%20III.md#ch12): C++类的动态内存分配方式，讨论了类的一些特殊成员以及返回对象
- [CH13](./C++%20Primer%20Plus.%20III.md#ch13): 类的继承，虚函数，抽象基类(Abstract Bases Classes, ABC)
- [CH14](./C++%20Primer%20Plus.%20III.md#ch14): 类的"has-a"关系, 多重继承，类模板
  
### Extension I: Multiple-Thread Programing in C++  
这个文件主要介绍如何在C++中实现多线程编程:  
- [create mutiple threads](./Extension%20I-Mutiple%20Thread%20Programming%20in%20C%2B%2B.md#create_thread): 介绍"pthread.h"库文件，这个库文件中提供了一些过程化的多线程方法    
- [thread class](./Extension%20I-Mutiple%20Thread%20Programming%20in%20C%2B%2B.md#thread_class): 使用"std::thread"类实现OOP风格的多线程编程
  
### Extension II: C++ Network Programming
这个文件主要介绍如何在C++中进行网络编程：
- [Introduction](./Extension%20II:%20C++%20Network%20Programming.md#introduction): 网络编程中的一些基本概念  
- [Socket Programming](./Extension%20II:%20C++%20Network%20Programming.md#socket): 简述套接字编程  
- [Implementation](./Extension%20II:%20C++%20Network%20Programming.md#implementation): 实现套接字编程的一个实例  
  
ref.  
*C++ Primer Plus, 5th edition*  
[Michael Kerrisk - man7.org](https://man7.org/index.html)  
[GeeksforGeeks | A computer science portal for geeks](https://www.geeksforgeeks.org/)  
[cppreference.com](https://en.cppreference.com/w/)  
[Reference - C++ Reference](http://www.cplusplus.com/reference/)
