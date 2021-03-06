# TCP-IP 详解：ARP 地址解析协议

ARP 为 IP 地址到对应的硬件地址之间提供动态映射，这个过程是自动完成的，一般应用程序用户或系统管理员不必关心。

## 一个 ftp 请求的完整流程

当我们输入下面这个命令时：

```bash
ftp bsdi
```

会进行以下步骤：

1. 应用程序 FTP 客户端调用函数`gethostbyname()`把主机名 bsdi 转换成 32bit 的 IP 地址，这个函数在 DNS 中称作解析器。
2. FTP 客户端请求 TCP 用得到的 IP 地址建立连接。
3. TCP 发送一个链接请求到远端的主机，即用上述 IP 地址发送一份 IP 数据报。
4. 如果目的主机在本地网络上，那么 IP 数据报可以直接送到目的主机上。如果目的主机在一个远程网络上，那么就通过 IP 选路函数来确定位于本地网络上的下一站路由器地址，并让它转发 IP 数据报。
5. 假定是一个以太网，那么发送端主机必须把 32bit 的 IP 地址变换成 48 bit 的以太网地址。这就是 ARP 的功能。
6. ARP 发送一份称作 ARP 请求的以太网数据帧给以太网上的每个主机。这个过程称作广播。
7. 目的主机的 ARP 层收到这份广播报文后，识别出这是发送端在询问它的 IP 地址，于是发送一个 ARP 应答。这个 ARP 应答包含 IP 地址及对应的硬件地址。
8. 收到 ARP 应答后，使 ARP 进行请求-应答交换的 IP 数据报现在就可以传送了。
9. 发送 IP 数据报到目的主机。

## ARP 高速缓存

ARP 高效运行的关键是由于每个主机上都有一个 ARP 高速缓存，存放了最近 IP 地址到硬件地址之间的映射记录。

## ARP 的分组格式

![ARP 格式](https://cnymw.github.io/GolangStudy/docs/img/网络-TCP-IP详解-ARP-格式.png)

- 6位以太网目的地址：目的地址为全 1 的特殊地址是广播地址，电缆上的所有以太网接口都要接受广播的数据帧。
- 6位以太网源地址：源地址的以太网地址。
- 2位帧类型：表示后面数据的类型，对于 ARP 请求或应答来说，该字段值为 0x0806。
- 2位硬件类型：硬件地址的类型。它的值为 1 即表示以太网地址。
- 2位协议类型：表示要映射的协议地址类型。它的值为 0x0800 即表示 IP 地址。
- 1位硬件地址长度：表示硬件地址长度，以字节为单位。对于 ARP 来说，值为6。
- 1位协议地址长度：表示协议地址长度，以字节为单位。对于 ARP 来说，值为4。
- 2位操作类型：指定 4 种操作类型：1.ARP 请求 2.ARP 应答 3. RARP 请求 4.RARP 请求
- 6位发送端的以太网地址：发送端的硬件地址。
- 4位发送端 IP 地址：发送端的协议地址。
- 6位目的以太网地址：目的端的硬件地址。
- 4位目的端 IP 地址：目的端的协议地址。

## ARP 代理

如果 ARP 请求是从一个网络的主机发往另一个网络上的主机，那么连接这两个网络的路由器就可以回答该请求，这个过程称作委托 ARP 或 ARP 代理（Proxy ARP）。

这样可以欺骗发起 ARP 请求的发送端，使它误以为路由器就是目的主机。

# 思维导图

![思维导图](https://cnymw.github.io/GolangStudy/docs/img/网络-TCP-IP详解-ARP-思维导图.png)