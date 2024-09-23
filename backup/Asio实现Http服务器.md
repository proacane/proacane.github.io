# Http简介

## Http包头信息

一个标准的HTTP报文头通常由请求头和响应头两部分组成。

### Http请求头

HTTP请求头包括以下字段：

- **Request-line**：包含用于描述请求类型、要访问的资源以及所使用的HTTP版本的信息。
- **Host**：指定被请求资源的主机名或IP地址和端口号。
- **Accept**：指定客户端能够接收的媒体类型列表，用逗号分隔，例如 text/plain, text/html。
- **User-Agent**：客户端使用的浏览器类型和版本号，供服务器统计用户代理信息。
- **Cookie**：如果请求中包含cookie信息，则通过这个字段将cookie信息发送给Web服务器。
- **Connection**：表示是否需要持久连接（keep-alive）。

### Http响应头

HTTP响应头包括以下字段：

- **Status-line**：包含协议版本、状态码和状态消息。
- **Content-Type**：响应体的MIME类型。
- **Content-Length**：响应体的字节数。
- **Set-Cookie**：服务器向客户端发送cookie信息时使用该字段。
- **Server**：服务器类型和版本号。
- **Connection**：表示是否需要保持长连接（keep-alive）。

# beast搭建Http服务器

整体流程:

1. 初始化和启动服务器

   - 在 `main` 函数中，服务器绑定到指定的地址和端口（127.0.0.1:8080）。
   - 创建一个 `boost::asio::io_context` 对象，用于处理异步 I/O 操作。
   - 创建一个 `tcp::acceptor` 对象，用于接受新的连接。
   - 调用 `http_server` 函数开始监听连接，并进入事件循环 (`ioc.run()`)，等待事件触发。

   ```c++
   int main() {
       try {
           auto const address = net::ip::make_address("127.0.0.1");
           auto port_num = static_cast<unsigned short>(8080);
           net::io_context ioc{1};
           // 接收连接
           tcp::acceptor acceptor{ioc, {address, port_num}};
           tcp::socket socket(ioc);
           // 开始监听
           http_server(acceptor,socket);
           ioc.run();
       } catch (std::exception &exception) {
           std::cerr << "Exception: " << exception.what() << std::endl;
           return EXIT_FAILURE;
       }
       return 0;
   }
   ```

2. 处理新连接

   - `http_server` 函数调用 `acceptor.async_accept` 来异步接受新的连接。
   - 当有新的连接时，创建一个 `http_connection` 对象，并调用其 `start` 方法开始处理该连接。
   - 再次调用 `http_server` 来继续监听新的连接。

   ```c++
   void http_server(tcp::acceptor &acceptor, tcp::socket &socket) {
       acceptor.async_accept(socket, [&](boost::system::error_code ec) {
           if (!ec) {
               std::make_shared<http_connection>(std::move(socket))->start();
           }
           // 继续监听其他连接
           http_server(acceptor, socket);
       });
   }
   ```

3. 处理 HTTP 请求

   - `http_connection::start` 方法调用 `read_request` 方法来异步读取请求数据。
   - read_request` 使用 `http::async_read` 来读取请求，并在读取完成后调用 `process_request` 方法处理请求。
   - 使用 `boost::asio::steady_timer` 异步等待进行超时检测

http_connection类：

```c++
class http_connection : public std::enable_shared_from_this<http_connection> {
public:
   explicit http_connection(tcp::socket socket) : _socket(std::move(socket)) {}
    void start() {
        // 读取请求
        read_request();
        // 超时检测
        check_deadline();
    }
private:
    tcp::socket _socket;
    // 临时存储读取数据的缓冲区
    beast::flat_buffer _buffer{8192};
    // 存储从客户端读取的 HTTP 请求消息
    http::request<http::dynamic_body> _request;
    http::response<http::dynamic_body> _response;
    // 定时器，初始化：获取调度器，60 秒为超时时间
    net::steady_timer _deadline{
            _socket.get_executor(), std::chrono::seconds(60)
    };
    
    void read_request() {
        auto self = shared_from_this();
        http::async_read(_socket, _buffer, _request, [self](beast::error_code ec, std::size_t bytes_transferred) {
            boost::ignore_unused(bytes_transferred);
            if (!ec) {
                // 读取完开始处理请求
                self->process_request();
            }
        });
    }
	// 异步等待，超时就关闭
    void check_deadline() {
        auto self = shared_from_this();
        _deadline.async_wait([self](boost::system::error_code ec) {
            if (!ec) {
                // 超时关闭
                self->_socket.close(ec);
            }
        });
    }

    void process_request() {
        // 设置响应头的版本
        _response.version(_request.version());
        // 短连接
        _response.keep_alive(false);
        // 根据请求类型分别处理
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


    void create_response() {
        // 判断请求路由
        if (_request.target() == "/count") {
            _response.set(http::field::content_type, "text/html");
            // 写到响应体中
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

    void write_response() {
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

    void create_post_response() {
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
};
```

代码地址：[proacane/boost-asio-learn (github.com)](https://github.com/proacane/boost-asio-learn)