# 协程
协程，英文Coroutines，是一种基于线程之上，但又比线程更加轻量级的存在，这种由程序员自己写程序来管理的轻量级线程叫做『用户空间线程』，具有对内核来说不可见的特性。
![image-20240804134832525](https://github.com/user-attachments/assets/a40e1f20-cfaf-4fcd-a75c-f68cc30b71db)
**特点**
1. 线程的切换由操作系统负责调度，协程由用户自己进行调度，因此减少了上下文切换，提高了效率。
2. 线程的默认Stack大小是1M，而协程更轻量，接近1K。因此可以在相同的内存中开启更多的协程。
3. 由于在同一个线程上，因此可以避免竞争关系而使用锁。
4. 适用于被阻塞的，且需要大量并发的场景。但不适用于大量计算的多线程，遇到此种情况，更好实用线程去解决。

# demo
流程：

1. 初始化 io_context 和信号处理
2. 启动监听协程，等待客户端连接
3. 主线程运行 io_context.run()
4. 监听协程中，获取当前协程的执行器，用来创建其他协程；同时监听连接；异步等待客户端连接，每个连接都创建一个 socket 与 echo 协程来处理数据
5. echo 协程中进行数据处理，co_await 关键字可以挂起协程的执行，直到异步操作完成

```c++
#include <iostream>
#include <boost/asio/co_spawn.hpp>
#include <boost/asio/detached.hpp>
#include <boost/asio/io_context.hpp>
#include <boost/asio/ip/tcp.hpp>
#include <boost/asio/signal_set.hpp>
#include <boost/asio/write.hpp>
#include <cstdio>

using boost::asio::ip::tcp;
using boost::asio::awaitable;
using boost::asio::co_spawn;
using boost::asio::detached;
using boost::asio::use_awaitable;
namespace this_coro = boost::asio::this_coro;

awaitable<void> echo(tcp::socket socket) {
    try {
        char data[1024];
        for (;;) {
            std::size_t n = co_await socket.async_read_some(boost::asio::buffer(data), use_awaitable);
            co_await async_write(socket, boost::asio::buffer(data, n), use_awaitable);
        }
    }
    catch (const std::exception &e) {
        std::cout << "Error: " << e.what() << std::endl;
    }
}

awaitable<void> listener() {
    // 获取协程的调度器，捕获不到就挂起，运行其他协程
    auto executor = co_await this_coro::executor;
    tcp::acceptor acceptor(executor, {tcp::v4(), 1234});

    // 进入循环，等待客户端连接
    for (;;) {
        tcp::socket socket = co_await acceptor.async_accept(use_awaitable);
        // 连接到了创建新协程来处理连接
        co_spawn(executor, echo(std::move(socket)), detached);
    }
}

int main() {
    try {
        boost::asio::io_context io_context(1);
        // 这两个信号绑定到 io_context 中
        boost::asio::signal_set signals(io_context, SIGINT, SIGTERM);
        signals.async_wait([&](auto, auto) {
            // 捕获到就停止
            io_context.stop();
        });
        // 启动一个协程来运行 listener() ，与 detached 关联，使其可以独立执行
        co_spawn(io_context, listener(), detached);
        // 进入事件循环，处理异步操作
        io_context.run();
    }
    catch (const std::exception &e) {
        std::cout << "Error: " << e.what() << std::endl;
    }
    return 0;
}
```