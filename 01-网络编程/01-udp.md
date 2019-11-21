# 01-网络编程

## 1 UDP

### 1.1 Linux命令

- 命令跳转到行首/行尾

  I/A

- 开启/关闭网卡

  Ifconfig 网卡名 up/down

- 配置网卡信息

  sudo ifconfig enp0s5 11.0.0.57 修改网卡enp0s5的IP

- 跳转到某行

  nG

- 复制某一行

  yyp
  
- 向上/下插入一行

  大写O/小写o

### 1.2 端口

- 知名端口
  
  知名端口是众所周知的端口号，范围从0到1023
  
  ```
  80端口分配给HTTP服务
  21端口分配给FTP服务
  ```
  
  - 查看端口：netstat -an
  - lsof -i [tcp/udp]:2425

- 动态端口（Dynamic Ports）

  动态端口的范围是从1024到65535

  之所以称为动态端口，是因为它一般不固定分配某种服务，而是动态分配。

  动态分配是指当一个系统程序或应用程序程序需要网络通信时，它向主机申请一个端口，主机从可用的端口号中分配一个供它使用。

  当这个程序关闭时，同时也就释放了所占用的端口号

### 1.3 socket简介

socket(简称 `套接字`) 是进程间通信的一种方式，它与其他进程间通信的一个主要不同是：

它能实现不同主机间的进程间通信，我们网络上各种各样的服务大多都是基于 Socket 来完成通信的

```python
import socket
socket.socket(AddressFamily, Type)
```

**说明：**

函数 socket.socket 创建一个 socket，该函数带有两个参数：

- Address Family：可以选择 AF_INET（用于 Internet 进程间通信） 或者 AF_UNIX（用于同一台机器进程间通信）,实际工作中常用AF_INET
- Type：套接字类型，可以是 SOCK_STREAM（流式套接字，主要用于 TCP 协议）或者 SOCK_DGRAM（数据报套接字，主要用于 UDP 协议）

创建一个tcp socket（tcp套接字）

```python
import socket

# 创建tcp的套接字
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# ...这里是使用套接字的功能（省略）...

# 不用的时候，关闭套接字
s.close()
```

创建一个udp socket（udp套接字）

```python
import socket

# 创建udp的套接字
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

# ...这里是使用套接字的功能（省略）...

# 不用的时候，关闭套接字
s.close()
```

**说明**

- 套接字使用流程 与 文件的使用流程很类似
  1. 创建套接字
  2. 使用套接字收/发数据
  3. 关闭套接字

### 1.4 发送接收数据

#### 1.4.1 发送数据

```python
#coding=utf-8

from socket import *

# 1. 创建udp套接字
udp_socket = socket(AF_INET, SOCK_DGRAM)

# 2. 准备接收方的地址
# '192.168.1.103'表示目的ip地址
# 8080表示目的端口
dest_addr = ('192.168.1.103', 8080)  # 注意 是元组，ip是字符串，端口是数字

# 3. 从键盘获取数据
send_data = input("请输入要发送的数据:")

# 4. 发送数据到指定的电脑上的指定程序中
udp_socket.sendto(send_data.encode('utf-8'), dest_addr)

# 5. 关闭套接字
udp_socket.close()
```

#### 1.4.2 接收数据

```python
#coding=utf-8

from socket import *

# 1. 创建udp套接字
udp_socket = socket(AF_INET, SOCK_DGRAM)

# 2. 准备接收方的地址
dest_addr = ('192.168.236.129', 8080)

# 3. 从键盘获取数据
send_data = input("请输入要发送的数据:")

# 4. 发送数据到指定的电脑上
udp_socket.sendto(send_data.encode('utf-8'), dest_addr)

# 5. 等待接收对方发送的数据
recv_data = udp_socket.recvfrom(1024)  # 1024表示本次接收的最大字节数

# 6. 显示对方发送的数据
# 接收到的数据recv_data是一个元组
# 第1个元素是对方发送的数据
# 第2个元素是对方的ip和端口
print(recv_data[0].decode('gbk'))
print(recv_data[1])

# 7. 关闭套接字
udp_socket.close()
```

### 1.5 python3编码转换

```python
str->bytes:encode编码
bytes->str:decode解码
```

字符串通过编码成为字节码，字节码通过解码成为字符串。

```python
>>> text = '我是文本'
>>> text
'我是文本'
>>> print(text)
我是文本
>>> bytesText = text.encode()
>>> bytesText
b'\xe6\x88\x91\xe6\x98\xaf\xe6\x96\x87\xe6\x9c\xac'
>>> print(bytesText)
b'\xe6\x88\x91\xe6\x98\xaf\xe6\x96\x87\xe6\x9c\xac'
>>> type(text)
<class 'str'>
>>> type(bytesText)
<class 'bytes'>
>>> textDecode = bytesText.decode()
>>> textDecode
'我是文本'
>>> print(textDecode)
我是文本
```

其中decode()与encode()方法可以接受参数，其声明分别为:

```python
bytes.decode(encoding="utf-8", errors="strict")
str.encode(encoding="utf-8", errors="strict")
```

其中的encoding是指在解码编码过程中使用的编码(此处指“编码方案”是名词)，errors是指错误的处理方案。

### 1.6 udp绑定端口

```python
#coding=utf-8

from socket import *

# 1. 创建套接字
udp_socket = socket(AF_INET, SOCK_DGRAM)

# 2. 绑定本地的相关信息，如果一个网络程序不绑定，则系统会随机分配
local_addr = ('', 7788) #  ip地址和端口号，ip一般不用写，表示本机的任何一个ip
udp_socket.bind(local_addr)

# 3. 等待接收对方发送的数据
recv_data = udp_socket.recvfrom(1024) #  1024表示本次接收的最大字节数

# 4. 显示接收到的数据
print(recv_data[0].decode('gbk'))

# 5. 关闭套接字
udp_socket.close()
```

### 1.7 udp聊天器

```python
import socket


def send_msg(udp_socket):
    """获取键盘数据，并将其发送给对方"""
    # 1. 从键盘输入数据
    msg = input("\n请输入要发送的数据:")
    # 2. 输入对方的ip地址
    dest_ip = input("\n请输入对方的ip地址:")
    # 3. 输入对方的port
    dest_port = int(input("\n请输入对方的port:"))
    # 4. 发送数据
    udp_socket.sendto(msg.encode("utf-8"), (dest_ip, dest_port))


def recv_msg(udp_socket):
    """接收数据并显示"""
    # 1. 接收数据
    recv_msg = udp_socket.recvfrom(1024)
    # 2. 解码
    recv_ip = recv_msg[1]
    recv_msg = recv_msg[0].decode("utf-8")
    # 3. 显示接收到的数据
    print(">>>%s:%s" % (str(recv_ip), recv_msg))


def main():
    # 1. 创建套接字
    udp_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    # 2. 绑定本地信息
    udp_socket.bind(("", 7890))
    while True:
        # 3. 选择功能
        print("="*30)
        print("1:发送消息")
        print("2:接收消息")
        print("="*30)
        op_num = input("请输入要操作的功能序号:")

        # 4. 根据选择调用相应的函数
        if op_num == "1":
            send_msg(udp_socket)
        elif op_num == "2":
            recv_msg(udp_socket)
        else:
            print("输入有误，请重新输入...")

if __name__ == "__main__":
    main()
```

## 2 TCP

### 2.1 TCP简介

#### 2.1.1 概念

**TCP协议，传输控制协议（英语：Transmission Control Protocol，缩写为 TCP）**是一种面向连接的、可靠的、基于字节流的传输层通信协议，由IETF的RFC 793定义。

TCP通信需要经过**创建连接、数据传送、终止连接**三个步骤。

TCP通信模型中，在通信开始之前，一定要先建立相关的链接，才能发送数据，类似于生活中，"打电话""

#### 2.1.2 特点

1. 面向连接

通信双方必须先建立连接才能进行数据的传输，双方都必须为该连接分配必要的系统内核资源，以管理连接的状态和连接上的传输。

双方间的数据传输都可以通过这一个连接进行。

完成数据交换后，双方必须断开此连接，以释放系统资源。

这种连接是一对一的，因此TCP不适用于广播的应用程序，基于广播的应用程序请使用UDP协议。

2. 可靠运输

1）**TCP采用发送应答机制**

TCP发送的每个报文段都必须得到接收方的应答才认为这个TCP报文段传输成功

2）**超时重传**

发送端发出一个报文段之后就启动定时器，如果在定时时间内没有收到应答就重新发送这个报文段。

TCP为了保证不发生丢包，就给每个包一个序号，同时序号也保证了传送到接收端实体的包的按序接收。然后接收端实体对已成功收到的包发回一个相应的确认（ACK）；如果发送端实体在合理的往返时延（RTT）内未收到确认，那么对应的数据包就被假设为已丢失将会被进行重传。

3）**错误校验**

TCP用一个校验和函数来检验数据是否有错误；在发送和接收时都要计算校验和。

4) **流量控制和阻塞管理**

流量控制用来避免主机发送得过快而使接收方来不及完全收下。

#### 2.1.3 TCP与UDP的不同点

- 面向连接（确认有创建三方交握，连接已创建才作传输。）
- 有序数据传输
- 重发丢失的数据包
- 舍弃重复的数据包
- 无差错的数据传输
- 阻塞/流量控制

**UDP模型：**

udp通信模型中，在通信开始之前，不需要建立相关的链接，只需要发送数据即可，类似于生活中，"写信""

![img](/Users/tanchao/Desktop/python/黑马/课件资料/基础班-就业班课件资料 -1-4位基础班  5-14就业班/05python和linux高级编程阶段/1-6课件/Python高级-全部（html版）/Images/06day/02-就业班-02-1.jpg)

**TCP模型：**

udp通信模型中，在通信开始之前，一定要先建立相关的链接，才能发送数据，类似于生活中，"打电话""

![img](/Users/tanchao/Desktop/python/黑马/课件资料/基础班-就业班课件资料 -1-4位基础班  5-14就业班/05python和linux高级编程阶段/1-6课件/Python高级-全部（html版）/Images/06day/02-就业班-02-12.png)

### 2.2 客户端与服务器端

#### 2.2.1 客户端

tcp的客户端要比服务器端简单很多，如果说服务器端是需要自己买手机、查手机卡、设置铃声、等待别人打电话流程的话，那么客户端就只需要找一个电话亭，拿起电话拨打即可，流程要少很多

```python
from socket import *

# 创建socket
tcp_client_socket = socket(AF_INET, SOCK_STREAM)

# 目的信息
server_ip = input("请输入服务器ip:")
server_port = int(input("请输入服务器port:"))

# 链接服务器
tcp_client_socket.connect((server_ip, server_port))

# 提示用户输入数据
send_data = input("请输入要发送的数据：")

tcp_client_socket.send(send_data.encode("gbk"))

# 接收对方发送过来的数据，最大接收1024个字节
recvData = tcp_client_socket.recv(1024)
print('接收到的数据为:', recvData.decode('gbk'))

# 关闭套接字
tcp_client_socket.close()
```

#### 2.2.2 服务器端

**生活中的电话机：**

如果想让别人能更够打通咱们的电话获取相应服务的话，需要做以下几件事情：

1. 买个手机
2. 插上手机卡
3. 设计手机为正常接听状态（即能够响铃）
4. 静静的等着别人拨打

**tcp服务器：**

如同上面的电话机过程一样，在程序中，如果想要完成一个tcp服务器的功能，需要的流程如下：

1. socket创建一个套接字
2. bind绑定ip和port
3. listen使套接字变为可以被动链接
4. accept等待客户端的链接
5. recv/send接收发送数据

```python
from socket import *

# 创建socket
tcp_server_socket = socket(AF_INET, SOCK_STREAM)

# 本地信息
address = ('', 7788)

# 绑定
tcp_server_socket.bind(address)

# 使用socket创建的套接字默认的属性是主动的，使用listen将其变为被动的，这样就可以接收别人的链接了
tcp_server_socket.listen(128)

# 如果有新的客户端来链接服务器，那么就产生一个新的套接字专门为这个客户端服务
# client_socket用来为这个客户端服务
# tcp_server_socket就可以省下来专门等待其他新客户端的链接
client_socket, clientAddr = tcp_server_socket.accept()

# 接收对方发送过来的数据
recv_data = client_socket.recv(1024)  # 接收1024个字节
print('接收到的数据为:', recv_data.decode('gbk'))

# 发送一些数据到客户端
client_socket.send("thank you !".encode('gbk'))

# 关闭为这个客户端服务的套接字，只要关闭了，就意味着为不能再为这个客户端服务了，如果还需要服务，只能再次重新连接
client_socket.close()
```

#### 2.2.3 注意点

1. tcp服务器一般情况下都需要绑定，否则客户端找不到这个服务器
2. tcp客户端一般不绑定，因为是主动链接服务器，所以只要确定好服务器的ip、port等信息就好，本地客户端可以随机
3. tcp服务器中通过listen可以将socket创建出来的主动套接字变为被动的，这是做tcp服务器时必须要做的
4. 当客户端需要链接服务器时，就需要使用connect进行链接，udp是不需要链接的而是直接发送，但是tcp必须先链接，只有链接成功才能通信
5. 当一个tcp客户端连接服务器时，服务器端会有1个新的套接字，这个套接字用来标记这个客户端，单独为这个客户端服务
6. listen后的套接字是被动套接字，用来接收新的客户端的链接请求的，而accept返回的新套接字是标记这个新客户端的
7. 关闭listen后的套接字意味着被动套接字关闭了，会导致新的客户端不能够链接服务器，但是之前已经链接成功的客户端正常通信。
8. 关闭accept返回的套接字意味着这个客户端已经服务完毕
9. 当客户端的套接字调用close后，服务器端会recv解堵塞，并且返回的长度为0，因此服务器可以通过返回数据的长度来区别客户端是否已经下线

### 2.3 tcp握手

#### 2.3.1 三次握手

![img](https://tva1.sinaimg.cn/large/006y8mN6ly1g90eebqz5zj30c406dmx2.jpg)

#### 2.3.2 四次握手

![img](https://tva1.sinaimg.cn/large/006y8mN6ly1g90ee640hcj30e10f1gq5.jpg)

