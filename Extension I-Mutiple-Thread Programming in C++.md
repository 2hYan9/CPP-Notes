### Extension I: Mutiple-Thread Programming in C++
Let's talk about the concept of mutiple thread.  

>A thread is a path of execution within a process. A process can contain multiple threads.  
>A thread is also known as lightweight process. The idea is to achieve parallelism by dividing a process into multiple threads. For example, in a browser, multiple tabs can be different threads. MS Word uses multiple threads: one thread to format the text, another thread to process inputs, etc.  
>From [Geeksforgeeks.org](https://www.geeksforgeeks.org/thread-in-operating-system/)

<span id="create_thread"></span>
#### Create mutiple thread
The following instance shows how to create mutiple thread in a process using the "pthread.h" library:  
```c++
#include <iostream>
#include <pthread.h>

using namespace std;

#define NUM_THREADS 5

void * PrintHello(void * threadid){
    cout << "Hello World!" << endl;
    pthread_exit(NULL);
}

int main(){
    pthread_t threads[NUM_THREADS];

    for(int i = 0; i < NUM_THREADS; i++){
        int ret = pthread_create(&threads[i], NULL, PrintHello, NULL);
        if(ret != 0)
            cout << "Pthread_create error : error code = " << ret << endl;
    }

    pthread_exit(NULL);
}
```
compile with -lpthread  
and the runing result is like this:  
```
Hello World!Hello World!

Hello World!Hello World!Hello World!



```
it will get different result for different running.  

The function `pthread_create` is used to create threads, and the prototype is as followed:  
```c++
int pthread_create(pthread_t *restrict thread,
                          const pthread_attr_t *restrict attr,
                          void *(*start_routine)(void *),
                          void *restrict arg);
```
> the key word "restrict" is a qualifier used to optimize the process of compiling.  
  
- `pthread_t * restrict thread`: the identifier of the current thread
- `const pthread_attr_t * restrict attr`: points to a pthread_attr_t structure whose contents are used at thread creation time to determine attributes for the new thread;  
- `void *(*start_routine)(void *)` and `void * restrict arg`: The thread is created executing start_routine with arg as its sole argument.  

If the start_routine returns, the effect shall be as if there was an implicit call to pthread_exit() using the return value of start_routine as the exit status.  

And the return value of `pthread_create()` is zero if the process create a thread successfully. Otherwise, it will return the coresponding error code.  

#### Pass arguments to thread
Process can pass some arguments to thread using the `void * arg` of the function `pthread_create`  
The following instance shows a process how to pass arguments to thread:  
```c++
#include <iostream>
#include <pthread.h>
#include <cstring>
#include <cstdlib>

#define NUM 3

using namespace std;

struct ThreadData{
    int threadID;
    char string[60];
};

void * printMessage(void * threadarg){
    ThreadData * args = (ThreadData *)threadarg;
    cout << "Thread ID is: " << args -> threadID << endl;
    cout << "Message is: " << args -> string << endl;
    delete args;
    return 0;
}

int main(){
    int ret;
    pthread_t thread[NUM];
    for(int i = 0; i < NUM; i++){
        ThreadData * data = new ThreadData;
        data -> threadID = i;
        strcpy(data -> string, "This is message.");
        ret = pthread_create(&thread[i], NULL, printMessage, data);
        if(ret){
            cout << "Failed to create thread.\n";
            exit(-1);
        }
    }
    pthread_exit(NULL);
    return 0;
}
```

and the output is as followed:  
```
Thread ID is: Thread ID is: 1Thread ID is: 02


Message is: Message is: Message is: This is message.This is message.This is message.

```
As the same as the last instance, it will gets different output for different runing.  
#### Join&Detach the threads

Join a thread means block the process for the thread to terminate.  
Based on the instance of passing argument to a thread, here is an example of joining a thread:  
```c++
#include <iostream>
#include <pthread.h>
#include <cstring>
#include <cstdlib>
#include <unistd.h>

#define NUM 3

using namespace std;

struct ThreadData{
    int threadID;
    char string[60];
};

void * printMessage(void * threadarg){
    ThreadData * args = (ThreadData *)threadarg;
    cout << "Thread ID is: " << args -> threadID << endl;
    printf("The message is: %s\n", args -> string);
    delete args;
    return 0;
}

int main(){
    int ret, rc;
    pthread_t thread[NUM];
    pthread_attr_t attr;
    void *status;

    //initialize the thread as joinable
    pthread_attr_init(&attr);
    pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_JOINABLE);

    for(int i = 0; i < NUM; i++){
        ThreadData * data = new ThreadData;
        data -> threadID = i;
        strcpy(data -> string, "This is message.");
        ret = pthread_create(&thread[i], NULL, printMessage, data);
        if(ret){
            cout << "Failed to create thread.\n";
            exit(-1);
        }
        rc = pthread_join(thread[i], &status);
        if(rc){
            cout << "Failed to join" << endl;
            exit(-1);
        }
    }

    //Clear the attribution
    pthread_attr_destroy(&attr);
    
    pthread_exit(NULL);
    return 0;
}
```
and the output is  
```
Thread ID is: 0
The message is: This is message.
Thread ID is: 1
The message is: This is message.
Thread ID is: 2
The message is: This is message.

```
It's clearly to tell the difference with the output of the last instance.  
To join a thread, the listed method are required:  
- `int pthread_join(pthread_t thread, void **retval)`: This method waits for the thread specified by thread to terminate. If that thread has already terminated, then pthread_join() returns immediately. The thread specified by thread must be joinable. This method will returns 0 on success. Otherwise, it will returns the coresponding error code.
- `int pthread_attr_init(pthread_attr_t *attr)` and `int pthread_attr_destroy(pthread_attr_t *attr)`: The pthread_attr_init() method initializes the thread attributes object pointed by the attr. After this call, indivadual attributes of the object can be set using various related functions and then the object can be used in one or more pthread_create calls that create threads. When a thread attributes object is no longer required, it should be destroyed using the pthread_attr_destroy() function. Destroying a thread attributes object has no effect on threads that were created using that object.
- `int pthread_attr_setdetachstate(const pthread_attr_t *attr, int *detachstate)`: This method sets the detach state attribute of the thread attributes object referred to by attr to the value specified in detachstate. The detach state attribute determine whether a thread created using the thread attributes object attr will be created in a joinable(PTHREAD_CREATED_JOINABLE) a detachd(PTHREAD_CREATED_DETACH) state. 

Detach a thread means that the thread of excution is "detached" from the thread object an is no longer represented by a thread object. The C++ thread object can be destroyed and the OS thread of excution can continue on. If the program needs to know when that thread of excution has completed, some other mechanism needs to be used. The pthread_join() method can not be called on that thread object anymore, since it is no longer associated with a thread of excution.  

To detach a thread, the pthread_detach() should be called on:  
`int pthread_detach(pthread_t thread)`: This method marks the thread identified by thread as detached. When a detached thread terminates, its resources are automatically released back to the system without the need for another thread to join with the terminated thread. It will returns 0 on success. Otherwise, it returns the corresponding error code.

<span id="thread_class"></span>
#### Mutilple thread in C++ Classes
C++ includes built-in support for threads, which defined in the header \<thread\>.  
在这个头文件中，包含有`thread`类，`jthread`类以及`thread::id`类，而这里主要讨论`thread`类。  
thread类表示一个正在执行中的线程，而多线程能够允许程序能够同时执行多个函数。  
每当创建一个`thread`类的对象后，与这个`thread`类相关联的线程就立即开始执行，而创建一个`thread`类的对象时需要提供一个函数作为参数，而这个函数就是与这个类相关联的线程需要执行的函数。当这个函数返回时或者抛出一个异常时，将会调用`std::terminate`。这个函数可以通过`std::promise`方法修改共享变量来将自己的返回值或者异常传递给创建这个线程的父线程，而这些内容可以或涉及到同步和互斥。  
正如前面提到过的一样，`std::thread`类的对象可能会处于一种不代表任何线程的状态（一般是以下情况：默认构造器构造，move from，detach或者join），而一个正在执行中的线程可能也并不会与一个`std::thread`类的对象相关联。  
需要注意的是，不可能存在两个不同的`std::thread`类的对象会与同一个线程相关联，这是因为`std::thread`类是不可复制构造或者复制赋值的。  
Synopsis of `thread` class：
```c++
class thread {
    public:
    // types
    class id;
    using native_handle_type = /* implementation-defined */;
 
    // construct/copy/destroy
    thread() noexcept;
    template<class F, class... Args> explicit thread(F&& f, Args&&... args);
    ~thread();
    thread(const thread&) = delete;
    thread(thread&&) noexcept;
    thread& operator=(const thread&) = delete;
    thread& operator=(thread&&) noexcept;
 
    // members
    void swap(thread&) noexcept;
    bool joinable() const noexcept;
    void join();
    void detach();
    id get_id() const noexcept;
    native_handle_type native_handle();
 
    // static members
    static unsigned int hardware_concurrency() noexcept;
  };
```
下面的这个实例演示了如何通过`std::thread`类实现多线程开发：
```c++
#include <iostream>
#include <utility>
#include <thread>
#include <chrono>

void f1(int n){
    for(int i = 0; i < 5; i++){
        std::cout << "Thread 1 execution." << std::endl;
        n++;
        std::this_thread::sleep_for(std::chrono::milliseconds(10));
    }
}

void f2(int &n){
    for(int i = 0; i < 5; i++){
        std::cout << "Thread 2 execution." << std::endl;
        n++;
        std::this_thread::sleep_for(std::chrono::milliseconds(10));
    }
}

class foo{
    public:
    void bar(){
        for(int i = 0; i < 5; i++){
            std::cout << "Thread 3 execution." << std::endl;
            n++;
            std::this_thread::sleep_for(std::chrono::milliseconds(10));
        }
    }
    int n = 0;
};

int main(){
    int n = 0;
    foo f;
    std::thread t1;                     //t1 is not associated with any thread
    std::thread t2(f1, n + 1);          //pass by value
    std::thread t3(f2, std::ref(n));    //pass by reference
    std::thread t4(std::move(t3));      //t4 is now running f2(), and t3 is no longer a thread
    std::thread t5(&foo::bar, &f);      //t4 runs foo::bar() on object f
    t2.join();
    t4.join();
    t5.join();
    std::cout << "Final value of n is " << n << std::endl;
    std::cout << "Final value of f.n (foo::n) is " << f.n << std::endl;
    return 0;
}
```
这个实例的演示结果如下所示：
```
Thread 1 execution.Thread 2 execution.

Thread 3 execution.
Thread 2 execution.
Thread 3 execution.
Thread 1 execution.
Thread 2 execution.
Thread 3 execution.
Thread 1 execution.
Thread 3 execution.
Thread 1 execution.
Thread 2 execution.
Thread 3 execution.
Thread 1 execution.
Thread 2 execution.
Final value of n is 5
Final value of f.n (foo::n) is 5
```
每次运行都会得到不同的结果。

See moer details on [cppreference.com](https://en.cppreference.com/w/cpp/thread)  
