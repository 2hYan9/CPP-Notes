# C++-Primer-Plus-notes
The reading notes of the *C++ Primer Plus*  
reader of this notes require some C foundation,  
and this notes may be helpful for those who get in touch with OOP(object-oriented programing) for the first time.  
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

### Extension: Multiple Thread Programing in C++ 11  
Let's talk about the concept of mutiple thread.  

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

and the runing result is like this:  
```
Hello World!Hello World!

Hello World!Hello World!Hello World!



```
it will get different result for different running.  

There, the function `pthread_create` is used to create threads, and the prototype is as followed:  
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

And the return value of `pthread_create()` is zero if the process create a thread successfully.  


ref.  
[cplusplus.com](http://www.cplusplus.com/reference/)  
[cppreference.com](https://en.cppreference.com/w/)
