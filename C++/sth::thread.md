* **thread并发编程总结**
  * [管理线程](#管理线程) (`easy` `基础概念`)
       * [线程异常的捕获](#线程异常的捕获)
       * [传递参数给线程](#传递参数给线程)
       * [线程绑核](#线程绑核)
  * 线程间共享数据




## 管理线程
### 1、线程异常的捕获
<details><summary>当你在main thread中调用 sub thread时，如果main thread出现异常，导致未能及时调用sub thread的join()，可能会引发未知问题。</summary>
示例如下：
 
```c++
void func_main(){
    std::thread t(myfunc);
    do_something_in_current_thread();//异常发生地
    t.join();
}
```
为了解决这种问题，有两种解决办法：

（1）throw-catch
```c++
void func_main(){
    std::thread t(myfunc);
    try{
        do_something_in_current_thread();//异常发生地
    }
    catch(...){
        t.join();
        throw;
    }
    t.join();
}
```

(2)RAII管理线程生命周期---->更为优雅的方式
```c++
class Thread_guard{
    std::thread m_t; //默认private
public:
    //单参数构造函数建议声明为explicit
    explicit Thread_guard(std::thread& t):m_t(t){}
    ~Thread_guard(){
        if(m_t.joinable()){
            t.join();
        }
    }
    //拷贝和赋值构造函数标记为delete，因为可能会导致thread_guard变量的声明周期变成，达不到期望的析构时立即join的操作
    Thread_guard(Thread_guard const&)=delete;   
    Thread_guard& operator=(Thread_guard const&)=delete;  
}
void func_main(){
    std::thread t(myfunc);
    Thread_guard(t);
    do_something_in_current_thread();//异常发生地点
}
```
</details>

### 2、传递参数给线程

线程函数默认是按值传递参数，根据thread构造函数传进来的参数，在类内部生成一份副本，再将该副本按照线程函数要求的传值or引用进行传递，这样会引发的问题有两个，第一是不会进行隐式类型转换，导致指针类型的浅拷贝问题，第二个是引用失效问题。

示例1：
```c++
void f(int i,std::string const& s);
void main_thread(){
    char buffer[1024];
    sprinf(buffer,"%i",some_param);
    std::thread t(f,3,buffer); //期望是传引用，实际buffer在main_thread调用结束就会析构，buffer指向的内存地址会被清空，t中若访问s就会发生未定义行为
    //std::thread t(f,3,std::string(buffer));  正确做法是由std::string 生成临时变量，去托管buffer
    t.detach();
}
```

示例2：ref来显示声明引用
```
std::thread t(update_data,std::ref(data));

```

参数传递还有一点需要注意，如果线程函数参数是movable，但不是copyable。比如unique_ptr需要在构造函数中指定std::move(p)

```c++
void process_big_object(std::unique_ptr<big_object> a);

{
    std::unique_ptr<big_object> p(new big_object);
    p->prepare_data(42);
    std::thread t(process_big_object,std::move(p));
}

```


### 3、线程绑核
