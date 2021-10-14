### 进程间通信

- 管道：FIFO的队列，只能一端读取，一端写入

  > 实质上是位于**内核**中的一段缓存

  ```
  	---------------
  write(fd[1])	   read(fd[0])		
  	---------------
  ```

  - 匿名管道：通过`|`（系统调用`pipe`）创建，只能在具有亲缘关系的进程间进行通信

    EX.

    ```shell
    ps -aux | grep sh
    ```

    原理：父进程创建管道，然后`fork`出一个子进程，该子进程继承了父进程的文件描述符，因此可以在父子进程间通信。兄弟进程也是类似，它们可以通过父进程创建的匿名管道进行通信。

  - 命名管道：通过`mkfifo`命令创建，它是一个管道文件。值得注意的是：**当管道中的stream没有被读取的时候会一直阻塞**，直到其被其他进程读取。

  由于是**管道是阻塞式的通信，因此效率较低**。可以用消息队列将进程通信变成异步的形式。

- 消息队列：在**内核**中维护一个**消息队列链表**，每个节点是一个消息体，通信的进程间需要协商消息体的格式。

  消息的读取和写入是**异步**的，但是仍然存在内核空间与用户空间消息体的拷贝。可以用共享内存的方式来解决。

- 共享内存：

  > mmap详细介绍：https://www.cnblogs.com/huxiao-tee/p/4660352.html
  >
  > `mmap, munmap - map or unmap files or devices into memory`
  
  ```
  进程A													进程B
  |   	...			|						|					|
  |	mapping area	|-->	物理内存	  <-- |	  mapping area	  |
  |					|						|					|
  |					|						|					|
  ```
  
  可以通过以下方式**创建**/访问一段共享内存：
  
  ```
  #include <sys/ipc.h>
  #include <sys/shm.h>
  
  int shmget(key_t key, size_t size, int shmflg); // 返回memory segment的标识符
  ```
  
  共享内存被创建后还不能直接被访问，还需要将其**attach**到调用进程的地址空间中：
  
  ```
  void *shmat(int shmid, const void *shmaddr, int shmflg); // 如果shmaddr为NULL，则由OS决定映射的位置，否则将映射到调用进程的shmaddr
  ```
  
  不再使用共享内存后需要将进程中共享内存映射的地址与共享内存**分离**（detach）：
  
  ```
  int shmdt(const void *shmaddr);
  ```
  
  **同样，也可使用**`mmap`**将不同进程的地址空间映射到同一个文件中来实现共享内存**：
  
  ```
  进程A													进程B
  |   	...			|						|					|
  |	mapping area	|-->	磁盘文件	  <-- |	  mapping area	  |
  |					|						|					|
  |					|						|					|
  ```
  
- 信号

- 信号量

- socket通信

- 条件变量

  条件变量与互斥量一起使用，允许线程以无竞争的方式等待特定的条件发生