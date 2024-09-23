# 单线程通信流程
![image-20240726093453475](https://github.com/user-attachments/assets/a53c98fb-c149-403e-b102-d110905e5eb4)
# 同步读写的示例
- 服务端
```cpp
#include <iostream>
#include <boost/asio.hpp>
#include <memory>
#include <set>
#include <thread>

using namespace std;
using namespace boost::asio::ip;

const int MAX_LEN = 1024;
typedef shared_ptr<tcp::socket> socket_ptr;
std::set<std::shared_ptr<std::thread>> thread_set;

void session(socket_ptr sock) {
    try {
        for (;;) {
            char data[MAX_LEN];
            boost::system::error_code error;
            // 读取客户端发送的内容
            size_t length = sock->read_some(boost::asio::buffer(data), error);

            if (error == boost::asio::error::eof) {
                std::cout << "Connection closed by peer\n";
                break;
            } else if (error) {
                throw boost::system::system_error(error);
            }

            std::string received_message(data, length);
            std::cout << "Received from " << sock->remote_endpoint().address().to_string() << ": " << received_message << std::endl;

            // 转换为 大写
            // Process the received message (e.g., convert to upper case)
            std::transform(received_message.begin(), received_message.end(), received_message.begin(), ::toupper);
            // 再写回客户端
            boost::asio::write(*sock, boost::asio::buffer(received_message));
        }
    } catch (exception &e) {
        std::cerr << "Exception in thread: " << e.what() << std::endl;
    }
}

// 建立连接
void server(boost::asio::io_context &ios, unsigned short port_num) {
    boost::asio::ip::tcp::acceptor acceptor(ios, boost::asio::ip::tcp::endpoint(boost::asio::ip::tcp::v4(), port_num));
    std::cout << "Server started on port " << port_num << std::endl;
    // 没连接就一直阻塞
    for (;;) {
        socket_ptr socket(new tcp::socket(ios));
        acceptor.accept(*socket);
        std::cout << "Accepted connection from " << socket->remote_endpoint().address().to_string() << std::endl;
        // 为连接创建一个线程，开始通信
        auto t = make_shared<std::thread>(session, socket);
        thread_set.insert(t);
    }
}

int main() {
    try {
        boost::asio::io_context ios;
        server(ios, 1234);
        // 防止有数据未传输完时，主线程就结束了
        for (auto &t: thread_set) {
            t->join();
        }
    } catch (exception &e) {
        std::cerr << "Exception in main thread: " << e.what() << std::endl;
    }

    return 0;
}
```
# 异步读写示例
![image-20240726095026183](https://github.com/user-attachments/assets/bd621b55-b962-4eaf-9488-5bb8f7c47f79)
## 注意的问题

- 异常处理时，为防止 Session 二次析构，需要继承 std::enable_shared_from_this<CSession>；使用智能指针与 map 延长 Session 的生命周期

- 发送消息时，为保证消息的有序性，需要增加发送队列

- 处理粘包问题，消息结构采用 TLV 格式，使用 async_read_some 比较麻烦，使用 async_read 比较简洁

  ​							![image-20240726093831093](https://github.com/user-attachments/assets/45a3e63d-53d6-4124-b647-d9742bc9897e)

  - async_read_some：回调函数里不断判断已经处理的字节

  - async_read：两层回调，第一层处理包头，第二层处理包体

- 字节序的处理：发送前转换为网络字节序，接收后转换为本地字节序 (boost::asio::detail::socket_ops::network_to_host_short)
- 发送数据使用jsoncpp进行封装

CServer.h

```cpp
#ifndef ASYNCSERVER_CSERVER_H
#define ASYNCSERVER_CSERVER_H
#include "CSession.h"
#include <iostream>
class CSession;
/**
 * 接受新的客户端连接，每个连接都会创建一个新的 Session 实例
 */
class CServer {
public:
    CServer(boost::asio::io_context &ioc, unsigned short port_num);
    // 移除已处理完成的 Session 实例
    void clear_session(std::string uuid);
private:
    // 创建一个新的 Session 并等待新的客户端连接
    void start_accept();
    // 处理新的客户端连接，将其加入 _sessions 中，并启动数据处理
    void handle_accept(std::shared_ptr<CSession> newSession, const boost::system::error_code errorCode);

    boost::asio::io_context &_ioc;
    boost::asio::ip::tcp::acceptor _acceptor;
    // 管理连接
    std::map<std::string, std::shared_ptr<CSession>> _sessions;

};
#endif //ASYNCSERVER_CSERVER_H
```

CServer.cpp

```cpp
#include "CServer.h"

CServer::CServer(boost::asio::io_context &ioc, unsigned short port_num) : _ioc(ioc),
                                                                          _acceptor(_ioc,
                                                                                    tcp::endpoint(tcp::v4(),
                                                                                                  port_num)) {
    std::cout << "Acceptor create succeed! port number is " << port_num << std::endl;
    start_accept();
}

void CServer::start_accept() {
    // 使用智能指针来管理 session 实例，以保证不会二次析构
    std::shared_ptr<CSession> newSession = std::make_shared<CSession>(_ioc, this);
    // 绑定到服务上，新连接到来后触发回调函数 handle_accept
    _acceptor.async_accept(newSession->get_socket(),
                           std::bind(&CServer::handle_accept, this, newSession, std::placeholders::_1));

}

void CServer::handle_accept(std::shared_ptr<CSession> newSession, const boost::system::error_code errorCode) {
    if (errorCode.value() != 0) {
        std::cerr << "Error occurred int accept connection: " << errorCode.message() << std::endl;
//        delete newSession;
    } else {
        std::cout << "Accept connection successfully " << std::endl;
        std::cout << "Start dealing with data" << std::endl;
        // 没问题就开始处理数据
        newSession->start();
        _sessions.insert(std::make_pair(newSession->get_uuid(), newSession));
    }
    // 继续接收新连接
    start_accept();
}

void CServer::clear_session(std::string uuid) {
    _sessions.erase(uuid);
}
```

CSession.h

```cpp
#ifndef ASYNCSERVER_CSESSION_H
#define ASYNCSERVER_CSESSION_H

#include <boost/asio.hpp>
#include <string>
#include <map>
#include <boost/uuid/uuid_generators.hpp>
#include <boost/uuid/uuid_io.hpp>
#include "CServer.h"
#include <queue>
#include "global.h"
using namespace boost::asio::ip;

class MsgNode;

class CServer;

/**
 * 处理客户端消息收发
 */
class CSession : public std::enable_shared_from_this<CSession> {
public:
    tcp::socket &get_socket() { return _socket; };

    explicit CSession(boost::asio::io_context &ioc, CServer *server);;

    // 开始处理
    void start();

    // 获取 uuid
    std::string &get_uuid() {
        return _uuid;
    }

    /**
     * 发送接口
     * @param msg
     * @param max_length
     */
    void send(char *msg, int max_length);

    void send(std::string msg);
private:
    // 写的回调函数
    void handle_write(const boost::system::error_code &errorCode, std::shared_ptr<CSession> self_shared);

    // 读的回调函数
    void handle_read(const boost::system::error_code &errorCode, size_t bytes_transferred,
                     std::shared_ptr<CSession> self_shared);

    /**
     * 读取包头的回调函数
     * @param errorCode
     * @param bytes_transferred
     * @param self_shared
     */
    void handle_read_head(const boost::system::error_code &errorCode, size_t bytes_transferred,
                          std::shared_ptr<CSession> self_shared);

    /**
     * 读取包体的回调函数
     * @param errorCode
     * @param bytes_transferred
     * @param self_shared
     */
    void handle_read_msg(const boost::system::error_code &errorCode, size_t bytes_transferred,
                          std::shared_ptr<CSession> self_shared);
    // 打印收到的二进制数据
    void printRecvData(char* data, int length);
    // 传输信息的 socket
    tcp::socket _socket;
    // 保存数据

    char _data[MAX_LENGTH];
    // 隶属于哪个 server
    CServer *_server;
    // uuid
    std::string _uuid;
    // 发送队列
    std::queue<std::shared_ptr<MsgNode>> _send_que;
    // 互斥锁，防止多个线程同时操作队列
    std::mutex _send_lock;

    //收到的消息结构
    std::shared_ptr<MsgNode> _recv_msg_node;
    // 标记头部结构是否接收完毕
    bool _b_head_parse;
    //收到的头部结构
    std::shared_ptr<MsgNode> _recv_head_node;
};

/**
 * 消息结点
 */
class MsgNode {
    friend class CSession;

public:
    MsgNode(const char *msg, short max_len) : _total_len(max_len + HEAD_LENGTH), _cur_len(0) {
        _data = new char[_total_len + 1];
        // 包头字节序转换
        int max_len_host = boost::asio::detail::socket_ops::host_to_network_short(max_len);
        // 记录长度
        memcpy(_data, &max_len_host, HEAD_LENGTH);
        // 拷贝数据
        memcpy(_data + HEAD_LENGTH, msg, max_len);
        _data[_total_len] = '\0';
    }

    MsgNode(short max_len) : _total_len(max_len), _cur_len(0) {
        _data = new char[_total_len + 1];
    }

    void clear() {
        memset(_data, 0, _total_len);
        _cur_len = 0;
    }

    ~MsgNode() {
        delete[] _data;
    }

private:
    int _cur_len;
    int _total_len;
    char *_data;
};

#endif //ASYNCSERVER_CSESSION_H
```

CSession.cpp

```cpp
#include <iostream>
#include <iomanip>
#include <thread>
#include "CSession.h"
#include "proto/msg.pb.h"
#include <json/json.h>
#include <json/value.h>
#include <json/reader.h>

CSession::CSession(boost::asio::io_context &ioc, CServer *server) : _socket(ioc), _server(server),
                                                                    _b_head_parse(false) {
    std::memset(_data, '\0', MAX_LENGTH);
    // 生成 uuid
    boost::uuids::uuid a_uuid = boost::uuids::random_generator()();
    _uuid = boost::uuids::to_string(a_uuid);
    _recv_head_node = std::make_shared<MsgNode>(HEAD_LENGTH);
}

void CSession::start() {
    std::cout << "First read from client\n";
    // 先在客户端读取
//    _socket.async_read_some(boost::asio::buffer(_data, MAX_LENGTH),
//                            std::bind(&CSession::handle_read, this, std::placeholders::_1, std::placeholders::_2,
//                                      shared_from_this()));
    boost::asio::async_read(_socket, boost::asio::buffer(_recv_head_node->_data, HEAD_LENGTH),
                            std::bind(&CSession::handle_read_head, this, std::placeholders::_1, std::placeholders::_2,
                                      shared_from_this()));
}

void CSession::handle_read(const boost::system::error_code &errorCode, size_t bytes_transferred,
                           std::shared_ptr<CSession> self_shared) {
    try {
        if (errorCode.value() != 0) {
            std::cerr << "Error occurred int read: " << errorCode.message() << std::endl;
            // 移除连接
            _server->clear_session(_uuid);
            return;
        }
        // 测试粘包
        printRecvData(_data, bytes_transferred);
        std::chrono::milliseconds dura(2000);
        std::this_thread::sleep_for(dura);

        while (bytes_transferred > 0) {
            // 记录已经处理的字节
            int copy_len = 0;
            if (!_b_head_parse) {
                // 头部结构还没处理完
                // 收到的数据比头部结构小
                if (bytes_transferred < HEAD_LENGTH) {
                    // 先接收这部分数据
                    memcpy(_recv_head_node->_data + _recv_head_node->_cur_len, _data + copy_len, bytes_transferred);
                    _recv_head_node->_cur_len += bytes_transferred;
                    // 清空 _data 缓冲区
                    memset(_data, 0, MAX_LENGTH);
                    // 继续监听
                    _socket.async_read_some(boost::asio::buffer(_data, MAX_LENGTH),
                                            std::bind(&CSession::handle_read, this, std::placeholders::_1,
                                                      std::placeholders::_2, self_shared));
                    return;
                }
                // 收到的数据比头部结构大
                int head_remain = HEAD_LENGTH - _recv_head_node->_cur_len;
                // 剩余的数据拷贝进去
                memcpy(_recv_head_node->_data + _recv_head_node->_cur_len, _data + copy_len, head_remain);
                _recv_head_node->_cur_len += head_remain;
                copy_len += head_remain;
                bytes_transferred -= head_remain;
                // 获取头部存储的数据
                short data_len = 0;
                memcpy(&data_len, _recv_head_node->_data, HEAD_LENGTH);
                // 网络字节序转换为本地字节序
                data_len = boost::asio::detail::socket_ops::network_to_host_short(data_len);

                std::cout << "Receive data length is " << data_len << std::endl;
                if (data_len > MAX_LENGTH) {
                    std::cerr << "invalid data length is " << data_len << std::endl;
                    return;
                }

                _recv_msg_node = std::make_shared<MsgNode>(data_len);
                // 剩余消息的长度小于数据长度，先将部分数据放到节点里
                if (bytes_transferred < data_len) {
                    memcpy(_recv_msg_node->_data + _recv_msg_node->_cur_len, _data + copy_len, bytes_transferred);
                    _recv_msg_node->_cur_len += bytes_transferred;
                    // 重置 _data
                    memset(_data, 0, MAX_LENGTH);
                    // 继续监听
                    _socket.async_read_some(boost::asio::buffer(_data, MAX_LENGTH),
                                            std::bind(&CSession::handle_read, this, std::placeholders::_1,
                                                      std::placeholders::_2, self_shared));
                    // 头部处理完毕
                    _b_head_parse = true;
                    return;
                }
                // 大于直接拷贝进去
                memcpy(_recv_msg_node->_data + _recv_msg_node->_cur_len, _data + copy_len, data_len);
                _recv_msg_node->_cur_len += data_len;
                copy_len += data_len;
                bytes_transferred -= data_len;
                _recv_msg_node->_data[_recv_msg_node->_total_len] = '\0';
                // 调用 send 测试
//                MsgData msgData;
//                std::string receive_data;
//                msgData.ParseFromString(std::string(_recv_msg_node->_data,_recv_msg_node->_total_len));
//                std::cout << "Received msg id  is " << msgData.id() << " msg data is " << msgData.data() <<std:: endl;
//                std::string return_str = "Server has received msg, msg data is " + msgData.data();
//                MsgData msgReturn;
//                msgReturn.set_id(msgData.id());
//                msgReturn.set_data(return_str);
//                msgReturn.SerializeToString(&return_str);
//                send(return_str);
//                Json::Reader reader;
//                Json::Value root;
//                reader.parse(std::string(_recv_msg_node->_data, _recv_msg_node->_total_len), root);
//                std::cout << "Received msg id  is " << root["id"].asInt() << " msg data is '"
//                          << root["data"].asString() << "'" << std::endl;
//                root["data"] = "Server has received msg, msg data is '" + root["data"].asString() + "'";
//                send(root.toStyledString());
                send(_recv_msg_node->_data,_recv_msg_node->_total_len);
                // 继续处理剩下的字节
                _b_head_parse = false;
                _recv_head_node->clear();
                // 如果小于等于0，其实只能等于
                if (bytes_transferred <= 0) {
                    // 需要继续监听
                    memset(_data, 0, MAX_LENGTH);
                    _socket.async_read_some(boost::asio::buffer(_data, MAX_LENGTH),
                                            std::bind(&CSession::handle_read, this, std::placeholders::_1,
                                                      std::placeholders::_2, self_shared));
                    return;
                }
                // 继续循环即可
                continue;
            }
            // 头结点处理完了，接着处理数据
            int remain_msg = _recv_msg_node->_total_len - _recv_msg_node->_cur_len;
            // 本次接收的还是不足
            if (bytes_transferred < remain_msg) {
                // 拷贝部分，继续监听
                memcpy(_recv_msg_node->_data + _recv_msg_node->_cur_len, _data + copy_len, bytes_transferred);
                _recv_msg_node->_cur_len += bytes_transferred;
                memset(_data, 0, MAX_LENGTH);
                _socket.async_read_some(boost::asio::buffer(_data, MAX_LENGTH),
                                        std::bind(&CSession::handle_read, this, std::placeholders::_1,
                                                  std::placeholders::_2, self_shared));
                return;
            }
            // 足够就直接拷进来
            memcpy(_recv_msg_node->_data + _recv_msg_node->_cur_len, _data + copy_len, remain_msg);
            _recv_msg_node->_cur_len += remain_msg;
            copy_len += remain_msg;
            bytes_transferred -= remain_msg;
            _recv_msg_node->_data[_recv_msg_node->_total_len] = '\0';
            // 调用 send 测试
//            MsgData msgData;
//            std::string receive_data;
//            msgData.ParseFromString(std::string(_recv_msg_node->_data,_recv_msg_node->_total_len));
//            std::cout << "Received msg id  is " << msgData.id() << " msg data is " << msgData.data() <<std:: endl;
//            std::string return_str = "Server has received msg, msg data is " + msgData.data();
//            MsgData msgReturn;
//            msgReturn.set_id(msgData.id());
//            msgReturn.set_data(return_str);
//            msgReturn.SerializeToString(&return_str);
//            send(return_str);

//            Json::Reader reader;
//            Json::Value root;
//            reader.parse(std::string(_recv_msg_node->_data, _recv_msg_node->_total_len), root);
//            std::cout << "Received msg id  is " << root["id"].asInt() << " msg data is '"
//                      << root["data"].asString() << "'" << std::endl;
//            root["data"] = "Server has received msg, msg data is '" + root["data"].asString() + "'";
//            send(root.toStyledString());
            send(_recv_msg_node->_data,_recv_msg_node->_total_len);

            // 继续处理
            _b_head_parse = false;
            _recv_head_node->clear();

            // 不剩下数据了
            if (bytes_transferred <= 0) {
                memset(_data, 0, MAX_LENGTH);
                _socket.async_read_some(boost::asio::buffer(_data, MAX_LENGTH),
                                        std::bind(&CSession::handle_read, this, std::placeholders::_1,
                                                  std::placeholders::_2, self_shared));
                return;
            }
            // 继续循环即可
            continue;
        }
    } catch (std::exception &e) {
        std::cerr << "Error occurred: " << e.what() << std::endl;
    }
}

void CSession::handle_write(const boost::system::error_code &errorCode, std::shared_ptr<CSession> self_shared) {
    try {
        if (errorCode.value() != 0) {
            std::cerr << "Error occurred int write: " << errorCode.message() << std::endl;
            // 移除连接
            _server->clear_session(_uuid);
            return;
        }
        std::cout << "Writing to client successfully, start read from client\n";

        // 调用回调函数表示已经写完
        // 上锁
        std::lock_guard<std::mutex> lock(_send_lock);
        // 弹出队列元素
        _send_que.pop();
        // 队列是否还有剩下的消息
        if (!_send_que.empty()) {
            // 接着发，直到队列为空
            auto &msgNode = _send_que.front();
            boost::asio::async_write(_socket, boost::asio::buffer(msgNode->_data, msgNode->_total_len),
                                     std::bind(&CSession::handle_write, this, std::placeholders::_1, self_shared));
        }
    } catch (std::exception &e) {
        std::cerr << "Error occurred: " << e.what() << std::endl;
    }
}

void CSession::send(char *msg, int max_length) {
    // 发送队列里是否还有没法完的数据
    bool pending = false;
    // 上锁
    std::lock_guard<std::mutex> lock(_send_lock);
    int send_que_size = _send_que.size();
    if (send_que_size > MAX_SENDQUE) {
        std::cout << "session: " << _uuid << " send que fulled, size is " << MAX_SENDQUE << std::endl;
        return;
    }

    // 加入发送队列
    _send_que.push(std::make_shared<MsgNode>(msg, max_length));

    if (send_que_size > 0) {
        // 之前的还没发送完
        return;
    }
    auto &msgNode = _send_que.front();
    // 之前的全发完了，直接发本次的
    boost::asio::async_write(_socket, boost::asio::buffer(msgNode->_data, msgNode->_total_len),
                             std::bind(&CSession::handle_write, this, std::placeholders::_1, shared_from_this()));
}

// 打印二进制数据
void CSession::printRecvData(char *data, int length) {
    std::stringstream ss;
    std::string result = "0x";
    for (int i = 0; i < length; i++) {
        std::string hexstr;
        ss << std::hex << std::setw(2) << std::setfill('0') << int(data[i]) << std::endl;
        ss >> hexstr;
        result += hexstr;
    }
    std::cout << "receive raw data is : " << result << std::endl;;
}

void CSession::send(std::string msg) {
    std::lock_guard<std::mutex> lock(_send_lock);

    int send_que_size = _send_que.size();
    if (send_que_size > MAX_SENDQUE) {
        std::cout << "session: " << _uuid << " send que fulled, size is " << MAX_SENDQUE << std::endl;
        return;
    }
    _send_que.push(std::make_shared<MsgNode>(msg.c_str(), msg.size()));
    if (send_que_size > 0) {
        return;
    }
    auto &msgNode = _send_que.front();
    boost::asio::async_write(_socket, boost::asio::buffer(msgNode->_data, msgNode->_total_len),
                             std::bind(&CSession::handle_write, this, std::placeholders::_1, shared_from_this()));
}

void CSession::handle_read_head(const boost::system::error_code &errorCode, size_t bytes_transferred,
                                std::shared_ptr<CSession> self_shared) {
    if (errorCode.value() != 0) {
        std::cerr << "Error occurred in read head: " << errorCode.message() << std::endl;
        // 移除连接
        _server->clear_session(_uuid);
        return;
    }
    if (bytes_transferred < HEAD_LENGTH) {
        std::cerr << "Error occurred in read head: " << errorCode.message() << std::endl;
        // 移除连接
        _server->clear_session(_uuid);
        return;
    }
    // 解析包头数据
    short data_len = 0;
    memcpy(&data_len, _recv_head_node->_data, HEAD_LENGTH);
    // 字节序处理
    data_len = boost::asio::detail::socket_ops::network_to_host_short(data_len);
    if (data_len > MAX_LENGTH) {
        std::cerr << "Invalid data length :" << data_len << std::endl;
        _server->clear_session(_uuid);
        return;
    }


    _recv_msg_node = std::make_shared<MsgNode>(data_len);

    boost::asio::async_read(_socket, boost::asio::buffer(_recv_msg_node->_data, _recv_msg_node->_total_len),
                            std::bind(&CSession::handle_read_msg, this, std::placeholders::_1, std::placeholders::_2,
                                      shared_from_this()));
}

void CSession::handle_read_msg(const boost::system::error_code &errorCode, size_t bytes_transferred,
                               std::shared_ptr<CSession> self_shared) {
    if (errorCode.value() != 0) {
        std::cerr << "Error occurred in read msg: " << errorCode.message() << std::endl;
        // 移除连接
        _server->clear_session(_uuid);
        return;
    }
    // 测试粘包
    printRecvData(_recv_msg_node->_data, bytes_transferred);
    std::chrono::milliseconds dura(2000);
    std::this_thread::sleep_for(dura);
    _recv_msg_node->_data[_recv_msg_node->_total_len] = '\0';
    std::cout << "receive data is " << _recv_msg_node->_data << std::endl;
    send(_recv_msg_node->_data, _recv_msg_node->_total_len);
    // 再次接收头部数据
    _recv_head_node->clear();
    boost::asio::async_read(_socket, boost::asio::buffer(_recv_head_node->_data, HEAD_LENGTH),
                            std::bind(&CSession::handle_read_head, this, std::placeholders::_1, std::placeholders::_2,
                                      shared_from_this()));
}
```