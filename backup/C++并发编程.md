# std::thread的基本操作
## 发起线程
  在定义一个 std::thread 变量时，可以在该线程中指定要执行的函数
```c++
void thread_work1(std::string str){
    std::cout<<"Thread work 1 start"<<std::endl;
    std::cout<<"str is "<<str<<std::endl;
}

int main() {
    std::thread t1(thread_work1,"Hello, World!");
    return 0;
}
```
## 线程等待
当线程启动时，线程可能不会立即执行，如果在局部作用域启动了一个线程，或者main函数中，很可能子线程没运行就被回收了，回收时会调用线程的析构函数，执行terminate操作。为了防止主线程退出或者局部作用域结束导致子线程被析构的情况，可以通过join函数，让主线程等待子线程运行，等子线程运行结束后再结束主线程
```C++
void thread_work1(const std::string &str){
    spdlog::info("str is {}",str);
}

int main() {
    // 设置 spdlog 显示线程 ID 的日志格式
    spdlog::set_pattern("[%Y-%m-%d %H:%M:%S] [Thread %t] [%l] %v");
    std::thread t1(thread_work1,"Hello, World!");
    // 主线程等待t1线程执行完
    spdlog::info("this is main thread");
    t1.join();
    return 0;
}
```
## 仿函数作为参数
在构造std::thread 对象时，也可以将仿函数作为参数传递，使得线程执行仿函数的内容
```C++
class background_task {
public:
    void operator()(const std::string &str) {
        log(str);
    }
};
    std::thread t2(background_task(),"hello world");
    std::thread t3{background_task(), "hello world"};
```
直接传递 background_task() 是错误的，这样会使得编译器将本应创建的线程当作一个函数对象，返回值是 std::thread，参数是一个函数指针
## lambda 表达式
lambda 表达式也可以作为参数传递给std::thread
```C++
 std::thread t4([](const std::string& str){
        log(str);
    },"Lambda thread");
    t4.join();
```
## detach
线程允许通过 detach 将其分离到后台运行，C++ concurrent programing书中称其为守护线程
```C++
struct func{
    int& _i;
    explicit func(int &i):_i(i){};
    void operator()(){
        for(int i = 0;i < 3;i++){
            _i = i;
            log("_i is "+std::to_string(_i));
            std::this_thread::sleep_for(std::chrono::seconds(1));
        }
    }
};

void oops(){
    int some_local_state = 0;
    func myfunc(some_local_state);
    std::thread functhread(myfunc);
    //隐患，访问局部变量，局部变量可能会随着}结束而回收或随着主线程退出而回收
    functhread.detach();
}

int main() {
    oops();
    //防止主线程退出过快，需要停顿一下，让子线程跑起来detach
    std::this_thread::sleep_for(std::chrono::seconds(1));
}
```
因为 `some_local_state` 是局部变量，当oops()调用结束后`some_local_state`可能就被释放了，而线程通过detach还在后台运行，所以会崩溃
为了解决在线程中使用局部变量的问题，可以采取如下措施：
- 通过智能指针传递参数，因为引用计数会随着赋值增加，可保证局部变量在使用期间不被释放，这也就是我们之前提到的伪闭包策略。
- 将局部变量的值作为参数传递，这么做需要局部变量有拷贝复制的功能，而且拷贝耗费空间和效率。
- 将线程运行的方式修改为join，这样能保证局部变量被释放前线程已经运行结束。但是这么做可能会影响运行逻辑。
上面的例子有如下几种修改方式：
1. some_local_state 作为oops的参数传递（智能指针或值传递）
 ```C++
struct func{
    int& _i;
    explicit func(int &i):_i(i){};
    void operator()(){
        for(int i = 0;i < 3;i++){
            _i = i;
            log("_i is "+std::to_string(_i));
            std::this_thread::sleep_for(std::chrono::seconds(1));
        }
    }
};

void oops(int &i){
    func myfunc(i);
    std::thread functhread(myfunc);
    functhread.detach();
}

int main() {
    log("This is main thread");
    int some_local_state = 0;
    oops(some_local_state);
    //防止主线程退出过快，需要停顿一下，让子线程跑起来detach
    std::this_thread::sleep_for(std::chrono::seconds(5));
    return 0;
}
```
2. 使用join
```C++
void use_join() {
    int some_local_state = 0;
    func myfunc(some_local_state);
    std::thread functhread(myfunc);
    functhread.join();
}
```