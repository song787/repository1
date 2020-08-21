## 多进程编程

















## 多线程编程





线程有自己独立的寄存器，在线程切换的时候会保护现场；



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



1．线程（进程）同步的主要任务

答：在引入多线程后，由于线程执行的异步性，会给系统造成混乱，特别是在急用临界资源时，如多个线程急用同一台打印机，会使打印结果交织在一起，难于区分。当多个线程急用共享变量，表格，链表时，可能会导致数据处理出错，因此线程同步的主要任务是使并发执行的各线程之间能够有效的共享资源和相互合作，从而使程序的执行具有可再现性。

2．线程（进程）之间的制约关系？

当线程并发执行时，由于资源共享和线程协作，使用线程之间会存在以下两种制约关系。

（1）．间接相互制约。一个系统中的多个线程必然要共享某种系统资源，如共享CPU，共享I/O设备，所谓间接相互制约即源于这种资源共享，打印机就是最好的例子，线程A在使用打印机时，其它线程都要等待。

（2）．直接相互制约。这种制约主要是因为线程之间的合作，如有线程A将计算结果提供给线程B作进一步处理，那么线程B在线程A将数据送达之前都将处于阻塞状态。

间接相互制约可以称为**互斥**，直接相互制约可以称为**同步**，对于互斥可以这样理解，线程A和线程B互斥访问某个资源则它们之间就会产个顺序问题——要么线程A等待线程B操作完毕，要么线程B等待线程操作完毕，这其实就是线程的同步了。因此**同步包括互斥，互斥其实是一种特殊的同步**。