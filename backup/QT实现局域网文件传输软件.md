# 总体架构
软件通过 UDP 组播查询局域网内的设备，每个设备可以选择指定用户进行发送文件；接收文件时可以指定保存路径；系统支持多线程

# 实现思路
## 准备工作
手动实现 clickedLabel，在点击时可以切换到 stackedWidget 的不同页
![image](https://github.com/user-attachments/assets/bb3513d2-d74e-4f81-ab95-16bec9c14182)
```C++
enum ClickedLabelState {
    Normal = 1,
    // Hover = 2,
    Selected = 3
};

class ClickedLabel : public QLabel {
    Q_OBJECT
  public:
    explicit ClickedLabel(QWidget* parent = nullptr);
    void setInfo(const QString& normalPath, const QString& selectedPath, const QString& text);
    ClickedLabelState getCurState() const;
    void setSelected(bool selected);

  private:
    ClickedLabelState _curstate;
    void setImage(const QString& path);
    // 两种状态的图片路径
    QString _normal_path;
    QString _selected_path;
    QString _text;
  signals:
    void clicked();

  protected:
    // 鼠标点击
    virtual void mousePressEvent(QMouseEvent* event) override;
    // 鼠标松开
    virtual void mouseReleaseEvent(QMouseEvent* event) override;
    // 鼠标悬浮
    virtual void enterEvent(QEnterEvent* event) override;
    // 鼠标移走
    virtual void leaveEvent(QEvent* event) override;
};
```
```C++
ClickedLabel::ClickedLabel(QWidget* parent) : QLabel(parent), _curstate(ClickedLabelState::Normal) {

}

void ClickedLabel::setInfo(const QString& normalPath, const QString& selectedPath, const QString& text) {
    _normal_path = normalPath;
    _selected_path = selectedPath;
    _text = text;
}

ClickedLabelState ClickedLabel::getCurState() const {
    return _curstate;
}


void ClickedLabel::setSelected(bool selected) {
    if (selected) {
        _curstate = ClickedLabelState::Selected;
        setImage(_selected_path);
    } else {
        _curstate = ClickedLabelState::Normal;
        setImage(_normal_path);
    }
}

void ClickedLabel::setImage(const QString& path) {
    int imageWidth = 20;   // 图像宽度
    int imageHeight = 20;  // 图像高度

    // 使用 HTML 标签设置图像和文字
    QString labelText =
        QString("<img src='%1' width='%2' height='%3'> %4").arg(path).arg(imageWidth).arg(imageHeight).arg(_text);
    setText(labelText);
}

void ClickedLabel::mousePressEvent(QMouseEvent* event) {
    if (event->button() == Qt::LeftButton) {
        // 按下左键，切换状态
        if (_curstate == ClickedLabelState::Normal) {
            // qInfo() << "Mouse press event, set state selected";
            _curstate = ClickedLabelState::Selected;
        }else{
            qInfo()<<"This label is already selected";
        }
        return;
    }
    QLabel::mousePressEvent(event);
}

void ClickedLabel::mouseReleaseEvent(QMouseEvent* event) {
    if (event->button() == Qt::LeftButton) {
        if (_curstate == ClickedLabelState::Selected) {
            setImage(_selected_path);
            emit clicked();
        }else{
            qInfo()<<"This label is already selected";
        }
        return;
    }
    // 调用基类的mousePressEvent以保证正常的事件处理
    QLabel::mousePressEvent(event);
}

void ClickedLabel::enterEvent(QEnterEvent* event) {
    setCursor(Qt::PointingHandCursor);
    QLabel::enterEvent(event);
}

void ClickedLabel::leaveEvent(QEvent* event) {
    setCursor(Qt::ArrowCursor);
    QLabel::leaveEvent(event);
}
```
## 设备信息
创建 DeviceInfo 头文件，存储设备的一些信息，为了发送与接收的时候方便解析，添加转换函数
```C++
struct DeviceInfo {
    QString deviceName;
    QString ip;
    // tcp 监听的端口
    QString port;]
    // 是否已经退出
    bool isQuit = false;
    // 重载运算符用于序列化
    friend QDataStream& operator<<(QDataStream& out, const DeviceInfo& device) {
        out << device.deviceName << device.ip << device.port << device.isQuit;
        return out;
    }

    // 重载运算符用于反序列化
    friend QDataStream& operator>>(QDataStream& in, DeviceInfo& device) {
        in >> device.deviceName >> device.ip >> device.port >> device.isQuit;
        return in;
    }
};

// 将结构体转换为QByteArray
static QByteArray deviceInfoToByteArray(const DeviceInfo& device) {
    QByteArray byteArray;
    QDataStream out(&byteArray, QIODevice::WriteOnly);
    out.setVersion(QDataStream::Qt_6_5);  // 设置版本，确保兼容性
    out << device;                        // 使用重载的<<运算符
    return byteArray;
}

// 从QByteArray恢复结构体
static DeviceInfo byteArrayToDeviceInfo(const QByteArray& byteArray) {
    DeviceInfo device;
    QDataStream in(byteArray);
    in.setVersion(QDataStream::Qt_6_5);  // 设置版本，确保兼容性
    in >> device;                        // 使用重载的>>运算符
    return device;
}
```
## 组播功能
创建 DiscoveryService 单例类，进行组播，采用定时器定时组播，查询到的设备存放到 QMap 中进行管理
### 成员变量
```C++
class DiscoveryService : public QObject {
    Q_OBJECT
  public:
    static DiscoveryService* getInstance();
    // 开启组播服务
    void startDiscovery();
    DiscoveryService& operator=(const DiscoveryService&) = delete;
    DiscoveryService(const DiscoveryService&) = delete;
    void setDeivceName(const QString& name);
    ~DiscoveryService();
    void stopDiscovery();
  signals:
    // 发现了新设备
    void deviceDiscovered(const DeviceInfo& receiveDevice);
    // 更新设备名称
    void deviceNameChanged(const DeviceInfo& receiveDevice);
    void deviceRemoved(const DeviceInfo& removeDevice);

  private:
    explicit DiscoveryService(QObject* parent = nullptr);
    // 组播频率
    const int DISCOVERY_INTERVAL_MS = 500;
    // 组播的端口号
    const quint16 multicastPort;
    // 组播地址
    const QHostAddress multicastAddress;
    QUdpSocket* _udp_socket;
    QTimer* _discovery_timer;
    // 存放本机设备信息
    DeviceInfo _device;
    // 组播查询到的设备列表
    QMap<QString, DeviceInfo> _devices;
    // 获取本机IP地址
    const QString getLocalAddress();

  private slots:
    void processPendingDatagrams();
};
```
### 构造函数
初始化UDPSocket、绑定组播端口、地址、设备的ip，绑定信号和槽函数
```C++
DiscoveryService::DiscoveryService(QObject* parent) :
    QObject{parent}, multicastPort(45454), multicastAddress(QHostAddress("239.255.255.250")) {
    _udp_socket = new QUdpSocket(this);
    _discovery_timer = new QTimer(this);
    _device.ip = getLocalAddress();
    // 绑定本地端口
    _udp_socket->bind(QHostAddress::AnyIPv4, multicastPort);
    // 加入组播组
    _udp_socket->joinMulticastGroup(multicastAddress);
    // 开始接受数据包
    connect(_udp_socket, &QUdpSocket::readyRead, this, &DiscoveryService::processPendingDatagrams);
}
const QString DiscoveryService::getLocalAddress() {
    QStringList ipAddressesList;  // 创建一个字符串列表来存储本机的所有 IP 地址
    // 获取所有网络接口的 IP 地址列表
    const QList<QHostAddress>& allAddresses = QNetworkInterface::allAddresses();
    // 遍历所有 IP 地址，筛选出有效的 IPv4 地址（忽略回环地址和本地链路地址）
    for (const QHostAddress& address : allAddresses) {
        if (address.protocol() == QAbstractSocket::IPv4Protocol &&  // 只选择 IPv4 地址
            !address.isLoopback()) {                                // 忽略回环地址（如 127.0.0.1）
            ipAddressesList.append(address.toString());             // 将符合条件的地址添加到列表中
        }
    }
    // 如果找到至少一个有效的本机 IP 地址，返回第一个
    if (!ipAddressesList.isEmpty()) {
        qDebug() << "Local address is " << ipAddressesList.first();
        return ipAddressesList.first();  // 返回第一个符合条件的本机 IP 地址
    } else {
        qWarning() << "Fail to find local address";
        return "0.0.0.0";  // 如果没有找到有效地址，返回 "0.0.0.0"
    }
}
```
### 发送与处理组播消息的槽函数
发送：将本机的信息进行组播，使用定时器定时发送
```C++
void DiscoveryService::startDiscovery() {
    connect(_discovery_timer, &QTimer::timeout, this, [this]() {
        // 发送组播请求
        // TODO 本机分配给tcp的端口获取
        QByteArray datagram = deviceInfoToByteArray(_device);  // 组播请求内容
        // qDebug() << "Sending datagram:" << datagram.toHex();   // 打印数据包内容
        _udp_socket->writeDatagram(datagram, multicastAddress, multicastPort);
    });
    _discovery_timer->start(DISCOVERY_INTERVAL_MS);
}
```
接收：解析收到的设备信息
1. 本机设备就直接return
2. isQuit为true就移除该设备
3. 已经存在于设备列表中并且设备名字也没更改也直接跳过
4. 已经存在于设备列表中但是设备名字更改了就发送deviceNameChanged信号通知ui更新
5. 不存在于设备列表中就发送deviceDiscovered信号通知ui更新
```C++
void DiscoveryService::processPendingDatagrams() {
    while (_udp_socket->hasPendingDatagrams()) {
        QByteArray datagram;
        // 接收到的设备信息
        QHostAddress sender;
        quint16 senderPort;
        // 接收数据包
        datagram.resize(_udp_socket->pendingDatagramSize());
        _udp_socket->readDatagram(datagram.data(), datagram.size(), &sender, &senderPort);

        DeviceInfo receiveDevice = byteArrayToDeviceInfo(datagram);
        // 处理接收到的数据包
        QString ip = receiveDevice.ip;
        QString port = receiveDevice.port;
        QString name = receiveDevice.deviceName;
        bool quit = receiveDevice.isQuit;
        // 发现的是本机
        if (name == _device.deviceName && ip == _device.ip) {
            return;
        }
        if (quit) {
            qDebug() << "Device removed: " << name;
            // 退出了
            _devices.remove(ip);
            emit deviceRemoved(receiveDevice);
            return;
        }
        // 第一次组播到该设备
        if (!_devices.contains(ip)) {
            // 更新设备列表
            _devices[ip] = receiveDevice;
            qDebug() << "New device discovered:" << ip << ", Tcp port:" << port << ", name: " << name;
            // 发现新设备
            emit deviceDiscovered(receiveDevice);
        } else {
            // 已经加到列表里了
            QString exist_name = _devices[ip].deviceName;
            if (exist_name == name) {
                // 如果名字一样就不更新
                return;
            } else {
                _devices[ip] = receiveDevice;
                // 更新名字
                emit deviceNameChanged(receiveDevice);
            }
        }
    }
}
```
停止组播与析构：先将定时器关闭，将isQuit设置为true再进行一次组播，其它设备接受到后就会在上面的函数中执行从设备列表中移除该设备的逻辑
```C++
DiscoveryService::~DiscoveryService() {
    _udp_socket->leaveMulticastGroup(multicastAddress);
    _udp_socket->abort();
    qDebug() << "Exit multicast";
}

void DiscoveryService::stopDiscovery() {
    _discovery_timer->stop();
    //  移除该设备
    _device.isQuit = true;
    QByteArray datagram = deviceInfoToByteArray(_device);  // 组播请求内容
    // qDebug() << "Sending datagram:" << datagram.toHex();   // 打印数据包内容
    _udp_socket->writeDatagram(datagram, multicastAddress, multicastPort);
}
```
### MainWindow中对上面的各个信号的处理、初始化：
构造函数中，初始化各个图标，设置本机的设备名，开始进行组播，并连接信号与槽
```C++
MainWindow::MainWindow(QWidget* parent) : QMainWindow(parent), ui(new Ui::MainWindow) {
    ui->setupUi(this);

    // .... 设置ui 的操作.....略
    // 获取本机设备名
    ui->edit_device_name->setText(QHostInfo::localHostName());
    DiscoveryService::getInstance()->setDeivceName(ui->edit_device_name->text());
    DiscoveryService::getInstance()->startDiscovery();
    // 发现新设备
    connect(DiscoveryService::getInstance(), &DiscoveryService::deviceDiscovered, this, &MainWindow::updateDeviceList);
    // 更新设备列表的名字
    connect(DiscoveryService::getInstance(), &DiscoveryService::deviceNameChanged, this, &MainWindow::updateDeviceName);
    // 移除设备
    connect(DiscoveryService::getInstance(), &DiscoveryService::deviceRemoved, this, &MainWindow::removeDevice);
}
```
发现了新设备的槽函数：将设备名称用于显示，ip等信息保存到data中即可
```C++
void MainWindow::updateDeviceList(const DeviceInfo& receiveDevice) {
    QListWidgetItem* item = new QListWidgetItem(receiveDevice.deviceName);
    QByteArray data;
    QDataStream stream(&data, QIODevice::WriteOnly);
    stream << receiveDevice.ip << receiveDevice.port;
    // 存储ip 和端口
    item->setData(Qt::UserRole, data);
    ui->deviceListWidget->addItem(item);
}
```
更新设备名称的槽函数：找到ip一样的，进行更新界面即可
```C++
void MainWindow::updateDeviceName(const DeviceInfo& receiveDevice) {
    for (int i = ui->deviceListWidget->count() - 1; i >= 0; i--) {
        QListWidgetItem* item = ui->deviceListWidget->item(i);
        QByteArray data = item->data(Qt::UserRole).toByteArray();
        QDataStream stream(&data, QIODevice::ReadOnly);
        QString ip;
        // 读取 ip
        stream >> ip;
        if (ip == receiveDevice.ip) {
            qDebug() << "Found devices that name changed ip: " << ip;
            // 更新设备名称
            qDebug() << "Updating device name to: " << receiveDevice.deviceName;
            item->setText(receiveDevice.deviceName);
            ui->deviceListWidget->update();
            return;
        }
    }
}
```
移除设备的槽函数：找到设备名称一样的item进行移除即可
```C++
void MainWindow::removeDevice(const DeviceInfo& removeDevice) {
    for (int i = ui->deviceListWidget->count() - 1; i >= 0; i--) {
        QListWidgetItem* item = ui->deviceListWidget->item(i);
        if (item->text() == removeDevice.deviceName) {
            qDebug() << "Remove device: " << item->text();
            ui->deviceListWidget->removeItemWidget(item);
            delete ui->deviceListWidget->takeItem(ui->deviceListWidget->row(item));
            return;
        }
    }
}
```
添加信号与槽：在设备名称的输入框修改后，同步更改DiscoveryService中的本机设备信息
```C++
void MainWindow::on_edit_device_name_editingFinished() {
    qDebug() << "Editing finished slot";
    DiscoveryService::getInstance()->setDeivceName(ui->edit_device_name->text());
}
```
在MainWindow的析构函数中，停止组播即可：
```C++
MainWindow::~MainWindow() {
    DiscoveryService::getInstance()->stopDiscovery();
    delete ui;
}
```
## 文件类
为了文件传输的完整性、保存位置的灵活性，将发送的文件封装成一个类：
```C++
class TLVFile {
  public:
    enum MessageType { ID_FILE = 1, ID_TEXT = 2, ID_FOLDER = 3 };
    TLVFile(MessageType type, const QString& fileName, const QByteArray& content);
    // 序列化
    QByteArray toByteArray() const;
    // 反序列化
    static TLVFile fromByteArray(const QByteArray& data);
    // 获取消息类型
    MessageType getType() const;
    // 获取文件长度
    qint32 getLength() const;
    // 获取内容
    QByteArray getContent() const;
    // 获取文件名称
    QString getFileName() const;

  private:
    MessageType _type;
    qint32 _length;       // 消息长度
    QByteArray _content;  // 消息内容
    QString _fileName;    // 文件名称
};
```
```C++
TLVFile::TLVFile(MessageType type, const QString& fileName, const QByteArray& content) :
    _type(type), _fileName(fileName), _content(content) {
    _length = content.size();
}

// 获取文件名称
QString TLVFile::getFileName() const { return _fileName; }

// 序列化实现
QByteArray TLVFile::toByteArray() const {
    QByteArray result;
    QDataStream stream(&result, QIODevice::WriteOnly);
    stream << static_cast<qint32>(_type);
    stream << _length;
    stream << _fileName;
    stream << _content;
    return result;
}

// 反序列化实现
TLVFile TLVFile::fromByteArray(const QByteArray& data) {
    QDataStream stream(data);
    qint32 type;
    qint32 length;
    QString fileName;
    QByteArray content;

    stream >> type >> length >> fileName >> content;

    return TLVFile(static_cast<MessageType>(type), fileName, content);
}

TLVFile::MessageType TLVFile::getType() const { return _type; }

qint32 TLVFile::getLength() const { return _length; }

QByteArray TLVFile::getContent() const { return _content; }
```
## 创建TCP服务器
一个客户端只能有一个TCP服务器，直接采用单例模式即可，端口号自动分配，在组播时将获取的TCP端口号进行设置，在接收到连接后，将接收任务交给线程池来处理
```C++
class FileTransferServer : public QObject {
    Q_OBJECT
  public:
    static FileTransferServer* getInstance();
    FileTransferServer(const FileTransferServer&) = delete;
    FileTransferServer& operator=(const FileTransferServer&) = delete;
    ~FileTransferServer();
    // 开启服务器
    void startServer();
    // 获取服务器端口
    quint16 getPort();

  private:
    explicit FileTransferServer(QObject* parent = nullptr);
    QTcpServer* _server;
    quint16 _port;
  private slots:
    // 处理新连接
    void acceptConnection();
  signals:
};
```
```C++
FileTransferServer* FileTransferServer::getInstance() {
    static FileTransferServer instance;
    return &instance;
}

FileTransferServer::FileTransferServer(QObject* parent) : QObject{parent}, _server(nullptr) {
    // 限制为5个线程
    QThreadPool::globalInstance()->setMaxThreadCount(5);
}

void FileTransferServer::acceptConnection() {
    QTcpSocket* clientSocket = _server->nextPendingConnection();
    qDebug() << "New connection from " << clientSocket->peerAddress().toString();
    // 接收连接后的任务处理交给线程池
    // TODO 接收功能实现
    FileReceiver* receiver = new FileReceiver(clientSocket);
    QThreadPool::globalInstance()->start(receiver);  // 将任务加入线程池
}

FileTransferServer::~FileTransferServer() {
    if (_server) {
        _server->close();
    }
}

void FileTransferServer::startServer() {
    if (!_server) {
        _server = new QTcpServer(this);
        connect(_server, &QTcpServer::newConnection, this, &FileTransferServer::acceptConnection);
        // 自动分配端口
        if (!_server->listen(QHostAddress::AnyIPv4, 0)) {
            qWarning() << "Server failed to start!";
        } else {
            _port = _server->serverPort();
            qDebug() << "Server started on port" << _port;
        }
    }
}

quint16 FileTransferServer::getPort() { return _port; }
```
## TCP发送端
发送端获取服务端ip、端口和本地的文件路径，包装成TLVFile类型进行发送即可
```C++
class FileTransferClient : public QObject {
    Q_OBJECT
  public:
    explicit FileTransferClient(QObject* parent = nullptr);
    void sendFile(const QString& serverIp, quint16 serverPort, const QString& filePath);

  private:
    QTcpSocket* _socket;
  signals:
};
```C++
FileTransferClient::FileTransferClient(QObject* parent) : QObject{parent}, _socket(new QTcpSocket(this)) {}

void FileTransferClient::sendFile(const QString& serverIp, quint16 serverPort, const QString& filePath) {
    QFile file(filePath);
    if (!file.open(QIODevice::ReadOnly)) {
        qDebug() << "Failed to open file";
        return;
    }
    QByteArray fileContent = file.readAll();
    TLVFile tlvFile(TLVFile::ID_FILE, QFileInfo(file).fileName(), fileContent);
    // 连接到服务器
    _socket->connectToHost(serverIp, serverPort);
    connect(_socket, &QTcpSocket::connected, this, [=]() {
        // 连接成功进行发送
        _socket->write(tlvFile.toByteArray());
        _socket->disconnectFromHost();
    });
}
```
## 多线程接收
在服务端中，对连接的处理放到了FileReceiver中进行处理，实现如下：
```C++
class FileReceiver : public QRunnable {
  public:
    FileReceiver(QTcpSocket* socket);
    void run() override;

  private:
    QTcpSocket* _socket;
};
```
```C++
FileReceiver::FileReceiver(QTcpSocket* socket) : _socket(socket) {}

void FileReceiver::run() {
    qDebug() << "FileReceiver thread started.";

    while (_socket->waitForReadyRead()) {
        QByteArray data = _socket->readAll();

        // 反序列化 TLV 数据
        TLVFile tlvFile = TLVFile::fromByteArray(data);

        if (tlvFile.getType() == TLVFile::ID_FILE) {
            QString fileName = tlvFile.getFileName();
            // 验证并保存文件
            // 让用户选择保存路径
            QString savePath = QFileDialog::getSaveFileName(nullptr, "Save File", fileName);
            if (!savePath.isEmpty()) {
                QFile file(savePath);
                if (file.open(QIODevice::WriteOnly)) {
                    file.write(tlvFile.getContent());
                    file.close();
                    qDebug() << "File saved to" << savePath;
                }
            } else {
                qWarning() << "Didn't choose right save path";
            }
        }
    }

    _socket->close();
    delete _socket;
}
```
## 界面操作
点击设备列表中的设备时，缓存其ip、端口，_peer_data 是增加的QByteArray型成员变量
```C++
void MainWindow::on_deviceListWidget_itemClicked(QListWidgetItem* item) {
    // 获取选中的 item 的ip、端口
    _peer_data = item->data(Qt::UserRole).toByteArray();
    if (_peer_data.isEmpty()) {
        qWarning() << "Selected ip port is null";
    }
}
```
点击上传文件时，选择文件路径
```C++
void MainWindow::on_btn_add_file_clicked() {
    QString fileName = QFileDialog::getOpenFileName(this, "选择文件", "",
                                                    "所有文件 (*.*);;文本文件 (*.txt);;图像文件 (*.png *.jpg*.bmp) ");
    if (!fileName.isEmpty()) {
        _selected_file_path = fileName;  // 保存文件路径
        qDebug() << "Selected file path is: " << _selected_file_path;
        ui->btn_send->setEnabled(true);
    }
}
```
点击发送进行文件发送
```C++
void MainWindow::on_btn_send_clicked() {
    // 发送文件
    if (!ui->deviceListWidget->currentItem()) {
        QMessageBox::warning(this, "警告", "未选择接收设备！");
        return;
    }
    if (_selected_file_path.isEmpty()) {
        QMessageBox::warning(this, "警告", "未选择文件");
        return;
    }
    QDataStream stream(&_peer_data, QIODevice::ReadOnly);
    QString ip;
    quint16 port;
    stream >> ip >> port;
    if (ip.isEmpty() || port == 0) {
        qWarning() << "Failed to parse peer ip, port";
    }
    _client->sendFile(ip, port, _selected_file_path);
}
```