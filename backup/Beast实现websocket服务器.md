# 流对象

WebSocket连接需要一个有状态对象，由Beast中的一个类模板websocket::stream表示。该接口使用分层流模型。一个websocket stream对象包含另一个流对象，称为“下一层”，它用于执行I/O操作。以下是每个模板参数的描述：

```c++
namespace boost {
namespace beast {
namespace websocket {
template<
    class NextLayer,
    bool deflateSupported = true>
class stream;
} // websocket
} // beast
} // boost
```

`websocket`命名空间下包含一个模板类`stream`，用于表示WebSocket连接。

`stream`类有两个模板参数：`NextLayer`和`deflateSupported`。其中，`NextLayer`表示WebSocket连接使用的下一层流类型，例如TCP套接字或TLS握手后的数据流；而`deflateSupported`则是一个bool值，表示是否支持WebSocket协议内置的压缩功能。

**创建一个基于 tcp/ip socket 和 io_context 的 websocket 流对象**

```c++
stream<tcp_stream> ws(ioc);
```

stream是Beast库中WebSocket流类模板的别名，其下一层流类型为tcp_stream

websocket内置超时检测，使用 websocket 时需要禁用其他层的超时设置； websocket 的超时时间可以用流对象的 `set_option` 来设置

**创建线程安全的流对象**

```c++
stream<tcp_stream> ws(net::make_strand(ioc));
```

**也可以通过右值来进行构造**

```c++
stream<tcp_stream> ws(std::move(sock));
```

通过调用 next_layer 来访问下一层流对象

```c++
ws.next_layer().close();
```

# 使用 ssl

使用`net::ssl::stream`类模板作为流的模板类型，并且将`net::io_context`和`net::ssl::context`参数传递给包装流的构造函数

```c++
stream<ssl_stream<tcp_stream>> wss(net::make_strand(ioc), ctx);
```

`get_lowest_layer` 可以获取最底层流

# 连接

首先连接 WebSocket 流，然后执行 WebSocket 握手。WebSocket 流将建立连接的任务委托给下一层流，下面是客户端的示例代码：

```c++
stream<tcp_stream> ws(ioc);
net::ip::tcp::resolver resolver(ioc);
get_lowest_layer(ws).connect(resolver.resolve("example.com", "ws"));
```

对于服务器接收连接，在WebSocket服务器中使用一个acceptor来接受传入的连接。当建立了一个传入连接时，可以从acceptor返回的socket构造WebSocket流

```c++
net::ip::tcp::acceptor acceptor(ioc);
acceptor.bind(net::ip::tcp::endpoint(net::ip::tcp::v4(), 0));
acceptor.listen();
stream<tcp_stream> ws(acceptor.accept());
```

也可以通过使用acceptor成员函数的另一个重载，将传入连接直接接受到WebSocket流拥有的socket中

```c++
stream<tcp_stream> ws(net::make_strand(ioc));
acceptor.accept(get_lowest_layer(ws).socket());
```

# 握手

websocket通过握手将http升级为websocket协议，一个websocket协议如下

```c++
GET / HTTP/1.1
Host: www.example.com
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2pGeTR0DsE4dfZs2pH+8MA==
Sec-WebSocket-Version: 13
User-Agent: Boost.Beast/216
```

## 客户端升级流程

使用流对象的 `handshake` 函数（或async_handshake），用于使用所需的主机名和目标字符串发送请求。该代码连接到从主机名查找返回的IP地址，然后在客户端角色中执行WebSocket握手；

如果传入了 `response_type` 类型的参数，那么响应结果会存储在这个参数里

```c++
stream<tcp_stream> ws(ioc);
// 接收到的消息类型，可选参数
response_type res;
net::ip::tcp::resolver resolver(ioc);
get_lowest_layer(ws).connect(resolver.resolve("www.example.com", "ws"));
ws.handshake(
    res,
    "www.example.com",  // The Host field
    "/"                 // The request-target
);
```

## 服务器升级流程

对于接受传入连接的服务器，websocket::stream可以读取传入的升级请求并自动回复。如果握手符合要求，流将发送带有101切换协议状态码的升级响应。如果握手不符合要求，或者超出了调用者之前设置的流选项允许的参数范围，流将发送一个带有表示错误的状态码的HTTP响应。根据保持活动设置，连接可能保持打开状态，以进行后续握手尝试。在接收到升级请求握手后，由实现创建和发送的典型HTTP升级响应如下所示：

```c++
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
Server: Boost.Beast
```

流对象的`accept`和`async_accept`用于从已连接到传入对等方的流中读取WebSocket HTTP升级请求握手，然后发送WebSocket HTTP升级响应

**实现支持 websocket 的http 服务器**

服务器检测传入的 http 请求是否为 websocket 请求即可，可以使用 is_upgrade；

一旦调用者确定HTTP请求是WebSocket升级请求，就会提供额外的accept和async_accept重载版本，这些版本接收整个HTTP请求头作为一个对象，以进行握手处理。通过手动读取请求，程序可以处理普通的HTTP请求以及升级请求。程序还可以根据HTTP字段强制执行策略，例如基本身份验证。在这个示例中，首先使用HTTP算法读取请求，然后将其传递给新构建的流：

```c++
flat_buffer buffer;
http::request<http::string_body> req;
http::read(sock, buffer, req);
if(websocket::is_upgrade(req))
{
    stream<tcp_stream> ws(std::move(sock));
    BOOST_ASSERT(buffer.size() == 0);
    ws.accept(req);
}
else
{
    // 执行 http 处理
}
```

# 收发数据

同步读写，同时根据接收消息的类型设置 WebSocket 连接的模式

```c++
flat_buffer buffer;
ws.read(buffer);
ws.text(ws.got_text());
ws.write(buffer.data());
buffer.consume(buffer.size());
```

如果遇到了不能通过 buffer 一次性读完的情况，例如：

1. 向终端流式传输多媒体：在流式传输多媒体到终端时，通常不适合或不可能预先缓冲整个消息。例如，在实时视频流或音频流的传输过程中，数据可能以非常大的速率产生，并且需要立即传输给接收端进行实时播放。由于数据量巨大且需即时传输，预先缓冲整个消息可能会导致延迟或资源耗尽。
2. 发送超出内存容量的消息：有时候需要发送的消息太大，无法一次性完全存储在内存中。这可能发生在需要传输大型文件或大量数据的情况下。如果尝试将整个消息加载到内存中，可能会导致内存溢出或系统性能下降。在这种情况下，需要通过分块或逐步读取的方式来发送消息，以便逐步加载和传输数据。
3. 提供增量结果：在某些情况下，需要在处理过程中提供增量的结果，而不是等待整个处理完成后再返回结果。这可以在长时间运行的计算、搜索或处理任务中发生。通过逐步提供部分结果，可以让用户或应用程序更早地获得一些数据，并可以在处理过程中进行进一步的操作或显示。这种方式可以改善用户体验，并减少等待时间。

则需要采用逐步处理、流式传输或增量输出的方式

```c++
//存储读取的 WebSocket 数据
multi_buffer buffer;
// 连续调用 ws.read_some 方法从 WebSocket 连接中读取数据，并将其存储到 buffer 中，直到 WebSocket 消息全部接收完成
do
{
    ws.read_some(buffer, 512);
}
while(! ws.is_message_done());
//将 WebSocket 连接设置为二进制模式，以便在后续的写入操作中正确处理二进制数据
ws.binary(ws.got_binary());
//创建了一个 cb 对象，它是 buffer 的后缀子序列。它提供了对 buffer 中已接收数据的访问
buffers_suffix<multi_buffer::const_buffers_type> cb{buffer.data()};
for(;;)
{
    if(buffer_bytes(cb) > 512)
    {
        //发送 cb 的前缀（前 512 字节）到 WebSocket 连接中，并保留剩余的数据
        ws.write_some(false, buffers_prefix(512, cb));
        //消耗了前 512 字节的数据
        cb.consume(512);
    }
    else
    {	//发送 cb 中的所有数据到 WebSocket 连接中
        ws.write_some(true, cb);
        break;
    }
}
//清空 buffer 中存储的数据
buffer.consume(buffer.size());
```

# 关闭连接

close 或 async_close 

```c++
get_lowest_layer(wss).close();
```

# TCP升级websocket的demo

WebSocketServer 类创建并接收连接；

```c++
#include "ConnectionMgr.h"

class WebSocketServer {
public:
    WebSocketServer(const WebSocketServer &) = delete;

    WebSocketServer &operator=(const WebSocketServer &) = delete;

    WebSocketServer(net::io_context &ioc, unsigned short port);

    // tcp 层接收连接
    void startAccept();

private:
    net::ip::tcp::acceptor _acceptor;
    net::io_context &_ioc;
};
```

```c++
#include "../include/WebSocketServer.h"

WebSocketServer::WebSocketServer(net::io_context &ioc, unsigned short port) : _ioc(ioc), _acceptor(ioc,
                                                                                                   net::ip::tcp::endpoint(
                                                                                                           net::ip::tcp::v4(),
                                                                                                           port)) {
    std::cout << "Server start on port: " << port << std::endl;
}

void WebSocketServer::startAccept() {
    auto connection_ptr = std::make_shared<Connection>(_ioc);
    _acceptor.async_accept(connection_ptr->getSocket(), [this, connection_ptr](error_code ec) {
        try {
            if(!ec){
                // 升级成 websocket
                connection_ptr->asyncAccept();
            }else{
                std::cerr << "async_accept failed: " << ec.what() << std::endl;
            }

            this->startAccept();
        } catch (std::exception &e) {
            std::cerr << "async_accept error: " << e.what() << std::endl;
        }
    });
}
```

Connection 为 websocket 连接类，其成员函数流对象使用智能指针管理，职能类似 socket；只不过是对 socket 又进行了封装；创建的 Connection 对象交给管理类统一管理；首次接收时进行异步读，读完后再写回客户端

```c++
#include <boost/beast.hpp>
#include <boost/beast/ssl.hpp>
#include <boost/asio.hpp>
#include <boost/asio/ssl.hpp>
#include <memory>
#include <boost/uuid/uuid.hpp>
#include <boost/uuid/uuid_io.hpp>
#include <boost/uuid/uuid_generators.hpp>
#include <queue>
#include <mutex>
#include <iostream>

namespace net = boost::asio;
namespace beast = boost::beast;
using namespace boost::beast;
using namespace boost::beast::websocket;

class Connection : public std::enable_shared_from_this<Connection> {
public:
    explicit Connection(net::io_context &ioc);

    std::string getUuid() const {
        return _uuid;
    }

    // 返回 tcp 层的 socket
    net::ip::tcp::socket &getSocket();

    // websocket 层升级
    void asyncAccept();

    // 接收数据，进行处理
    void start();

    void asyncSend(std::string msg);

private:
    // websocket
    std::unique_ptr<stream<tcp_stream >> _ws_ptr;
    // 唯一 id
    std::string _uuid;
    net::io_context &_ioc;
    // 存储
    flat_buffer _rec_buffer;
    // 发送队列
    std::queue<std::string> _send_que;
    std::mutex _send_mutex;
};
```

```cpp
#include "../include/connection.h"
#include"../include/ConnectionMgr.h"

Connection::Connection(net::io_context &ioc) : _ioc(ioc),
                                               _ws_ptr(std::make_unique<stream<tcp_stream>>(net::make_strand(ioc))) {
    // 生成uuid
    boost::uuids::random_generator generator;
    boost::uuids::uuid uuid = generator();
    _uuid = boost::uuids::to_string(uuid);
}

net::ip::tcp::socket &Connection::getSocket() {
    // 获取最底层 tcp 的 socket
    return boost::beast::get_lowest_layer(*_ws_ptr).socket();
}

void Connection::asyncAccept() {
    auto self = shared_from_this();
    // tcp 升级成 websocket，捕获 self 增加引用计数，防止回调之前 Connection 被析构
    _ws_ptr->async_accept([self](error_code ec) {
        try {
            if (!ec) {
                // 添加连接
                ConnectionMgr::getInstance().addConnection(self);
                // 进行数据处理
                self->start();
            } else {
                std::cerr << "websocket accept failed, error is " << ec.what() << std::endl;
            }
        } catch (std::exception &e) {
            std::cerr << "websocket asyncAccept error is " << e.what() << std::endl;
        }
    });
}

void Connection::start() {
    auto self = shared_from_this();
    _ws_ptr->async_read(_rec_buffer, [self](error_code ec,size_t size) {
        try {
            if (!ec) {
                // 设置类型
                self->_ws_ptr->text(self->_ws_ptr->got_text());
                std::string rec_data = buffers_to_string(self->_rec_buffer.data());
                // 清空 buffer
                self->_rec_buffer.consume(self->_rec_buffer.size());
                std::cout << "websocket receive length is '" << size << "'\n";
                std::cout << "websocket receive message is '" << rec_data << "'\n";
                // 发送回去，减少拷贝使用 move
                self->asyncSend(std::move(rec_data));
                // 继续监听读
                self->start();
            } else {
                std::cerr << "websocket read failed, error is " << ec.what() << std::endl;
                // 移除连接
                ConnectionMgr::getInstance().removeConnection(self->getUuid());
                return;
            }
        } catch (std::exception &e) {
            std::cerr << "websocket asyncRead error is " << e.what() << std::endl;
            // 移除连接
            ConnectionMgr::getInstance().removeConnection(self->getUuid());
        }
    });
}

void Connection::asyncSend(std::string msg) {
    // 运行完代码块释放锁
    {
        std::lock_guard<std::mutex> lock(_send_mutex);
        short send_size = _send_que.size();
        // 最大大小 1000
        if (send_size == 1000) {
            std::cout << "Send queue is full\n";
            return;
        }
        _send_que.push(msg);
        if (send_size > 0) {
            // 之前数据没法送完
            return;
        }
    }
    // 开始发送
    auto self = shared_from_this();
    _ws_ptr->async_write(boost::asio::buffer(msg.c_str(), msg.length()),
                         [self](error_code ec, size_t buffer_transferred) {
                             try {
                                 if (!ec) {
                                     std::string send_msg;
                                     // 查看队列中还有没有数据
                                     {
                                         std::lock_guard<std::mutex> lock(self->_send_mutex);
                                         self->_send_que.pop();
                                         if (self->_send_que.empty()) {
                                             return;
                                         }
                                         send_msg = self->_send_que.front();
                                     }
                                     self->asyncSend(std::move(send_msg));
                                 } else {
                                     std::cerr << "websocket asyncWrite failed, error is " << ec.what() << std::endl;
                                     // 移除连接
                                     ConnectionMgr::getInstance().removeConnection(self->getUuid());
                                 }
                             } catch (std::exception &e) {
                                 std::cerr << "websocket asyncWrite error is " << e.what() << std::endl;
                                 // 移除连接
                                 ConnectionMgr::getInstance().removeConnection(self->getUuid());
                             }
                         });

}
```

# 同时支持http和websocket的demo

创建acceptor接收连接后，在处理类中读取请求，并通过websocket::is_upgrade函数来判断是不是websocket请求，如果是 websocket 请求，就创建 websocket 流对象并绑定socket，并进行监听和处理数据（长连接）

```c++
#include <boost/beast/core.hpp>
#include <boost/beast/http.hpp>
#include <boost/beast/version.hpp>
#include <boost/beast.hpp>
#include <chrono>
#include <ctime>
#include <cstdlib>
#include <memory>
#include <string>
#include <json/json.h>
#include <json/value.h>
#include <json/reader.h>
#include <iostream>
#include <boost/beast/websocket.hpp>
#include <queue>
#include <mutex>
#include <boost/asio/strand.hpp>
#include "ProgramState.h"

namespace beast = boost::beast;
namespace http = boost::beast::http;
namespace net = boost::asio;
using tcp = boost::asio::ip::tcp;
using namespace boost::beast;
using namespace boost::beast::websocket;


class http_connection : public std::enable_shared_from_this<http_connection> {
public:
    explicit http_connection(tcp::socket socket, boost::asio::io_context &ioc);

    void start();

private:
    tcp::socket _socket;
    // 临时存储读取数据的缓冲区
    beast::flat_buffer _buffer{8192};
    // 存储从客户端读取的 HTTP 请求消息
    http::request<http::dynamic_body> _request;
    http::response<http::dynamic_body> _response;

    // 定时器，获取调度器，60 秒为超时时间
    net::steady_timer _deadline{
            _socket.get_executor(), std::chrono::seconds(60)
    };

    std::unique_ptr<stream<boost::beast::tcp_stream>> _ws_ptr;

    void read_request();

    void check_deadline();

    void stop_deadline();

    void process_request();

    void create_response();

    void write_response();

    void create_post_response();

    void upgrade_websocket(const tcp::socket &&socket, std::size_t bytes_transferred);

    void websocket_async_read();

    void websocket_async_write(std::string msg);

    void async_send_to_queue(std::string msg);

    flat_buffer _websocket_buffer;
    std::queue<std::string> _send_que;
    std::mutex _mutex;
    boost::asio::strand<boost::asio::io_context::executor_type> _strand;
    boost::asio::io_context &_ioc;

    void convert_websocket(tcp::socket &&);
};
```

```c++
void http_connection::start() {
    // 读取请求
    read_request();
    // 超时检测
    check_deadline();
}

void http_connection::read_request() {
    auto self = shared_from_this();
    http::async_read(_socket, _buffer, _request, [self](beast::error_code ec, std::size_t bytes_transferred) {

        if (ec) {
            std::cerr << "async_read error ! msg is : " << ec.message() << std::endl;
            return;
        }
        if (websocket::is_upgrade(self->_request)) {
            // 升级成 websocket
            self->upgrade_websocket(std::move(self->_socket), bytes_transferred);
        } else {
            // http 处理
            self->process_request();
        }
    });
}

void http_connection::check_deadline() {
    auto self = shared_from_this();
    _deadline.async_wait([self](boost::system::error_code ec) {
        if (!ec) {
            // 超时关闭
            self->_socket.close(ec);
        }
    });
}

void http_connection::process_request() {
    // 设置响应头的版本
    _response.version(_request.version());
    // 短连接
    _response.keep_alive(false);
    switch (_request.method()) {
        case http::verb::get:
            _response.result(http::status::ok);
            _response.set(http::field::server, "Beast");
            // 创建响应头
            create_response();
            break;
        case http::verb::post:
            _response.result(http::status::ok);
            _response.set(http::field::server, "Beast");
            create_post_response();
            break;
        default:
            // 错误请求
            _response.result(http::status::bad_request);
            _response.set(http::field::content_type, "text/plain");
            beast::ostream(_response.body()) << "Invalid request-method '" << std::string(_request.method_string())
                                             << "'";
            break;
    }

    // 发送回去
    write_response();
}

void http_connection::create_response() {
    // 判断请求路由
    if (_request.target() == "/count") {
        _response.set(http::field::content_type, "text/html");
        beast::ostream(_response.body()) << "<html>\n"
                                         << "<head><title>Request count</title></head>\n"
                                         << "<body>\n"
                                         << "<h1>Request count</h1>\n"
                                         << "<p>There have been "
                                         << my_program_state::request_count()
                                         << " requests so far.</p>\n"
                                         << "</body>\n"
                                         << "</html>\n";

    } else if (_request.target() == "/time") {
        _response.set(http::field::content_type, "text/html");
        beast::ostream(_response.body()) << "<html>\n"
                                         << "<head><title>Current time</title></head>\n"
                                         << "<body>\n"
                                         << "<h1>Current time</h1>\n"
                                         << "<p>The current time is "
                                         << my_program_state::now()
                                         << " seconds since the epoch.</p>\n"
                                         << "</body>\n"
                                         << "</html>\n";
    } else {
        _response.result(http::status::not_found);
        _response.set(http::field::content_type, "text/plain");
        beast::ostream(_response.body()) << "Page not found\r\n";
    }
}

void http_connection::write_response() {
    auto self = shared_from_this();
    // 设置响应体长度
    _response.content_length(_response.body().size());
    // 发送
    http::async_write(_socket, _response, [self](beast::error_code ec, size_t bytes_transferred) {
        // 关闭发送端
        self->_socket.shutdown(tcp::socket::shutdown_send, ec);
        // 关闭定时器
        self->_deadline.cancel();
    });
}

void http_connection::create_post_response() {
    if (_request.target() == "/email") {
        auto &body = this->_request.body();
        auto body_str = beast::buffers_to_string(body.data());
        std::cout << "Receive body is " << body_str << std::endl;
        _response.set(http::field::content_type, "text/json");
        Json::Value root;
        Json::Reader reader;
        Json::Value src_root;
        bool parse_res = reader.parse(body_str, src_root);
        if (!parse_res) {
            std::cout << "Failed to parse json data\n";
            root["error"] = 1001;
            std::string jsonstr = root.toStyledString();
            beast::ostream(_response.body()) << jsonstr;
            return;
        }

        auto email = src_root["email"].asString();
        std::cout << "email is " << email << std::endl;
        root["error"] = 0;
        root["email"] = email;
        root["message"] = "received email post success";
        std::string jsonstr = root.toStyledString();
        beast::ostream(_response.body()) << jsonstr;
    } else {
        _response.result(http::status::not_found);
        _response.set(http::field::content_type, "text/plain");
        beast::ostream(_response.body()) << "Page not found\r\n";
    }
}

http_connection::http_connection(tcp::socket socket, boost::asio::io_context &ioc) : _socket(std::move(socket)),
                                                                                     _ws_ptr(nullptr), _ioc(ioc),
                                                                                     _strand(ioc.get_executor()) {
}

void http_connection::stop_deadline() {
    _deadline.cancel();
}

void http_connection::upgrade_websocket(const tcp::socket &&socket, std::size_t bytes_transferred) {
    auto self = shared_from_this();
    // 使用 websocket 必须关闭其他的定时器
    stop_deadline();
    convert_websocket(std::move(_socket));
    BOOST_ASSERT(_websocket_buffer.size() == 0);

    self->_ws_ptr->async_accept(self->_request,[self](boost::system::error_code ec){
        if(!ec){
            std::cout<<"Websocket async_accept succeed\n";
            self->websocket_async_read();
        }else{
            std::cerr<<"Websocket has error: "<<ec.what()<<std::endl;
            get_lowest_layer(*(self->_ws_ptr)).close();
        }
    });

}

void http_connection::convert_websocket(tcp::socket &&socket) {
    _ws_ptr.reset(new stream<tcp_stream>(std::move(socket)));
//    _wsptr(std::make_unique<stream<tcp_stream>>(net::make_strand(_ioc)));
}

void http_connection::websocket_async_read() {
    auto self = shared_from_this();

    self->_ws_ptr->async_read(self->_websocket_buffer,[self](boost::system::error_code ec, std::size_t bytes_transferred){
        try {
            if(!ec){
                std::cout << "websocket async_read succeed\n";
                self->_ws_ptr->text(self->_ws_ptr->got_text());
                std::string rec_data = buffers_to_string(self->_websocket_buffer.data());
                self->_websocket_buffer.consume(self->_websocket_buffer.size()) ;
                std::cout << "websocket receive data is " << rec_data << std::endl;
                self->async_send_to_queue(rec_data);
                self->websocket_async_read();
            }else{
                std::cerr<<"async_read failed, error is: "<<ec.what()<<std::endl;
                get_lowest_layer(*(self->_ws_ptr)).close();
            }
        } catch (std::exception&e) {
            std::cerr<<"Exception in async_read, error is: "<<e.what()<<std::endl;
            get_lowest_layer(*(self->_ws_ptr)).close();
        }
    });
}

void http_connection::websocket_async_write(std::string msg) {
    auto self = shared_from_this();
    self->_ws_ptr->async_write(boost::asio::buffer(msg.c_str(),msg.size()), boost::asio::bind_executor(_strand,[self](boost::system::error_code ec,size_t bytes_transferred){
            try {
                if(!ec){
                    std::cout<<"async_write succeed\n";
                    std::string send_msg;
                    {
                        std::lock_guard<std::mutex> lock(self->_mutex);
                        self->_send_que.pop();
                        if(self->_send_que.empty()){
                            std::cout<<"send queue is empty\n";
                            return;
                        }
                        send_msg = self->_send_que.front();
                    }
                    self->async_send_to_queue(send_msg);
                }else{
                    std::cerr<<"async_write failed, error is: "<<ec.what()<<std::endl;
                    get_lowest_layer(*(self->_ws_ptr)).close();
                }
            } catch (std::exception &ec) {
                std::cerr<<"Exception in async_write, error is: "<<ec.what()<<std::endl;
                get_lowest_layer(*(self->_ws_ptr)).close();
            }
    }));
}

void http_connection::async_send_to_queue(std::string msg) {
    std::size_t que_size = 0;
    {
        std::lock_guard<std::mutex> lock(_mutex);
        que_size = _send_que.size();
        if (que_size == 1000) {
            return;
        }
        _send_que.push(msg);
        std::cout<<"Put msg into send queue succeed\n";
    }
    if(que_size >0){
        return;
    }
    websocket_async_write(msg);
}

```

代码地址:[proacane/boost-asio-learn (github.com)](https://github.com/proacane/boost-asio-learn)

