 * [C和C++区别](#1、C和C++区别) 
 * [单例模式](#单例模式) 
 * [指针和引用的区别](#指针和引用的区别) 
 * [new和delete的底层实现](#new和delete的底层实现) 
 * [限制一个类只能在堆上分配或者只能在栈上分配](#限制一个类只能在堆上分配或者只能在栈上分配) 
 * [string类](#string类) 
 * [strcpy](#strcpy) 
 * [strncpy](#strncpy)
 * [memcpy](#memcpy)
 * [内联函数inline](#内联函数inline)
 * [static的使用场景](#static的使用场景)
 * [const的使用场景](#const的使用场景)
 * [const与#define的区别](#const与#define的区别)
 * [初始化成员列表](#初始化成员列表)
 * [如何在多个文件共享一个全局变量](#如何在多个文件共享一个全局变量)
 * [各种sizeof](#各种sizeof)
 * [结构体内存对齐](#结构体内存对齐) 
 * [C++如何在main之前执行调用函数？](#C++如何在main之前执行调用函数？)
 * [强制类型转换](#强制类型转换)
 * [程序分析题](#程序分析题)
 * [各种锁机制](#各种锁机制)
 * [exit和_exit区别](#exit和_exit区别)
 * [仿函数](#仿函数)
 * [迭代器失效](#迭代器失效)
 * [三个线程循环打印ABC](#三个线程循环打印ABC)
 
 
 
  

 ## C++头文件中可以放那些内容
 
 头文件通过#inlcude预处理被调用，在被调用文件中完全展开，因此头文件中的内容设置有个原则：当多个文件中都有这行语句时，不会报错，所以一般只能存在变量或者函数的声明，而不要放定义。
 
 *  void fun()
 * const 对象的定义
 * extern int a   //变量的声明
 * 头文件中可以写内联函数（inline）的定义
 * 头文件中可以写类（class）的定义



## 1、C和C++区别


C++是在C语言的基础上开发的一种面向对象编程语言，C++支持多种编程范式 －－面向对象编程、泛型编程和过程化编程。常用于系统开发，引擎开发等应用领域，支持类：类、封装、重载等特性!

c++在c的基础上增添类，C是一个结构化语言，它的重点在于算法和数据结构。C程序的设计首要考虑的是如何通过一个过程，对输入（或环境条件）进行运算处理得到输出（或实现过程（事务）控制），而对于C++，首要考虑的是如何构造一个对象模型，让这个模型能够契合与之对应的问题域，这样就可以通过获取对象的状态信息得到输出或实现过程（事务）控制。


2 设计模式
---
设计模式（Design pattern）是一套被反复使用、多数人知晓的、经过分类编目的、代码设计经验的总结。
比如单例模式，保证一个类仅有一个实例，并提供一个访问它的全局访问点。适用于：当类只能有一个实例而且客户可以从一个众所周知的访问点访问它时；当这个唯一实例应该是通过子类化可扩展的，并且客户应该无需更改代码就能使用一个扩展的实例时。
比如工厂模式，定义一个用于创建对象的接口，让子类决定实例化哪一个类。Factory Method 使一个类的实例化延迟到其子类。适用于：当一个类不知道它所必须创建的对象的类的时候；当一个类希望由它的子类来指定它所创建的对象的时候；当类将创建对象的职责委托给多个帮助子类中的某一个，并且你希望将哪一个帮助子类是代理者这一信息局部化的时候。

## 单例模式

懒汉式
构造函数私有化。private
提供一个全局访问点public。。
创建静态成员变量。每一次调用全局访问点的时候，都要判断该成员变量。
```
class LazySingleton {
    //懒汉式单例模式
    //比较懒，在类加载时，不创建实例，因此类加载速度快，但运行时获取对象的速度慢
    //c+＋的构造函数，不能保证是线程安全的。
 private： LazySingleton()
    {
        //私有构造函数
    }
 private ：static LazySingleton *intance = null;//静态私用成员，没有初始化   
 public ：
     LazySingleton *LazySingleton getInstance()    //静态，同步，公开访问点
    {
        if(intance == null)
        {
            intance = new LazySingleton();
            static CGarhuishou cl;//释放的时机
        }
        return intance;
    }
    class CGarhuishou//类中套类，用来释放对象
    {
        ~CGarhuishou()
        {
            if(LazySingleton::intance)
            {
                delete LazySingleton::intance;
                LazySingleton::intance=NULL;
            }
        }
    }
    

}
LazySingleton *LazySingleton::intance=null;


void main()
{
    LazySingleton *myp1=LazySingleton::getInstance();
    LazySingleton *myp2=LazySingleton::getInstance();//返回的是同一个对象
}
```
懒汉式遇上多线程怎么办？
---
多个线程有可能创建多个对象，解决办法有两个：

一：饿汉式(不推荐)

缺点：创建类成员时直接new，提前创建 不满足业务需求

LazySingleton *LazySingleton::intance=new LazySingleton ;

二：加锁（必问）两次检查原理：第一次查有没有实例，第二次查有没有🔒
```
class LazySingleton {
public ：
     static LazySingleton * getInstance()    //静态，同步，公开访问点
    {
        if(intance == null)//双重锁定，第一次查有没有实例
        {
            std::unique_lock<std::mutex> myGuard(mymutex);//第二次查有没有🔒
            if(intance == null)
            {
                intance = new LazySingleton();
                static CGarhuishou cl;//释放的时机
            }  
        }
        return intance;
    }
}
void mythread()
{
    LazySingleton *myp1=LazySingleton::getInstance();
}
void main()
{
    std::thread myobj1(mythread);
    std::thread myobj2(mythread);
}
```

三、C++11下单例模式最简单的写法，利用静态局部变量的线程安全性
```
class Foo
{
public:
    static Foo *getInstance()
    {
        static Foo s_instance;
        return &s_instance;
    }
private:
    Foo() {}
};

```
四、模板单例模式
```
template<class T> 
class Singletion 
{ 
public: 
static T* GetInstance() {
     static T m_Instance;
     return &m_Instance; 
} 

private: 
    Singletion(){};
    Singletion(const Singletion& ){}; 
    Singletion& operator=(const Singletion&){};  
    virtual ~Singletion(){}; 
}; 

```

## 4、指针和引用的区别
##  指针和引用的区别

1：引用是变量的一个别名，内部实现是只读指针

2：引用只能在初始化时被赋值，其他时候值不能被改变，指针的值可以在任何时候被改变，且指针可以改变所指的对象；

3：引用不能为NULL，指针可以为NULL；引用必须被初始化，指针不必。

4：“sizeof 引用" = 指向变量的大小 ， "sizeof 指针"= 指针本身的大小

5：引用可以取地址操作，返回的是被引用变量本身所在的内存单元地址

6：引用使用在源代码级相当于普通的变量一样使用，做函数参数时，内部传递的实际是变量地址
 
## 哪些情况下，只能用引用，不能用指针？


5.内存分配和管理
---
malloc、calloc、realloc、alloca

- malloc：申请指定字节数的内存。申请到的内存中的初始值不确定。
- calloc：为指定长度的对象，分配能容纳其指定个数的内存。申请到的内存的每一位（bit）都初始化为 0。
- realloc：更改以前分配的内存长度（增加或减少）。当增加长度时，可能需将以前分配区的内容移到另一个足够大的区域，而新增区域内的初始值则不确定。
- alloca：在栈上申请内存。程序在出栈的时候，会自动释放内存。但是需要注意的是，alloca 不具可移植性, 而且在没有传统堆栈的机器上很难实现。alloca 不宜使用在必须广泛移植的程序中。C99 中支持变长数组 (VLA)，可以用来替代 alloca。

```
char *str = (char*) malloc(100);
assert(str != nullptr);//申请内存

free(p); 
p = nullptr;//释放内存后指针置空
```

6 C++中有了malloc / free , 为什么还需要 new / delete   
---
  malloc与free是C++/C语言的标准库函数，new/delete是C++的运算符。它们都可用于申请动态内存和释放内存。
  
a、对象在创建的同时要自动执行构造函数，对象在消亡之前要自动执行析构函数。 由于malloc/free是库函数而不是运算符，不能做到自动执行构造析构。

b、对于非内部数据类型的对象而言，光用maloc/free无法满足动态对象的要求。new可以创建复杂数据类型，而malloc只能给系统数据类型分配内存。
 
C、malloc和free返回的是void类型指针（必须进行类型转换），new和delete返回的是具体类型指针。

delete与 delete []区别
---
delete只会调用一次析构函数，而delete[]会调用每一个成员的析构函数。”delete与new配套，delete []与new []配套
```
MemTest *mTest1=new MemTest[10];
MemTest *mTest2=new MemTest;
Int *pInt1=new int [10];
Int *pInt2=new int;
delete[]pInt1; //-1-  
delete[]pInt2; //-2-  √，对于简单类型，delete和delete[]功能是相同的
delete[]mTest1;//-3-
delete[]mTest2;//-4-  报错
在-4-处报错。
```
这就说明：对于内建简单数据类型，delete和delete[]功能是相同的。对于自定义的复杂数据类型，delete和delete[]不能互用。delete[]删除一个数组，delete删除一个指针。简单来说，用new分配的内存用delete删除；用new[]分配的内存用delete[]删除。delete[]会调用数组元素的析构函数。内部数据类型没有析构函数，所以问题不大。如果你在用delete时没用括号，delete就会认为指向的是单个对象，否则，它就会认为指向的是一个数组。

7 什么是内存泄漏？面对内存泄漏和指针越界，你有哪些方法？你通常采用哪些方法来避免和减少这类错误？
---
答：用动态存储分配函数动态开辟的空间，在使用完毕后未释放，结果导致一直占据该内存单元即为内存泄露。比如new之后忘记delete，比如基类对象没有虚析构函数，delete只是delete基类对象而没有析构子类，也会内存泄漏。

* 使用的时候要记得指针的长度。
* malloc的时候得确定在那里free.
* 对指针赋值的时候应该注意被赋值指针需要不需要释放.
* 动态分配内存的指针最好不要再次赋值.

在 linux 或者 unix 下，我们的 C++ 程序缺乏相应的手段来检测内存信息，而只能使用 top 指令观察进程的动态内存总额。而且程序退出时，我们无法获知任何内存泄漏信息。为了更好的辅助在 linux 下程序开发，我们在我们的类库项目中设计并实现了一个内存检测子系统。基于 C++ 中的 new 和 delete 的基本原理，可以实现内存泄漏的检测。


7.1 内存检测的基本实现原理
---
## new和delete的底层实现

当我们在程序中写下 new 和 delete 时，我们实际上调用的是 C++ 语言内置的 new operator 和 delete operator。以 new operator 为例，它总是先分配足够的内存，而后再调用相应的类型的构造函数初始化该内存。new operator 为分配内存所调用的函数名字是 operator new，其通常的形式是 void * operator new(size_t size); 其返回值类型是 void*，参数 size 确定分配多少内存，你能增加额外的参数重载函数 operator new，但是第一个参数类型必须是 size_t。

这里有一个问题，就是当我们调用 new operator 分配内存时，有一个 size 参数表明需要分配多大的内存。但是当调用 delete operator 时，却没有类似的参数，那么 delete operator 如何能够知道需要释放该指针指向的内存块的大小呢？答案是：对于系统自有的数据类型，语言本身就能区分内存块的大小，而对于自定义数据类型（如我们自定义的类），则 operator new 和 operator delete 之间需要通过cookie互相传递信息。当我们使用 operator new 为一个自定义类型对象分配内存时，实际上我们得到的内存要比实际对象的内存大一些，这些内存除了要存储对象数据外，还需要记录这片内存的大小，此方法称为 cookie。

当我们为数组分配/释放内存时，虽然我们仍然使用 new operator 和 delete operator，但是其内部行为却有不同：new operator 调用了operator new 的数组版的兄弟－ operator new[]，而后针对每一个数组成员调用构造函数。

实际上operator new/operator delete是malloc/free的一层封装。负责分配空间/释放空间，同时调用构造函数和析构函数来初始化/清理对象。 

new 做了两件事 ：

1. 调用operator new分配空间。 

2. 调用构造函数初始化对象。

基于new和delete的底层，内存泄漏检测的原理？
---
要想检测内存泄漏，就必须对程序中的内存分配和释放情况进行记录，所能够采取的办法就是首先重载所有形式的operator new 和 operator delete，截获 new operator 和 delete operator 执行过程中的内存操作信息。然后在全局记录申请和释放信息，程序运行结束时打印。

四个重载函数为：
```
void* operator new( size_t nSize, char* pszFileName, int nLineNum )
void* operator new[]( size_t nSize, char* pszFileName, int nLineNum )
void operator delete( void *ptr )
void operator delete[]( void *ptr )
```
在operator new函数，除了必须的 size_t nSize 参数外，还增加了文件名和行号，这个信息将在发现内存泄漏时输出，以帮助用户定位泄漏具体位置。对于全局堆内存申请记录，这里所采用的数据结构是一个 STL 的 map，以指针值为 key 值，文件名和行号信息为value。当 operator delete 被调用时，如果调用方式正确的话，我们就能以传入的指针值在 map 中找到相应的数据项并将之删除，而后调用 free 将指针所指向的内存块释放。当程序退出的时候，map 中的剩余的数据项就是我们企图检测的内存泄漏信息－－已经在堆上分配但是尚未释放的分配信息。

对于重载函数：我们用过宏替换，将之前缺省的 new operato 替换为new (__FILE__,__LINE__) 调用，#define new  new(__FILE__, __LINE__ )

对于全局map：用于管理客户信息的这个 map 必须在客户程序第一次调用 new operator 或者 delete operator 之前被创建，而且在最后一个 new operator 和 delete operator 调用之后进行泄漏信息的打印，也就是说它需要先于程序而出生，而在程序退出之后进行分析。所以必须将其设计为全局对象。设计一个类来封装这个 map 以及这对它的插入删除操作，然后构造这个类的一个全局对象（appMemory），在全局对象（appMemory）的构造函数中创建并初始化这个数据结构，而在其析构函数中对数据结构中剩余数据进行分析和输出。

Operator new 中将调用这个全局对象（appMemory）的 insert 接口将指针、文件名、行号、内存块大小等信息以指针值为 key 记录到 map 中，在 operator delete 中调用 erase 接口将对应指针值的 map 中的数据项删除。注意不要忘了对 map 的访问需要进行互斥同步，因为同一时间可能会有多个线程进行堆上的内存操作。

最后，为了让用户能够方便的 enable 和 disable 这个内存检测功能，毕竟内存泄漏的检测应该在程序的调试和测试阶段完成。我们可以使用条件编译的特性，在用户被检测文件中使用如下宏定义来实现开关功能：
```
#if defined( MEM_DEBUG )
#define new new(__FILE__, __LINE__ )
```
#include "MemRecord.h"中定义：
```
#define DEBUG_NEW new(__FILE__, __LINE__ )
```

自己的文件中定义：
```
#include "MemRecord.h"
#if defined( MEM_DEBUG )
#define new DEBUG_NEW
#endif
```

调用时g++ -c -DMEM_DEBUG xxxxxx.cpp，属性-DMEM_DEBUG 相当于检测工具的开关，在不需要检测时不加这个属性，就能


针对第二步：我们用于管理客户信息的这个 map 必须在客户程序第一次调用 new operator 或者 delete operator 之前被创建，而且在最后一个 new operator 和 delete operator 调用之后进行泄漏信息的打印，也就是说它需要先于客户程序而出生，而在客户程序退出之后进行分析。能够包容客户程序生命周期的确有一人--全局对象（appMemory）。我们可以设计一个类来封装这个 map 以及这对它的插入删除操作，然后构造这个类的一个全局对象（appMemory），在全局对象（appMemory）的构造函数中创建并初始化这个数据结构，而在其析构函数中对数据结构中剩余数据进行分析和输出。Operator new 中将调用这个全局对象（appMemory）的 insert 接口将指针、文件名、行号、内存块大小等信息以指针值为 key 记录到 map 中，在 operator delete 中调用 erase 接口将对应指针值的 map 中的数据项删除，

注意不要忘了对 map 的访问需要进行互斥同步，因为同一时间可能会有多个线程进行堆上的内存操作。

内存泄漏检测工具
---
windows下：CRT      Linux下：Valgrind 

基本原理就是重载 new和delete操作符，编译的时候调用的是dbg_new和dbg_delete，这两个重载过的操作符会在调用期间记录内存的使用情况。

```cpp
#define _CRTDBG_MAP_ALLOC 
#include<stdlib.h>
#include<crtdbg.h>

_CrtDumpMemoryLeaks();//感觉某处可能存在泄漏，通过该语句可以在某处打印当前地内存情况


程序结束之前打印泄漏信息：
一般用于全局的内存泄漏问题，在程序刚开始的时候就可以用起来。使用方法也很简单，执行下面两个语句, 先初始化, 然后设置在第几次的内存分配停住.
_CrtSetDbgFlag(_CrtSetDbgFlag(_CRTDBG_REPORT_FLAG) | _CRTDBG_LEAK_CHECK_DF);
_CrtSetBreakAlloc(long); 
```

7.2 常用的智能指针介绍？智能指针怎么防止内存泄漏的？ 两个智能指针相互引用造成的内存泄漏原理？
---
![智能指针](https://github.com/Planck-a/skill/blob/master/%E6%99%BA%E8%83%BD%E6%8C%87%E9%92%88.md)

## 8、怎么限制一个类的对象实例,只能在"堆"上分配,或者只能在"栈"上分配
## 限制一个类只能在堆上分配或者只能在栈上分配
---
定义类对象有两种方式，A a和A *a=new A；

限制只在堆上分配内存：即只能用new,不能直接定义。

做法：将析构函数设为私有，类对象就无法建立在栈上了，如果直接使用A  a 来建立对象，编译报错，提示析构函数无法访问，只能使用new操作符来建立对象。由于new时会分配内存+构造函数，构造函数可以访问，所以new 不会报错。但是不能delete来释放对象，因为delete会调析构函数，但是析构函数为private，所以要手动调用destory来析构。

```
class A  
{  
public:  
    A(){}  
    void destory(){delete this;}  
private:  
    ~A(){}  
}; 
```

缺点1：必须自己手动打造destory函数来析构对象，而不能用delete。     

缺点2：析构函数做成私有的话，无法正常继承---》protected

改进：可以将构造函数设为protected，然后提供一个public的static函数来完成构造，这样不使用new，而是使用一个函数来构造，使用一个函数来析构。
```
class A  
{  
protected:  
    A(){}  
    ~A(){}  
public:  
    static A* create()  
    {  
        return new A();  
    }  
    void destory()  
    {  
        delete this;  
    }  
};  
```
这样，调用create()函数在堆上创建类A对象，调用destory()函数释放内存。而且可以正常继承！但是直接调用new会报错！

限制只在堆上分配内存：

只要禁用new运算符就可以实现类对象只能建立在栈上。将operator new()设为私有即可。重载类的new操作符，使重载后的new操作符的功能为空。这样就使外层程序无法在堆上分配对象，只可以在栈上分配。代码如下：
```
class A  
{  
private:  
    void* operator new(size_t t){}     // 注意函数的第一个参数和返回值都是固定的  
    void operator delete(void* ptr){} // 重载了new就需要重载delete  
public:  
    A(){}  
    ~A(){}  
}; 
```
9 malloc的原理，brk系统调用干什么的，mmap呢？
---
参考：https://blog.csdn.net/gfgdsg/article/details/42709943

内核数据结构mm_struct中的成员变量start_code和end_code是进程代码段的起始和终止地址，start_data和 end_data是进程数据段的起始和终止地址，start_stack是进程堆栈段起始地址，
ulimit-s可以查看系统栈内存大小。start_brk是进程动态内存分配起始地址（堆的起始地址），还有一个 brk（堆的当前最后地址），就是动态内存分配当前的终止地址。

A=malloc(40k)，当申请的大小小于128k时，内部是调用的brk，把堆内存截止地址向高内存扩40k；

B=malloc(100k)，仍然是调用brk，在A的基础上堆内存截止地址向高内存扩100k；

注意只是改变终止地址，并没有映射！只有在真正访问一个地址的时候才建立这个地址的物理映射。

注：brk()是一个非常简单的系统调用，只是简单地改变mm_struct结构的成员变量brk的值。

![1](https://github.com/Planck-a/image-folder/blob/master/%E5%86%85%E5%AD%98%E7%AE%A1%E7%90%86%E7%A4%BA%E6%84%8F%E5%9B%BE/memory.png)

C=malloc(200k),此时调用的是mmap，是在堆和栈中间选择200k给C，而不是按顺序在B上分配；

这时free(C)，可以直接把这部分内存稀释掉，但是在B析构之前，析构A的话，A对应的虚拟内存和物理内存都没有释放,因为只有一个_edata指针，如果往回推，那么B这块内存怎么办呢？

注：mmap系统调用实现了更有用的动态内存分配功能，可以将一个磁盘文件的全部或部分内容映射到用户空间中

![2](https://github.com/Planck-a/image-folder/blob/master/%E5%86%85%E5%AD%98%E7%AE%A1%E7%90%86%E7%A4%BA%E6%84%8F%E5%9B%BE/memory2.png)

这时A和B连起来，就有140k的内存，当可用的空闲内存组合起来超过128k时，才会执行内存紧缩，将_edata回调到A之前。否则，这部分并没有释放掉，在下次申请内存小于128k时，直接分配给调用者。

为什么brk申请的内存不能直接释放，为什么不用mmap全部分配呢？而是仅仅对于大于 128k 的大块内存才使用 mmap ？ 

答：进程向 OS 申请和释放地址空间会产生很多系统调用，过多的系统调用会消耗内存。是一个连续空间，并且堆内碎片由于没有归还 OS ，如果可重用碎片，再次访问该内存很可能不需产生任何系统调用和缺页中断，这将大大降低 CPU 的消耗。

malloc最大能申请多大的内存？
---
首先区分是一次性申请的最大内存，还是多次加起来的最大内存。两个是不一样的，一个是在mmp申请，一个是brk。

如果是2.4版本之前的话，因为映射区是在1G地址位置，而且映射区与栈相对生长，malloc申请的空间大于128KB的话，调用的是mmap函数，因此分配的地址起始在1G位置，末端为3G，最大2G左右，所以一次最大申请为2G左右，如果小块小块累计申请的话算上堆区128M到1G区间的话，小块申请 malloc调用brk总和就0.9G，累计能申请到的为2.9G

10 虚拟内存
---
mm_struct

虚拟内存是为了把不用的放到外存，等到需要时再调入内存

虚拟内存中，允许将一个作业分多次调入内存。釆用连续分配方式时，会使相当一部分内存空间都处于暂时或“永久”的空闲状态，造成内存资源的严重浪费，而且也无法从逻辑上扩大内存容量。因此，虚拟内存的实现需要建立在离散分配的内存管理方式的基础上。虚拟内存的实现有以下三种方式：

① 请求分页存储管理。
② 请求分段存储管理。
③ 请求段页式存储管理。

堆和栈的区别
---
  一个由c/C++编译的程序占用的内存分为以下几个部分 
  
  1、栈区（stack）―   由编译器自动分配释放 ，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈。 
  
  2、堆区（heap） ―   一般由程序员分配释放， 若程序员不释放，程序结束时可能由OS回收 。
  
   注意它与数据结构中的堆是两回事，分配方式倒是类似于链表。 
     
  3、全局区（静态区）（static），全局变量和静态变量的存储是放在一块的， 初始化的全局变量和静态变量在一块区域， 未初始化的全局变量和未初始化的静态变量在相邻的另一块区域。 - 程序结束后有系统释放 
  
  4、文字常量区  ―常量字符串就是放在这里的。 程序结束后由系统释放 
  
  5、程序代码区―存放函数体的二进制代码。
  
  6、栈大小比较有限，通常只有几kb，如果在死循环内不断申请内存的话，会造成栈溢出overflow。而堆是内存不连续区域，是用链表来存储地址的，堆有可能产生内存碎片，堆大小受限于计算机系统中有效虚拟内存。由此可见，堆获得空间比较灵活，也比较大。
  
11.栈溢出问题、什么时候在栈分配内存？什么时候在堆分配内存？
---
栈溢出：栈内存一般就2M，如果定义  ulimit -s 可以查看栈内存大小

栈是从高地址向下生长，堆是从低地址向上生长;最高的1G是内核空间

EasyTcpClient client[10];//client类数组，如果每个类的有100kb的话，10个就会爆炸

//正确的方式：
EasyTcpClient *client[10];//client类指针数组，用new在堆上分配内存
for(int i=0;i<10;i++)
{
    client[n]=new EasyTcpClient;
}

C++的内存分配方式以及它们的区别?
---
1） 从静态存储区域分配。内存在程序编译的时候就已经分配好，这块内存在程序的整个运行期间都存在。例如全局变量，static 变量。

2） 在栈上创建。在执行函数时，函数内局部变量的存储单元都可以在栈上创建，函数执行结束时这些存储单元自动被释放。栈内存分配运算内置于处理器的指令集。

3） 从堆上分配，亦称动态内存分配。程序在运行的时候用malloc 或new 申请任意多少的内存，程序员自己负责在何时用free 或delete 释放内存。动态内存的生存期由程序员决定，使用非常灵活，但问题也最多。

int a = 0; //全局初始化区 

char *p1;//全局未初始化区 

void main(void)

{

  int b; //栈 

  char s[] = "abc"; //栈 

  char *p2; //栈 

  char *p3 = "123456"; //123456\0在常量区，p3在栈上

  static int c =0; //全局（静态）初始化区 

  p1 = (char *)malloc(10); //堆 

  p2 = (char *)malloc(20); //堆 

}

12.this 指针
---
this 指针是一个隐含于每一个非静态成员函数中的特殊指针。它指向调用该成员函数的那个对象。

当对一个对象调用成员函数时，编译程序先将对象的地址赋给 this 指针，然后调用成员函数，每次成员函数存取数据成员时，都隐式使用 this 指针。

当一个成员函数被调用时，自动向它传递一个隐含的参数，该参数是一个指向这个成员函数所在的对象的指针。

this 指针被隐含地声明为: ClassName *const this，这意味着不能给 this 指针赋值；在 ClassName 类的 const 成员函数中，this 指针的类型为：const ClassName* const，这说明不能对 this 指针所指向的这种对象是不可修改的（即不能对这种对象的数据成员进行赋值操作）；

this 并不是一个常规变量，而是个右值，所以不能取得 this 的地址（不能 &this）。

在以下场景中，经常需要显式引用 this 指针：
- 为实现对象的链式引用；
- 为避免对同一对象进行赋值操作；
- 在实现一些数据结构时，如 list。

delete this 合法吗？
---
Is it legal (and moral) for a member function to say delete this?

合法，但：

- 必须保证 this 对象是通过 new（不是 new[]、不是 placement new、不是栈上、不是全局、不是其他对象成员）分配的
- delete this后，不能访问该对象任何的成员变量及虚函数（delete this回收的是数据，不包括代码、成员函数）
- delete this后，不能再访问this指针。换句话说，你不能去检查它、将它和其他指针比较、和 NULL比较、打印它、转换它，以及其它的任何事情；
- delete this不能在析构函数中进行，因为delete首先会调析构函数，会死循环。

针对第一点，可以将构造函数私有化（如果有子类，则protected化，保证子类能够正确继承）--以保证对象必须使用new在堆上分配内存；同时提供（可以在仅仅在基类中）Destroy(void)函数，里面仅有一句delete this，来将分配的内存回收。


空指针调用类成员函数会发生什么？this指针详解
---
```
#include<iostream>
using namespace std;
 
class TestNullPtr {
public:
    void print() {
        cout << "print" << endl;
    }
 
    void getA() {
        cout << a << endl;
    }
 
    virtual test() {
        cout << "test" << endl;
    }
 
private:
    int a = 100;
}

int main() {
    TestNullPtr* ptr = nullptr;
    ptr->print(); //运行结果成功执行并正确打印;
    ptr->getA();//程序虽然不会编译报错，但返回的是一个错误码，也没有正常执行：
    ptr->test();//调用test虚方法,程序可以运行，但是返回错误码，没有执行对应代码：
}
```
那为什么同一个类的不同对象调用对应成员函数可以出现不同的结果？
--
答案就是this指针。

共有的成员函数体之所以能够把不同对象的数据区分开来，靠的是隐式传递给成员函数的this指针，成员函数中对成员变量的访问都是转化成"this->数据成员"的方式。因此，从这一角度说，成员函数与普通函数一样，只是多了一个隐式参数，即指向对象的this指针。

因此当所调用的成员函数有需要访问对象元素(getA)或者更改对象元素值(setA)时，由于没有this指针(因为现在this指针指向的是nullptr)，就会导致不能正确执行。同理，因为虚方法的调用是依赖于this指针的，所以同样会产生错误。

13.复杂数据类型解释
---
`
1.void * ( * (*fp1)(int))[10];   fp1是一个指针，指向一个函数，这个函数的参数为int型，函数的返回值是一个指针，这个指针指向一个数组，这个数组有10个元素，每个元素是一个void*型指针。

2.float (*(* fp2)(int,int,int))(int);   fp2是一个指针，指向一个函数，这个函数的参数为3个int型，函数的返回值是一个指针，这个指针指向一个函数，这个函数的参数为int型，函数的返回值是float型。

3.int (* ( * fp3)())[10]();   fp3是一个指针，指向一个函数，这个函数的参数为空，函数的返回值是一个指针，这个指针指向一个数组，这个数组有10个元素，每个元素是一个指针，指向一个函数，这个函数的参数为空，函数的返回值是int型。

int *p[10]  //数组p，数组内每个元素都是指向int类型的指针变量。

int (*p)[10]//数组指针,指向含10个int的数组

int (*s[10])(int) 函数指针数组s，每个指针指向一 int func(int param)的函数。

int *p(int) //函数

int (*p)(int)//函数指针`



14.解释下封装、继承和多态？
---
答：一、封装：

封装是实现面向对象程序设计的第一步，封装就是将数据或函数等集合在一个个的单元中（我们称之为类）。

封装的意义在于保护或者防止代码（数据）被我们无意中破坏。

二、继承：

继承主要实现重用代码，节省开发时间。
子类可以继承父类的一些东西。

三、多态

多态：同一操作作用于不同的对象，可以有不同的解释，产生不同的执行结果。在运行时，可以通过指向基类的指针，来调用实现派生类中的方法。

虚函数、多重继承和虚继承
---
![虚函数和虚继承](https://github.com/Planck-a/skill/blob/master/C%2B%2B/%E8%99%9A%E5%87%BD%E6%95%B0%E5%92%8C%E8%99%9A%E7%BB%A7%E6%89%BF.md)

继承的几种方式：
---
* public/protected/privated 继承的关系和区别？
* 多继承
* 虚继承

15 子类析构时要调用父类的析构函数吗？
---
析构函数调用的次序是先派生类的析构后基类的析构，也就是说在基类的的析构调用的时候,派生类的信息已经全部销毁了。定义一个对象时先调用基类的构造函数、然后调用派生类的构造函数。

构造函数调用顺序，析构函数呢？
---
调用所有虚基类的构造函数，顺序为从左到右，从最深到最浅

基类的构造函数：如果有多个基类，先调用纵向上最上层基类构造函数，如果横向继承了多个类，调用顺序为派生表从左到右顺序。

如果该对象需要虚函数指针(vptr)，则该指针会被设置从而指向对应的虚函数表(vtbl)。

成员类对象的构造函数：如果类的变量中包含其他类（类的组合），需要在调用本类构造函数前先调用成员类对象的构造函数，调用顺序遵照在类中被声明的顺序。
派生类的构造函数。析构函数与之相反。

拷贝构造函数如果用值传递会有什么影响？
---
答：会形成死循环；举例用s1初始s2的过程，s1---->st(形参)---->s2

当给s2初始化的时候调用了s2的拷贝构造函数，由于是值传递，系统会给形参st重新申请一段空间，然后调用自身的拷贝构造函数把s1的数据成员的值传给st。当s1调用自身的拷贝构造函数的时候又因为是值传递，所以。。。

也就是说，只要调用拷贝构造函数，就会重新申请一段空间，只要重新申请一段空间，就会调用拷贝构造函数，这样一直下去就形成了一个死循环。所以拷贝构造函数一定不能是值传递。

16、多态，虚函数，纯虚函数
---
多态：是对于不同对象接收相同消息时产生不同的动作。C++的多态性具体体现在运行和编译两个方面：在程序运行时的多态性通过继承和虚函数来体现；
在程序编译时多态性体现在函数和运算符的重载上。

虚函数：在基类中冠以关键字 virtual 的成员函数。 它提供了一种接口界面。允许在派生类中对基类的虚函数重新定义。
纯虚函数的作用：在基类中为其派生类保留一个函数的名字，以便派生类根据需要对它进行定义，作为接口而存在。纯虚函数不具备函数的功能，一般不能直接被调用。
从基类继承来的纯虚函数，在派生类中仍是虚函数。如果一个类中至少有一个纯虚函数，那么这个类被称为抽象类（abstract class）。

抽象类中不仅包括纯虚函数，也可包括虚函数。抽象类必须用作派生其他类的基类，抽象类不能用于直接创建对象实例。但仍可使用指向抽象类的指针支持运行时多态性。

什么是虚函数？什么是纯虚函数？
---
虚函数声明如下： virtual ReturnType FunctionName(Parameter)；引入虚函数是为了动态绑定。
纯虚函数声明如下：virtual ReturnType FunctionName()= 0；引入纯虚函数是为了派生接口。
虚函数

定义一个函数为虚函数，不代表函数为不被实现的函数

定义他为虚函数是为了允许用基类的指针来调用子类的这个函数

C++中的虚函数的作用主要是实现了多态的机制。基类定义虚函数，子类可以重写该函数；在派生类中对基类定义的虚函数进行重写时，需要再派生类中声明该方法为虚方法。

当子类重新定义了父类的虚函数后，当父类的指针指向子类对象的地址时，[即B b; A a = &b;] 父类指针根据赋给它的不同子类指针，动态的调用子类的该函数，而不是父类的函数（如果不使用virtual方法，请看后面），且这样的函数调用发生在运行阶段，而不是发生在编译阶段，称为动态联编。而函数的重载可以认为是多态，只不过是静态的。注意，非虚函数静态联编，效率要比虚函数高，但是不具备动态联编能力。
如果使用了virtual关键字，程序将根据引用或指针指向的对象类型来选择方法，否则使用引用类型或指针类型来选择方法。

2、 纯虚函数是否可以实例化

c++中包含纯虚函数的类是不允许被实例化的！！！进一步说，如果继承该类的类不重写这个纯虚函数的话，也是不允许被实例化的。即，包含纯虚函是的类派生出来的类都必须重写这个纯虚函数！

不能声明为虚函数的有哪些
---
1、静态成员函数； 2、类外的普通函数； 3、构造函数； 4、友元函数  5、普通函数

（1） 类的构造函数不能是虚函数

因为在调用构造函数时，虚表指针并没有在对象的内存空间中，必须要构造函数调用完成后才会形成虚表指针

（2） 类的静态函数（static）不能是虚函数

类的静态成员函数是该类共用的，与该类的对象无关，静态函数里没有this指针，所以不能为虚函数。

（3）内联函数

inline是在编译器将函数类容替换到函数调用处，是静态编译的。而虚函数是动态调用的，在编译器并不知道需要调用的是父类还是子类的虚函数，所以不能够inline声明展开，当然声明是不会报错的，只不过没有了多态的效果。

（4）友元函数

友元函数与该类无关，没有this指针，所以不能为虚函数。

（5）普通函数（非类成员函数）不能是虚函数

17、重载（overload）和重写（overried） 重定义
---
总结：重载是一个类中的同名函数，参数不同；重写是两个类间的多态，virtual函数；重定义是继承中的两个类，子类的函数会覆盖父类函数。

重载：是指允许存在多个同名函数，而这些函数的参数表不同（或许参数个数不同，或许参数类型不同，或许两者都不同），返回值不是重载的标志。
--》同一个类中
重写：是指子类重新定义父类虚函数的方法。--》继承中

从实现原理上来说：

重载：编译器根据函数不同的参数表，对同名函数的名称做修饰，然后这些同名函数就成了不同的函数（至少对于编译器来说是这样的）。如，有两个同名函数：function func(p:integer):integer;和function func(p:string):integer;。那么编译器做过修饰后的函数名称可能是这样的：int_func、str_func。对于这两个函数的调用，在编译器间就已经确定了，是静态的。也就是说，它们的地址在编译期就绑定了（早绑定），因此，重载和多态无关！

重写：和多态真正相关。当子类重新定义了父类的虚函数后，父类指针根据赋给它的不同的子类指针，动态的调用属于子类的该函数，这样的函数调用在编译期间是无法确定的（调用的子类的虚函数的地址无法给出）。因此，这样的函数地址是在运行期绑定的（晚绑定）。

基类的析构函数不是虚函数，会带来什么问题？

【参考答案】用基类指针指向子类对象，当delete这个基类指针时，不会调子类的析构函数，造成派生类的析构函数用不上，会造成资源的泄漏。

返回值为什么不能作为重载的一个条件？

答：编译器在编译时，不会去判断函数的返回类型！也就是说你返回类型虽然不同，但是只有函数调用以后，编译器才去验证返回类型。
```
void fun();
int fun();

void main()
{	
     int i = fun();//这种调用还能区分函数
     fun();//这种函数调用，不会报错，但是编译器区分不开是哪个函数
}

```

C++构造函数和析构函数中可以调用虚函数吗？
---
《Effective C++》条款9：永远不要在构造函数或析构函数中调用虚函数 。

１. 从语法上讲，调用完全没有问题，不会报错。 
２. 但是从效果上看，构造函数调用虚函数，则失去多态效果，析构函数也是。 

   当实例化一个子类对象时，首先进行基类部分的构造，然后再进行派生类部分的构造。即创建Derive对象时，会先调用Base的构造函数，再调用Derive的构造函数。当在基类构造函数时，派生类还没被完全创建，从某种意义上讲此时它只是个基类对象。即当Base::Base()执行时Derive对象还没被完全创建，此时它被当成一个Base对象，而不是Derive对象，因此Foo永远绑定的是Base的Foo，失去了函数重载的效果。

## 18.手写string类
## string类
---
```cpp
#include "StringRealize.h"

//普通构造函数
StringRealize::StringRealize(const char *str)
{
    if (str == NULL)
    {
        this->m_data = NULL;
    }
    else
    {
        this->m_data = new char[strlen(str) + 1];
        strcpy(this->m_data,str);
    }
}

//拷贝构造函数
StringRealize::StringRealize(const StringRealize& str)
{
    if (str.m_data == NULL)
    {
        this->m_data = NULL;
    }
    else
    {
        this->m_data = new char[strlen(str.m_data) + 1];
        strcpy(this->m_data,str.m_data);
    }
}

//析构函数
StringRealize::~StringRealize(void)
{
    if (this->m_data)
    {
        delete[] this->m_data;
        this->m_data = NULL;
    }
}

//赋值运算符'='号重载
StringRealize& StringRealize::operator=(const StringRealize& str)
{
    if (this != &str)
    {
        delete[] this->m_data;
        this->m_data = NULL;

        if (str.m_data == NULL)
        {
            this->m_data = NULL;
        }
        else
        {
            this->m_data = new char[strlen(str.m_data) + 1];
            strcpy(this->m_data,str.m_data);
        }
    }

    return *this;
}

//链接字符串‘+’号重载
StringRealize StringRealize::operator+(const StringRealize& str)
{
    StringRealize newString;
    if (str.m_data == NULL)
    {
        return *this;
    } 
    else if (this->m_data == NULL)
    {
        newString = str;
    }
    else
    {
        newString.m_data = new char[strlen(str.m_data) + strlen(this->m_data) + 1];
        strcpy(newString.m_data,this->m_data);
        strcpy(newString.m_data,str.m_data);
    }

    return newString;
}

//等号运算符‘==’号重载
bool StringRealize::operator==(const StringRealize& str)
{
    if (strlen(this->m_data) != strlen(str.m_data)  )
    {
        return false;
    }
    else
    {
        return strcmp( this->m_data, str.m_data ) ? false : true;
    }

}

//获取字符串的长度
int StringRealize::getLength()
{
    return strlen(this->m_data);
}

//输出符重载
std::ostream& operator<<(std::ostream& os, const StringRealize& str)
{
    os<<str.m_data;
    return os;
}

//输入符重载
std::ostream& operator>>(std::ostream& os, const StringRealize& str)
{
    os>>str.m_data;
    return os;
}
```


19.全局变量和局部变量有什么区别？是怎么实现的？操作系统和编译器是怎么知道的？
---
【参考答案】

生命周期不同：全局变量随主程序创建和创建，随主程序销毁而销毁；局部变量在局部函数内部，甚至局部循环体等内部存在，退出就不存在；
使用方式不同：通过声明后全局变量程序的各个部分都可以用到；局部变量只能在局部使用；分配在栈区。 
操作系统和编译器通过内存分配的位置来知道的，全局变量分配在全局数据段并且在程序开始运行的时候被加载。局部变量则分配在堆栈里面 。


局部变量能否和全局变量重名？如果能，请说明系统如何处理；如果不能，请说明原因？
---
答案：在程序中如果出现了相同的两个变量，一个是局部变量，一个是全局变量，编译可以通过，但是打印出的值是局部变量的值，如果想打印全局变量的值的话，在全局变量之前加上“：：”就可以了。
```cpp
  int a = 3;      //定义局部变量并赋值
  cout<<"a = "<<a<<endl;
  ::a = 2;    //给全局变量赋值
  cout<<"a = "<<::a<<endl;
```
# 20.strcpy、考虑内存重叠的strcpy、strncpy、memcpy

strcpy 有什么危险？
---
答：strcpy 不检查copy目标串的Size，如果目标空间不够，就有BufferOverflow问题。如果在用的时候加上相关的长度判断，则会大大降低出此错误的危险。

char *strcpy(char *dest,const char *src); 

VS2005开始已经推出相应的安全版本——strcpy_s（末尾的s可能代表safe）。他们在接口增加了：

1、一个参数numElems来表明dest中的字节数，防止目标指针dest中的空间不够而导致出现Bug，

2、返回值改成返回错误代码，而不是为了一些所谓的方便而返回char*。这样接口的定义就比原来安全很多。

strncpy

strncpy(dest, src, sizeof(dest))；//链式编程，返回值是des

dest[sizeof(dest)-1] = ‘\0’;//务必要把dest的最后一个字节手工设置为0. 因为strncpy仅在src的长度小于dest时，对剩余的字节填0；如果des较小时，手动在最后一位补\0并覆盖最后一位。


## strcpy
```
char *strcpy(char *strDes, const char *string)//源字符串不可变const
{
	if (string == NULL && strDes == NULL)
		return NULL;
	char* address = strDes;
	while (*string != '\0')
		*(strDes++) = *(string++);
	*strDes = '\0';//一定注意最后结束时一定要加一个\0结尾；
	return address;//strDes 此时已经不在开始位置了
}
```
//考虑内存重叠后安全的版本
```
char *mystrcpy2(char *strDest, const char *strSrc)
{
	assert((strDest != NULL) && (strSrc != NULL));
 
	char *ret = strDest;
	char *d, *s;
	int size = 0;
	d = (char *)strDest;
	s = (char *)strSrc;
 
	size = strlen(strSrc) + 1;
	printf("size = %d\n", size);
	if (strDest >= strSrc && strDest <= strSrc + size) {
		d += size - 1;
		s += size - 1;
		while (size--){
			*d-- = *s--;
		}
	}
	else {
		while (size--)
			*strDest++ = *strSrc++;
	}
 
	return ret;
}
```

## strncpy

```
char *strncpy(char *dst, const char *src, size_t len)
{
    assert(des != NULL && src != NULL);
    int offset = 0;
    char *ret = dst;
    char *tmp;
    if(strlen(src) < len)
    {
        offset = len - strlen(src);
        len = strlen(src);
    }
    if(dst >= src && dst <= src + len -1)
    {
        dst = dst + len - 1;
        src = src + len - 1;
        tmp = dst;
        while(len--)
            *dst-- = *src--;
    }
    else
    {
        while(len--)
            *dst++ = *src++;
        tmp = dst;
    }
    while(offset--)
        *tmp++ = '\0';
    return ret;
}
	
```
## memcpy
```
void *my_memcpy(char *dest,const char *src,size_t n)
{
    assert(NULL != dest);
    assert(NULL != src);
    char *res = (char*)dest;


    char *p = (char*)dest;
    const char *q = (const char*)src;
    if(p <= q || p >= q+n)
    {
        while(n--)
        {
            *p++ = *q++;
        }
    }
    else
    {
        p = p+n-1;
        q = q+n-1;
        while(n--)
        {
            *p-- = *q--;
        }
    }
    return res;
}

```

21、C语言中变量的存储类型有哪些
---
https://www.cnblogs.com/zhangruilin/p/5769842.html

C语言中的存储类型有auto，extern，register，static四种

* auto：自动变量，可以不写，默认情况下局部变量就是auto
* register：寄存器变量，把经常被调用的变量声明为寄存器变量，快速存储
* extern: 静态存储类别的变量，在另一个文件中，通过extern 全局变量名的声明
* static：静态存储类别的变量

静态存储类别的变量，从程序的开始处就存在，其生命期伴随整个程序，一直存在程序的执行过程。static变量仅仅在变量的作用范围内可见,而全局变量是在所有地方都可见的,这就是static变量与全局变量的区别，全局变量是不显示用static修饰的全局变量，但全局变量默认是静态的，作用域是整个工程。

22.const 有什么用途
----

## const的使用场景
 主要有四点：

1：定义只读变量，即常量（类成员变量和普通变量）， 修饰位置为函数的参数或者函数的返回值
 
2：修饰指针，分为指向常量的指针和指针常量；

3： const修饰类成员函数，说明该成员函数内不能修改成员变量，不会调用其它非 const 成员函数。

注意：类中定义const成员变量，那么这个变量只能在初始化列表中进行初始化。

4 C++11常量引用const string &，经常用于形参类型，即避免了拷贝，又避免了函数对值的修改；此外 const string &，常引用做形参，既可以接左值（有名），又可以接右值（匿名）

```
// 类
class A
{
private:
    const int a;                // 常对象成员，只能在初始化列表赋值

public:
    // 构造函数
    A() : a(0) { };
    A(int x) : a(x) { };        // 初始化列表

    // const可用于对重载函数的区分
    int getValue();             // 普通成员函数
    int getValue() const;       // 常成员函数，不得修改类中的任何数据成员的值
};

void function()
{
    // 对象
    A b;                        // 普通对象，可以调用全部成员函数、更新常成员变量
    const A a;                  // 常对象，只能调用常成员函数
    const A *p = &a;            // 常指针
    const A &q = a;             // 常引用

    // 指针
    char greeting[] = "Hello";
    char* p1 = greeting;                // 指针变量，指向字符数组变量
    const char* p2 = greeting;          // 指针变量，指向字符数组常量
    char* const p3 = greeting;          // 常指针，指向字符数组变量
    const char* const p4 = greeting;    // 常指针，指向字符数组常量
}

// 函数
void function1(const int Var);           // 传递过来的参数在函数内不可变
void function2(const char* Var);         // 参数指针所指内容为常量
void function3(char* const Var);         // 参数指针为常指针
void function4(const int& Var);          // 引用参数在函数内为常量

// 函数返回值
const int function5();      // 返回一个常数
const int* function6();     // 返回一个指向常量的指针变量，使用：const int *p = function6();
int* const function7();     // 返回一个指向变量的常指针，使用：int* const p = function7();
```

## const与#define的区别

一：区别

（1）就起作用的阶段而言： #define是在编译的预处理阶段起作用，而const是在 编译、运行的时候起作用。

（2）就起作用的方式而言： #define只是简单的字符串替换，没有类型检查。而const有对应的数据类型，是要进行判断的，可以避免一些低级的错误。

（3）就存储方式而言：#define只是进行展开，有多少地方使用，就替换多少次，它定义的宏常量在内存中有若干个备份；const定义的只读变量在程序运行过程中只有一份备份，const可节省空间，避免不必要的内存分配，提高效率。

（4）从代码调试的方便程度而言： const常量可以进行调试的，define是不能进行调试的，因为在预编译阶段就已经替换掉了。

const int a;===int const a;  定义常量类型a

const int *a;===Int const *a; 指针指向的地方不可修改

int *const a; const指针

23 关键字static的作用---->const（变为常量，防修改）static（限制区域）
---
## static的使用场景

类外：

1、修饰函数体内的变量， static 变量的作用范围为该函数体，不同于 auto 变量， 该变量的内存只被分配一次，因此其值在下次调用时仍维持上次的值。static的数据记忆性可以满足函数在不同调用期的通信。修饰普通变量，修改变量的存储区域和生命周期，使变量存储在静态区，在 main 函数运行前就分配了空间，如果有初始值就用初始值初始化它，如果没有初始值系统用默认值初始化它。

2、修饰全局变量或者全局函数，仅在定义该函数的文件内才能使用。在多人开发项目时，为了防止与他人命名空间里的函数重名，可以将函数定位为 static。

类内：

 1.  在类内的 static 变量可以被模块内所有函数访问，但不能被模块外其他函数访问；static 成员只能类外初始化；变量属于整个类所拥有，可以满足同一个类的多个实例间的通信，对类的所有对象只有一份拷贝；类的静态成员变量在类实例化之前就已经存在了，并且分配了内存，不需要生成对象就可以访问该成员。
 
2.  在类内的static 函数只能被这一模块内的其他函数调用，函数的使用范围仅在声明它的模块内； static 成员函数属于整个类所拥有，这个函数不接收 this 指针，因而只能访问类的 static 成员变量。静态成员函数的意义，不在于信息共享，数据沟通，而在于管理静态数据成员，完成对静态数据成员的封装。

 隐藏作用详解：（static函数，static变量均可） 
 
当同时编译多个文件时，所有未加static前缀的全局变量和函数都具有全局可见性。举例来说明。同时编译两个源文件，一个是a.c，另一个是main.c。
```cpp
   //a.c
    char a = 'A';               // global variable
    void msg()
    {
      printf("Hello\n");
    }

  //main.c
   int main()
   {
     extern char a;       // extern variable must be declared before use
     printf("%c ", a);
     (void)msg();
     return 0;
   }
```
程序的运行结果是： A Hello

为什么在a.c中定义的全局变量a和函数msg能在main.c中使用？

  前面说过，所有未加static前缀的全局变量和函数都具有全局可见性，其它的源文件也能访问。此例中，a是全局变量，msg是函数，并且都没有加static前缀，因此对于另外的源文件main.c是可见的。
  
  如果加了static，就会对其它源文件隐藏。例如在a和msg的定义前加上static，main.c就看不到它们了。 利用这一特性可以在不同的文件中定义同名函数和同名变量，而不必担心命名冲突。static可以用作函数和变量的前缀，对于函数来讲，static的作用仅限于隐藏

24 写一个“标准”宏MIN，这个宏输入两个参数并返回较小的一个？
---

答案：#define MIN(A,B) ((A) <= (B) ? (A) : (B)) 

要点：

A.参数用括号括起来;

B.考察能否合理运用条件运算符;

   
25、#define 宏定义
---
（1）宏定义和const区别？

宏替换发生在编译阶段之前，属于文本插入替换；const作用发生于编译过程中。

宏不检查类型；const会检查数据类型。

宏定义的数据没有分配内存空间，只是插入替换掉；const定义的变量只是值不能改变，但要分配内存空间。

（2）宏定义和typedef区别？

宏主要用于定义常量及书写复杂的内容；typedef主要用于定义类型别名。

宏替换发生在编译阶段之前，属于文本插入替换；typedef是编译的一部分。

宏不检查类型；typedef会检查数据类型。

宏不是语句，不在在最后加分号；typedef是语句，要加分号标识结束。

注意对指针的操作，typedef char * p_char和#define p_char char *区别巨大。

（3）宏定义和内联函数(inline)区别？

使用宏定义的地方都可以使用inline函数；inline函数代码是被放到符号表中，使用时像宏一样展开，没有调用的开销，所以效率很高；

在使用时，宏只做简单字符串替换（编译前）；而内联函数可以进行参数类型检查（编译时），且具有返回值。

内联函数本身是函数，强调函数特性，具有重载等功能。

内联函数可以作为某个类的成员函数，这样可以使用类的保护成员和私有成员。而当一个表达式涉及到类保护成员或私有成员时，宏就不能实现了。

26 内联函数inline
---
## 内联函数inline
特征

- 相当于把内联函数里面的内容写在调用内联函数处，n次调用就有n次展开；相当于宏，却比宏多了类型检查，真正具有函数特性；
- 相当于不用执行函数调用进入函数的步骤，而是直接执行函数体；
- 编译器一般不内联包含循环、递归、switch 等复杂操作的内联函数；
- 在类声明中定义的函数，除了虚函数的其他函数都会自动隐式地当成内联函数。

编译器对 inline 函数的处理步骤;

将 inline 函数体复制到 inline 函数调用点处；为所用 inline 函数中的局部变量分配内存空间；将 inline 函数的的输入参数和返回值映射到调用方法的局部变量空间中；如果 inline 函数有多个返回点，将其转变为 inline 函数代码块末尾的分支（使用 GOTO）。

```
// 声明1（加 inline，建议使用）
inline int functionName(int first, int second,...);

// 声明2（不加 inline）
int functionName(int first, int second,...);

// 定义
inline int functionName(int first, int second,...) {/****/};

// 类内定义，隐式内联
class A {
    int doA() { return 0; }         // 隐式内联
}

// 类外定义，需要显式内联
class A {
    int doA();
}
inline int A::doA() { return 0; }   // 需要显式内联
```
优点

- 内联函数同宏函数一样将在被调用处进行代码展开，省去了参数压栈、栈帧开辟与回收，结果返回等，从而提高程序运行速度。
- 内联函数相比宏函数来说，在代码展开时，会做安全检查或自动类型转换（同普通函数），而宏定义则不会。
- 在类中声明同时定义的成员函数，自动转化为内联函数，因此内联函数可以访问类的成员变量，宏定义则不能。
- 内联函数在运行时可调试，而宏定义不可以。

缺点

- 代码膨胀。内联是以代码膨胀（复制）为代价，消除函数调用带来的开销。如果执行函数体内代码的时间，相比于函数调用的开销较大，那么效率的收获会很少。另一方面，每一处内联函数的调用都要复制代码，将使程序的总代码量增大，消耗更多的内存空间。
- inline 函数无法随着函数库升级而升级。inline函数的改变需要重新编译，不像 non-inline 可以直接链接。
- 对于类内函数是否内联，程序员不可控。内联函数只是对编译器的建议，是否对函数内联，决定权在于编译器。

为什么不能把所有的函数写成inline函数？
---
  内联函数以省去函数调用的开销来提高执行效率。所以一方面如果内联函数体内代码执行时间>>函数调用开销，没有太大的意义；另一方面每一处内联函数的调用都要复制代码，消耗更多的内存空间，因此如果函数体代码较长（占用空间大）或者函数循环较大（占用时间长），不宜使用内联函数。
  
虚函数（virtual）可以是内联函数（inline）吗？
--
Are "inline virtual" member functions ever actually "inlined"?

虚函数可以是内联函数，内联是可以修饰虚函数的，但是当虚函数表现多态性的时候不能内联。
内联是在编译器建议编译器内联，而虚函数的多态性在运行期，编译器无法知道运行期调用哪个代码，因此虚函数表现为多态性时（运行期）不可以内联。
inline virtual 唯一可以内联的时候是：编译器知道所调用的对象是哪个类（如 Base::who()），这只有在编译器具有实际对象而不是对象的指针或引用时才会发生。

27.exrern “Ｃ”关键字的作用？      
---
为了在C++文件中包含一些C写的文件，比如memset。为了兼容C，C++使用ifdef作为开关在C++文件重新编译该函数，添加一些符号修饰！
```
#ifdef __cplusplus
extern "C"{
#endif
 
void * memset(void * ,int,size_t);
 
#ifdef "__cplusplus"
}

```
答：一、 C++中调用C编译好的函数，通过extern "C" void fun(int a, int b)，告诉c++编译器在编译时按照c格式进行编译。C++语言支持函数重载，C语言不支持函数重载，函数被C++编译器编译后在库中的名字与C语言的不同，假设某个函数原型为：

   void foo(int x, inty);
	  
该函数被C编译器编译后在库中的名字为:  _foo。而C++编译器则会产生像: _foo_int_int之类的名字。_foo_int_int这样的名字包含了函数名和函数参数数量及类型信息，C++就是靠这种机制来实现函数重载的。 为了实现C和C++的混合编程，C++提供了C连接交换指定符号extern "C"来解决名字匹配问题，函数声明前加上extern "C"后，则编译器就会按照C语言的方式将该函数编译为_foo，这样C语言中就可以调用C++的函数了。


extern关键字的作用
---
extern修饰变量或者函数，作用是声明函数或者变量的作用范围

修饰函数：函数的声明extern关键词是可有可无的，因为函数本身不加修饰的话就是extern的。

修饰变量：

如A模块中的 extern int g_Int，只要在B模块包含了A的头文件，那么B就可以正常使用，在编译阶段起作用。但是有一点要注意，在使用extern时候要严格对应声明时的格式，比如A中定义char a[6]，B中定义extern char *a，那么会报错，因为没有严格匹配。

28 、volatile有什么作用？   --------》explicit
---
volatile 关键字是一种类型修饰符，用它声明的类型变量表示可以被某些编译器未知的因素（操作系统、硬件、其它线程等）更改。所以使用 volatile 告诉编译器不应对这样的对象进行优化。

volatile定义变量的值是易变的，每次用到这个变量的值的时候都要去重新读取这个变量的值，而不是读寄存器内的备份。

多线程中被几个任务共享的变量需要定义为volatile类型。


explicit的使用
```
struct A
{
	A(int) { }
	operator bool() const { return true; }
};

struct B
{
	explicit B(int) {}
	explicit operator bool() const { return true; }
};

void doA(A a) {}

void doB(B b) {}

int main()
{
	A a1(1);		// OK：直接初始化
	A a2 = 1;		// OK：复制初始化
	A a3{ 1 };		// OK：直接列表初始化
	A a4 = { 1 };		// OK：复制列表初始化
	A a5 = (A)1;		// OK：允许 static_cast 的显式转换 
	doA(1);			// OK：允许从 int 到 A 的隐式转换
	if (a1);		// OK：使用转换函数 A::operator bool() 的从 A 到 bool 的隐式转换
	bool a6（a1）;		// OK：使用转换函数 A::operator bool() 的从 A 到 bool 的隐式转换
	bool a7 = a1;		// OK：使用转换函数 A::operator bool() 的从 A 到 bool 的隐式转换
	bool a8 = static_cast<bool>(a1);  // OK ：static_cast 进行直接初始化

	B b1(1);		// OK：直接初始化
	B b2 = 1;		// 错误：被 explicit 修饰构造函数的对象不可以复制初始化
	B b3{ 1 };		// OK：直接列表初始化
	B b4 = { 1 };		// 错误：被 explicit 修饰构造函数的对象不可以复制列表初始化
	B b5 = (B)1;		// OK：允许 static_cast 的显式转换
	doB(1);			// 错误：被 explicit 修饰构造函数的对象不可以从 int 到 B 的隐式转换
	if (b1);		// OK：被 explicit 修饰转换函数 B::operator bool() 的对象可以从 B 到 bool 的按语境转换
	bool b6(b1);		// OK：被 explicit 修饰转换函数 B::operator bool() 的对象可以从 B 到 bool 的按语境转换
	bool b7 = b1;		// 错误：被 explicit 修饰转换函数 B::operator bool() 的对象不可以隐式转换
	bool b8 = static_cast<bool>(b1);  // OK：static_cast 进行直接初始化

	return 0;
}
```

29、int (*s[10])(int) 表示的是什么？
---
int (*s[10])(int) 函数指针数组s，每个指针指向一 int func(int param)的函数。

30、将程序跳转到指定内存地址
---
要对绝对地址0x100000赋值，我们可以用(unsigned int*)0x100000 = 1234;那么要是想让程序跳转到绝对地址是0x100000去执行，应该怎么做？
```cpp
　　*((void (*)( ))0x100000 ) ( );
　　首先要将0x100000强制转换成函数指针,即:
　　(void (*)())0x100000
　　然后再调用它:
　　*((void (*)())0x100000)();
　　用typedef可以看得更直观些:
　　typedef void(*)() voidFuncPtr;
　　*((voidFuncPtr)0x100000)();
  ```
31.STL库用过吗？常见的STL容器有哪些？算法用过哪几个？
---
答：STL包括三部分内容：容器、算法、迭代器（还有空间适配器）。（重要的还有融合这二者的迭代器）

容器，即存放数据的地方。比如array等。

在STL中，容器分为两类：序列式容器和关联式容器。

序列式容器，其中的元素不一定有序，但都可以被排序。如：vector、list、deque、stack、queue、heap、priority_queue、slist；

关联式容器，内部结构基本上是一颗平衡二叉树。所谓关联，指每个元素都有一个键值和一个实值，元素按照一定的规则存放。如：RB-tree、set、map、multiset、multimap、hashtable、hash_set、hash_map、hash_multiset、hash_multimap。

下面各选取一个作为说明。

vector：它是一个动态分配存储空间的容器。区别于c++中的array，array分配的空间是静态的，分配之后不能被改变，而vector会自动重分配（扩展）空间。Vector通过一个连续的数组存放元素，如果集合已满，在新增数据的时候，就要分配一块更大的内存，将原来的数据复制过来，释放之前的内存，在插入新增的元素；不同的编译器实现的扩容方式不一样，VS2015中以1.5倍扩容，GCC以2倍扩容。

注意：vector<MyString>vec;
	   vec.reserve(100);	//预先分配100个空间，vector的大小是按1 2 4 8 16 32.....的方式增长，若不预分配则会导致vec扩容时产生更多次数的拷贝构造函数。
	
set：其内部元素会根据元素的键值自动被排序。区别于map，它的键值就是实值，而map可以同时拥有不同的键值和实值。

算法，如排序，复制……以及个容器特定的算法。这点不用过多介绍，主要看下面迭代器的内容。

迭代器是STL的精髓，我们这样描述它：迭代器提供了一种方法，使它能够按照顺序访问某个容器所含的各个元素，但无需暴露该容器的内部结构。它将容器和算法分开，好让这二者独立设计。

32.头文件种的ifndef/define/endif 是干什么用的
---
      防止头文件被重复包含
      
可以通过加#define，并通过#ifdef来判断，将某些具体模块包括进要编译的内容。

用于子程序前加#define DEBUG用于程序调试。

应对硬件的设置（机器类型等）。

条件编译功能if也可实现，但条件编译可以减少被编译语句，从而减少目标程序大小。

源文件到可执行文件的过程？
---
预处理-------》编译------》链接

预处理-------》编译--------》汇编转二进制文件------》链接

```
//预处理：处理#include、宏替换和条件编译
//编译：进行词法与语法分析并生成目标文件（.cpp->.o），有时候分两步执行，先汇编再转二进制
//链接：链接多个目标文件
//例子：
//main.cpp
int main(){
    fun();  //编译不可通过，提示函数fun()未声明,找不到函数
}


void fun();
int main(){
    fun();//编译可通过，链接不可通过，提示函数fun(）未定义
}


//编译是把函数的符号地址记录下来，只要声明了就能通过，链接时该函数符号有定义才会变成具体的地址。
//到了链接阶段才会知道各个函数的调用关系，才能出现调用某个函数，但是该函数没有声明找不到的情况。
```

还有一种典型的编译出错，显示函数未定义：自己在main之后定义一个函数，但是在使用之前没有声明，直接使用也会报错。

静态链接和动态链接？
---
在我们的实际开发中，不可能将所有代码放在一个源文件中，所以会出现多个源文件，而且多个源文件之间会存在多种依赖关系，如一个源文件可能要调用另一个源文件中定义的函数。但是每个源文件都是独立编译的，即每个*.c文件会形成一个*.o文件。所以，在生成可执行文件时，则需要将这些源文件产生的目标文件进行链接，这个链接的过程就是静态链接。

- 静态链接的优缺点：

    静态链接的缺点很明显，一是浪费空间，每个可执行程序中对所有需要的目标文件都要有一份副本，所以同一个目标文件都在内存存在多个副本；另一方面就是更新比较困难，因为每当库函数的代码修改了，这个时候就需要重新进行编译链接形成可执行程序。但是静态链接的优点就是，在可执行程序中已经具备了所有执行程序所需要的任何东西，在执行的时候运行速度快。
    
动态链接：

为了解决静态链接的几个缺点设计的的。动态链接的基本思想是把程序按照模块拆分成各个相对独立部分，在程序运行时才将它们链接在一起形成一个完整的程序，而不是像静态链接一样把所有程序模块都链接成一个单独的可执行文件。虽然调用时才链接会损失一部分性能，据估算，动态链接和静态链接相比，性能损失大约在5%以下。但是经过实践证明，这点性能损失用来换区程序在空间上的节省和程序构建和升级时的灵活性是值得的。

在设计源文件时，在一个源文件中尽量少定义几个函数。因为链接器在链接静态链接库的时候是以目标文件为单位的。比如我们引用了静态库中的printf()函数，那么链接器就会把库中包含printf()函数的那个目标文件链接进来，如果很多函数都放在一个目标文件中，很可能很多没用的函数都被一起链接进了输出结果中。每个函数独立地放在一个目标文件中可以尽量减少空间的浪费，那些没有被用到的目标文件就不要链接到最终的输出文件中。

读取头文件的顺序
---
 
33 深拷贝和浅拷贝
---
判断是否需要自己写拷贝构造函数的标准：类中是否有指针成员变量！

调用拷贝构造函数的三种场景：

* 当出现对象的等号赋值时，会调用拷贝函数；
* 当出现把对象做函数参数进行传递时，会调用拷贝构造函数；
* 对象做函数返回值时，会调用拷贝构造函数。

在这三种场景中，如果在未显示定义拷贝构造函数的情况下，系统会调用默认的拷贝函数——即浅拷贝，它能够完成成员的一一复制。当数据成员中没有指针时，浅拷贝是可行的。但是若类成员变量有指针的话，会造成同一块资源释放多次，崩溃或者内存泄漏 。

浅拷贝：拷贝构造函数，赋值重载， 指针型成员变量只复制指针本身，而不复制指针所指向的目标--浅拷贝。 多个对象共用同一块资源，同一块资源释放多次，崩溃或者内存泄漏 。

深拷贝：每个对象共同拥有自己的资源，必须显式提供拷贝构造函数和赋值运算符，在这两个函数中，手动的在堆内存中另外申请空间来储存数据。

34 C++11 新特性 之右值引用、移动拷贝、移动赋值
---
[为什么需要右值引用？](https://github.com/Planck-a/skill/blob/master/C%2B%2B11%E6%96%B0%E7%89%B9%E6%80%A7.md)

35 什么时候需要初始化成员列表？必须在构造函数初始化式里进行初始化的数据成员有哪些？
---
## 初始化成员列表

1、const成员或引用类型的成员。 因为const对象或引用类型只能初始化，不能对他们赋值。 

2  引用类型，引用必须在定义的时候初始化，并且不能重新赋值，所以也要写在初始化列表里面

3 没有默认构造函数的类类型，因为使用初始化列表可以不必调用默认构造函数来初始化，而是直接调用拷贝构造函数初始化

4、调用数据成员对象或者基类的构造函数，且这个构造函数中有参数需要初始
```cpp
class Base  
{  
public:  
    Base(const string &str = "", int i = 0) : Bstr(str), _i(i) // 使用const引用避免复制,  
    // 如果只使用const则无法使用字面常量"DerivedStr"为str赋值  
    {  
        cout << "Base Constructor" << " Bstr = " << Bstr << ", _i = " << _i << endl;  
    }  
    string Bstr;  
    int _i;  
};  
class Derived : public Base  
{  
public:  
    // 调用基类构造函数，而它拥有一组参数时，要使用成员初始化列表  
    Derived() : Base("DerivedStr", 200)  // 这个是正确的  
    {  
        //Base::Bstr = "DerivedStr"; // 基类构造函数再次之前调用，这里赋值没有用。  
        //Base::_i = 200;  
        cout << "Derived Constructor" << endl;  
    }  
    string Dstr;  
};  
```
初始化列表的成员初始化顺序？
---
C++初始化类成员时，是按照声明的顺序初始化的，而不是按照出现在初始化列表中的顺序。

为什么调用初始化列表比在构造函数中初始化更快？
---
答：对于成员变量是内置类型的的，没有影响；对于成员变量是类对象的，如果在构造函数中初始化，则要先调用类的默认构造函数，再调用自己写的构造函数，而写在初始化列表中，少了一次调用默认构造函数的过程。
```
class A
{
public:
    A()
    {
        cout << "A()" << endl;
    }
    A(int a)
    {
        value = a;
        cout << "A(int "<<value<<")" << endl;
    }

    A(const A& a)
    {
        value = a.value;
        cout << "A(const A& a):  "<<value << endl;
    }
    int value;
};

class B
{
public:
    B() : a(1)
    {
        b = A(2);
    }
    A a;
    A b;
};
int main()
{
    B b;
    system("pause");
}
```
结果：
- A( int 1) 
- A()
- A(int 2)

能看出，对于在初始化列表就构造的对象a，并没有调用默认的构造函数，只是调用了自己写的构造函数；而对于在构造函数中才初始化的对象b，先会调用一次默认的A构造函数，然后才会调用自己写的构造函数。


36  变量声明和定义
---
extern int i; //声明，不分配内存；表示多个文件共享这个变量，在其中的某个文件中分配了内存
int i; //定义 ，分配内存
External int i=1;//定义，分配内存    
引用一个已经定义的全局变量，必须用extern；或者说全局变量在外部使用声明时，extern关键字是必须的。
谨记：声明可以多次，定义只能一次。

## 如何在多个文件共享一个全局变量
---
exrern关键字的作用

extern修饰变量或者函数，作用是声明函数或者变量的作用范围，如A模块中的 extern int g_Int，只要在B模块包含了A的头文件，那么B就可以正常使用，在编译阶段起作用。但是要注意：

（1）在使用extern时候要严格对应声明时的格式，比如A中定义char a[6]，B中定义extern char *a，那么会报错，因为没有严格匹配。

（2）还有一点，头文件只能声明，不能定义！

37 零值比较"？
---
bool类型：if(flag)
int类型：if(flag == 0)
指针类型：if(flag == null)
float类型：if((flag >= -0.000001) && (flag <= 0. 000001)) //float不能直接比较 a==0
 

38 strlen和sizeof区别？
---
strlen是函数，找到\0就停止；

sizeof是运算符，得到的是分配了多大的内存，传入数组指针时参数不退化，在编译时确定，所以不能用来得到动态分配（运行时分配）存储空间的大小。

## 各种sizeof

```cpp
int *a=new int(10);
cout<<sizeof(a)<<endl;//指针 ，结果是4

int a[3]={1,2,3};
cout<<sizeof(a)<<endl;//3*4=12

char* ss = "0123456789";
sizeof(ss) 结果 4 ＝＝＝》ss是指向字符串常量的字符指针
sizeof(*ss) 结果 1 ＝＝＝》*ss是第一个字符

char ss[] = "0123456789";
sizeof(ss) 结果 11 ＝＝＝》ss是数组，计算到\0位置，因此是10＋1
sizeof(*ss) 结果 1 ＝＝＝》*ss是第一个字符

char ss[100] = "0123456789";
sizeof(ss) 结果是100 ＝＝＝》ss表示在内存中的大小 100×1
strlen(ss) 结果是10 ＝＝＝》strlen是个函数内部实现是用一个循环计算到\0为止之前

int ss[100] = "0123456789";
sizeof(ss) 结果 400 ＝＝＝》ss表示再内存中的大小 100×4
strlen(ss) 错误 ＝＝＝》strlen的参数只能是char* 且必须是以''\0''结尾的

int a=10;
int &b=a;
cout<<sizeof(b)<<endl;//引用的结果是它指向地址的内存大小



结构体的 sizeof
union的  sizeof
类的  sizeof

空类的sizeof------》1，而不是0，需要注意

```

39 结构体内存对齐
---
## 结构体内存对齐

什么是对齐：在访问特定类型变量的时候经常在特定的内存地址访问，这就需要各种类型数据按照一定的规则在空间上排列，而不是顺序的一个接一个的排放，这就是对齐。如果不按照适合其平台要求对 数据存放进行对齐，会在存取效率上带来损失。（举例：对于32位系统，如果int型的起始地址为偶地址，那么一个周期可以读取；如果放在奇地址，需要两个周期，效率低）

（1）实际对齐单位n=min(n,max(所有成员变量))

（2）对于某一成员变量而言，它的起始地址必须是min(n,该成员变量大小)的整数倍

（3）最后计算出的整体大小，必须是n的整数倍。不够的要补上

（4）未特殊说明时，按结构体中size最大的成员对齐（若有double成员，按8字节对齐）。
```cpp
计算过程：
　struct stu3
　　{
　　char c1;//偏移量为0符合要求
　　int i;//偏移量为1， 结构体变量中成员的偏移量必须是成员大小的整数倍（0被认为是任何数的整数倍），故偏移量应为4
　　char c2;//偏移量为8（偏移量4+int大2小4），符合要求
}
```
  算出sizeof( stu3 )=1+8=9，但9不是int的整数倍故最终大小为12。
  
类对象的内存大小
---
（1）非静态成员的数据类型大小之和。

（2）编译器加入的额外成员变量（如指向虚函数表的指针）。

（3）为了边缘对齐优化加入的padding。

40、数组名和指针
---
数组名不是真正意义上的指针，可以理解为常指针，所以数组名没有自增、自减等操作。

当数组名当做形参传递给调用函数后，就失去了原有特性，退化成一般指针，多了自增、自减操作，但sizeof运算符不能再得到原数组的大小了。

41、野指针
---
不是指向null的指针，是指向垃圾内存的指针。

（1）指针变量未及时初始化 => 定义指针变量及时初始化，要么置空。

（2）指针free或delete之后没有及时置空 => 释放操作后立即置空。

42 union 联合
---
联合（union）是一种节省空间的特殊的类，一个 union 可以有多个数据成员，但是在任意时刻只有一个数据成员可以有值。当某个成员被赋值后其他成员变为未定义状态。联合有如下特点：

- 默认访问控制符为 public
-- 可以含有构造函数、析构函数
- 不能含有引用类型的成员
- 不能继承自其他类，不能作为基类
- 不能含有虚函数
- 匿名 union 在定义所在作用域可直接访问 union 成员
- 匿名 union 不能包含 protected 成员或 private 成员
- 全局匿名联合必须是静态（static）的

43、怎么判断两个struct相等？
---
答：我会选择重载==运算符，逐一比较成员变量是否相等；

那能不能用内存比较memcmp来判断呢？

A：不能，涉及字节对齐，可能有内存间隙，这里的值是随机的

44、struct和class 的区别
---
`c++中`

1.不同点

 	          struct	       class
	     
默认继承权限	     public              private

默认数据访问控制   public	        private

模板参数	      不能定义	       可以用于定义模板参数

2.相同点

可以继承，有数据成员，方法，构造函数等。成员都可以为public，protected，private

`C中`

struct没有函数成员
struct没有private、public、protected访问权限限定
struct没有继承关系

struct 和union的区别
---
* 所有成员函数共用内存块
* 共用体的大小为共用体中成员变量最大的那个。
* 在任意时刻，共用体中只存放了一个成员。
* 对公用体的成员赋值，将会对其他成员冲击，原来的成员值就不在了。


45、C++在main函数之前和之后都做了哪些工作？
---
相当于fork一个进程先做了哪些工作才执行main

main之前：

- （1）初始化环境变量
- （2）初始化堆，每个进程都有属于自己的堆
- （3） I/O初始化，继承父进程的文件描述符表
- （4）初始化C库的一些数据
- （5） 全局变量构造（调用全局变量构造函数），如各个全局类对象的构造函数调用和标记 __attribute__((constructor))属性的各个函数。

main之后：
- （1）析构函数，和标记 __attribute__((deconstructor))属性的各个函数在main之后会被调用，而且析构函数顺序和构造相反
- （2）释放堆
- （3）释放其他系统资源
- （4）调用exit系统API清除缓冲区后陷入内核态，调_exit系统调用退出进程。


## C++如何在main之前执行调用函数？

```cpp
答：
（1）全局对象、静态对象的构造函数在main( )函数之前执行。
(2)static func * before1[] = { before_main1 };  
(3)int g_iValue = func();
(4)linux下：
_attribute__((destructor)) void after_main() { 
  printf("after main\n"); }
（5）main函数之后：
_onexit_t _func{
   _onexit_t function
};
```


46 C++11支持的四种类型转换
---
## 强制类型转换
static_cast, dynamic_cast, const_cast, reinterpret_cast

static_cast
- 用的最多，对于各种隐式转换，非const转const，void*转指针等, static_cast能用于多态向上转化，如果向下转能成功但是不安全，结果未知；
- 通常用于转换数值数据类型（如 float -> int），用于非多态类型的转换
- 不执行运行时类型检查（转换安全性不如 dynamic_cast）。上安全，下危险！
- 可以在整个类层次结构中移动指针，子类转化为父类安全（向上转换），父类转化为子类不安全（因为子类可能有不在父类的字段或方法）

reinterpret_cast 
- 几乎什么都可以转，用来转两个不相关的变量，比如将指针转为整型，可能会出问题，尽量少用；
- 用于位的简单重新解释
- 滥用 reinterpret_cast 运算符可能很容易带来风险。 除非所需转换本身是低级别的，否则应使用其他强制转换运算符之一。
- reinterpret_cast 运算符不能丢掉 const、volatile 或__unaligned 特性。

const_cast
- 用于将const变量转为非const 
- 用于删除 const、volatile 和__unaligned 特性（如将 const int 类型转换为 int 类型 ）

dynamic_cast
- 用于动态类型转换。只能用于含有虚函数的类！用于类层次间的父类子类转化，出错会返回，是安全的
- 执行行运行时类型检查
- 只适用于指针或引用
- 对不明确的指针的转换将失败（返回 nullptr），但不引发异常
```
classB
{
public:
int m_iNum;
virtual void foo();
};
 
classD:publicB
{
public:
char* m_szName[100];
};
 
void func(B* pb)
{
D* pd1=static_cast<D*>(pb);
D* pd2=dynamic_cast<D*>(pb);
}
```
在上面的代码段中，如果 pb 指向一个 D 类型的对象，pd1 和 pd2 是一样的，并且对这两个指针执行 D 类型的任何操作都是安全的；但是，如果 pb 指向的是一个 B 类型的对象(向下转换)，那么 pd1 将是一个指向该对象的指针，对它进行 D 类型的操作将是不安全的（如访问 m_szName），而 pd2 将是一个空指针（出错返回null）。


bad_cast
- 由于强制转换为引用类型失败，dynamic_cast 运算符引发 bad_cast 异常。

bad_cast 使用
```
try {  
    Circle& ref_circle = dynamic_cast<Circle&>(ref_shape);   
}  
catch (bad_cast b) {  
    cout << "Caught: " << b.what();  
} 
```

运行时类型信息 (RTTI)
---
dynamic_cast 用于多态类型的转换



47 函数模板、类模板、特化和偏特化1、C++模板之间的关系
---
1、C++模板

说到C++模板特化与偏特化，就不得不简要的先说说C++中的模板。我们都知道，强类型的程序设计迫使我们为逻辑结构相同而具体数据类型不同的对象编写模式一致的代码，而无法抽取其中的共性，这样显然不利于程序的扩充和维护。C++模板就应运而生。C++的模板提供了对逻辑结构相同的数据对象通用行为的定义。这些模板运算对象的类型不是实际的数据类型，而是一种参数化的类型。C++中的模板分为类模板和函数模板。

注意：

编译器并不是把函数模板处理成能够处理任何类型的函数；函数模板通过具体类型产生不同的函数；编译器会对函数模板进行两次编译，在声明处对模板代码本身进行编译（检查语法），在调用处对参数替换后的代码进行编译。

函数模板的实例化是由编译程序在处理函数调用时自动完成的，而类模板的实例化必须有程序员在程序中显式地指定，即 函数模板允许隐式调用和显式调用而类模板只能显示调用。

注意在继承中，类模板的写法
```cpp
template<typename T>
class A{};
 
template<typename T>
class B:public A<typename T>{};


#include <iostream>
using namespace std;
  
template <class T>
class TClass
{
public:
     // TClass的成员函数  
private:
     T DateMember;
};


函数模板如下：
template <class T>
T Max(const T a, const T b)
{
     return  a > b ? a : b;
}
```
2、模板特化---->退化为普通的类

有时为了需要，针对特定的类型，需要对模板进行特化，也就是所谓的特殊处理。比如有以下的一段代码：
```
#include <iostream>
using namespace std;
  
template <class T>
class TClass
{
public:
     bool Equal(const T& arg, const T& arg1);
};
  
template <class T>
bool TClass<T>::Equal(const T& arg, const T& arg1)
{
     return (arg == arg1);
}
  
int main()
{
     TClass<int> obj;
     cout<<obj.Equal(2, 2)<<endl;
     cout<<obj.Equal(2, 4)<<endl;
}
```
类里面就包括一个Equal方法，用来比较两个参数是否相等；上面的代码运行没有任何问题；但是，你有没有想过，在实际开发中是万万不能这样写的，对于float类型或者double的参数，绝对不能直接使用“==”符号进行判断。所以，对于float或者double类型，我们需要进行特殊处理，处理如下：
```
#include <iostream>
using namespace std;
  
template <class T>
class Compare
{
public:
     bool IsEqual(const T& arg, const T& arg1);
};
  
// 已经不具有template的意思了，已经明确为float了
template <>
class Compare<float>
{
public:
     bool IsEqual(const float& arg, const float& arg1);
};
  
// 已经不具有template的意思了，已经明确为double了
template <>
class Compare<double>
{
public:
     bool IsEqual(const double& arg, const double& arg1);
};
  
template <class T>
bool Compare<T>::IsEqual(const T& arg, const T& arg1)
{
     cout<<"Call Compare<T>::IsEqual"<<endl;
     return (arg == arg1);
}
  
bool Compare<float>::IsEqual(const float& arg, const float& arg1)
{
     cout<<"Call Compare<float>::IsEqual"<<endl;
     return (abs(arg - arg1) < 10e-3);
}
  
bool Compare<double>::IsEqual(const double& arg, const double& arg1)
{
     cout<<"Call Compare<double>::IsEqual"<<endl;
     return (abs(arg - arg1) < 10e-6);
}
  
int main()
{
     Compare<int> obj;
     Compare<float> obj1;
     Compare<double> obj2;
     cout<<obj.IsEqual(2, 2)<<endl;
     cout<<obj1.IsEqual(2.003, 2.002)<<endl;
     cout<<obj2.IsEqual(3.000002, 3.0000021)<<endl;
}
```

3、模板偏特化------>匹配更准确的类模板，比如专门匹配 T* 或者const T* 的类

上面对模板的特化进行了总结。那模板的偏特化呢？所谓的偏特化是指提供另一份template定义式，而其本身仍为templatized；也就是说，针对template参数更进一步的条件限制所设计出来的一个特化版本。这种偏特化的应用在STL中是随处可见的。

与模板特化的区别在于，模板特化以后，实际上其本身已经不是templatized，而偏特化，仍然带有templatized。我们来看一个实际的例子：
```
#include <iostream>
using namespace std;
  
// 一般化设计
template <class T, class T1>
class TestClass
{
public:
     TestClass()
     {
          cout<<"T, T1"<<endl;
     }
};
  
// 针对普通指针的偏特化设计
template <class T, class T1>
class TestClass<T*, T1*>
{
public:
     TestClass()
     {
          cout<<"T*, T1*"<<endl;
     }
};
  
// 针对const指针的偏特化设计
template <class T, class T1>
class TestClass<const T*, T1*>
{
public:
     TestClass()
     {
          cout<<"const T*, T1*"<<endl;
     }
};
  
int main()
{
     TestClass<int, char> obj;
     TestClass<int *, char *> obj1;
     TestClass<const int *, char *> obj2;
  
     return 0;
}
```

4、特化与偏特化的调用顺序

对于模板、模板的特化和模板的偏特化都存在的情况下，编译器在编译阶段进行匹配时，是如何抉择的呢？从哲学的角度来说，应该先照顾最特殊的，然后才是次特殊的，最后才是最普通的。也就是哪个最优匹配，就选择哪个；如果都不匹配，那就选择通用的。编译器进行抉择也是尊从的这个道理。
  
##三个线程循环打印ABC
---
```CPP
#include <iostream>
#include <thread>
#include <condition_variable>
#include <vector>
#include <algorithm>
 
std::mutex mtx;
std::condition_variable cvar;
char g_ch = 0;
 
void print_fun(char ch)
{
	int cyle_cnt = 10;
	char ch_ = ch - 'A';
 
	for (int i = 0; i < cyle_cnt; i++)
	{
		std::unique_lock<std::mutex>ulk(mtx);
		cvar.wait(ulk, [ch_] {return ch_ == g_ch; });
		std::cout << (char)(ch_ + 'A');
		g_ch = (ch_ + 1) % 3;
		ulk.unlock(); 
		cvar.notify_all();
	}
} 
int main()
{
	std::vector<std::thread> threads;
	threads.push_back(std::thread(print_fun, 'A'));
	threads.push_back(std::thread(print_fun, 'B'));
	threads.push_back(std::thread(print_fun, 'C'));
 
	std::for_each(threads.begin(), threads.end(), std::mem_fn(&std::thread::join));
 
	std::cout << std::endl; 
	system("pause"); 
	return 0;
 }
```

   
49、++i和i++效率比较
--
答案： 

内建数据类型的情况，效率没有区别。 只有自定义数据类型的情况，++i效率较高。

解释：对于类对象的++i，因为前缀式（++i）可以直接返回对象的引用，而后缀式（i++）必须产生一个临时对象保存更改前对象并返回，(实现过自定义类型++运算符定义的就知道)，所以导致在大对象的时候会额外产生临时对象，产生了较大的复制开销，引起效率降低，因此处理使用者自定义类型（注意不是指内建类型）的时候，应该尽可能的使用前缀式地增/递减。

50、函数返回局部变量问题
---
返回 数值没有问题！在C中，16bit程序中，返回值保存在ax寄存器中，32bit程序中，返回值保持在eax寄存器中，如果是64bit返回值，edx寄存器保存高32bit，eax寄存器保存低32bit。 综上，函数是可以将临时变量的值作为返回值的。

返回 字符常量区指针没有问题
返回 栈区字符指针乱码！
返回 static没有问题！
返回指向全局变量 没有问题！
```
#include <stdio.h>   
char *returnStr()   
{   
    char *p="hello world!";   //字符常量区
    return p;   
}   
int main()   
{   
    char *str;   
    str=returnStr();   
    printf("%s\n", str);   //显示不会报错
    return 0;   
}  

#include <stdio.h>   
char *returnStr()   
{   
    char p[]="hello world!";   //栈区
    return p;   //返回会显示乱码
}   
int main()   
{   
    char *str;   
    str=returnStr();   
    printf("%s\n", str);   
    return 0;   
} 


#include <stdio.h>   
char *returnStr()   
{   
    static char p[]="hello world!";   
        //声明为static可解决上述问题，同理返回数组也要声明为static
    return p;   
}   
int main()   
{   
    char *str;   
     str=returnStr();   
    printf("%s\n", str);   
  
    return 0;   
}   
```
51、rand()和srand()
---
rand()要产生0-99的随机数：rand()%100

srand() 用来设置 rand() 产生随机数时的随机数种子，srand(unsigned int seed),如果每次 seed 都设相同值，rand() 所产生的随机数值每次就会一样。

一般会设置成 srand(time(0))，来产生不同的随机种子

## 程序分析题
---
[牛客程序分析题集合](https://www.nowcoder.com/ta/review-c/review?page=1)

（1）请问运行Test函数会有什么样的结果？
```
void GetMemory(char *p)
{
   p=(char*)malloc(100);
}

void Test(void)
{
 char *str = NULL;
 GetMemory(str);
 strcpy(str,"helloworld");
 printf(str);
}
```
答：程序崩溃。因为GetMemory并不能传递动态内存，Test函数中的str一直都是NULL。strcpy(str,"helloworld");将使程序崩溃。p参数是复制原有指针的值，而不是原来的指针，所以即使p重新指向新的地址，但原来的指针指向的地址不变。

(2)请问运行Test函数会有什么样的结果？
```
char *GetMemory(void)
{
 char p[]="helloworld";
 return p;
}
void Test(void)
{
 char *str = NULL;
 str = GetMemory();
 printf(str);
}
```
答：可能是乱码。因为GetMemory返回的是指向“栈内存”的指针，该指针的地址不是NULL，但其原先的内容已经被清除，新内容不可知。p是局部变量，在离开作用域后栈空间会被回收，结果不可预料。

(3)请问运行Test函数会有什么样的结果？
```
void GetMemory2(char **p, int num)
{
 *p = (char*)malloc(num);
}
void Test(void)
{
 char *str = NULL;
 GetMemory(&str, 100);
 strcpy(str, "hello");
 printf(str);
}
```
请问运行Test函数会有什么样的结果？
答：（1）能够输出hello（2）内存泄漏     p是指向传入的指针的指针，*p为传入的指针赋值，所以改变了传入指针的值，

（4）
```
void test1()
{
 char string[10];
 char* str1 = "0123456789";
 strcpy( string, str1 );
}
```
字符串str1需要11个字节才能放得下（包括末尾的’\0’），strcpy会导致数组越界；  

（5）
```
void test2()
{
 char string[10], str1[10];
 int i;
 for(i=0; i<10; i++)
 {
 str1  = 'a';
 }
strcpy( string, str1 );
}
```
错误一：str1[i]  = 'a';

错误二：strcpy会从源地址一直往后拷贝，直到遇到'\0'为止。如果一直没有遇到'\0'导致越界访问非法内存，程序就崩了。
```
正确版本：
void test2()
{
    char string[10], str1[10];
    int i;
    for(i=0; i<9; i++)
    {
        str1[i]  = 'a';
    }
    str1[9] = '\0';
    strcpy( string, str1 );
}
```
## 各种锁机制
---
Linux的4种锁机制：

① 互斥锁：mutex，用于保证在任何时刻，都只能有一个线程访问该对象。当获取锁操作失败时，线程会进入睡眠，等待锁释放时被唤醒

② 读写锁：rwlock，分为读锁和写锁。处于读操作时，可以允许多个线程同时获得读操作。但是同一时刻只能有一个线程可以获得写锁。其它获取写锁失败的线程都会进入睡眠状态，直到写锁释放时被唤醒。 注意：写锁会阻塞其它读写锁。当有一个线程获得写锁在写时，读锁也不能被其它线程获取；写者优先于读者（一旦有写者，则后续读者必须等待，唤醒时优先考虑写者）。适用于读取数据的频率远远大于写数据的频率的场合。

③自旋锁：spinlock，在任何时刻同样只能有一个线程访问对象。但是当获取锁操作失败时，不会进入睡眠，而是会在原地自旋，直到锁被释放。这样节省了线程从睡眠状态到被唤醒期间的消耗，在加锁时间短暂的环境下会极大的提高效率。但如果加锁时间过长，则会非常浪费CPU资源。

④RCU：即read-copy-update，在修改数据时，首先需要读取数据，然后生成一个副本，对副本进行修改。修改完成后，再将老数据update成新的数据。使用RCU时，读者几乎不需要同步开销，既不需要获得锁，也不使用原子指令，不会导致锁竞争，因此就不用考虑死锁问题了。而对于写者的同步开销较大，它需要复制被修改的数据，还必须使用锁机制同步并行其它写者的修改操作。在有大量读操作，少量写操作的情况下效率非常高。 

mysql：

① 乐观锁：在写时才会加锁检查，读时不检查。适用于读>>写

② 悲观锁：每次拿数据时都加锁。关系型数据库中的行锁、表锁、读写锁都属于悲观锁。

54、exit和_exit区别
---
```
int main(int argc , char* argv[])
{ 
　　printf("hello\n")
　　printf("world");
　　exit(0);    //输出hello world
}
int main(int argc , char* argv[])
{ 
　　printf("hello\n")
　　printf("world");
　　_exit(0);  //输出hello 
}
```
区别：_exit不带缓冲区，exit带缓冲区。带缓冲区的，只有缓冲区刷新，才会打印出来！

在linux的标准库函数中，有一套称作高级I/O的函数，我们熟知的printf 、fopen 、fread 、fwrite都在此列，他们也被称作缓冲I/O。其特征是对应每一个打开的文件，在内存中都有一片缓冲区，每次读文件会多读若干条记录先放到缓冲区中，这样下次读文件时就可以直接从内存的缓存中取出；同样每次写文件时也仅仅是写入到内存的缓冲区，等待满足一定的条件（达到一定的数量，或者遇到特定字符，如换行和文件结束符EOF），再将缓冲区的内容一次性的写入文件，这样就大大增加了文件读写的速度。

换行符是缓冲区往文件中写的一个标识符。

因为最后一句话上没有加上特殊的字符，如换行和文件结束符，因为这个时候文件是存放在缓冲区的。exit(0)的作用是检查缓冲区，把没有写入的数据写入到文件。_exit是立刻关闭文件，文件缓冲区的内容也就消失了，这个时候就不可能再输出到显示设备了。如果向保证数据的完整性，就一定要使用exit函数。

## Linux中write和fwrite
read/write 是系统调用，read每次读的数据是调用者要求的大小，比如调用要求读取10个字节数据，read就会读10个字节数据到数组中。

fread就是通过read来实现的，为了加快读的速度，fread每次都会读比要求更多的数据，然后放到缓冲区中，这样下次再读数据只需要到缓冲区中去取就可以了。缓冲区大小为4096字节。

55 、C++参数入栈顺序是怎样的？为什么采用从右到左的参数方式,而不使用从左到右的传参方式呢?
---
C++的参数是从右到左入栈的，第一个参数在栈顶。

这样设计的主要原因就在于处理变长参数.

一般我们命名一个变长函数时都类似于int display(int i, ...);注意参数都是用压栈方式实现的,

这里我用反证法来举证：

假如使用从左到右的传参方式,栈顶看到的是最后一个参数,那么我怎样知道首参数是哪一个呢?

因为要想知道首参数的值,则必须要知道参数的长度.而栈里并不知道这个长度,那么就无法通过指针偏移的方式找到首参数.

但是，如果使用从右到左的传参方式,栈顶看到的就是左边输入的首参数,因此,无论怎样的变长,都可以通过指针偏移的方式找到值.

56、字典树
---

## 仿函数
```
template<typename T>
struct plus:public binarry_function<T,T>{
    T operator()(const T& x,const T& y){
        return x+y;
    }
}
```

## 迭代器失效

### vector的迭代器失效
* **插入元素**
  * 插入一个元素后，end操作返回的迭代器肯定失效
  * 插入一个元素但是capacity没有变，那么插入点之前的迭代器有效，之后的iterator会失效
  * 插入元素导致capacity不够，此时first和end的操作返回的迭代器都会失效
* **删除元素**
  * 删除点之前的迭代器有效，指向删除点及以后的全部迭代器失效
  
错误做法：  
```
while (it != v.end())
    {
        if (*it % 2 == 0)
        {
            it = v.erase(it);//错误！！
            ++it;
        }
        
    }
```
正确的做法：
```
while (it != v.end())
    {
        if (*it % 2 == 0)
        {
            it = v.erase(it);//直接返回删除点之后节点的位置，不需要++
        }
        else
        {
            ++it;
        }        
    }
```


