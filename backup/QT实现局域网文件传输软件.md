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