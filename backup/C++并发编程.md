记录《C++Concurrency In Action》的学习过程
# 并发
并发的两种方式：真正的并行与任务切换
![image](https://github.com/user-attachments/assets/11994029-b407-4a19-9374-de8ad04bc5de)
## 多进程并发
将应用程序分为多个独立的进程同时运行；不同的进程可以通过信号、套接字等渠道进行通信，但是这种进程间的通信复杂，且速度慢。因为操作系统会对进程进行保护，以避免一个进程去修改另一个进程的数据；另外，运行多个进程还有固定开销：需要时间启动进程，操作系统需要资源来管理进程等等
![image](https://github.com/user-attachments/assets/77866f24-9af1-4ea4-aa4f-f478314d8971)
这种并发方式可以更容易编写安全的并发代码；也可以使用远程连接的方式，在不同机器上运行独立的进程
## 多线程并发
指的是在单进程中运行多个线程，每个线程相互独立运行，并且可以在不同的指令序列中运行；进程中的所有线程都共享地址空间，并且能访问到大部分数据
![image](https://github.com/user-attachments/assets/feded280-ebc0-4fd6-8196-f8957cb579a3)
虽然多线程开销远远小于多进程，但是如果多个线程同时访问数据，必须保证每个线程访问的数据一致

# 线程管理
## 线程的基本操作
为线程创建`std::thread`对象后，需要等这个线程结束
### 启动线程
线程在`std::thread`对象创建时就会启动，其参数通常是无参数无返回值的函数，在这个函数执行完毕后，线程也就结束了
利用`spdlog`进行日志打印，显示执行任务的线程
```C++
void log(const std::string &str) {
    spdlog::set_pattern("[%Y-%m-%d %H:%M:%S] [Thread %t] [%l] %v");
    spdlog::info(str);
}
```
`std::thread`构造函数接收可调用对象（如函数指针、lambda表达式、函数对象等），并在新线程中执行
***
#### 仿函数（函数对象）作`std::thread`构造的参数：
```C++
class background_task{
    public:
        void operator()()const {
            log("Functor Thread");
        }
    };
background_task f;
std::thread my_thread(f);
```
执行时，函数对象f会拷贝（拷贝构造）到新线程的存储空间，函数对象的执行和调用都在新线程的内存空间中执行，新线程结束时，析构线程内的函数对象；
需要注意，如果`std::thread`的构造函数直接传输一个临时的函数对象变量，C++编译器会将其解释为一个函数声明：
`std::thread my_thread(background_task());`
也就是会解析为创建一个`my_thread`函数，返回值是`std::thread`，参数是一个函数指针，指向没有参数并返回background_task对象的函数；
如果不需要提前创建函数对象，可以使用多组括号、初始化语法来解决上述问题：
`std::thread my_thread((background_task()));  // 1`
`std::thread my_thread{background_task()};    // 2`
***
#### 使用lambda表达式作`std::thread`构造的参数：
改写为：
```C++
std::thread my_thread([]{
  log("back_ground task");
});
```
***
#### detach
线程创建后，需要指出让线程分离到后台运行还是直接运行，通过`detach`的方式可以将线程放到后台运行
```C++
  struct func {
        int &i;

        func(int &i_) : i(i_) {}

        void operator()() const{
            for (unsigned j = 0; j < 1000000; ++j) {
                log(std::to_string(i));          // 1 潜在访问隐患：空引用
            }
        }
    };

    void oops() {
        int some_local_state = 0;
        func my_func(some_local_state);
        std::thread my_thread(my_func);
        my_thread.detach();          // 2 不等待线程结束
    }                              // 3 新线程可能还在运行
```
当`oops()`执行完后，线程`my_thread`可能还在后台运行，这时就会执行`log(std::to_string(i))`，访问已销毁的变量
`oops()`的执行过程：
| 主线程| 新线程|
| ----------- | ----------- |
| 使用`some_local_state`构造`my_func`| |
| 开启`my_thread`| 启动 |
||调用`operator()`|
|分离`my_thread`|继续执行，可能会调用`some_local_state`的引用|
|销毁`some_local_state`|持续运行|
|退出`oops`函数|持续执行`operator()`|可能会调用some_local_state的引用 --> 导致未定义行为|
解决方法：
1. 通过智能指针传递参数，因为引用计数会随着赋值增加，可保证局部变量在使用期间不被释放
2. 将局部变量的值作为参数传递，这么做需要局部变量有拷贝复制的功能
3. 将线程运行的方式修改为join，这样能保证局部变量被释放前线程已经运行结束。但是这么做可能会影响运行逻辑。
#### join
使用`join()`可以确保主线程等待子线程完成任务后才继续执行，防止子线程意外终止，同时也可以清理与线程相关的内存资源（操作系统级别的句柄等）
#### 异常处理
为了防止程序出现异常，在调用`join()`之前抛出，需要在异常处理中调用`join()`，避免生命周期的问题，可以使用“资源获取即初始化方式”(RAII，Resource Acquisition Is Initialization)，提供一个类，在析构函数中使用join()
```C++
class thread_guard
{
  std::thread& t;
public:
  explicit thread_guard(std::thread& t_):
    t(t_)
  {}
  ~thread_guard()
  {
    if(t.joinable()) // 1
    {
      t.join();      // 2
    }
  }
  thread_guard(thread_guard const&)=delete;   // 3
  thread_guard& operator=(thread_guard const&)=delete;
};

struct func; // 定义在代码2.1中

void f()
{
  int some_local_state=0;
  func my_func(some_local_state);
  std::thread t(my_func);
  thread_guard g(t);
  do_something_in_current_thread();
}    // 4
```
线程执行到④时，局部变量就要被销毁，调用`thread_guard`的析构，也就是t的`join`；`thread_guard`的拷贝构造函数和拷贝赋值标记为`delete`是很有必要的，这样可以防止记录的线程混乱
#### 后台运行线程
detach分离的线程称为**守护线程**，这种线程的特点就是长时间运行，线程的生命周期可能会从应用的起始到结束，可能会在后台监视文件系统，还有可能对缓存进行清理，亦或对数据结构进行优化。另外，分离线程只能确定线程什么时候结束，发后即忘(fire and forget)的任务使用到就是分离线程。
## 传递参数
`std::thread`构造函数传入的函数有参数的话，依次添加即可，这些参数会拷贝到新线程的内存空间中（与临时变量一样），**即使函数中的参数是引用的形式，拷贝操作也会执行**
### 类型转换
```C++
void f(int i, std::string const& s);
std::thread t(f, 3, "hello");
```
上述代码创建了一个调用f(3, "hello")的线程，函数要求一个`std::string`对象作为第二个参数，但这里用的是`const char *`类型，线程上下文自动完成类型转换，如果指向动态变量的指针作为参数，就需要注意了：
```C++
void f(int i,std::string const& s);
void oops(int some_param)
{
  char buffer[1024]; // 1
  sprintf(buffer, "%i",some_param);
  std::thread t(f,3,buffer); // 2
  t.detach();
}
```
buffer①是一个指向局部变量的指针变量，这个局部变量通过buffer传递到新线程中②。函数`oops`可能在buffer转换为`std::string`之前结束，buffer可能会引用已经销毁的内存；解决方法就是显式转换为`std::string`
```C++
void f(int i,std::string const& s);
void not_oops(int some_param)
{
  char buffer[1024];
  sprintf(buffer,"%i",some_param);
  std::thread t(f,3,std::string(buffer));  // 使用std::string，避免悬空指针
  t.detach();
}
```
### 引用参数
`std::thread`构造传入的函数参数为引用类型，需要将参数显示转化为引用对象传递给线程的构造函数，否则编译不通过
```C++
 void change_param(int& param) {
        param++;
    }
    void ref_oops(int some_param) {
        std::cout << "before change , param is " << some_param << std::endl;
        //需使用引用显示转换
        std::thread  t2(change_param, some_param);
        t2.join();
        std::cout << "after change , param is " << some_param << std::endl;
    }
```
就像上文说的，`std::thread`会无视参数类型，永远都会拷贝已提供的变量，内部代码会将拷贝的参数以右值的方式进行传递，会尝试以这个右值为实参调用`change_param`这个函数，而该函数的参数是非常量引用做参数，所以编译出错；
通过`std::ref`将参数转换成引用的形式
`std::thread  t2(change_param, std::ref(some_param));`
这样`change_param`就会收到`some_param`的引用了
### 类的成员函数
需要让线程执行类的成员函数时：
```C++
class X
{
public:
    void do_lengthy_work() {
        std::cout << "do_lengthy_work " << std::endl;
    }
};
void bind_class_oops() {
    X my_x;
    std::thread t(&X::do_lengthy_work, &my_x);
    t.join();
}
```
第一个参数是要执行的成员函数指针，第二个参数是类的地址，后面的参数依次就是成员函数的参数
```C++
class X
{
public:
  void do_lengthy_work(int);
};
X my_x;
int num(0);
std::thread t(&X::do_lengthy_work, &my_x, num);
```
### std::move
如果传递给线程的参数是独占的（不支持拷贝构造和拷贝赋值），就通过`std::move`将变量的所有权转移给新线程
```C++
void deal_unique(std::unique_ptr<int> p) {
    std::cout << "unique ptr data is " << *p << std::endl;
    (*p)++;
    std::cout << "after unique ptr data is " << *p << std::endl;
}
void move_oops() {
    auto p = std::make_unique<int>(100);
    std::thread  t(deal_unique, std::move(p));
    t.join();
    //不能再使用p了，p已经被move废弃
   // std::cout << "after unique ptr data is " << *p << std::endl;
}
```