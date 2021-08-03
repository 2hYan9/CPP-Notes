# C++-Primer-Plus-notes
The reading notes of the *C++ Primer Plus*  
reader of this notes require some C foundation,  
and this notes may be helpful for those who get in touch with OOP(object-oriented programing) for the first time.  
Note: All these files are written in Chinese.  
### Some Basic Knowledge of the C++  
The file "C++ Primer Plus I.md" mainly introduce some basic knowledge of C++, containing these following contents:
- [CH00](./C++%20Primer%20Plus.%20I.md#ch00): Some knowledge about complation process
- [CH01](./C++%20Primer%20Plus.%20I.md#ch01): OOP concept
- [CH02](./C++%20Primer%20Plus.%20I.md#ch02): A brief introduction about function definition and namespace in C++
- [CH03](./C++%20Primer%20Plus.%20I.md#ch03): Basic variable types and the *const* identifier 
- [CH04](./C++%20Primer%20Plus.%20I.md#ch04): some complex types, new & delete operator, and storage types  
### More Advanced Freature of C++
The file "C++ Primer Plus II.md" talks about some features of the C++. Contains these following contents:
- [CH05](./C++%20Primer%20Plus.%20II.md#ch05): Read input by 'cin' object and loop, structure traverse a range in a loop structure and clock() function  
- [CH06](./C++%20Primer%20Plus.%20II.md#ch06): Some simple file I/O cases
- [CH07](./C++%20Primer%20Plus.%20II.md#ch07): Take array or pointer as arguement in Function
- [CH08](./C++%20Primer%20Plus.%20II.md#ch08): Some advanced knowledge about the function in C++
- [CH09](./C++%20Primer%20Plus.%20II.md#ch09): The storage model in C++, and some details about namespace
### Classes in C++
The file "C++ Primer Plus III.md" is mainly about the C++ classes:
- [CH10](./C++%20Primer%20Plus.%20III.md#ch10): Some basic knowledge about C++ classes
- [CH11](./C++%20Primer%20Plus.%20III.md#ch11): Operator reloading(friendship) & type conversion in C++ classes
- [CH12](./C++%20Primer%20Plus.%20III.md#ch12): Dynamic memory allocation in C++ classes
- [CH13](./C++%20Primer%20Plus.%20III.md#ch13): Inheritance for C++ classes, virtual member function and Abstract bases class
- [CH14](./C++%20Primer%20Plus.%20III.md#ch14): Talking about the "has-a" relationship, mutiple inheritance and class template

### Extension I: Multiple Thread Programing in C++ 11  
Let's talk about the concept of mutiple thread.  

>A thread is a path of execution within a process. A process can contain multiple threads.  
>A thread is also known as lightweight process. The idea is to achieve parallelism by dividing a process into multiple threads. For example, in a browser, multiple tabs can be different threads. MS Word uses multiple threads: one thread to format the text, another thread to process inputs, etc.  
>From [Geeksforgeeks.org](https://www.geeksforgeeks.org/thread-in-operating-system/)

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

ref.  
*C++ Primer Plus*  
[man7.org](https://man7.org/index.html)  
[cplusplus.com](http://www.cplusplus.com/reference/)  
[cppreference.com](https://en.cppreference.com/w/)
