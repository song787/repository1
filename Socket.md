## Socket网络编程

### socket的作用与组成

- 在网络传输中用于唯一标识两个端点（IP+Port）之间的连接；
- 组成：客户端地址、客户端端口，服务端地址、服务端端口；

### Socket相关技术

#### 1、阻塞与非阻塞





#### 2、I/O复用























## 常用函数

#### sockaddr_in

此数据结构用做bind、connect、recvfrom、sendto等函数的参数，指明地址信息。

```c++
//sockaddr_in（在netinet/in.h中定义）：
struct sockaddr_in{ 
//Address family一般来说AF_INET（地址族）PF_INET（协议族）  
short sin_family;
//Port number(必须要采用网络数据格式,普通数字可以用htons()函数转换成网络数据格式的数字)    
unsigned short sin_port;
//IP address in network byte order（Internet address）
struct in_addr sin_addr;
//Same size as struct sockaddr没有实际意义,只是为了　跟SOCKADDR结构在内存中对齐
unsigned char sin_zero[8];
};

//（在WinSock2.h中定义）：
struct sockaddr_in {
        short   sin_family;
        u_short sin_port;
        struct  in_addr sin_addr;
        char    sin_zero[8];
};
```

```c++
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

```c++
//inet_addr
in_addr_t inet_addr(const char* strptr);
/*
inet_addr方法可以转化字符串，主要用来将一个十进制的数转化为二进制的数，用途多于ipv4的IP转化。
返回：若字符串有效则将字符串转换为32位二进制网络字节序的IPV4地址，否则为INADDR_NONE
*/
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

#### strnacsecmp()

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

stdin是C语言中标准输入流，一般用于获取键盘输入到缓冲区里的东西。

作为fgets( )的stream参数可以实现读键盘输入到指定字符串的功能

