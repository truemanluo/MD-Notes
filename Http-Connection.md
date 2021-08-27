### HTTP连接

> 学习目标：
>
> - HTTP是如何使用TCP连接的
> - TCP连接的时延、瓶颈以及存在的障碍
> - HTTP的优化，并行连接、长连接和管道化连接

#### HTTP与HTTPS

![image-20210823140538383](https://i.loli.net/2021/08/23/L3GvTjakNRu1t62.png)

#### HTTP报文的载体

![image-20210823140905330](https://i.loli.net/2021/08/23/4VKrGvzmfNo6Xsh.png)

#### Socket函数

![image-20210823141605460](https://i.loli.net/2021/08/23/kFwlBnzuDGfVrms.png)

- `bind`执行绑定操作，将`<local IP: PORT>`绑定到指定套接字上
- `listen`的主要作用是将套接字声明为**被动套接字**，可以接受连接请求

#### 影响HTTP时延的因素

- DNS解析
- 建立连接时的时延（由TCP决定）
- 请求报文的传输时延及服务器处理请求的时延
- 服务器回送数据的时延

#### TCP时延

- TCP连接建立握手

  TCP连接的开销大于数据交换的开销

- TCP慢启动**拥塞控制**

- 数据聚焦的Nagle算法

- 用于捎带确认的TCP延迟确认算法

- TIME_WAIT时延和端口耗尽

#### 持久连接

- HTTP/1.0 + keep-alive连接

  在`Connection`字段中指明，**但客户端和服务器并不一定会同意进行`keep-alive`会话**，它们可以在任意时刻关闭空闲的`keep-alive`连接

- `keep-alive`选项

  - `timeout`: 将保持连接的时长
  - `max`: 服务器还希望为多少个事务保持此连接的活跃状态

- `keep-alive`的限制和规则

- HTTP/1.1持久连接

  默认情况下是激活的，要想事务结束之后立即关闭连接，需要显示添加`Connection: close`首部

