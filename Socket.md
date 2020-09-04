## Socket网络编程

### socket的作用与组成

- 在网络传输中用于唯一标识两个端点（IP+Port）之间的连接；
- 组成：客户端地址、客户端端口，服务端地址、服务端端口；

### socket建立服务器端与客户端的流程

```c++
//socket建立TCP连接-服务器端
/*
步骤：
1.socket()创建TCP套接字                                                                              
2.bind()将创建的套接字绑定到一个本地地址和端口上                                        
3.listen()，将套接字设为监听模式，准备接受客户请求                                        
4.accept()等用户请求到来时接受，返回一个对应此连接新套接字   
5.用accept()返回的套接字和客户端进行通信，recv()/send() 接受/发送信息。                               
6.返回，等待另一个客户请求。
7.关闭套接字
*/

//socket()创建TCP套接字   
int socket(int af, int type, int protocol);
/*
af：af为地址族，常用的是AF_INET代表IPv4，AF_INET6代表IPv6；  本机地址用127.0.0.1表示；
type：数据传输的方式；SOCK_STREAM（流格式套接字/面向连接的套接字）、SOCK_DGRAM（数据报套接字/无连接的套接字）；
protocol：表示传输协议，IPPROTO_TCP 表示TCP协议，IPPROTO_UDP表示UDP协议；
*/
SOCKET server = socket(AF_INET,SOCK_STREAM,IPPROTO_TCP);

//bind()将创建的套接字绑定到一个本地地址和端口上        
int bind(SOCKET s,const sockaddr *name,int namelen);
/*
s：传入一个被绑定的套接字；
name：一个指向套接字地址类型的指针；
namelen：
*/
sockaddr_in serveraddr;//创建套接字地址类型的变量； 
serveraddr.sin_family = AF_INET;//为变量填充内容
serveraddr.sin_port = htons(1234);
serveraddr.sin_addr.s_addr = inet_addr("192.168.1.102");
bind(server,(SOCKADDR *) &serveraddr,sizeof(serveraddr));//完成套接字与本地的绑定操作；

//listen()，将套接字设为监听模式，准备接受客户请求      
int listen(SOCKET s,int backlog);
/*
s:传入一个用来监听的套接字；
backlog：可以连接客户机的最大数，也就是内核监听队列的最大长度；
*/
listen(server,5);

//accept()等用户请求到来时接受，返回一个对应此连接新套接字  
SOCKET accept(SOCKET s, sockaddr* addr, int * addrlen);
/*
s:监听者的套接字；
addr：用来获取被接受连接的远端socket地址，客户机地址信息；
addrlen：指出上面addr地址的长度；
返回值为连接socket的文件描述符；
*/
sockaddr_in clientaddr;
int len = sizeof(clientaddr);
SOCKET client = accept(server,(SOCKADDR*) &clientaddr, &len);

//用accept()返回的套接字和客户端进行通信，recv()/send() 接受/发送信息。   
int recv(SOCKET s, char *buf, int len, int flags);
/*
s:客户机的socket；
buf：接收数据的缓冲区；
len：接收数据的长度；
flags：标志位；
*/
char recvdata[1024] = {0};
recv(client,recvdata,1023,0);    
```

```c++
//socket建立TCP连接-客户端
/*
1.socket()创建TCP套接字。
2.connect()建立到达服务器的连接。
3.与客户端进行通信，recv()/send()接受/发送信息，write()/read() 子进程写入管道，父进程从管道中读取信息然后send给客户端。
4. close() 关闭客户连接。
*/

//socket()创建TCP套接字。
SOCKET client = socket(AF_INET,SOCK_STREAM,IPPROTO_TCP);

//connect()建立到达服务器的连接。
int connect(SOCKET s,sockaddr *addr, int * addrlen);
SOCKADDR_IN serveraddr;
serveraddr.sin_family = AF_INET;//为变量填充内容
serveraddr.sin_port = htons(1234);
serveraddr.sin_addr.s_addr = inet_addr("192.168.1.102");
connect(client,(SOCKADDR*)& serveraddr,sizeof(serveraddr));

//与客户端进行通信，recv()/send()接受/发送信息，write()/read() 子进程写入管道，父进程从管道中读取信息然后send给客户端。
int send(SOCKET s, char *buf, int len, int flags);
char buf[1024];
send(client,buf,len,0);

//close() 关闭客户连接。
```

```c++
//UDP
/*
UDP通信的过程如图所示：
服务端：
（1）使用函数socket()，生成套接字文件描述符；
（2）通过struct sockaddr_in 结构设置服务器地址和监听端口；
（3）使用bind() 函数绑定监听端口，将套接字文件描述符和地址类型变量（struct sockaddr_in ）进行绑定；
（4）接收客户端的数据，使用recvfrom() 函数接收客户端的网络数据；
（5）向客户端发送数据，使用sendto() 函数向服务器主机发送数据；
（6）关闭套接字，使用close() 函数释放资源；
客户端：
（1）使用socket()，生成套接字文件描述符；
（2）通过struct sockaddr_in 结构设置服务器地址和监听端口；
（3）向服务器发送数据，sendto() ；
（4）接收服务器的数据，recvfrom() ；
（5）关闭套接字，close() ；
*/
//recvfrom替代recv函数，用于接收一个数据包，并且保存源地址；
int WSAAPI recvfrom{
    SOCKET s,//绑定到服务器端的socket；
    char *buf,
   	int len,
    int flags,
    sockaddr *from,
    int *fromlen
};//返回值为接收的字节数；
//sendto替代send函数；
```

## Socket相关技术

### socket地址解析

```c++
INT WSAAPI getaddrinfo(//ANSI码主机名到地址 与协议无关的转换；即ANSI主机名->对应的套接字
  PCSTR           pNodeName,
  PCSTR           pServiceName,
  const ADDRINFOA *pHints,
  PADDRINFOA      *ppResult
);
typedef struct addrinfo {
  int             ai_flags;
  int             ai_family;
  int             ai_socktype;
  int             ai_protocol;
  size_t          ai_addrlen;
  char            *ai_canonname;
  struct sockaddr *ai_addr;
  struct addrinfo *ai_next;
} ADDRINFOA, *PADDRINFOA;

INT WSAAPI getnameinfo(//由套接字来解析主机名；
  const SOCKADDR *pSockaddr,
  socklen_t      SockaddrLength,
  PCHAR          pNodeBuffer,
  DWORD          NodeBufferSize,
  PCHAR          pServiceBuffer,
  DWORD          ServiceBufferSize,
  INT            Flags
);
```

### socket 的同步与异步

越是 I/O 密集，越要让内核去处理 I/O 的问题，用户态尽可能不要在 I/O 上进行等待，所以从阻塞 I/O 到 multiplexing 到 edge trigger + non-blocking，都是在减轻用户态代码在 I/O 上等待的时间，而尽可能把时间交给内核。

回答题主问题，epoll 跟 select / poll 一样是 I/O multiplexing，用于在多个 socket 上等待读写事件。人们通常用 epoll 的 edge trigger 搭配 non-blocking
blocking / non-blocking 的差别在于你尝试读取一定量字符而 socket 暂时没有的时候，是直接返回错误还是会等在 read / recv 操作上。

Socket传输中拿TCP传输为例。假设服务器A 客户机B进行通信传输。首先需要在A机建立监听线程。监听某一端口，那么B机可以向A机发送通讯请求，B机连接到A机以后。A机可以从他的监听队列中取的一个监听对象。在A端拿到了这个Socket对象就可以进行接收跟发送数据了。这里问题就出现了。假如B端在请求A端的时候请求成功就发送一条数据。那么 A端就可以直接拿Socket对象得到他的信息。但是假如B端并没有在连接成功后直接发送信息而是在后来不确定的时间这内发送的信息。那么A端就无法得到这条信息。通常的做法是用一个定时器去不短的扫描这个数据缓存区。看是不是有数据存在这样效率非常低下。那么如何解决这个问题呢。就用到了我们的异步传输。异步传输的原理是。在A端得到这个SOCKET对象以后并不是直接去接收数据而是建立一个回调函数。回调函数是由系统维护的。他在指定的时间自动去扫描数据存储区。假如有数据他就把数据存储到指定的字节数组中。不用用户自己去关心。
那么同步与异步分别应用于什么情况呢？假如用户的SOCKET连接数据比较短暂的。一次连接直接发送数据的或客户端比较少的就使用同步假如用户的SOCKET属于长时间连接的就使用异步方式。

I/O 密集型进程所执行的 I/O 操作比执行的处理操作更多。CPU 密集型的进程所执行的处理操作比 I/O 操作更多。

> 同步阻塞IO

![image](http://images.cnblogs.com/cnblogs_com/zhuowei/WindowsLiveWriter/synchronousasynchronousblockingnonblocki_A122/image_thumb_1.png)



> 同步非阻塞IO

![image](http://images.cnblogs.com/cnblogs_com/zhuowei/WindowsLiveWriter/synchronousasynchronousblockingnonblocki_A122/image_thumb_2.png)

> 异步阻塞IO

**配置的是非阻塞** **I/O****，然后使用阻塞** **select** **系统调用来确定一个** **I/O** **描述符何时有操作**。

![image](http://images.cnblogs.com/cnblogs_com/zhuowei/WindowsLiveWriter/synchronousasynchronousblockingnonblocki_A122/image_thumb_3.png)

> 异步非阻塞IO

**读请求会立即返回，说明** **read** **请求已经成功发起了。在后台完成读操作时，应用程序然后会执行其他处理操作。当** **read** **的响应到达时，就会产生一个信号或执行一个基于线程的回调函数来完成这次** **I/O** **处理过程。**

![image](http://images.cnblogs.com/cnblogs_com/zhuowei/WindowsLiveWriter/synchronousasynchronousblockingnonblocki_A122/image_thumb_4.png)

### 阻塞与非阻塞



### 死循环+来连接时新建线程的方法效率有点低，怎么改进？

提前创建好一个线程池，用生产者消费者模型，创建一个任务队列，队列作为临界资源，有了新连接，就挂在到任务队列上，队列为空所有线程睡眠。







### I/O复用

IO复用函数本身是阻塞的，它们能够提高程序效率的原因是具有同时监听多个IO事件的能力；

从理论上来说，阻塞IO、IO复用、信号驱动IO都是同步IO模型，因为IO的读写操作，都是在IO事件发生之后，由应用程序来完成的；而异步IO的定义：用户可以直接对IO执行读写操作，这些操作告诉内核用户读写缓冲区的位置，以及IO操作完成之后内核通知应用程序的方式；异步的IO读写操作总是立即返回的，不论IO是否是阻塞，因为真正的读写操作已经由内核接管了。

同步IO要求用户代码自行执行IO操作，而异步IO是由内核来完成IO操作，

> IO模型中：同步异步区分的是内核向应用程序通知的是哪种IO事件（是就绪事件还是完成事件）；以及该由谁来完成IO读写（是应用程序还是内核）；
>
> 并发模式中：同步指的是程序完全按照代码序列的顺序执行，异步指的是程序的执行需要由系统事件（中断、信号）来驱动；

#### 1. select

```c++
int WSAAPI select(
  	int nfds,  //是一个整数值，是指集合中所有文件描述符的范围，即所有文件描述符的最大值加1，不能错！在Windows中这个参数值无所谓，可以设置不正确。
    fd_set *readfds, //这个集合中应该包括文件描述符，我们是要监视这些文件描述符的读变化的，即我们关心是否可以从这些文件中读取数据;
    fd_set *writefds, //这个集合中应该包括文件描述符，我们是要监视这些文件描述符的写变化的，即我们关心是否可以向这些文件中写入数据;
    fd_set *exceptfds, //用来监视文件错误异常;一般设置为NULL；
    struct timeval *timeout//timeout是select的超时时间，NULL则表示为阻塞状态，如果设置为0秒0毫秒则为非阻塞状态，大于0则为等待的时间；
);//返回值：大于0成功调用，小于0为出错，等于0为超时；

FD_ZERO(fd_set *fdset);//清空fdset与所有文件句柄的联系。      
FD_SET(int fd, fd_set *fdset);//建立文件句柄fd与fdset的联系。      
FD_CLR(int fd, fd_set *fdset);//清除文件句柄fd与fdset的联系。      
FD_ISSET(int fd, fd_set *fdset);//检查fdset联系的文件句柄fd是否可读写，>0表示可读写。
```

#### 2. poll

```c++
int poll(
    struct pollfd *fds, 
    unsigned int nfds, 
    int timeout
);
struct pollfd {
    int   fd;         /* file descriptor */
    short events;     /* requested events */
    short revents;    /* returned events */
};
```

#### 3. select与poll的比较

1、select 和 poll 的功能基本相同，不过在一些实现细节上有所不同。

- select 会修改描述符，而 poll 不会；
- select 的描述符类型使用数组实现，FD_SETSIZE 大小默认为 1024，因此默认只能监听少于 1024 个描述符。如果要监听更多描述符的话，需要修改 FD_SETSIZE 之后重新编译；而 poll 没有描述符数量的限制；poll
- poll 提供了更多的事件类型，并且对描述符的重复利用上比 select 高。
- 如果一个线程对某个描述符调用了 select 或者 poll，另一个线程关闭了该描述符，会导致调用结果不确定。

2、速度：select 和 poll 速度都比较慢，每次调用都需要将全部描述符从应用进程缓冲区复制到内核缓冲区。

3、可移植性：几乎所有的系统都支持 select，但是只有比较新的系统支持 poll。

#### 4. epoll

```c++
int epoll_create(int size);
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event)；//epoll_ctl() 用于向内核注册新的描述符或者是改变某个文件描述符的状态。
int epoll_wait(int epfd, struct epoll_event * events, int maxevents, int timeout);//已注册的描述符在内核中会被维护在一棵红黑树上，通过回调函数内核会将 I/O 准备好的描述符加入到一个链表中管理，进程调用 epoll_wait() 便可以得到事件完成的描述符。
```

\1. 调用epoll_create建立一个epoll对象(在epoll文件系统中给这个句柄分配资源)；创建了红黑树和就绪链表；

\2. 调用epoll_ctl向epoll对象中添加这100万个连接的套接字；如果增加socket句柄，则检查在红黑树中是否存在，存在立即返回，不存在则添加到树干上，然后向内核注册回调函数，用于当中断事件来临时向准备就绪链表中插入数据；

\3. 调用epoll_wait收集发生事件的连接。立刻返回准备就绪链表里的数据。

这样只需要在进程启动时建立1个epoll对象，并在需要的时候向它添加或删除连接就可以了，因此，在实际收集事件时，epoll_wait的效率就会非常高，因为调用epoll_wait时并没有向它传递这100万个连接，内核也不需要去遍历全部的连接。

当某一进程调用epoll_create方法时，Linux内核会创建一个eventpoll结构体，我们在**调用epoll_create**时，内核除了帮我们在epoll文件系统里建了个file结点，**在内核cache里建了个红黑树**用于存储以后epoll_ctl传来的socket外，**还会再建立一个rdllist双向链表，用于存储准备就绪的事件**，**当epoll_wait调用时，仅仅观察这个rdllist双向链表里有没有数据即可。有数据就返回**，没有数据就sleep，等到timeout时间到后即使链表没数据也返回。所以，epoll_wait非常高效。

```c++
struct eventpoll {
　　...
　　/*红黑树的根节点，这棵树中存储着所有添加到epoll中的事件，
　　也就是这个epoll监控的事件*/
　　struct rb_root rbr;
　　/*双向链表rdllist保存着将要通过epoll_wait返回给用户的、满足条件的事件*/
　　struct list_head rdllist;
　　...
};
```

所有添加到epoll中的事件都会与设备(如网卡)驱动程序**建立回调关系**，也就是说相应事件的发生时会调用这里的回调方法。这个回调方法在内核中叫做**ep_poll_callback**，它会把这样的事件放到上面的rdllist双向链表中。在epoll中对于每一个事件都会建立一个`epitem`结构体.

当调用epoll_wait检查是否有发生事件的连接时，只是检查eventpoll对象中的**rdllist双向链表是否有epitem元素**而已，如果rdllist链表不为空，则这里的事件复制到用户态内存（使用共享内存提高效率）中，同时将事件数量返回给用户。因此epoll_wait效率非常高。epoll_ctl在向epoll对象中添加、修改、删除事件时，从rbr红黑树中查找事件也非常快，也就是说epoll是非常高效的，它可以轻易地处理百万级别的并发连接。

`epoll`的特点：

- epoll 只需要将描述符从进程缓冲区向内核缓冲区拷贝一次，并且进程不需要通过轮询来获得事件完成的描述符。
- epoll 仅适用于 Linux OS。
- epoll 比 select 和 poll 更加灵活而且没有描述符数量限制。
- epoll 对多线程编程更有友好，一个线程调用了 epoll_wait() 另一个线程关闭了同一个描述符也不会产生像 select 和 poll 的不确定情况。
- epoll使用“事件”的就绪通知方式，通过epoll_ctl注册fd，一旦该fd就绪，内核就会采用类似callback的回调机制来激活该fd，epoll_wait便可以收到通知。

epoll的工作模式：

LT模式：当 epoll_wait() 检测到描述符事件到达时，将此事件通知进程，进程可以不立即处理该事件，下次调用 epoll_wait() 会再次通知进程。是默认的一种模式，并且同时支持 Blocking 和 No-Blocking。

ET模式：和 LT 模式不同的是，通知之后进程必须立即处理事件，下次再调用 epoll_wait() 时不会再得到事件到达的通知。很大程度上减少了 epoll 事件被重复触发的次数，因此效率要比 LT 模式高。只支持 No-Blocking，以避免由于一个文件句柄的阻塞读/阻塞写操作把处理多个文件描述符的任务饿死。

>  为什么会有ET模式

系统中一旦有大量你不需要读写的就绪文件描述符，它们每次调用epoll_wait都会返回，这样会大大降低处理程序检索自己关心的就绪文件描述符的效率.。而采用EPOLLET这种边缘触发模式的话，当被监控的文件描述符上有可读写事件发生时，epoll_wait()会通知处理程序去读写。如果这次没有把数据全部读写完(如读写缓冲区太小)，那么下次调用epoll_wait()时，它不会通知你，也就是它只会通知你一次，直到该文件描述符上出现第二次可读写事件才会通知你！这种模式比水平触发效率高，系统不会充斥大量你不关心的就绪文件描述符。

```c++
Epoll 用法（三步曲）：
int epoll_create(int size)//系统调用，创建一个epoll句柄，参数size用来告诉内核监听的数目，size为epoll支持的最大句柄数。
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event)  //事件注册函数参数 epfd为epoll的句柄。参数op 表示动作 三个宏来表示：EPOLL_CTL_ADD注册新fd到epfd 、EPOLL_CTL_MOD 修改已经注册的fd的监听事件、EPOLL_CTL_DEL从epfd句柄中删除fd。参数fd为需要监听的标识符。参数结构体epoll_event告诉内核需要监听的事件。
int epoll_wait(int epfd, struct epoll_event * events, int maxevents, int timeout) //等待事件的产生，通过调用收集在epoll监控中已经发生的事件。参数struct epoll_event 是事件队列 把就绪的事件放进去。

服务端使用epoll的时候步骤如下：
1.调用epoll_create()在linux内核中创建一个事件表。
2.然后将文件描述符(监听套接字listener)添加到事件表中
3.在主循环中，调用epoll_wait()等待返回就绪的文件描述符集合。
4.分别处理就绪的事件集合，本项目中一共有两类事件：新用户连接事件和用户发来消息事件。
```

#### 5. 三种IO复用的应用场景

##### 1. select 应用场景

select 的 timeout 参数精度为微秒，而 poll 和 epoll 为毫秒，因此 select 更加适用于实时性要求比较高的场景，比如核反应堆的控制。

select 可移植性更好，几乎被所有主流平台所支持。

##### 2. poll 应用场景

poll 没有最大描述符数量的限制，如果平台支持并且对实时性要求不高，应该使用 poll 而不是 select。

##### 3. epoll 应用场景

只需要运行在 Linux 平台上，有大量的描述符需要同时轮询，并且这些连接最好是长连接。

需要同时监控小于 1000 个描述符，就没有必要使用 epoll，因为这个应用场景下并不能体现 epoll 的优势。

需要监控的描述符状态变化多，而且都是非常短暂的，也没有必要使用 epoll。因为 epoll 中的所有描述符都存储在内核中，造成每次需要对描述符的状态改变都需要通过 epoll_ctl() 进行系统调用，频繁系统调用降低效率。并且 epoll 的描述符存储在内核，不容易调试。

### IO多路复用与多进程

如果压力不是很大，并且处理性能相对于IO可以忽略不计

- IO多路复用+单进（线）程比较省资源
- 适合处理大量的闲置的IO
- IO多路复用+多单进（线）程与线程池方案相比有好处，但是并不会有太大的优势

如果压力很大，什么方案都得跪，这时就得扩容。当然因为IO多路复用+单进（线）程比较省资源，所以扩容时能省钱。

### 两种高效的事件处理模式

处理IO事件、信号和定时事件；

#### Reactor模式-同步IO实现

要求主线程（IO处理单元）只负责监听文件描述上是否有事件发生，有就立即将该事件通知工作线程（逻辑单元），除此之外，主线程不做任何其他实质性的工作，读写数据，接受新的连接，以及处理客户请求均在工作线程中完成；

#### Proactor模式-异步IO实现

Proactor模式将所有IO操作都交给主线程和内核来处理，工作线程仅仅负责业务逻辑，所以Proactor模式更符合基本的服务器编程框架； 

### 两种高效的并发模式



#### 半同步/半异步模式

> 并发模式中：同步指的是程序完全按照代码序列的顺序执行，异步指的是程序的执行需要由系统事件（中断、信号）来驱动；
>
> 同步线程：效率低，实时性差，但是逻辑简单；
>
> 异步线程：效率高，实时性强，但是编写程序相对复杂，难于调试和扩展，不适合大量的并发；

该模式中，同步线程用于处理客户逻辑，异步线程用于处理IO事件，异步线程监听到客户请求后，将其封装成请求对象并插入请求队列中，请求队列将通知（RR、条件变量、信号量）某个工作在同步模式的工作线程来读取并处理该请求对象；

->半同步/半反应堆模式



->高效的半同步/半异步模式



#### 领导者/追随者模式

领导/追随者模式是多个工作线程轮流获得事件源集合，轮流监听、分发并处理事件的一种模式。在任意时间点，程序都仅有一个领导者线程，它负责监听IO事件，而其他线程则都是追随者，它们休眠在线程池中等待成为新的领导者，当前的领导者如果检测到IO事件，首先要从线程池中推选出新的领导者线程，然后处理IO事件，此时新的领导者等待新的IO事件，而原来的领导者则处理IO事件，二者实现了并发；

该模式包含如下组件：句柄集（HandleSet）、线程集（ThreadSet）、事件处理器（EventHandler）、具体的事件处理器（ConcreteEventHandler）；

### 有限状态机

 逻辑单元（工作线程）内部的一种高效编程的方法；

可以设置开始状态，结束状态以及一些中间过程的状态，





### 池

#### 内存池

平常我们使用new、malloc在堆区申请一块内存，但由于每次申请的内存大小不一样就会产生很多内存碎片，造成不好管理与浪费的情况。内存池则是在真正使用内存之前，先申请分配一定数量的、大小相等(一般情况下)的内存块留作备用。当有新的内存需求时，就从内存池中分出一部分内存块，若内存块不够再继续申请新的内存。这样做的一个显著优点是尽量避免了内存碎片，使得内存分配效率得到提升。

#### 进程池、线程池

这两个问题有一定的相似度，在面向对象程序编程中，对象的创建与析构都是一个较为复杂的过程，较费时间，所以为了提高程序的运行效率尽可能减少创建和销毁对象的次数，特别是一些很耗资源的对象创建和销毁。所以我们可以创建一个进程池（线程池）,预先放一些进程（线程）进去,要用的时候就直接调用,用完之后再把进程归还给进程池,省下创建删除进程的时间,不过当然就需要额外的开销了。利用线程池与进程池可以使管理进程与线程的工作交给系统管理，不需要程序员对里面的线程、进程进行管理。

##### **线程池主要用于**

1、需要大量的线程来完成任务，且完成任务的时间比较短。 WEB服务器完成网页请求这样的任务，使用线程池技术是非常合适的。因为单个任务小，而任务数量巨大，你可以想象一个热门网站的点击次数。但对于长时间的任务，比如一个Telnet连接请求，线程池的优点就不明显了。因为Telnet会话时间比线程的创建时间大多了。

2、对性能要求苛刻的应用，比如要求服务器迅速响应客户请求。

3、接受突发性的大量请求，但不至于使服务器因此产生大量线程的应用。突发性大量客户请求，在没有线程池情况下，将产生大量线程，虽然理论上大部分操作系统线程数目最大值不是问题，短时间内产生大量线程可能使内存到达极限，并出现"OutOfMemory"的错误。

线程池能够减少创建的线程个数。通常线程池所允许的并发线程是有上界的，如果同时需要并发的线程数超过上界，那么一部分线程将会等待。而传统方案中，如果同时请求数目为2000，那么最坏情况下，系统可能需要产生2000个线程。尽管这不是一个很大的数目，但是也有部分机器可能达不到这种要求。因此线程池的出现正是着眼于减少线程本身带来的开销。采用预创建的技术，在应用程序启动之后，将立即创建一定数量的线程(N1)，放入空闲队列中。这些线程都是处于阻塞（Suspended）状态，不消耗CPU，但占用较小的内存空间。当任务到来后，缓冲池选择一个空闲线程，把任务传入此线程中运行。当N1个线程都在处理任务后，缓冲池自动创建一定数量的新线程，用于处理更多的任务。在任务执行完毕后线程也不退出，而是继续保持在池中等待下一次的任务。当系统比较空闲时，大部分线程都一直处于暂停状态，线程池自动销毁一部分线程，回收系统资源。基于这种预创建技术，线程池将线程创建和销毁本身所带来的开销分摊到了各个具体的任务上，执行次数越多，每个任务所分担到的线程本身开销则越小，不过我们另外可能需要考虑线程之间同步所带来的开销

















## 常用函数

#### sockaddr_in 

此数据结构用做bind、connect、recvfrom、sendto等函数的参数，指明地址信息,是socket的地址数据类型。

```c++
//sockaddr_in（在netinet/in.h中定义）：
struct sockaddr_in{ 
short sin_family; //地址族AF_INET（IPV4）PF_INET（IPV4）   
unsigned short sin_port;//Port number(必须要采用网络数据格式,普通数字可以用htons()函数转换成网络数据格式的数字)   
struct in_addr sin_addr; //用inet_addr("");
unsigned char sin_zero[8];//Same size as struct sockaddr没有实际意义,只是为了跟SOCKADDR结构在内存中对齐
};

//（在WinSock2.h中定义）：
struct sockaddr_in {
        short   sin_family;
        u_short sin_port;
        struct  in_addr sin_addr;
        char    sin_zero[8];
};

//inet_addr(arpa/inet.h)
in_addr_t inet_addr(const char* strptr);
/*
inet_addr方法可以转化字符串，主要用来将一个十进制的数转化为二进制的数，用途多于ipv4的IP转化。
返回：若字符串有效则将字符串转换为32位二进制网络字节序的IPV4地址，否则为INADDR_NONE
*/

//in_addr结构
//linux下：
typedef uint32_t in_addr_t;
struct in_addr
  {
    in_addr_t s_addr;
  };

//windows下：
typedef struct in_addr
{
    union{
            struct { unsigned char s_b1,s_b2,s_b3,s_b4; } S_un_b;
            struct { unsigned short s_w1,s_w2; } S_un_w;
            unsigned long S_addr;
    }S_un;
}in_addr;
```

#### bzero（）

```c++
extern void bzero（void *s, int n）;
/*
参数说明：s 要置零的数据的起始地址； n 要置零的数据字节个数。
说明：bzero无返回值，并且使用string.h头文件，string.h曾经是posix标准的一部分，但是在POSIX.1-2001标准里面，这些函数被标记为了遗留函数而不推荐使用。在POSIX.1-2008标准里已经没有这些函数了。推荐使用memset替代bzero。
*/
```

#### memset()

```c++
void *memset(void *s, int ch, size_t n);
/*
函数解释：将s中当前位置后面的n个字节 （#typedef unsigned int size_t ）用 ch 替换并返回 s 。
memset：作用是在一段内存块中填充某个给定的值，它是对较大的结构体或数组进行清零操作的一种最快方法。
memset()函数原型是extern void *memset(void *buffer, int c, int count) buffer：为指针或是数组,c：是赋给buffer的值,count：是buffer的长度.
*/
```

#### memcpy()

```c++
void *memcpy(void *str1, const void *str2, size_t n);
/*
C 库函数 void *memcpy(void *str1, const void *str2, size_t n) 从存储区 str2 复制 n 个字节到存储区 str1。   
str1 -- 指向用于存储复制内容的目标数组，类型强制转换为 void* 指针。
str2 -- 指向要复制的数据源，类型强制转换为 void* 指针。
 n   -- 要被复制的字节数。    
该函数返回一个指向目标存储区 str1 的指针。
*/
```

#### sprintf()

```c++
int sprintf(char *str, const char *format, ...) ;
/*
发送格式化输出到 str 所指向的字符串,
str -- 这是指向一个字符数组的指针，该数组存储了 C 字符串。
format -- 这是字符串，包含了要被写入到字符串 str 的文本。它可以包含嵌入的 format 标签，format 标签可被随后的附加参数中指定的值替换，并按需求进行格式化。format 标签属性是 %[flags][width][.precision][length]specifier
*/
```

#### strnacsecmp( )

```c++
int strncasecmp(const char *s1, const char *s2, size_t n);
/*
strncasecmp()用来比较参数s1 和s2 字符串前n个字符，比较时会自动忽略大小写的差异。
若参数s1 和s2 字符串相同则返回0。s1 若大于s2 则返回大于0 的值，s1 若小于s2 则返回小于0 的值。
*/
```

#### fgets()

```c++
char *fgets(char *str, int n, FILE *stream);
/*
从指定的流 stream 读取一行，并把它存储在 str 所指向的字符串内。当读取 (n-1) 个字符时，或者读取到换行符时，或者到达文件末尾时，它会停止，具体视情况而定。
str -- 这是指向一个字符数组的指针，该数组存储了要读取的字符串。
n -- 这是要读取的最大字符数（包括最后的空字符）。通常是使用以 str 传递的数组长度。
stream -- 这是指向 FILE 对象的指针，该 FILE 对象标识了要从中读取字符的流。
如果成功，该函数返回相同的 str 参数。如果到达文件末尾或者没有读取到任何字符，str 的内容保持不变，并返回一个空指针。
如果发生错误，返回一个空指针。
*/
```

#### stdin

```c++
//stdin是C语言中标准输入流，一般用于获取键盘输入到缓冲区里的东西。
//作为fgets( )的stream参数可以实现读键盘输入到指定字符串的功能
```



