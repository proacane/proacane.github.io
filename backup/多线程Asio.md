# 多线程模型
asio 的多线程模型有两种，IOServicePool 和 IOThreadPool
# IOServicePool
IOServicePool模型：
![image-20240801133223177](https://github.com/user-attachments/assets/c036abee-852d-42a7-ab2d-b2acb054f14f)
特点:
1. 每个 io_context 都运行在不同的线程内，对于一个 socket，会注册在同一个 io_context 内，并且回调函数的触发也是在同一个线程内，不存在并发安全问题
2. 不同的 socket，回调函数可能由不同的线程调用，如果回调函数中进行了共享数据的修改，就会存在线程安全问题，这时可以在回调函数中加锁或者使用逻辑队列的方式
3. 多线程模式极大的提高了并发能力；单线程模式下如果回调函数调用时间过长，会影响后续函数的调用；通过逻辑队列的方式将网络线程和逻辑线程解耦合了，不会出现前一个调用时间影响下一个回调触发的问题
## 实现

本质就是一个线程池，基本功能就是根据构造函数传入的数量创建n个线程和 io_context，然后每个线程跑一个 io_context，这样就可以并发处理不同 io_context 读写事件了。

io_context.run() 阻塞住的原因是调用之前 io_context 已经注册了监听事件。如果没有其他任务需要执行，那么`io_context`就会停止工作，导致所有正在进行的异步操作都被取消。

这时，我们需要使用`boost::asio::io_context::work`对象来防止`io_context`停止工作。`boost::asio::io_context::work`的作用是持有一个指向`io_context`的引用，并通过创建一个“工作”项来保证`io_context`不会停止工作，直到work对象被销毁或者调用`reset()`方法为止。当所有异步操作完成后，程序可以使用`work.reset()`方法来释放`io_context`，从而让其正常退出。

线程池要继承单例模板类，使用 vector 来存储多个 io_context 实例、对应的 work、及线程；采用简单的轮询算法实现负载均衡

```c++

#include "Singleton.h"
#include <boost/asio.hpp>
#include <vector>

#ifndef IOSERVICEPOOL_ASIOIOSERVICEPOOL_H
#define IOSERVICEPOOL_ASIOIOSERVICEPOOL_H

class AsioIOServicePool : public Singleton<AsioIOServicePool> {
    friend class Singleton<AsioIOServicePool>;
public:
    using IOService = boost::asio::io_context;
    using work  = boost::asio::io_context::work;
    // 使 work 不被拷贝
    using workPtr = std::unique_ptr<work>;
    ~AsioIOServicePool();
    // 使用 round-robin 的方式返回一个 io_context
    boost::asio::io_context& getIOService();
    void stop();
private:
    /**
     * @param size 获取 cpu 核数
     */
    AsioIOServicePool(std::size_t size = std::thread::hardware_concurrency());

    // 存储初始化的多个 io_context 实例
    std::vector<IOService> _io_services;
    // 存储 work 对象的智能指针，确保 io_context 不会在没有工作的情况下退出
    std::vector<workPtr> _works;
    // 管理线程，用于运行 io_context::run()
    std::vector<std::thread> _threads;
    // 轮询索引,用于实现负载均衡
    std::size_t _next_io_service;

    AsioIOServicePool(const AsioIOServicePool&) = delete;
    AsioIOServicePool& operator=(const AsioIOServicePool&) = delete;
};

#endif //IOSERVICEPOOL_ASIOIOSERVICEPOOL_H
```

构造函数中，初始化 io_context 实例数组与 work 对象，并进行分配，为每个 io_context 创建线程并运行

```cpp
#include "../include/AsioIOServicePool.h"

AsioIOServicePool::AsioIOServicePool(std::size_t size) : _io_services(size), _works(size), _next_io_service(0) {
    // 为每个 io_context 分配一个 work
    for (std::size_t i = 0; i < size; i++) {
        _works[i] = std::unique_ptr<work>(new work(_io_services[i]));
    }
    // 为每个 io_context 创建一个线程，并调用 run
    for (std::size_t i = 0; i < _io_services.size(); i++) {
       // 调用 thread 的构造函数构造线程
        _threads.emplace_back([this, i]() {
            _io_services[i].run();
        });
    }
}

AsioIOServicePool::~AsioIOServicePool() {
    std::cout << "AsioIOServicePool destructor\n";
}

boost::asio::io_context &AsioIOServicePool::getIOService() {
    auto &service = _io_services[_next_io_service];
    // 轮询策略返回 io_context 实例
    _next_io_service = (_next_io_service + 1) % _io_services.size();
    return service;
}

void AsioIOServicePool::stop() {
    // 清除所有 work 对象，停止 io_context 的运行
    for(auto &work:_works){
        work.reset();
    }
    for (auto& t : _threads) {
        t.join();
    }
}
```
CServer中，使用线程池内的 io_context 而不是创建连接的 io_context 来处理数据

```cpp
void CServer::start_accept() {
    // 线程池里取 io_context 来处理连接
    auto &io_context = AsioIOServicePool::getInstance()->getIOService();
    // 使用智能指针来管理 session 实例，以保证不会二次析构
    std::shared_ptr<CSession> newSession = std::make_shared<CSession>(io_context, this);
    // 绑定到服务上，新连接到来后触发回调函数 handle_accept
    _acceptor.async_accept(newSession->get_socket(),
                           std::bind(&CServer::handle_accept, this, newSession, std::placeholders::_1));

}
```

# IOThreadPool
IOThreadPool 模型：只创建一个 io_context 用来监听读写事件，让 io_context.run() 在多个线程中调用，这样回调函数就会被不同的线程触发
问题：
​	同一个 socket 的回调函数可能在不同的线程里，如果两个线程同时调用回调函数来进行 IO 操作，可能会产生并发问题
![Inkedimage-20240802135142490_LI](https://github.com/user-attachments/assets/294c23cf-b98b-45bc-80bd-edd96ea38a29)
```c++
#ifndef IOTHREADPOOL_ASIOTHREADPOOL_H
#define IOTHREADPOOL_ASIOTHREADPOOL_H

#include "Singleton.h"
#include <boost/asio.hpp>
#include <vector>

class AsioThreadPool : public Singleton<AsioThreadPool> {
    friend class Singleton<AsioThreadPool>;

public:
    ~AsioThreadPool() {};

    AsioThreadPool(const AsioThreadPool &) = delete;

    AsioThreadPool &operator=(const AsioThreadPool &) = delete;

    boost::asio::io_context &getIOService();

    void stop();

private:
    AsioThreadPool(int threadNum = std::thread::hardware_concurrency());

    boost::asio::io_context _service;
    std::unique_ptr<boost::asio::io_context::work> _work;
    // 管理线程
    std::vector<std::thread> _threads;
};

#endif //IOTHREADPOOL_ASIOTHREADPOOL_H
```

```cpp
#include "../include/AsioThreadPool.h"

AsioThreadPool::AsioThreadPool(int threadNum):_work(new boost::asio::io_context::work(_service)) {
    // 绑定 work 对象
    // 创建线程
    for (int i = 0; i < threadNum; i++) {
        _threads.emplace_back([this]() {
            _service.run();
        });
    }
}

boost::asio::io_context &AsioThreadPool::getIOService() {
    return _service;
}

void AsioThreadPool::stop() {
    // 释放 io_context 的 work
    _work.reset();
    for(auto &t:_threads){
        t.join();
    }
}
```
## strand 改进
当socket就绪后并不是由多个线程调用每个socket注册的回调函数，而是将回调函数投递给strand管理的队列，再由strand统一调度派发；为了让回调函数被派发到strand的队列，我们只需要在注册回调函数时加一层strand的包装即可。
![image-20240802145345080](https://github.com/user-attachments/assets/e5ac47ac-0781-4931-b8ad-ea1ccf312539)
CSession 中增加 strand 成员变量：

```c++
boost::asio::strand<boost::asio::io_context::executor_type> _strand;
```

strand 的初始化：

```c++
_strand(io_context.get_executor())
```

绑定：

```c++
_socket.async_read_some(boost::asio::buffer(_data, MAX_LENGTH),
        boost::asio::bind_executor(_strand, std::bind(&CSession::HandleRead, this,
            std::placeholders::_1, std::placeholders::_2, SharedSelf())));
```



## main 函数修改

因为 io_context.run() 是运行在线程池中的，所以线程池相当于是个主线程；CServer 创建后需要线程需要阻塞住，当收到停止信号时，再继续执行

```cpp
#include <iostream>
#include <csignal>
#include "include/CServer.h"
#include "include/AsioThreadPool.h"
std::mutex mutex_quit;
bool bstop = false;
std::condition_variable cond_quit;
int main() {
    try {
        auto pool = AsioThreadPool::getInstance();

        boost::asio::io_context ioc;
        // 这两个信号注册到 ioc 中
        boost::asio::signal_set signals(ioc,SIGINT,SIGTERM);
        // 异步等待信号被触发
        signals.async_wait([&ioc,&pool](auto,auto){
            // 收到了就停止服务器
            ioc.stop();
            pool->stop();
            std::unique_lock<std::mutex> lock(mutex_quit);
            bstop = true;
            cond_quit.notify_one();
        });
        CServer server(pool->getIOService(), 1234);
        {
            std::unique_lock<std::mutex> lock(mutex_quit);
            while(!bstop){
                // 收到停止信号前阻塞
                cond_quit.wait(lock);
            }

        }
    } catch (std::exception &e) {
        std::cerr << "Unknown error occurred: " << e.what() << std::endl;
    }
    return 0;
}
```
代码地址：[proacane/boost-asio-learn (github.com)](https://github.com/proacane/boost-asio-learn)