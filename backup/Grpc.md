# 下载

vcpkg install grpc

# 配置

cmake 中：

```cmake
find_package(Protobuf CONFIG REQUIRED)
find_package(gRPC CONFIG REQUIRED)
include_directories(${CMAKE_CURRENT_BINARY_DIR})
arget_link_libraries(${projectName} PRIVATE gRPC::grpc++ protobuf::libprotobuf)
```

# 编译

编写好 proto文件，在 powershell 中使用如下命令，插件选择自己的路径：

```cmd
protoc --proto_path=. --cpp_out=. demo.proto
protoc --proto_path=. --grpc_out=. --plugin=protoc-gen-grpc="D:\develop_tools\cppsoft\vcpkg\packages\grpc_x64-windows\tools\grpc\grpc_cpp_plugin.exe" demo.proto
```

生成文件添加到 cmakelists.txt 中

# 通信流程

1. 连接到服务端的地址
2. 创建客户端实例，调用函数，将请求消息发送给服务端
3. 服务端接收到请求，处理请求消息并生成响应消息。
4. 服务端返回一个 `Status` 对象，指示调用是否成功，并传递响应消息。
5. 客户端接收到服务端的响应，检查调用状态。
6. 客户端根据状态处理响应消息或错误信息。

# 服务端示例

demo.proto：

```protobuf
syntax = "proto3";
package hello;
service Greeter {
    rpc SayHello (HelloRequest) returns (HelloReply) {}
}
message HelloRequest {
    string message = 1;
}
message HelloReply {
    string message = 1;
}
```

```c++
#include <iostream>
#include <memory>
#include<string>
#include <grpcpp/grpcpp.h>
#include "demo.grpc.pb.h"

using grpc::Server;
using grpc::ServerBuilder;
using grpc::ServerContext;
using grpc::Status;
using hello::HelloRequest;
using hello::HelloReply;
using hello::Greeter;

class GreeterServiceImpl final : public Greeter::Service {
    // 返回状态，重写的基类函数
    Status SayHello(ServerContext *context, const HelloRequest *request,
                    HelloReply *response) override {
        std::string prefix("Grpc Server has received:");
        // 回应信息
        response->set_message(prefix + request->message());
        return Status::OK;
    };
};

void runServer() {
    std::string server_address("0.0.0.0:50051");
    GreeterServiceImpl service;
    // 创建 grpc 服务器的构建器
    ServerBuilder builder;
    // 指定服务器监听的端口和使用的凭据
    builder.AddListeningPort(server_address, grpc::InsecureServerCredentials());
    // 注册实现的服务
    builder.RegisterService(&service);
    // 构建并启动服务器
    std::unique_ptr<Server> server(builder.BuildAndStart());
    std::cout << "Server listening on " << server_address << std::endl;
    // 阻塞当前线程，直到服务器停止
    server->Wait();
}

int main() {
    runServer();
    return 0;
}
```

# 客户端示例

```c++
#include <iostream>           // 引入标准输入输出库，用于打印信息
#include <string>             // 引入字符串库，用于处理字符串
#include <memory>             // 引入智能指针库，用于管理动态分配的对象
#include <grpcpp/grpcpp.h>   // 引入 gRPC 的 C++ API
#include "demo.grpc.pb.h"    // 引入由 Protobuf 编译器生成的 gRPC 头文件

// 使用 grpc 命名空间中的相关类和函数
using grpc::ClientContext;
using grpc::Channel;
using grpc::Status;

// 使用 hello 命名空间中的消息和服务类
using hello::HelloReply;
using hello::HelloRequest;
using hello::Greeter;

// 定义客户端类 FCClient
class FCClient {
public:
    // 构造函数，接收一个共享的 gRPC 通道
    FCClient(std::shared_ptr<Channel> channel)
            : _stub(Greeter::NewStub(channel)) {}; // 创建一个 Greeter 服务的客户端存根

    // 定义一个方法来调用 SayHello 服务
    std::string SayHello(std::string name) {
        ClientContext context;      // 创建客户端上下文
        HelloReply reply;           // 创建响应对象
        HelloRequest request;       // 创建请求对象
        request.set_message(name);  // 设置请求消息的内容

        // 调用 SayHello 方法，并将请求和响应对象传递进去
        Status status = _stub->SayHello(&context, request, &reply);

        if (status.ok()) {
            return reply.message();  // 如果调用成功，返回响应中的消息
        } else {
            return "failure " + status.error_message(); // 如果调用失败，返回错误信息
        }
    }

private:
    // 存根指针，用于调用服务端的方法（客户端）
    std::unique_ptr<Greeter::Stub> _stub;
};

// 主函数
int main() {
    // 创建一个 gRPC 通道，连接到服务端地址
    auto channel = grpc::CreateChannel("127.0.0.1:50051", grpc::InsecureChannelCredentials());

    // 创建客户端实例，传入之前创建的通道
    FCClient client(channel);

    // 调用 SayHello 方法，并传入消息
    std::string result = client.SayHello("hello ,club !");

    // 打印从服务端得到的结果
    printf("get result [%s]\n", result.c_str());

    return 0; // 程序正常退出
}
```