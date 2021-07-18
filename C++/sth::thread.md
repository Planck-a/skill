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

### 3、线程绑核
