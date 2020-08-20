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

