## 多进程编程













**关于多进程与多线程：**

进程有独立的地址空间，而同一进程中的线程则需要共享
开一个进程成本高于线程，优点是隔离度好
开一个线程的成本较低，但是需要程序员管控的东西也多，相互影响而出问题的机会也较大

对于频繁启动的、大量的执行单元，恐怕只能选择线程（纤程）模型

进程是OS管理的，进程和进程之间本来就是逻辑隔离的，只要OS不出问题，一个进程的错误一般不会影响到其它进程：好处，隔离得好；缺点，成本高，信息资源共享麻烦

线程是在进程中启动的执行单元，共享进程资源，有利于争夺CPU等分时资源，实现并发多任务...好处：性能优越，缺点，程序设计麻烦，这里的概念“线程安全”就是对于所有的资源使用冲突都解决得很好，并不容易的





## 进程

我们都知道计算机的核心是CPU，它承担了所有的计算任务；而操作系统是计算机的管理者，它负责任务的调度、资源的分配和管理，统领整个计算机硬件；应用程序侧是具有某种功能的程序，程序是运行于操作系统之上的。

进程是一个具有一定独立功能的程序在一个数据集上的一次动态执行的过程，是操作系统进行资源分配和调度的一个独立单位，是应用程序运行的载体。进程是一种抽象的概念，从来没有统一的标准定义。进程一般由程序、数据集合和进程控制块三部分组成。程序用于描述进程要完成的功能，是控制进程执行的指令集；数据集合是程序在执行时所需要的数据和工作区；程序控制块(Program Control Block，简称PCB)，包含进程的描述信息和控制信息，是进程存在的唯一标志。

进程具有的特征：

动态性：进程是程序的一次执行过程，是临时的，有生命期的，是动态产生，动态消亡的；

并发性：任何进程都可以同其他进程一起并发执行；

独立性：进程是系统进行资源分配和调度的一个独立单位；

结构性：进程由程序、数据和进程控制块三部分组成。

## **1. 多进程并发**

**优点：**

- 将应用程序分为多个、独立的、单纯和的进程，它们运行在同一时刻，就像你可以同时进行网页浏览和文字处理，这些独立的进程可以通过所有常规的进程间通信渠道互相传递信息（信号、套接字、文件、管道等）。
- 操作系统在进程间提供的附加保护操作和更高级别的通信机制，意味着可以比线程更容易地编写安全的并发代码。
- 使用独立的进程实现并发，可以在网络连接的不同的机器上运行独立的进程，虽然这增加了通信成本，但在一个精心设计的系统上，这可能是一个提高并行可用行和提高性能的低成本方法。

缺点：

- 这种进程之间的通信通常设置复杂，或是速度较慢，或两者兼备，因为操作系统通常在进程间提供了大量的保护，以避免一个进程不小心修改了属于另一个进程的数据。
- 运行多个进和所需的固有开销：启动进程需要时间，操作系统必须投入内部资源来管理进程。

## 线程

在早期的操作系统中并没有线程的概念，进程是能拥有资源和独立运行的最小单位，也是程序执行的最小单位。任务调度采用的是时间片轮转的抢占式调度方式，而进程是任务调度的最小单位，每个进程有各自独立的一块内存，使得各个进程之间内存地址相互隔离。

后来，随着计算机的发展，对CPU的要求越来越高，进程之间的切换开销较大，已经无法满足越来越复杂的程序的要求了。于是就发明了线程，线程是程序执行中一个单一的顺序控制流程，是程序执行流的最小单元，是处理器调度和分派的基本单位。一个进程可以有一个或多个线程，各个线程之间共享程序的内存空间(也就是所在进程的内存空间)。一个标准的线程由线程ID、当前指令指针(PC)、寄存器和堆栈组成。而进程由内存空间(代码、数据、进程空间、打开的文件)和一个或多个线程组成。

## **2. 多线程并发**

**优点：**

- 每个线程相互独立运行，且每个线程可以运行不同的指令序列。但进程中的所有的线程都共享相同的地址空间，并且从所有线程中访问大部分数据——全局变量仍然是全局的，指针、对象的引用或数据可以在线程之间传递。
- 共享的地址空间，以及缺少线程间的数据保护，使得使用多线程相关的开销远小于使用多进程，因为操作系统有更少的簿记要做。

**缺点：**

- 虽然通常可以在进程之间共享内存，但这难以建立并且通常难以管理，因为同一数据的内存地址在不同的进程中也不尽相同。
- 共享内存的灵活性是有代价的：如果数据要被多个线程访问，那么必须确保当每个线程访问时所看到的数据是一致的。



1.线程是程序执行的最小单位，而进程是操作系统分配资源的最小单位；

2.一个进程由一个或多个线程组成，线程是一个进程中代码的不同执行路线；

3.进程之间相互独立，但同一进程下的各个线程之间共享程序的内存空间(包括代码段、数据集、堆等)及一些进程级的资源(如打开文件和信号)，某进程内的线程在其它进程不可见；

4.调度和切换：线程上下文切换比进程上下文切换要快得多。



# 线程优先级

现在主流操作系统(如Windows、Linux、Mac OS X)的任务调度除了具有前面提到的时间片轮转的特点外，还有**优先级调度(Priority Schedule)**的特点。优先级调度决定了线程按照什么顺序轮流执行，在具有优先级调度的系统中，线程拥有各自的线程优先级(Thread Priority)。具有高优先级的线程会更早地执行，而低优先级的线程通常要等没有更高优先级的可执行线程时才会被执行。

线程的优先级可以由用户手动设置，此外系统也会根据不同情形调整优先级。通常情况下，频繁地进入等待状态(进入等待状态会放弃之前仍可占用的时间份额)的线程(如IO线程)，比频繁进行大量计算以至于每次都把所有时间片全部用尽的线程更受操作系统的欢迎。因为频繁进入等待的线程只会占用很少的时间，这样操作系统可以处理更多的任务。我们把频繁等待的线程称之为**IO密集型线程(IO Bound Thread)**，而把很少等待的线程称之为**CPU密集型线程(CPU Bound Thread)**。IO密集型线程总是比CPU密集型线程更容易得到优先级的提升。

## 线程饿死:

在优先级调度下，容易出现一种线程饿死的现象。一个**线程饿死**是说它的优先级较低，在它执行之前总是有比它优先级更高的线程等待执行，因此这个低优先级的线程始终得不到执行。当CPU密集型的线程优先级较高时，其它低优先级的线程就很可能出现饿死的情况；当IO密集型线程优先级较高时，其它线程相对不容易造成饿死的善，因为IO线程有大量的等待时间。为了避免线程饿死，调度系统通常会逐步提升那些等待了很久而得不到执行的线程的优先级。这样，一个线程只要它等待了足够长的时间，其优先级总会被提升到可以让它执行的程度，也就是说这种情况下线程始终会得到执行，只是时间的问题。

在优先级调度环境下，线程优先级的改变有三种方式：
\1. 用户指定优先级；
\2. 根据进入等待状态的频繁程度提升或降低优先级(由操作系统完成)；
\3. 长时间得不到执行而被提升优先级。

所谓**同步(synchronization)**就是指一个线程访问数据时，其它线程不得对同一个数据进行访问，即同一时刻只能有一个线程访问该数据，当这一线程访问结束时其它线程才能对这它进行访问。同步最常见的方式就是使用**锁(Lock)**，也称为线程锁。锁是一种非强制机制，每一个线程在访问数据或资源之前，首先试图**获取(Acquire)锁**，并在访问结束之后**释放(Release)锁**。在锁被占用时试图获取锁，线程会进入等待状态，直到锁被释放再次变为可用。



## 多线程编程



线程有自己独立的寄存器，在线程切换的时候会保护现场；

### 多线程编程思路

```c++
• 主线程生成工作线程
• 工作线程做具体工作。当工作线程工作完成后，它就自动结束了，也不会产生僵尸进程。
```

### **多线程相关模块**

```python
• thread和threading模块允许程序员创建和管理线程 
• thread模块提供了基本的线程和锁的支持，而threading提供了更高级别、功能更强的线程管理功能
• 推荐使用更高级别的threading模块
```

### 线程函数

#### CreateThread( )

```c++
//creatthread();
HANDLE CreateThread(
  LPSECURITY_ATTRIBUTES   lpThreadAttributes,//表示线程内核对象的安全属性，一般传入NULL表示使用默认设置。
  SIZE_T                  dwStackSize,//表示线程栈空间大小。传入0表示使用默认大小（1MB）。
  LPTHREAD_START_ROUTINE  lpStartAddress,//表示新线程所执行的线程函数地址，多个线程可以使用同一个函数地址。
  __drv_aliasesMem LPVOID lpParameter,//传给线程函数的参数。
  DWORD                   dwCreationFlags,//指定额外的标志来控制线程的创建，为0表示线程创建之后立即就可以进行调度，
    								//如果为CREATE_SUSPENDED则表示线程创建后暂停运行，这样它就无法调度，直到调用ResumeThread()。
  LPDWORD                 lpThreadId//将返回线程的ID号，传入NULL表示不需要返回该线程ID号。
);//函数返回值：成功返回新线程的句柄，失败返回NULL。 

//LPVOID是一个没有类型的指针，也就是说你可以将任意类型的指针赋值给LPVOID类型的变量（一般作为参数传递），然后在使用的时候在转换回来。
```

#### WaitForSingleObject( )

```c++
//WaitForSingleObject()
DWORD WaitForSingleObject(
  HANDLE hHandle,//要等待的内核对象。
  DWORD  dwMilliseconds //最长等待的时间，以毫秒为单位，如传入5000就表示5秒，传入0就立即返回，传入INFINITE表示无限等待。
);//函数返回值：在指定的时间内对象被触发，函数返回WAIT_OBJECT_0。超过最长等待时间对象仍未被触发返回WAIT_TIMEOUT。传入参数有错误将返回WAIT_FAILED
//因为线程的句柄在线程运行时是未触发的，线程结束运行，句柄处于触发状态。所以可以用WaitForSingleObject()来等待一个线程结束运行。
```



#### 读写锁

```c++
VOID InitializeSRWLock(PSRWLOCK SRWLock);//初始化（没有删除或销毁SRWLOCK的函数，系统会自动清理）
VOID AcquireSRWLockExclusive(PSRWLOCK SRWLock);//写入者线程申请写资源。
VOID ReleaseSRWLockExclusive(PSRWLOCK SRWLock);//写入者线程写资源完毕，释放对资源的占用。
VOID AcquireSRWLockShared(PSRWLOCK SRWLock);//读取者线程申请读资源。
VOID ReleaseSRWLockShared(PSRWLOCK SRWLock);//读取者线程结束读取资源，释放对资源的占用。
```

















### 小案例

```c++
//50个线程并发
#include<iostream>
#include<windows.h>

CRITICAL_SECTION m_sect;
int log_in_num;
const DWORD Thread_num = 50;
DWORD WINAPI Thread_func(LPVOID lpParameter){    
    Sleep(100);//使用了sleep是为了使得每个线程的执行之间有延迟，从而产生线程之间的干扰
    EnterCriticalSection(&m_sect);
    ++log_in_num;
    LeaveCriticalSection(&m_sect);
    Sleep(50); 
    return 0;
}

int main(){

    InitializeCriticalSection(&m_sect);
    int num = 20;
    while(num--){
        log_in_num = 0;
        HANDLE h_thread[Thread_num];
        for(int i = 0;i < Thread_num;++i){
            h_thread[i] = CreateThread(NULL,0,Thread_func,NULL,0,NULL);
            //WaitForSingleObject(h_thread[i],INFINITE);
        }
        WaitForMultipleObjects(Thread_num,h_thread,true,INFINITE);  //相对于waitforsingleobject（），multiple实现了并发，而single是串行；
        std::cout<<"有"<<Thread_num<<"个用户登陆，实际记录登陆结果为："<<log_in_num<<std::endl;
    }
    return 0;
}
```

```c++
//
#include<iostream>
#include<windows.h>

//1个生产者，2个消费者，4个缓冲区的例子
CRITICAL_SECTION m_sect;
HANDLE semaphore_buffEmpty,semaphore_buffFull;
int produce_num = 24;
const int buffer_size = 4;
int buffer[buffer_size];
int pro_i,con_i;
bool flag = true;

DWORD WINAPI ProduceThread(LPVOID lpP){

    for(int i = 0;i < produce_num;++i){
        WaitForSingleObject(semaphore_buffEmpty,INFINITE);

        EnterCriticalSection(&m_sect);
        buffer[pro_i] = i+1;      
        std::cout << "生产者向缓冲区（" << pro_i+1 << ")添加产品，产品编号为：" << buffer[pro_i] << std::endl;
        pro_i = (pro_i + 1) % buffer_size;
        LeaveCriticalSection(&m_sect);

        ReleaseSemaphore(semaphore_buffFull,1,NULL);
    }
    std::cout << "生产者顺利的完成了任务！" << std::endl;
    return 0;
}
DWORD WINAPI ConsummerThread(LPVOID lpP){

    while(true){
        WaitForSingleObject(semaphore_buffFull,INFINITE);

        EnterCriticalSection(&m_sect);
        if(flag == true)
            std::cout << "--->消费者编号为：" << GetCurrentThreadId() << "，消费了产品编号为：" << buffer[con_i] << "的产品！" << std::endl;
        if(buffer[con_i] == produce_num){
            if(flag == true)
                std::cout << "----> 所有产品均已消耗完毕！！" << std::endl;
            LeaveCriticalSection(&m_sect);
            ReleaseSemaphore(semaphore_buffFull,1,NULL);
            flag = false;
            break;
        }
        con_i = (con_i + 1) % buffer_size;
        
        LeaveCriticalSection(&m_sect); 
        Sleep(100);
        ReleaseSemaphore(semaphore_buffEmpty,1,NULL);
    }
    std::cout << "编号为：" << GetCurrentThreadId() << "的线程退出！" << std::endl;
    return 0;
}

int main(){

    InitializeCriticalSection(&m_sect);
    semaphore_buffFull = CreateSemaphore(NULL,0,4,NULL);
    semaphore_buffEmpty = CreateSemaphore(NULL,4,4,NULL);
    HANDLE Threads[3];
    Threads[0] = CreateThread(NULL,0,ProduceThread,NULL,0,NULL);
    Threads[1] = CreateThread(NULL,0,ConsummerThread,NULL,0,NULL);
    Threads[2] = CreateThread(NULL,0,ConsummerThread,NULL,0,NULL);
    pro_i = 0;
    con_i = 0;
    memset(buffer,0,sizeof(buffer));
    WaitForMultipleObjects(3,Threads,true,INFINITE);
    for(int i = 0;i < 3;++i)
        CloseHandle(Threads[i]);
    CloseHandle(semaphore_buffEmpty);
    CloseHandle(semaphore_buffFull);
    DeleteCriticalSection(&m_sect);
    std::cout << "baybay!~~" << std::endl;
    return 0;
}
```

```c++
//读者-写者问题
#include<iostream>
#include<windows.h>

using namespace std;

CRITICAL_SECTION m_sect1,m_sect2;
HANDLE hEventread,hEventwrite;
int reader_count;
const int Readerthreadcount = 5;

DWORD WINAPI thread_write(LPVOID lpP){

    EnterCriticalSection(&m_sect1);
    cout<<"写者在等待中…………"<<endl;
    LeaveCriticalSection(&m_sect1);
    WaitForSingleObject(hEventread,INFINITE);

    ResetEvent(hEventwrite);

    EnterCriticalSection(&m_sect2);
    cout<<"写者 正在 写 内容 ！" <<endl;
    Sleep(1000);
    cout<<"写着已经完成内容的修改！"<<endl;
    LeaveCriticalSection(&m_sect2);
    SetEvent(hEventwrite);

    return 0;
}
DWORD WINAPI thread_read(LPVOID lpP){

    EnterCriticalSection(&m_sect1);
    cout<<"编号为："<<GetCurrentThreadId()<<"的读者在等待中……………………"<<endl;
    LeaveCriticalSection(&m_sect1);

    WaitForSingleObject(hEventwrite,INFINITE);

    EnterCriticalSection(&m_sect2);
    reader_count++;
    if(reader_count == 1){
        ResetEvent(hEventread);
    }
    cout<<"读者编号为："<<GetCurrentThreadId()<<"的读者正在读内容中！！！"<<endl;
    Sleep(666 );
    cout<<"读者编号为："<<GetCurrentThreadId()<<"的读者 -退出- "<<endl;
    reader_count--;
    if(reader_count == 0){
        SetEvent(hEventread);
    }
    LeaveCriticalSection(&m_sect2);
    return 0;
}

int main(){

    reader_count = 0;
    InitializeCriticalSection(&m_sect2);
    InitializeCriticalSection(&m_sect1);

    hEventread = CreateEvent(NULL,false,true,NULL);
    hEventwrite = CreateEvent(NULL,true,true,NULL);

    HANDLE hThread[Readerthreadcount+1];
    int i = 0;
    for(i = 0;i < 2;i++){
        hThread[i] = CreateThread(NULL,0,thread_read,NULL,0,NULL);
    }

    hThread[2] = CreateThread(NULL,0,thread_write,NULL,0,NULL);
    Sleep(200);
    for(;i <= Readerthreadcount;++i){
        hThread[i] = CreateThread(NULL,0,thread_read,NULL,0,NULL);
    }
    WaitForMultipleObjects(Readerthreadcount+1,hThread,true,INFINITE);

    for(int i = 0;i <= Readerthreadcount;++i){
        CloseHandle(hThread[i]);
    }
    CloseHandle(hEventwrite);
    CloseHandle(hEventread);
    DeleteCriticalSection(&m_sect1);
    DeleteCriticalSection(&m_sect2);
    return 0;
}
```

```c++
//双线程读写队列数据
#include<iostream>
#include<windows.h>

using namespace std;

CRITICAL_SECTION m_sect;
HANDLE hSemaphoreFull,hSemaphoreEmpty;
int g_count_w,g_count_r;
const int buffer_size = 5;
int buffer[buffer_size];

DWORD WINAPI thread_write(LPVOID lpP){

    int i = 0;
    while(i < 20){

        WaitForSingleObject(hSemaphoreEmpty,INFINITE);
        
        buffer[g_count_w] = ++i;
        EnterCriticalSection(&m_sect);
        cout<<"写入数据："<<buffer[g_count_w]<<endl;
        LeaveCriticalSection(&m_sect);
        Sleep(200);
        
        g_count_w = (g_count_w + 1) % buffer_size;
        
        ReleaseSemaphore(hSemaphoreFull,1,NULL);
    }       
    return 0;
}
DWORD WINAPI thread_read(LPVOID lpP){

    int data = 0;
    while(data < 20){
        WaitForSingleObject(hSemaphoreFull,INFINITE);
        
        data = buffer[g_count_r];
        g_count_r = (g_count_r + 1) % buffer_size;
        EnterCriticalSection(&m_sect);
        cout<<"读取数据->"<<data<<endl;
        LeaveCriticalSection(&m_sect);
        Sleep(200);
        
        ReleaseSemaphore(hSemaphoreEmpty,1,NULL);
    }
    return 0;
}

int main(){

    g_count_w = 0;
    g_count_r = 0;
    InitializeCriticalSection(&m_sect);  
    hSemaphoreEmpty = CreateSemaphore(NULL,buffer_size,buffer_size,NULL);
    hSemaphoreFull = CreateSemaphore(NULL,0,buffer_size,NULL);

    HANDLE hThread[2];
    hThread[0] = CreateThread(NULL,0,thread_write,NULL,0,NULL);
    hThread[1] = CreateThread(NULL,0,thread_read,NULL,0,NULL);

    WaitForMultipleObjects(2,hThread,true,INFINITE);

    CloseHandle(hSemaphoreFull);
    CloseHandle(hSemaphoreEmpty);
    for(int i = 0;i < 2;i++){
        CloseHandle(hThread[i]);
    }
    DeleteCriticalSection(&m_sect);
    return 0;
}
```



1．线程（进程）同步的主要任务

答：在引入多线程后，由于线程执行的异步性，会给系统造成混乱，特别是在急用临界资源时，如多个线程急用同一台打印机，会使打印结果交织在一起，难于区分。当多个线程急用共享变量，表格，链表时，可能会导致数据处理出错，因此线程同步的主要任务是使并发执行的各线程之间能够有效的共享资源和相互合作，从而使程序的执行具有可再现性。

2．线程（进程）之间的制约关系？

当线程并发执行时，由于资源共享和线程协作，使用线程之间会存在以下两种制约关系。

（1）．间接相互制约。一个系统中的多个线程必然要共享某种系统资源，如共享CPU，共享I/O设备，所谓间接相互制约即源于这种资源共享，打印机就是最好的例子，线程A在使用打印机时，其它线程都要等待。

（2）．直接相互制约。这种制约主要是因为线程之间的合作，如有线程A将计算结果提供给线程B作进一步处理，那么线程B在线程A将数据送达之前都将处于阻塞状态。

间接相互制约可以称为**互斥**，直接相互制约可以称为**同步**，对于互斥可以这样理解，线程A和线程B互斥访问某个资源则它们之间就会产个顺序问题——要么线程A等待线程B操作完毕，要么线程B等待线程操作完毕，这其实就是线程的同步了。因此**同步包括互斥，互斥其实是一种特殊的同步**。