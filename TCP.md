## TCP

### 基础概念

1. 序列号和确认号：

   > https://marvinsblog.net/post/2017-06-21-tcp-reliability-mechanism/

### 异常情况分析

1. 三次握手中的异常

   > https://jishuin.proginn.com/p/763bfbd625bd

2. 半连接队列和全连接队列满了怎么办

   > https://mp.weixin.qq.com/s?__biz=MzUxODAzNDg4NQ==&mid=2247484569&idx=1&sn=1ca4daeb8043a957850ab7a8f4f1120e&scene=21#wechat_redirect

#### 建立连接

1. 三次握手流程

   ![图片](https://mmbiz.qpic.cn/mmbiz_png/liaczD18OicSwYwHHricpl9GWhKZow0pLjnFUOoBJtAGeG4X18RSjyo5HHLqiatuzl9fZQlDP9TwTibV9E14S0ETdXw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

   

2. 异常情况分析

   - 客户端发送的`SYN`丢了

     ​	这种情况下会触发TCP的超时重传机制，客户端进行重传尝试（通常会进行三次重传，总的尝试时间约为76s，可以修改`tcp_syn_retries`设置最大重传次数）

   - 服务器端发送的`SYN+ACK`丢了

     - 此时**对于客户端而言**，等效于发送的`SYN`丢了，因此会重传`SYN`（同上）；
     - 对于服务器端而言，由于它没有等到`SYN+ACK`的`ACK`，也会进行重传，会在3s、6s、12s后进行重传尝试，可通过设置`tcp_synack_retries`设置服务器端的最大重传次数。如果达到最大重传次数后，**服务器端直接断开连接**（将连接从半连接队列中移除）。

   - 客户端对`SYN+ACK`的`ACK`丢了

     - 此时的客户端认为连接已经建立（客户端进入`ESTABLISHED`状态），开始发送数据包，如果说数据包成功到达服务器端，由于每个TCP报文段中带有`ACK`字段，服务器端还是会认为连接已经成功建立，忽略掉丢失的`ACK`。
     - 服务器端因为没有受到`ACK`，会进行重传（同上）

   - `SYN`攻击

   - 服务端进程崩了

   - 客户端进程崩了

#### 断开连接

1. 四次挥手

   <img src="https://mmbiz.qpic.cn/mmbiz_png/liaczD18OicSwYwHHricpl9GWhKZow0pLjn1eWwhqrePHnAtSjfLy79rbPO1GiaWGickQBTWI3ibsS0gicw5wrhvyvn8Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1" alt="图片" style="zoom:50%;" />

2. 为什么要有TIME_WAIT状态？
3. 为什么TIME_WAIT状态时间为2MSL？

### 重传机制

> why-TCP需要通过重传机制来保证其可靠性
>
> what-对丢失的数据包进行重传
>
> how-

- 超时重传

  发送端设置一个定时器，`RTO`时间内未收到应答则进行重传。

  **注意**，`RTO`是动态变化的，如果超时重传也发生了超时，则`RTO`变为原来`RTO`的两倍

- 快重传

  > 超时重传超时周期较长，因此引入快重传，在超时定时器到达之前进行重传

  连续收到3个同样的`ACK`就会触发快重传机制

  > 但是它没有解决重传`ACK`的一个还是重传`ACK`后的所有报文的问题，于是引出了SACK

- SACK

  由接收方将缓存的地图发送给发送方，这样发送方就知道重传哪些报文了

  <img src="https://i.loli.net/2021/10/25/WsEARlaPSX6zUo1.png" alt="image-20211025141033167" style="zoom:67%;" />

- DSACK(Duplicate ACK)

  使⽤了 SACK 来告诉「发送⽅」有哪些数据被重复接收了。  

  <img src="https://i.loli.net/2021/10/25/TtJXA3dGcgY9xe6.png" alt="image-20211025141651741" style="zoom:50%;" />

  好处：

  - 可以让「发送方」知道，是发出去的包丢了，还是接收⽅回应的 ACK 包丢了;  
  - 可以知道是不是「发送方」的数据包被网络延迟了;  
  - 可以知道网络中是不是把「发送方」的数据包给复制了;  

### 滑动窗口

> TCP协议规定，每次发送一个数据包，都要进行确认应答，应答完再发送下一个。
>
> 这种“同步的方式”导致数据传输效率较低，于是引入滑动窗口的概念，**发送数据后即使没收到确认，也可以继续发送**。

从系统的观点来看，滑动窗口是操作系统在内核空间中开辟的一段缓冲区，缓冲区保存着发送后还没收到确认的数据，一旦数据被确认，则其被清除。

- 发送方窗口

![image-20211026151203819](https://i.loli.net/2021/10/26/IyDfCznRl67LvuY.png)

- 接受方窗口

![image-20211026151657772](https://i.loli.net/2021/10/26/zCNU42fMIFlAhJP.png)

### 流量控制

> 发送方根据接收方发送过来的**接收窗口**大小来进行流量控制，其中就是使用滑动窗口的方式。

窗口关闭导致的死锁问题：

- 当收到接收方窗口为0时，发送方启动一个定时器，过期之后发送一个探测报文（超过最大尝试次数之后可能直接`RST`）

<img src="https://i.loli.net/2021/10/27/cCVEkyHhAmlvXbD.png" alt="image-20211027105306171" style="zoom:67%;" />



### 拥塞控制

> why-在网络出现拥堵时，如果继续发送大量的数据包会导致传输时延增加、丢包等现象，因此会导致TCP重传数据，更加增加网络的拥塞
>
> what-当网络情况发生变化时，调整自身**发送**的数据量
>
> how-定义一个拥塞窗口，根据网络情况调整该窗口

#### 关键点：

- 拥塞窗口`cwnd`变化情况：
  - 只要网络没发生拥塞，就增大

  - 网络发生拥塞则减小

  - 与发送窗口`swnd`和接收窗口`rwnd`之间的关系

    ```
    swnd = min(rwnd, cwnd)
    ```

- 如何判断发生拥塞：发生了重传

- 拥塞控制算法：

  - 慢启动

    TCP连接建立完成后，逐步增加发送的数据量（也就是增大拥塞窗口大小），到达慢启动门限后进入拥塞避免

  - 拥塞避免

    减缓拥塞窗口的增长速度

  - 拥塞发生

    - 发生超时重传：

      `ssthresh`变为`cwnd/2`，`cwnd`置为1，重新进入慢启动阶段

    - 发生快速重传：

      `ssthresh`变为`cwnd`，`cwnd`置为`cwnd/2`，进入快恢复阶段

  - 快速恢复

    快速重传和快速恢复算法⼀般同时使⽤，快速恢复算法是认为，你还能收到 3 个重复 ACK 说明网络也不那么糟
    糕，所以没有必要像 RTO 超时那么强烈。 

  ![image-20211027105821848](https://i.loli.net/2021/10/27/rRjU2T6bkf83JZs.png)
