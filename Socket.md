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











### I/O复用

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

`epoll`的特点：

- epoll 只需要将描述符从进程缓冲区向内核缓冲区拷贝一次，并且进程不需要通过轮询来获得事件完成的描述符。
- epoll 仅适用于 Linux OS。
- epoll 比 select 和 poll 更加灵活而且没有描述符数量限制。
- epoll 对多线程编程更有友好，一个线程调用了 epoll_wait() 另一个线程关闭了同一个描述符也不会产生像 select 和 poll 的不确定情况。

epoll的工作模式：

ET模式：当 epoll_wait() 检测到描述符事件到达时，将此事件通知进程，进程可以不立即处理该事件，下次调用 epoll_wait() 会再次通知进程。是默认的一种模式，并且同时支持 Blocking 和 No-Blocking。

LT模式：和 LT 模式不同的是，通知之后进程必须立即处理事件，下次再调用 epoll_wait() 时不会再得到事件到达的通知。很大程度上减少了 epoll 事件被重复触发的次数，因此效率要比 LT 模式高。只支持 No-Blocking，以避免由于一个文件句柄的阻塞读/阻塞写操作把处理多个文件描述符的任务饿死。

```c++
Epoll 用法（三步曲）：
第一步：int epoll_create(int size)系统调用，创建一个epoll句柄，参数size用来告诉内核监听的数目，size为epoll支持的最大句柄数。
第二步:int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event)  事件注册函数参数 epfd为epoll的句柄。参数op 表示动作 三个宏来表示：EPOLL_CTL_ADD注册新fd到epfd 、EPOLL_CTL_MOD 修改已经注册的fd的监听事件、EPOLL_CTL_DEL从epfd句柄中删除fd。参数fd为需要监听的标识符。参数结构体epoll_event告诉内核需要监听的事件。
第三步：int epoll_wait(int epfd, struct epoll_event * events, int maxevents, int timeout) 等待事件的产生，通过调用收集在epoll监控中已经发生的事件。参数struct epoll_event 是事件队列 把就绪的事件放进去。

eg. 服务端使用epoll的时候步骤如下：
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



