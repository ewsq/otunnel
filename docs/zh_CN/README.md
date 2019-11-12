# otunnel 工具



## 目录

1. [设计](DESIGN.md)
2. [也许](Maybe.md)
3. [相关项目](docs/related.md)

### 平台

- [DD-WRT](./ddwrt.md)



## 架构

otunnel 模块说明

### client/peer/node


### relay server

在节点之间 **转发** TCP/UDP 数据包


### tracker

节点通过连接 tracker ，可以尝试获取对方/自己的 UDP 端口信息：

- client A 向 stun 询问自己的端口，再向 tracker 报告自己的连接信息
- client B 向 tracker 询问 client B 的连接信息，并尝试连接
- client A 与 B 交换数据

tracker 通常只用一个唯一串匹配资源。我们可以把每个连接 tracker 的 client
都设置一个 UID 。其他想建立连接的 client 可以使用该 UID 通过 tracker 找到该 client 。


### stun

### DHT


otunnel 是一款对称的安全隧道工具。特点及优势：

单二进制程序：otunnel 为一个独立的二进制程序，可以作为 server 和 client 端。

支持多操作系统平台：支持GNU/Linux, Unix-like, Mac, Windows，其他如 ddwrt 等 arm 平台。

无需配置文件：命令行使用

对称设计：同时支持 正、反向代理（端口映射）

安全加密：支持 AES 对称加密

反向代理示意图

简明 otunnel 使用方法
前提：

假设 server 的地址为 example.com

从 client 能访问 server ( client 与 server 无需在同一个网络 )

注意 提供不同的运行参数，otunnel 程序可以作为 server 角色或 client 角色

快速上手
server 端
?
1
./otunnel listen :10000 -s longlongsecret
client 端
反向代理
举例：将 client 可以访问的 192.168.1.3:22 映射到 server 上的 10022 端口：

?
1
2
3
./otunnel connect example.com:10000 \
     -s longlongsecret \
     -t 'r:192.168.1.3:22::10022'
现在访问 example.com:10022 即等于访问了 client 内网的 192.168.1.3:22

正向代理
举例：假设 example.com 的 127.0.0.1 网络有 3128 端口（你懂的），在 client 执行：

?
1
2
3
./otunnel connect example.com:10000 \
     -s longlongsecret \
     -t 'f::20080:127.0.0.1:3128'
现在 client 上的任何程序访问 20080 等于访问了 example.com 上的本地 3128 端口。

选项详解
-t 格式
包含多个字段信息，以 : 隔开(为空的字段也不能省略:)。

?
1
代理类型:本地地址:本地端口:远程地址:远程端口
字段	含义
代理类型	r 表示反向代理; f 表示正向代理
本地地址	IP或域名
本地端口	整数
远程地址	IP或域名
远程端口	整数
说明

本地地址 或 远程地址 如果为空，表示所有网口

otunnel 命令行可以包含多个 -t 选项，同时指定多条隧道规则
