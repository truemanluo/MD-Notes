### IO模型（重点说IO复用）

> Link: https://notes.shichao.io/unp/ch6/

#### IO模型分类

- 阻塞IO

![Figure 6.1. Blocking I/O model.](https://notes.shichao.io/unp/figure_6.1.png)

- 非阻塞IO

  ![Figure 6.2. Nonblocking I/O model.](https://notes.shichao.io/unp/figure_6.2.png)

- 异步IO：应用收到信号时，**数据已经从内核中拷贝到用户区**

  ![Figure 6.5. Asynchronous I/O model.](https://notes.shichao.io/unp/figure_6.5.png)

- IO复用：是一种同步IO模型，因为需要在读写事件就绪后自己进行读写操作

  ![Figure 6.3. I/O multiplexing model.](https://notes.shichao.io/unp/figure_6.3.png)

- 信号驱动IO：应用收到信号时，表示**可以开始从内核中拷贝数据到用户区**

  ![Figure 6.4. Signal-Driven I/O model.](https://notes.shichao.io/unp/figure_6.4.png)



#### IO模型对比

![Figure 6.6. Comparison of the five I/O models.](https://notes.shichao.io/unp/figure_6.6.png)

#### 同步、异步、阻塞、非阻塞

- 同步IO/异步IO：

  - POSIX对同步IO/异步IO的定义:
    - 同步IO：同步IO在IO操作完成之前**会造成请求进程的阻塞**
    - 异步IO：异步IO**不会造成请求进程的阻塞**

  **因此，阻塞IO、非阻塞IO、IO复用、信号驱动IO均属于同步IO，只有异步IO模型才符合异步IO的定义**

#### Linux系统中IO复用

- select

  > select让内核等待事件（读、写、异常）到达，然后唤醒进程。
  >
  > **注意：**select（包括poll、epoll）本身是阻塞的，只有关心的**事件发生、超时或被信号中断**才会返回

  select函数原型：

  ```c
  #include <sys/select.h>
  #include <sys/time.h>
  
  int select(int maxfdp1, fd_set *readset, fd_set *writeset, fd_set *exceptset,
             const struct timeval *timeout);
  /* Returns: positive count of ready descriptors, 0 on timeout, –1 on error */
  ```

  相关函数：

  ```c
  void FD_ZERO(fd_set *fdset);         /* clear all bits in fdset */
  void FD_SET(int fd, fd_set *fdset);  /* turn on the bit for fd in fdset */
  void FD_CLR(int fd, fd_set *fdset);  /* turn off the bit for fd in fdset */
  int FD_ISSET(int fd, fd_set *fdset); /* is the bit for fd on in fdset ? */
  ```

  > `select` modifies the descriptor sets pointed to by the *readset*, *writeset*, and *exceptset* pointers. These three arguments are value-result arguments. When we call the function, we specify the values of the descriptors that we are interested in, and on return, the result indicates which descriptors are ready. We use the `FD_ISSET` macro on return to test a specific descriptor in an `fd_set` structure. Any descriptor that is not ready on return will have its corresponding bit cleared in the descriptor set. To handle this, we turn on all the bits in which we are interested in all the descriptor sets each time we call select.
  >
  > 简言之，若传入```select```函数的`readset、writeset\exceptset`指针指向的描述符集合中的描述符上没有感兴趣的事件发生，则对应的位会被内核清除，因此每次调用select都要重新打开这些位

- poll

- epoll

#### `shutdown`函数

> It’s important to note that `shutdown()` doesn’t actually close the file descriptor—it just changes its usability. To free a socket descriptor, you need to use `close()`.
>
> 【1】`shutdown()`并不关闭描述符，只是修改它的可用性，实际关闭还是由`close`进行。
>
> 【2】如果有多个进程共享一个套接字，close每被调用一次，计数减1，直到计数为0时，也就是所用进程都调用了close，套接字将被释放。
>
> 【3】在多进程中如果一个进程中shutdown(sfd, SHUT_RDWR)后其它的进程将无法进行通信。如果一个进程close(sfd)将不会影响到其它进程。

- `close`的限制：
  - `close`递减描述符的引用计数并**只会在引用计数为0时关闭socket**
  - `close`关闭双向的连接

#### Reactor模式







