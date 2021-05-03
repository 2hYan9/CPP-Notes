# C++ Primer Plus. II
<span id = "ch05"></span>
### CH05. 循环和关系表达式

C++中的循环语句和C语言中的循环语句并没有太大的区别，使用的方法基本是一样的。这里再介绍一些C++中循环语句常见的使用方法

#### 基于范围的循环

基于范围的for循环，C++11新增了一种循环：基于范围(range-based)的for循环。和简化了一种常见的循环任务，即对数组（或容器类，如vector和array）的每个元素执行相同的操作。如下列所示：

```c++
double prices[5] = {4.99, 10.99, 6.87, 7.99, 8.49};
for(double x : prices)
	cout << x << endl;
```

如果要修改数组中的元素，需要使用不同的循环语法：

```c++
for(double &x : prices)
    x = x * 0.80;
```

上面的&符号表示x是一个引用变量，这里到后面再详细介绍。这里只需要知道，引用变量表示x是prices数组中元素的别名，从而能够修改数组中的元素。

还可结合使用基于范围的for循环和初始化列表：

```c++
for(int x : {3, 5, 2, 8, 6})
	cout << x << " ";
cout << "\n";
```

#### 编写延时循环

要完成这一功能，需要借助C++中的一个函数，clock()，返回程序开始执行后所用的系统时间。

只不过这个函数在可移植性上存在一定的问题：首先，clock()返回时间的单位不一定是秒；其次，该函数的返回类型在某些系统上可能是long，而在另一些系统上可能是unsigned long或其他类型。

但头文件ctime(较早的实现为time.h)提供了解决这些问题的方案。首先，它定义了一个符号常量——CLOCK_PER_SEC用于表示每秒钟包含的系统时间单位数。因此，使用系统时间除以这个值可以得到秒数，或者使用秒数乘以这个符号常量，可以得到系统时间单位为单位的时间。其次，ctime将clock_t作为clock()返回类型的别名。

下面的代码演示了如何使用循环创建延时：

```c++
//waiting.cpp -- using clock() in a time-delay loop
#include <iostream>
#include <ctime>

int main() {
    using namespace std;
    cout << "Enter the delay tiem is second: ";
    float secs;
    cin >> secs;
    cout << "starting\a\n";

    for(int i = 0; i < secs;i++){
        cout << i << endl;
        clock_t start = clock();
        while(clock() - start < CLOCKS_PER_SEC);
    }
    cout << secs << endl;
    cout << "Done\a\n";
    return 0;
}
```

#### 使用循环读取输入

如果学过C语言，那么使用循环读取输入并不是什么新鲜的结构。但是这里需要注意的是，C++读取输入的手段和C语言中的手段并不相同。

前面在介绍读取整行字符串的时候介绍了cin对象中的两个get()方法的版本，即:

*cin.get(arrayName, size);	//read a line of string to arrayName*

*cin.get();	//No additional parameter, read the next character in the input stream*

这再介绍一下这个方法的第三种版本：

*cin.get(ch);	//read a character to ch*

这里需要区分两种读取字符的方法的差异：

|                            | cin.get(ch)                        | ch = cin.get()     |
| -------------------------- | ---------------------------------- | ------------------ |
| 传递输入字符的方式         | 赋值给参数ch                       | 将函数返回值赋给ch |
| 用于字符输入时函数的返回值 | istream对象（可转换为bool值true）  | int类型的字符编码  |
| 到达EOF时函数的返回值      | istream对象（可转换为bool值false） | EOF                |

> istream类提供了一个可以将istream对象（如cin）转换成为bool值得函数；当cin出现在需要bool值得地方时，该转换函数将被调用。另外，如果最后一次读取成功了，则转换得到的bool值为true；否则为false。

在了解了这两种读取字符方法的区别后，就可以用下面这种方式使用循环读取字符了：

```c++
//textin.cpp -- using cin.get(ch) method to read character
#include <iostream>

int main() {
    using namespace std;
    char ch;
    int count = 0;
    while (cin.get(ch)) {
        cout << ch;
        count++;
    }
    cout << endl << count << " characters read\n";
    return 0;
}
```

或者使用另外一个版本进行读取：

```c++
//textin1.cpp -- using ch = cin.get() method to read character
#include <iostream>

int main() {
    using namespace std;
    int ch;
    int count = 0;
    ch = cin.get();
    while (ch != EOF) {
        cout.put(ch);
        count++;
        ch = cin.get();
    }
    cout << count << " characters read\n";
    return 0;
}
```

> 这里需要注意一下通常在windows环境下可以使用ctrl + Z和回车来模拟EOF条件，但是在Unix和类Unix（包括Linux和Cygwin）的环境下，用户使用ctrl + Z 组合键会将程序挂起，而使用命令fg恢复执行程序。
>
> 所以要在Unix或者类Unix环境中模拟EOF只能使用重定向：
>
> *./ excutableFile < inputFile*
<span id = "ch06"></span>
### CH06. 分支语句和逻辑运算符

如果已经学过C语言，那么这一章节的内容基本上可以跳过。

唯一需要注意的地方就只有C++中关于文件的输入与输出

#### 简单的文件输入/输出

使用cin进行输入时，程序将输入视为一系列的字节，其中每个字节都被解释为字符编码。不管目标数据类型是什么，输入一开始都是字符数据——文本数据。然后，cin对象负责将文本转换为其他类型。最后一步的实现依赖于C++中的函数重载特性。

##### 输出文本文件

对于文件输出，C++使用类似于cout的东西。所以下面来复习一些有关将cout用于控制台输出的基本事实：

- 必须包含头文件iostream
- 头文件iostream定义了一个用于处理输出的ostream类
- 头文件iostream声明了一个名为cout的ostream变量（对象）
- 必须指明名称空间std
- 可以结合使用cout和运算符<<来显示给种类型的数据

而文件的输出也与此类似：

- 必须包含头文件fstream
- 头文件fstream定义了一个用于处理输出的ofstream类
- 需要声明一个或多个ofstream变量（对象），并以自己喜欢的方式对其进行命名，条件是遵守常用的命名规则
- 必须指明名称空间std
- 需要将ofstream对象与文件关联起来。为此，方法之一是使用open()方法
- 使用完毕文件后，应使用方法close()将其关闭
- 可结合ofstream对象和运算符<<来输出各种类型的数据

注意方法open()接受一个C-风格字符串作为参数，这可以是一个字面字符串，也可以是存储在数组中的字符串。

上面所描述的步骤，简单来讲就是，声明一个ofstream对象并将其同文件关联起来后，便可以像使用cout那样使用它。所有可用于cout的操作和方法都可用于ofstream对象

下面的代码演示了输出一个文件内容的过程

```c++
#include <iostream>
#include <fstream>

int main() {
    using namespace std;
    char automobile[50];
    int year;
    double a_price;
    double d_price;

    ofstream outFile;
    outFile.open("fish.txt");

    cout << "Enter the make and model of automobile:";
    cin.getline(automobile,50);
    cout << "Enter the model year:";
    cin >> year;
    cout << "Enter the original asking price:";
    cin >> a_price;
    d_price = 0.913 * a_price;
//display information on screen with cout
    cout << fixed;
    cout.precision(2);
    cout.setf(ios_base::showpoint);
    cout << "Make an model: " << automobile << endl;
    cout << "Year: "<< year << endl;
    cout << "Was asking $"<< a_price << endl;
    cout << "Now asking $" << d_price << endl;
//now do exact same things using outFile instead of cout
    outFile << fixed;
    outFile.precision(2);
    outFile.setf(ios_base::showpoint);
    outFile << "Make an model: " << automobile << endl;
    outFile << "Year: " << year << endl;
    outFile << "Was asking $" << a_price << endl;
    outFile << "Now asking $" << d_price << endl;
    outFile.close();
    return 0;
}
```

值得注意的是，方法close()不需要使用文件名作为参数，这是因为outFile已经同特定的文件关联起来。

##### 读取文本文件

控制台输入涉及多个方面，下面首先总结这些方面。

- 必须包含头文件iostream
- 头文件iostream定义了一个用于处理输入的istream类
- 头文件iostream声明了一个名为cin的istream变量（对象）
- 必须指明名称空间std
- 可以结合使用cin和运算符>>来显示给种类型的数据
- 可以使用cin和get()方法来读取一个字符，使用cin和geiline()来读取一行字符。
- 可以结合使用cin和eof()、fail()方法来判别输入是否成功
- 对象cin本身被用作测试条件时，如果最后一个读取操作成功，它将被转换为布尔值true，否则被转换为布尔值false

文件输出于此极其相似：

- 必须包含头文件fstream
- 头文件fstream定义了一个用于处理输入的ifstream类
- 需要声明一个或多个ifstream变量（对象），并以自己喜欢的方式对其进行命名，条件是遵守常用的命名规则
- 必须指明名称空间std
- 需要将ifstream对象与文件关联起来。为此，方法之一是使用open()方法
- 使用完文件后，应使用close()方法将其关闭
- 可以结合使用ifstream对象和运算符>>来显示给种类型的数据
- 可以使用ifstream对象和get()方法来读取一个字符，使用cin和geiline()来读取一行字符。
- 可以结合使用ifstream对象和eof()、fail()方法来判别输入是否成功
- 对象ifstream对象本身被用作测试条件时，如果最后一个读取操作成功，它将被转换为布尔值true，否则被转换为布尔值false

下面的代码演示了文件读取过程：

```c++
#include <iostream>
#include <fstream>
#include <cstdlib>

const int SIZE = 60;

int main() {
    using namespace std;
    char filename[SIZE];
    ifstream inFile;
    cout << "Enter name of data file: ";
    cin.getline(filename, SIZE);
    inFile.open(filename);
    if(!inFile.is_open()){
        cout << "Could not open the file " << filename << endl;
        cout << "Program terminating.\n";
        exit(EXIT_FAILURE);
    }

    double value;
    double sum = 0.0;
    int count = 0;
    inFile >> value;
    while(inFile.good()){
        count++;
        sum += value;
        inFile >> value;
    }
    if(inFile.eof())
        cout << "End of file reached.\n";
    else if(inFile.fail())
        cout << "Input terminated by data mismatch.\n";
    else
        cout << "Input terminated for unknown reason.\n";
    if(count == 0)
        cout << "No data processed/\n";
    else{
        cout << "Items read: " << count << endl;
        cout << "Sum: " << sum << endl;
        cout << "Average: " << sum / count << endl;
    }
    inFile.close();
    return 0;
}
```

值得注意的是，检查文件是否被成功打开的首先方法是使用is_open()，如果文件被成功打开，方法is_open()返回true；所以如果文件没有被打开，表达式!is_open()为true，程序以参数EXIT_FAILURE调用exit()。

而在读取文件时，有几个点需要检查：

- 首先，程序读取文件时，不应超过EOF。如果最后一次读取数据时遇到EOF，方法eof()将返回true。
- 其次，程序可能遇到类型不匹配的情况。
- 最后，可能出现意外的问题，如文件受损或硬件故障。

检查这些问题最简单的方法就是good()方法，该方法在没有发生任何错误时返回true。
<span id = "ch07"></span>
### CH07. 函数——C++的编程模块

#### 函数与数组

传递常规变量时，函数将使用该变量的拷贝；但传递数组时，函数将使用原来的数组。

这里需要解释一下为什么将数组作为参数传递给函数时，需要将数组长度作为参数传递给函数。这是因为函数使用指针来处理数组，而正如前面所说的，指针与数组的一个区别在于，对数组名使用sizeof时得到的是整个数组的长度，而对指针使用sizeof时得到的是指针变量的长度，所以无法使用sizeof来得到原来的数组的长度，因此需要将数组长度作为参数传递。

下面的这段代码

```c++
void show_array(const double ar[], int n);
```

该声明表明，指针ar指向的是一个常量数据。但这里并不是意味着原始数组必须是常量，而只是意味着不能在show_array()函数中使用ar指针来修改这些数据。

C++将声明const double ar[]解释为const double \* ar。因此，该声明实际上是说，ar指向的是一个常量值。

> 指针和const：
>
> 可以用两种不同的方式将const关键字应用于指针。它们都具有不同的含义。
>
> 第一种用法：
>
> ```c++
> int age = 39;
> const int * pt = &age;
> ```
>
> 该声明的含义为\* pt的值是一个常量。但这并不意味着它指向的值实际上就是一个常量，而只是意味着对pt而言，这个值是常量，所以不能通过pt来修改age的值。
>
> C++禁止将const的地址赋给非const指针。这就是为什么在实际编程中需要尽可能使用const的原因，因为这样不仅可以避免无意间修改数据而导致的编程错误，而且使用const是的函数能够处理const和非const实参。
>
> 第二种用法：
>
> ```c++
> int sloth = 3;
> int * const ps = &sloth;
> ```
>
> 这种声明方法中的const用于修饰指针，也就是说指针变量中的值不可修改，只能指向初始化的地址。但允许使用指针修改变量中的值。

#### 函数与二维数组

假设有下面这段代码：

```c++
int data[3][4] = {{1, 2, 3, 4}, {9, 8, 7, 6}, {2, 4, 6, 8}};
int total = sum(data,3);
```

那么这里的sum()函数的原型是什么呢？函数为什么将行数(3)作为参数，而不将列数(4)作为参数呢？

data是一个数组名，该数组有三个元素，每个元素都由含4个int类型的元素的数组组成。因此data的类型是指向由4个int组成的数组的指针，因此正确的原型如下：

```c++
int sum(int (*ar)[4], int size);
```

上面的代码将声明出一个指向包含4个int元素的数组的指针（也就是说指针的单位是4个int元素组成的数组）

**需要注意的是，函数参数不能是数组。**

还有另外一种格式，这种格式与上述u按行的含义完全相同，但是可读性更强：

```c++
int sum(int ar[][4], int size);
```

指针类型指出，它指向由4个iny组成的数组。因此，指针类型指定了列数，这就是没有将列数作为独立的函数参数进行传递的原因。

如果要在函数中使用这样的二维数组，那么最简单的方法就是将形参看作一个二维数组的名称，因为C++中的数组名和指针一定程度上是等价的。

当然也可以使用指针的形式：

```c++
ar2[r][c] == *(*(ar2 + r) + c);
```

注意上面的声明中并没有使用const限定符，这是因为const用于指针时，只能修饰指向基本类型的指针，而上面个的声明中形参是一个指向指针的指针。

#### 函数指针

函数也有地址。函数的地址就是存储其机器语言代码的内存的开始地址。

函数指针的使用步骤：

1. 获取函数的地址：获取函数的地址很简单：只要使用函数名（后面不跟参数）即可。也就是说如果think()是一个函数，那么think就是这个函数的地址。

2. 声明函数指针：声明指向函数的指针时，也必须指定指针指向的函数类型。这意味着声明应制定函数的返回类型以及函数的特征标（参数列表）。具体可以看下面的示例：

   ```c++
   double pam(int);	//prototype
   double (*pf)(int)	//pf points to a function that takes one int
   				   //arguement and that returns type double
   pf = pam;
   ```

   如果要使用函数指针指向某个函数，那么必须保证函数指针和被指向的函数的特征标（即参数列表）和返回类型必须相同。如果不相同，那么编译器会拒绝这种赋值。

3. 使用函数指针来调用函数：使用函数指针和一般的指针很相似，需要通过解除引用运算符，如下面的示例：

   ```c++
   double pam(int);
   double (*pf)(int);
   pf = pam;
   double x = pam(4);
   double y = (*pf)(5);
   ```

   在上面的代码中，可以将(\*pf)看作其对应的函数名。
<span id = "ch08"></span>
### CH08. 函数幽探

#### 内联函数

先来看看常规的函数调用过程：执行到函数调用指令时，程序将在函数调用后立即存储该指令的内存地址，并将函数参数复制到堆栈，跳到标记函数起点的内存单元，执行函数代码，然后跳回到地址被保存的指令处。

来回跳跃并记录跳跃位置意味着需要一定的开销。

内联函数的编译代码与其他程序代码“内联”起来了。也就是说，编译器将使用相应的函数代码替换函数调用。对于内联代码，程序无需跳到另一个位置处执行代码后再跳回来。

内联函数比一般的函数更快，但是条件是需要占用更多的内存。

如果需要使一个函数称为内联函数，要么在函数声明前添加关键字Inline，要么在函数定义前添加关键字inline。但通常的做法是，将整个定义（即函数头和所有函数代码）放在本应提供原型的地方。

#### 引用变量

引用变量是C++中一种新添加的符合类型，其主要用途是用作函数函数的形参。通过将应用变量用作参数，函数将使用原始数据，而不是其副本。

创建一个引用变量的通用格式如下：

*typeName & varName = iniValue;*

其中&不是取址符号，而是类型标识符的一部分。就像声明中char \*指的是指向char的指针一样，int &指的是指向int的引用。

需要注意的是，必须在声明引用时将其初始化，引用更接近const指针，必须在创建时进行初始化，一旦与某个变量关联起来，就将一直效忠于它。也就是说引用变量关联的内存地址，一旦初始化就不可改变。

下面的这段代码演示了引用与指针之间的赋值关系：

```c++
int rats = 100;
int * pt = &rats;
int & rodents = *pt;
int bunnies = 50;
pt = &bunnies;
```

将rodents初始化为\*pt使得rodents指向rats。然后将pt改为指向bunnies，但rodents依然引用的是rats。

##### 将引用用作函数参数

引用经常被用作函数参数，使得函数中的变量名成为调用程序中的变量的别名。这种传递参数的方法称为按引用传递。按引用传递允许被调用的函数能够访问调用函数中的变量。也就是说让形参称为实参的一个别名，而内存单元中的变量就拥有了两个名称。

将引用用作函数参数时需要注意，所引用的实参必须是一个变量。而不能是算术表达式。

> 如果以const限定符修饰的引用变量作为形参，那么C++将会创建一个临时的无名变量，然后将其初始化为表达式的值。
>
> 如果引用参数为const，那么编译器会在下面两种情况下生成临时变量：
>
> - 实参的类型正确，但不是左值；
> - 实参的类型不正确，但可以转换为正确的类型
>
> 这里所说的左值，就是指可被引用的数据对象，例如，变量、数组元素、结构成员、引用和解除引用的指针都是左值。而非左值包括字面常量（用引号括起来的字符串除外，它们由其地址表示）和包含多项的表达式。
>
> 例如下面这段代码：
>
> ```c++
> double refcube(const double & ra){
>     return ra * ra * ra;
> }
> 
> int main{
>     double side = 3.0;
>     double * pd = &side;
>     double & rd = side;
>     long edge = 5L;
>     double lens[4] {2.0, 5.0, 10.0, 12.0};
>     double c1 = refcube(side);		//then ra is side
>     double c2 = refcube(lens[2]);	//then ra is lens[2]
>     double c3 = refcube(rd);		//then ra is rd is side
>     double c4 = refcube(*pd);		//then ra is *pd is side
>     double c5 = refcube(edge);		//ra is temporary variable
>     double c6 = refcube(7.0);		//ra is temporary variable
>     double c7 = refcube(side + 10.0);//ra is temporary variable
>     return 0;
> }
> ```

如果接受引用参数的函数的意图是修改作为参数传递的变量，那么创建临时变量的操作会阻止这种意图的实现。

##### 为何要返回引用

先来看看传统的返回机制，传统的返回机制与按值传递函数参数类似：计算关键字return后面的表达式，并将结果返回给调用函数。从概念上说，这个值被复制到一个临时位置，而调用程序将使用这个值。

而如果返回类型是引用类型，那么将不会有先复制到一个临时变量的这部操作。但前提是，以引用变量为返回类型的函数不能返回一个函数终止时不再存在的内存单元的引用。比如下面这样：

```c++
const int & clone2(int & a){
    int newguy;
    newguy = a;
    return newguy;
}
```

这里还需要解释一下为什么以引用类型作为返回类型时必须加上限定符const：这是为了避免给函数复制的情况。因为如果函数返回类型是引用类型，那么这个函数在等式左边时，会被编译器当作可修改的值，从而产生错误的运行结果。为了避免这种情况，可以直接在返回类型前添加限定符const。

#### 默认参数

C++中必须通过函数原型来设置默认参数。由于编译器通过查看圆形来了解函数所使用的参数数目，因此函数原型也必须将可能的默认参数告知程序。方法是将值赋给原型中的参数。

对于带参数列表的函数，必须从右向左添加默认值。也就是说要为某个参数设置默认值，则必须为它右边的所有参数提供默认值。而实参则是按从左向右的顺序依次被赋给相应的形参，而不能跳过任何参数。

#### 函数重载

默认参数能够允许使用不同数目的参数调用同一个函数，而函数重载则允许使用多个同名的函数。

函数重载的关键是函数的参数列表——也成为函数特征标(function signature)。如果两个函数的参数数目和类型相同，同时参数的排列顺序也相同，则它们的特征标相同，而变量名是无关紧要的。

C++允许定义名称相同的函数，条件是它们的特征标不同。如果参数数目和/或参数类型不同，则特征标也不同。这里需要注意的是，决定是否能够重载的是函数的特征标而不是函数的返回类型。

#### 函数模板

函数模板是通用的函数描述，也就是说，他们使用泛型来定义函数，其中的泛型可用具体的类型替换。通过将类型作为参数传递给模板，可使编译器生成该类型的函数。

函数模板允许一任意类型的方式来定义函数，比如下面这样：

```c++
template <typename AnyType>
void Swap(AnyType &a, AnyType &b){
    AnyType temp;
    temp = a;
    a = b;
    b = temp;
}
```

上面的代码定义了一个用于数据交换的函数模板。第一行指出，要建立一个模板，并将类型命名为AnyType。关键字template和typename是必需的，除非可以使用关键字class代替typename。另外，必须使用尖括号。类型名可以任意选择，只要遵守C++命名规则即可。余下的代码描述了交换两个AnyType值的算法。

模板并不创建任何函数，而只是告诉编译器任何定义函数。需要交换int的函数时，编译器将按模板模式创建这样的函数，并用int代替AnyType。

此外，还可以像重载常规函数那样重载模板定义。和常规重载一样，被重载的模板的函数特征标必须不同。下面是一个重载模板定义的例子，需要注意的是，并非所有的模板参数都必须是模板参数类型，也可以是具体的类型。

```c++
#include <iostream>

template <typename T>
void Swap(T &a, T &b);

template <typename T>
void Swap(T *a, T *b, int n);

void Show(int a[]);

const int Lim = 8;

int main() {
    using namespace std;
    int i = 10, j = 20;
    cout << "i, j = " << i << ", " << j << endl;
    cout << "Using compiler-generated int swapper:\n";
    Swap(i,j);
    cout << "Now j, j = " << i << ", " << j << endl;

    int d1[Lim] {0, 7, 0, 4, 1, 7, 7, 6};
    int d2[Lim] {0, 7, 2, 0, 1, 9, 6, 9};
    cout << "Original arrays:\n";
    Show(d1);
    Show(d2);
    Swap(d1, d2, Lim);
    cout << "Swapped arrays:\n";
    Show(d1);
    Show(d2);
    return 0;
}

template <typename T>
void Swap(T &a, T &b){
    T temp;
    temp = a;
    a = b;
    b = temp;
}

template <typename T>
void Swap(T a[], T b[], int n){
    T temp;
    for (int i = 0; i < n; i++){
        temp = a[i];
        a[i] = b[i];
        b[i] = temp;
    }
}

void Show(int a[]){
    using namespace std;
    cout << a[0] << a[1] << "/";
    cout << a[2] << a[3] << "/";
    for(int i = 4; i < Lim; i++){
        cout << a[i];
    }
    cout << endl;
}
```

上面的代码重载了一个函数模板Swap，使其能够对两个数组进行交换。

##### 显式具体化

考虑这样一个场景，假如要交换两个结构类型的变量，并且希望只交换结构中某个特定的元素。那么这种情况下需要使用不同的代码，但是提供给模板的参数仍然无法改变，那么也就无法重载。

解决方法是，可以提供一个具体化函数定义——称为显式具体化(explicit specialization)，其中包含所需的代码。当编译器找到与函数调用匹配的具体化定义时，将使用该定义，而不再寻找模板。

下面是一个用于交换结构类型变量的非模板函数、模板函数和显式具体化的原型：

```c++
struct job{
	char name[40];
	double salary;
	int floor;
}

//non template function prototype
void Swap(job &, job &);

//template prototype
template <typename T>
void Swap(T &, T &)；

//explicit specialization for the job type
template <> void Swap<job>(job &, job &);	//the "<job>" in "Swap<job>" is optional
template <> void Swap(job &, job &);		//simpler form
```

像上面这样，如果有多个原型，则编译器在选择原型时，非模板版本优先于显示具体化和模板版本，而显式具体化优先于使用模板生成的版本。

> 实例化与具体化：
>
> 在代码中包含函数模板本身并不会生成函数定义，它只是一个用于生成函数定义的方法。编译器使用模板为特定类型生成函数定义时，得到的是模板实例(instantiation)。
>
> 模板并非函数定义，但使用一个具体类型作为类型参数的模板实例时函数定义，这种实例化方式被称为隐式实例化(implicit instantiation)，因为编译器之所以知道需要进行定义，是由于程序传递了具体类型作为类型参数。
>
> 比较老的一些C++编译器版本只支持隐式实例化。但现在的C++还支持显式实例化(explicit instantiation)。这意味着可以直接命令编译器创建特定的实例，下面的代码演示了一个显式实例化的例子，需要注意显示实例化和显示具体化声明语法上的区别：
>
> ```c++
> template void Swap<int>(int &, int &);			//explicit instantiation
> 
> //to differ with explicit specialization
> template <> void Swap(int &, int &);			//explicit specialization
> template <> void Swap<int>(int &, int &);		//explicit specialization
> ```
>
> 显示实例化与显示具体化的区别在于：
>
> - 显式实例化声明语句会让编译器使用Swap模板生成一个使用int类型的实例。显式实例化声明的意思是“使用Swap()模板生成int类型的函数定义”
> - 显示具体化声明语句的意思是“不要使用Swap()模板来生成函数定义，而应使用专门为int类型显式定义的函数定义”。

##### 模板中的类型问题

在编写模板时的一个问题是，并非总能知道应在声明中使用哪种类型，比如下面的例子：

```c++
template<class T1, class T2>
void ft(T1 x, T2 y){
	...
	?type? xpy = x + y;
    ...
}
```

上面这段代码中的xpy应该是什么类型呢？这要取决于T1和T2具体是什么类型，比如这两个类型一个是double一个是int，那么它们的和就是double，如果一个是int一个short，那么它们的和就是int。因此没有办法在模板中声明xpy应该是什么类型。

这个问题的解决方案是C++11新增的关键字decltype，其使用方法大致如下：

```c++
int x;
decltype(x) y; //make y the same type as x
```

decltype的作用是使关键字后面的变量类型与参数中的类型一致，参数可以是表达式。

因此上面的问题的解决方案是：

```c++
decltype(x + y) xpy;
xpy = x + y;
```

或者将这两句代码合并：

```c++
decltype(x + y) xpy = x + y;
```

decltype关键字中的参数必须要先经过声明后才能使用。

此外，在定义模板时另一个与类型相关的问题是decltype无法解决的。即函数模板的返回类型是无法通过decltype识别的，因为在声明一个函数的返回类型时还没有声明任何其他变量。比如下面这样：

```c++
template<class T1, class T2>
?type? gt(T1 x, T2 y){
    ...
    return x + y;
}
```

这个问题可以通过后值返回类型解决：

```c++
double h(int x, float y);
//is the same as
auto h(int x, float y) -> double
```

然后再与decltype结合使用，便可以给gt()指定返回类型：

```C++
template <class T1, class T2>
auto gt(T1 x, T2 y) -> decltype(x + t){
	...
	return x + y;
}
```
<span id = "ch09"></span>
### CH09. 内存模型和名称空间

#### 单独编译

通常是将一个程序分成三部分：

- 头文件：包含结构声明和使用这些结构的函数的原型
- 源代码文件：包含与结构有相关的函数的代码
- 源代码文件：包含调用与结构相关的函数的代码

这里的建议是，不要将函数定义或变量声明放到头文件中，这样做对于简单的情况可能是可行的，但是通常会引来麻烦。例如，如果在头文件包含一个函数定义，然后在其他两个文件（属于同一个程序）包含这个头文件，则同一个程序中将包含同一个函数的两个定义，除非函数是内联的，否则这将报错。

下面列出头文件中通常包含的内容：

- 函数原型
- 使用#define或const定义的符号常量
- 结构声明
- 类声明
- 模板声明
- 内联函数

可以使用#include指令管理头文件，但是不要使用#include来包含源代码文件，因为这样可能会导致多重声明。

使用#include时，如果文件名包含在尖括号中，则C++编译器将在存储标准头文件的主机系统中查找；但如果文件名包含在双引号中，则编译器将首先查找当前的工作目录或者源代码目录（或其他目录，取决于编译器），如果没有找到头文件，则将在标准位置查找。

> 头文件管理：
>
> 在一般的大型项目中，很容易在不知情的情况下将一个头文件包含多次，比如可能会在一个头文件中包含另一个头文件。
>
> 避免这种错误的方法是，使用预处理器编译指令#ifndef（即，if not define）来处理。比如下面这段代码意味着仅当以前没有使用预处理器编译指令#define定义名称COORDIN_H_时，才处理#ifndef和#endif之间的语句：
>
> ```c++
> #ifndef COORDIN_H_
> #define COORDIN_H_
> ...
> #endif
> ```
>
> 如果在同一个文件中遇到其他包含coordin.h的代码，编译器将知道COORDIN_H_已经被定义了，从而跳到#endif后面的一行上。
>
> 需要注意的是，这样的方法并不能防止编译器将文件包含多次，而只是让它忽略除第一次包含之外的内容。

#### 存储持续性、作用域和连续性

作用域和链接性：

- 作用域用于描述一个名称在文件的多大范围内可见。
- 链接性描述了名称如何在不同单元间共享。

链接性为外部的名称可在文件间共享，链接性为内部的名称只能由一个文件中的函数共享。

1. 自动存储持续性：默认情况下，在函数中声明的函数参数和变量的存储持续性为自动，作用域为局部，没有链接性，因为它们不能共享。

   如果在代码块中定义了变量，则该变量的存在时间和作用域将被限制在该代码块内。

   如果有两个同名的变量（一个位于外部代码块中，另一个位于内部代码块中），那么新的定义将会隐藏以前的定义，新定义可见，旧定义不可见。在程序离开该代码块时，原来的定义又重新可见。

2. 静态持续变量：静态存储持续性变量具有三种链接性，外部链接性（可在其他文件中访问）、内部链接性（只能在当前文件中访问）和无链接性（只能在当前函数或代码块中访问）。

   - 要创建链接性为外部的静态持续变量，必须在代码块的外面声明它；
   - 要创建链接性为内部的静态持续变量，必须在代码块的外面声明它，并使用static限定符；
   - 要创建没有链接性的静态持续变量，必须在代码块内部声明它，并且使用static限定符。

   如果没有显示地初始化静态变量，编译器将把它设置为0。在默认情况下，静态数组和结构将每个元素或成员的所有位都设置为0.

单定义规则(One Definition Rule, ODR)：变量只能定义一次。

为了满足这样的规则，C++提供了两种变量声明。一种是定义声明(defining declaration)或简称定义(definition)，它给变量分配存储空间；另一种是引用声明(referencing declaration)或简称声明(declaration)，它不给变量分配空间，因为它引用已有的变量。

引用声明使用关键字extern，且不进行初始化；否则，声明为定义，导致分配存储空间。如果要在多个文件中使用外部变量，只需要在一个文件中包含该变量的定义（单定义规则），但在使用该变量的其他所有文件中，都必须使用关键字extern声明它。

如果在函数中声明一个与外部变量同名的变量，编译器将会把这种声明视为一个自动变量的定义，当程序执行自动变量所属的函数时，该变量将位于作用域内，而自动变量将隐藏同名的全局变量。在这种情况下，如果要使用全局变量的版本，就需要在变量名的前面加上一个作用域解析运算符(::)。

静态存储连续性、无链接性：无链接性的局部变量。这种变量是这样创建的，将static限定符用于在代码块中定义的变量。在代码块中使用static时，将导致局部变量，的存储持续性为静态的。这意味着虽然该变量只在该代码块中可用，但它在改代码块不处在活动状态时仍然存在。因此两次函数调用之间，静态局部变量的值将保持不变。并且如果初始化了静态局部变量，则程序只在启动时进行一次初始化。

##### 说明符和限定符

cv-限定符：

- const，这里再对const进行一个补充。由于外部定义的const数据的链接性为内部的，因此可以在所有文件中使用相同的声明。

  内部链接性意味着，每个文件都有自己的一组常量，而不是所有文件共享一组常量。每个定义都是其所属文件私有的，这就是能够将常量定义放在头文件中的原因。

  但如果出与某种原因希望某个常量的链接性为外部的，那么可以使用关键字extern来覆盖默认的内部链接性。

  ```c++
  extern const int states = 50;
  ```

  在这种情况下，必须在所有使用该常量的文件中使用extern关键字来声明它。而由于单个const在多个文件之间共享，因此只有一个文件可对其进行初始化。

- volatile，关键字volatile表明，即使程序代码没有对内存单元进行修改，其值也可能发生变化。这个关键字用于改善编译器的优化功能。比如编译器发现，程序在几条语句中两次是哦那个了某个变量的值，则编译器可能不是让程序查找这个值两次，而是将这个值缓存到寄存器中。这种优化假设变量的值在这两次使用之间不会变化。如果不将变量声明为volatile，则编译器将进行这种优化；将变量声明为volatile，相当于告诉编译器不要进行这种优化。

存储说明符：

- auto（在C++11中不再是说明符）
- register
- static
- extern
- thread_local（C++11新增的）
- mutable：用于指出，即使结构（或类）变量为const，其中的某个成员也是可以被修改的。

下面的表格总结了五种存储类型的变量的持续性：

| 存储描述         | 持续性 | 作用域 | 链接性 | 如何声明                         |
| ---------------- | ------ | ------ | ------ | -------------------------------- |
| 自动             | 自动   | 代码块 | 无     | 在代码块中                       |
| 寄存器           | 自动   | 代码块 | 无     | 在代码块中，使用关键字register   |
| 静态，无链接性   | 静态   | 代码块 | 无     | 在代码块中，使用关键字static     |
| 静态，外部链接性 | 静态   | 文件   | 外部   | 不在任何函数内                   |
| 静态，内部链接性 | 静态   | 文件   | 内部   | 不在任何函数内，使用关键字static |

##### 函数和链接性

可以在函数原型中使用关键字extern来指出函数是在另一个文件中定义的，不过这是可选的。要让程序在另一个文件中查找函数，该文件必须作为程序的组成部分被编译，或者是由链接程序搜索的库文件。

还可以使用关键字static将函数的链接性设置为内部的，使之只能在一个文件中使用。必须同时在原型和函数定义中使用static关键字。

```c++
static int private(double x);
...
static int private(double x){
	...
}
```

##### 定位new运算符

new负责在堆中找到一个足迹能够满足要求的内存块。new运算符还有另外一个版本被称为定位new运算符，能够允许指定要使用的位置。编程者可以通过这个特性来设置其内存管理规程、处理需要通过特定地址进行访问的硬件或在特定位置创建对象。

要使用定位new运算符，首先要包括头文件new，它提供了这种版本呢的new运算符原型；然后将new运算符用于提供了所需地址的参数。除了需要指定参数外，句法与常规new运算符相同。下面的代码演示了如何使用new的这种特性

```c++
//newplace.cpp -- using placement new
#include <iostream>
#include <new>

const int BUF = 512;
const int N = 5;
char buffer[BUF];
int main() {
    using namespace std;
    double *pd1, *pd2;
    int i;
    cout << "Calling new and placement new:\n";
    pd1 = new double [N];
    pd2 = new (buffer) double [N];
    for(i = 0; i < N; i++)
        pd2[i] = pd1[i] = 1000 + 20.0 * i;
    cout << "Memory addresses:\n" << "heap: " << pd1
        << " static: " << (void *) buffer << endl;
    cout << "Memory contents:\n";
    for(i = 0; i < N; i++){
        cout << pd1[i] << " at " << &pd1[i] << "; ";
        cout << pd2[i] << " at " << &pd2[i] << endl;
    }
    cout << "\nCalling new and placement new a second time:\n";
    double *pd3, *pd4;
    pd3 = new double[N];
    pd4 = new (buffer) double[N];
    for(i = 0; i < N; i++)
        pd4[i] = pd3[i] = 1000 + 40.0 * i;
    cout << "Memory contents:\n";
    for(i = 0; i < N; i++){
        cout << pd3[i] << " at " << &pd3[i] << "; ";
        cout << pd4[i] << " at " << &pd4[i] << endl;
    }
    cout << "\nCalling new and placement new a third time:\n";
    delete [] pd1;
    pd1 = new double [N];
    pd2 = new (buffer + N * sizeof(double)) double [N];
    for(i = 0; i < N; i++)
        pd2[i] = pd1[i] = 1000 + 60 * i;
    cout << "Memory contents:\n";
    for(i = 0; i < N; i++){
        cout << pd1[i] << " at " << &pd1[i] << "; ";
        cout << pd2[i] << " at " << &pd2[i] << endl;
    }
    delete [] pd1;
    delete [] pd3;
    return 0;
}
```

这里对上面代码需要指出的一点是，定位new运算符将数组pd2放在了数组buffer中。这里的buffer是char类型指针，所以在打印其存储的地址时需要将其强制转换为(void \*)。

定位new运算符使用传递给它的地址，而不跟踪哪些内存单元已被使用，也不查找未使用的内存块，这将一些内存管理的负担交给了程序员。

上面的代码中buffer指定的是静态内存，而delete只能用于这样的指针：指向常规new运算符分配的堆内存。也就是说，数组buffer位于delete的管辖区域之外，所以不能使用delete释放pd2指向的内存。

#### 名称空间

声明区域：是指可以在其中进行声明的区域。

潜在作用域：变量的潜在作用域从声明点开始，到其声明区域的结尾。因此潜在作用域比声明区域小，这是由于变量必须定义后才能使用。

需要注意的是，变量并非在其潜在作用域内的任意位置都是可见的。比如它可能被另一个在嵌套声明区域中声明的同名变量隐藏。

作用域：变量对程序可见的范围就是变量的作用域。

C++新增了这样一种功能，即通过定义一种新的声明区域来创建命名的名称空间，这样做的目的之一是提供一个声明名称的区域。一个名称空间中的名称不会与另外一个名称空间的相同名称发生冲突，同时允许程序的其他部分使用该名称空间中声明的东西。

下面的代码演示了如何创建名称空间：

```c++
namespace Jack{
	double pail;
	void fetch();
	int pal;
	struct Well() {...};
}
namespace Jill{
	double bucket(double n){...};
	double fetch;
	int pal;
	struct Hill{...};
}
```

名称空间可以是全局的，也可以位于另一个名称空间中，但不能位于代码块中。

名称空间是开放的(open)，即可以把名称加入到已有的名称空间中：

```c++
namespace Jill {
	char * goose(const char *);
}
```

同样，也可以在原来的名称空间中提供函数原型，然后在文件后面（或另一个文件中）再次使用这个名称空间来提供函数定义：

```c++
namespace Jack{
	void fetch();
	...
}
...
namespace Jack{
	void fetch(){
		...
	}
}
```

如果需要访问名称空间中的名称。最简单的方法就是，通过作用域运算符::来使用名称空间来限定该名称：

`Jack::pail = 12.34;  //use a variable`

在上面这行代码中，将未被装饰的名称（如pail）称为未限定名称(unqualified name)；包含名称空间的名称（如Jack::pail）称为限定名称(qualified name)；

##### using声明和using编译指令

using声明使特定的标识符可用，而using编译指令是整个名称空间可用。

下面是一个using声明的使用示例：

```c++
namespace Jill{
	double bucket(double n){...}
	double fetch;
	struct Hill{...};
}
char fetch;
int main(){
	using Jill::fetch;		//put fetch into local namespace
	//double fetch;			error!
    cin >> fetch;			//read a value into Jill::fetch
    cin >> ::fetch;			//read a value into global fetch
    ...
}
```

需要注意的是，如果using声明导入的函数被重载，则一个using声明将导入该函数的所有版本。

而using编译指令使所有的名称都可用，如比较常见的：`using namespace std;`

使用using编译指令导入一个名称空间中所有的名称与使用多个using声明是不一样的，这更像是大量的使用作用域解析运算符。

使用using声明时，就好像声明了相应的名称一样。如果某个名称已经在函数中声明了，则不能用using声明导入相同的名称。

然而使用using编译指令时，将进行名称解析，就像在包含using声明和名称空间本身的最小声明区域中声明了名称一样。

下面是一个using编译指令的使用示例：

```c++
namespace Jill{
	double bucket(double n){...}
	double fetch;
	struct Hill{...};
}
char fetch;
int main(){
	using namespace Jill;	//import all namespace names
	Hill Thrill;			//create a type Jill::Hill structure
	double water = bucket(2);
	double fetch;			//not an error, but hides Jill::fetch and global fetch
    cin >> fetch;			//read a value into the local fetch
    cin >> ::fetch;			//read a value into global fetch
    cin >> Jill::fetch;		//read a value into Jill::fetch
    ...
}

int foom(){
    //Hill top;				error!
    Jill::Hill crest;		//valid
}
```

需要注意上面代码中foom()函数中的内容，虽然函数中的using编译指令将名称空间中的名称视为在函数外声明的，但是这并不意味着可以在文件中的其他函数中使用这些名称。

名称空间的一些特性：

1. 可以将名称空间进行嵌套

   ```c++
   namespace elements{
   	namespace fire{
   		int flame;			//qulified name is elements::fire::flame
   	}
   	...
   }
   ```

2. 可以在名称空间中使用using声明和using编译指令

3. 可以给名称空间创建别名

   ```C++
   namespace my_very_favorite_things {...};
   namespace mvft = my_very_favorite_things;
   ```

4. 可以通过省略名称空间的名称来创建未命名的名称空间

注意，使用名称空间的主旨是简化大型编程项目的管理工作。
