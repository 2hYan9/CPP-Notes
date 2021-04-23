# C++ Primer Plus. III

### CH10. 对象和类

#### 过程性编程和面向对象编程

这里再简单复习一下面向过程和面向对象编程之间的区别：

采用过程性编程方法时，首先考虑要遵循的步骤，然后考虑如何表示这些数据（并不需要程序一直运行，用户可能希望能够将数据存储在一个文件中，然后从这个文件中读取数据）。

采用OOP方法时，首先从用户的角度考虑对象——描述对象所需的数据以及描述用户与数据交互所需的操作。完成对接口的描述后，需要确定如何实现接口和数据存储。最后，使用新的设计方案创建出程序。

用户与数据交互的方式有三种：初始化、更新和报告，这些就是用户接口。

#### 抽象和类

指定基本类型可以完成的工作：

- 决定数据对象需要的内存数量
- 决定如何解释内存中的位（long和float在内存中占用的位数相同，但将它们转换为数值的方法不同）
- 决定可使用数据对象执行的操作或方法

而在C++中，类规范有两个部分组成：1

1. 类声明：以数据成员的方式描述数据部分，以成员函数（或者称为方法）的方式描述共有接口
2. 类方法定义：描述如何实现类成员函数

类公有接口：接口由编写类的人提供的方法组成。接口让程序员能够编写与类对象交互的代码，从而让程序能够使用类对象。类设计禁止公共用户直接访问类，但可以通过接口（定义中的方法）来访问类。

定义一个类的通用格式：

```c++
class class_name{
	access_specifier_1:
		member1;
    access_specifier_2:
    	member2;
    ...
} object_name;			//object_name is optional
```

其中，关键字class指出这些代码定义了一个类设计，class_name是这个新类型的名称。而object_name是一个可选的属于这个类的对象名称列表。

access_specifier是一个描述访问控制的关键字，它可以是下面三种关键字中的一种：

1. private：即私有成员，私有成员只能由类的内部（也就是同一个类中的其他成员）访问，或者通过友元函数访问。
2. protected：即保护成员，保护成员不仅能够由相同类的其他成员或者友元函数访问，而且可以由这个类派生出的其他类访问。
3. public：即公有成员，公有成员可以在任何对象可见的范围内被访问。

使用访问控制的目的时进行数据隐藏。数据隐藏不仅可以防止直接访问数据，还可以让开发者（类的用户）无需了解数据是如何被表示的。从使用类的角度看，使用哪种方法没有什么区别。所需要知道的只是各种成员函数的功能。原则是将实现细节从接口设计中分离出来。如果能找到更好的、实现数据表示或成员函数细节的方法，可以对这些细节进行修改，而无需修改程序接口，这使程序维护起来更容易。

由于隐藏数据是OOP的主要目标之一，因此数据项通常放在私有部分，组成类接口的成员函数放在共有部分；否则，就无法从程序中调用这些函数（或者称之为接口）。

类声明中访问权限默认为private，而结构声明中访问权限默认为public。

成员函数的定义和常规函数的定义非常是相似，但是成员函数的定义有两个特征：

- 定义成员函数时，使用作用域解析符(::)来标识函数所属的类
- 类方法可以访问类的private组件

下面代码演示了定义一个类、然后实现其成员函数的细节，并且使用这个类：

```c++
//class example
#include<iostream>
using namespace std;

class Rectangle{
    private:
    	int width, height;
    public:
    	void set_values(int, int);
    	int area() {return width * height;}
};

void Rectangle::set_values(int x, int y){
    width = x;
    height = y;
}

int main(){
    Rectangle rect;
    rect.set_values(3, 4);
    cout << "area: " << rect.area();
    return 0;
}
```

定义位于类声明中的函数都将自动称为内联函数，比如上面的area()函数。也可以在类声明之外定义成员函数，并使用inline关键字使其称为内联函数。

使用对象的成员函数和使用结构成员一样，通过成员运算符"."。

需要注意的是，所创建的灭个新对象都有自己的存储空间，用于存储其内部变量和类成员；但同一个类的所有对象共享同一组类方法，即每种方法只有一个副本。

#### 类的构造函数和析构函数

由于类设计思想是数据隐藏，也就意味着无法直接访问类的数据。因此需要设计合适的成员函数才能成功地将对象初始化。为此，C++提供了一个特殊的成员函数——类构造函数，专门用于构造新对象、将值赋给它们的数据成员。实际上，C++为这些函数提供了名称和使用语法，只需要程序员提供给这些函数的定义。

构造函数的名称与类名相同，并且构造函数的原型和函数头虽然没有返回值，但没被声明为void类型。实际上，构造函数没有声明类型。

下面的代码演示了如何定义并使用类的构造函数：

```c++
// example: class constructor
#include <iostream>
using namespace std;

class Rectangle {
    int width, height;
  public:
    Rectangle (int,int);
    int area () {return (width*height);}
};

Rectangle::Rectangle (int a, int b) {
  width = a;
  height = b;
}

int main () {
  Rectangle rect (3,4);
  Rectangle rectb (5,6);
  cout << "rect area: " << rect.area() << endl;
  cout << "rectb area: " << rectb.area() << endl;
  return 0;
}
```

可以发现，Rectangle类的构造函数与前面那个版本中的set_value()函数的定义相同，但区别在于，程序声明对象时，将自动调用构造函数。

关于使用构造函数的方法，有两种：

一种是显式调用构造函数：`Rectangle rect = Rectangle(3, 4);`

另一种是隐式调用构造函数：`Rectangle rectb (5, 6);`

> 默认构造函数
>
> 默认构造函数是在未提供显示初始值时，用来创建对象的构造函数，比如下面这条语句就隐式地调用了默认构造函数：
>
> `Rectangle rectc;  //calls default constructor implicitly`
>
> 默认构造函数没有参数，因为声明中不包含值。
>
> 当且仅但没有定义任何构造函数时，编译器才会提供默认构造函数。如果为类提供了构造函数，就需要程序员为其提供默认构造函数。如果提供了非默认构造函数而没有提供默认构造函数，那么下面的声明会报错：
>
> `Rectangle rect;	//error!`
>
> 定义默认构造函数的方法有两种：
>
> 1. 给已有的构造函数的所有参数提供默认值：`Rectangle(int a = 0, int b = 0);`
>
> 2. 或者通过函数重载来定义另一个构造函数——一个没有参数的构造函数
>
>    ```C++
>    Rectangle::Rectangle(){
>    	width = 0;
>    	height = 0;
>    }
>    ```

用构造函数创建对象后，程序负责跟踪该对象，直到其过期为止。对象过期时，程序将自动调用一个特殊的成员函数，即析构函数。析构函数的名称就是在类名上加一个"~"，因此，Rectangle类的析构函数就是`~Rectangle()`，与构造函数不同的是，析构函数没有参数。

通常不应在代码中显式的调用析构函数。

> 如果创建的是静态存储类对象，则其析构函数将在程序结束时自动调用。
>
> 如果创建的是自动存储类对象，则其析构函数将在程序执行完代码块时自动被调用。
>
> 如果对象是通过new创建的，则它将驻留在栈内存或自由存储区中，当使用delete来释放内存时，其析构函数将自动被调用。

在类对象过期时析构函数将自动被调用，因此必须有一个析构函数。如果程序员没有提供析构函数，编译器将隐式地声明一个默认析构函数，并在发现导致对象被删除的代码后，提供默认析构函数的定义。

注意这样的代码：

```c++
Rectangle rect = Rectangle (3, 4);
...
rect = (5, 6);
```

因为rect对象是已经存在了的，因此上面代码的第三行并不是对rect进行初始化，而是将新值赋给它。这是通过让构造函数创建一个新的、临时的对象，然后将其内容复制给对象rect来实现的。随后程序调用析构函数，已删除该临时对象。

> const成员函数
>
> 现在如果要给Rectangle类添加一个成员函数show()，用于输出对象中的数据：
>
> ```c++
> class Rectangle{
>   private:
>     ...
>   public:
>     ...
>     void show();
> };
> 
> void Rectangle::show(){
> 	cout << width << ", " << height<<endl;
> }
> ```
>
> 因为show()的代码无法确保调用对象不被修改——调用对象和const一样，不应被修改。以前通过将函数参数声明为const引用和指向const的指针来解决这种问题。但这里存在的问题是，show()方法中并没有参数。相反，它所使用的对象是由方法调用隐式地提供的。
>
> C++提供的解决方法是将const关键字放在函数参数列表的括号后面。也就是说，show()方法的声明和定义应该是这样的：
>
> ```c++
> class Rectangle{
>   private:
>     ...
>   public:
>     ...
>     void show() const;				//promises not to change invoking object
> };
> 
> void Rectangle::show() const {		//promises not to change invoking object
> 	cout << width << ", " << height<<endl;
> }
> ```
>
> 以这种方式声明和定义的类函数被称为const成员函数。
>
> 就像应尽可能地将const引用和指针用作函数形参一样，只要类方法不修改调用对象，就应将其声明为const。

#### this指针

现在考虑如果要添加一个成员函数，用于比较两个同类对象某一个成员的值，然后返回更高的那个对象，那么这个成员函数的原型应该如下：

`const Rectangle & Rectangle::topval(const Rectangle & r) const;`

也就是说将一个对象作为参数传递给调用方法的对象，然后topval()返回目标对象的引用。

但现在的问题是，如果topval()方法通过比较以后将返回调用方法的对象，应该如何返回呢？这里只有作为参数的对象具有别名r，但是调用方法的对象并没有别名。

C++提供的解决方法是使用this指针，this指针用来指向调用成员函数的对象（this被作为隐藏参数传递给方法）。

如何方法需要引用整个调用对象，则可以使用表达式`*this`。

下面的代码演示了如何使用this指针：

```c++
const Rectangle & Rectangle::topval(const Rectangle & r) const{
	if(width < r.width)
		return r;
	else
		return *this;
}
```

这里需要注意的是，对象可以访问同类的不同对象的私有数据。

#### 类作用域

在前面的第九章介绍了作用域的概念，在那里只介绍了全局和局部两种作用域。可以在全部变量所属文件的任何地方使用它，而局部变量只能在其所属的代码块中使用。而这里将介绍另一种作用域，即类作用域。

在类中定义的名称（如类数据成员名和类成员函数名）就是的作用域为整个类，作用域为整个类的名称之在该类中是已知的，在类外是不可知的。

类作用域意味着，可以在不同类中使用相同的类成员名而不会引起冲突；此外，也不能从外部直接访问类的成员，公有成员也是如此，必须要通过对象调用公有成员。

总之，在类声明或成员函数定义中，可以使用未修饰的成员名称（未限定名称）。在其他情况下，使用类成员名时，必须根据上下文使用直接成员运算符(.)，间接成员运算符(->)或者作用域解析运算符(::)。

> 作用域为类的常量
>
> 由于一个常量对于所有对象来说都是相同的，因此可以创建一个有所有对象共享的常量。
>
> 最先做这样的尝试：
>
> ```c++
> class Bakery{
> 	private:
> 		const int Months = 12;
> 		double costs[Months];
> 		...
> };
> ```
>
> 上面这样的代码是行不通的，因为==类声明只是描述了对象的形式，并没有创建对象。==因此，创建对象之前，将没有用于存储常量的空间。
>
> 实际可行的方法有两种：
>
> 1. 在类声明中声明一个枚举：`enum {Months = 12};`
>
>    用这种方式声明枚举并不会创建类数据成员，也就是说，所有对象中都不包含这个枚举。Months只是一个符号名称，在作用域为整个类的代码中遇到它时，编译器将用12代替它。
>
> 2. 使用关键字static：`static const int Months = 12;`
>
>    这将创建一个名为Months的常量，该常量将与其他静态变量存储在一起，而不是存储在对象中。因此只有一个Months常量，而被所有Bakery对象共享。

#### 抽象数据类型

上面所介绍的类都太具体了，而实际中更倾向于通过定义类来表示更通用的概念，这就是所谓的抽象数据类型(abstract data type, ADT)。ADT以通用的方式描述数据类型，而没有引入语言或实现细节。

### CH11. 使用类

#### 运算符重载

运算符虫子啊是一种形式的C++多态。前面已经介绍过C++是如何使用户能够定义多个名称相同但特征标（参数列表）不同的函数。这被称为函数重载或者函数多态，旨在允许用同名的函数来完成相同的基本操作，即使这个操作被用于不同的数据类型。

而运算符重载将重载的概念扩展到运算符上，允许赋予C++运算符多种含义。

要重载运算符，需要使用被称为运算符函数的特殊函数形式。运算符函数的通用格式如下：

*type operator sign(parameters) {/\* ...body... /\*}*

其中type是运算符函数的返回类型，关键字operator用于指明这个函数是运算符函数，sign是具体的运算符（可以是+，\*，或者其他可重载的运算符）

下面代码演示了如何重载一个运算符：

```c++
// overloading operators example
#include <iostream>
using namespace std;

class CVector {
  public:
    int x,y;
    CVector () {};
    CVector (int a,int b){ x = a; y = b;}
    CVector operator + (const CVector&);
};

CVector CVector::operator+ (const CVector& param) {
  CVector temp;
  temp.x = x + param.x;
  temp.y = y + param.y;
  return temp;
}

int main () {
  CVector foo (3,1);
  CVector bar (1,2);
  CVector result;
  result = foo + bar;
  //or calls the operator function:
  //result = foo.operator+(bar);
  cout << result.x << ',' << result.y << '\n';
  return 0;
}
```

注意上面的代码中可以通过运算符函数的名称来调用运算符函数。

此外，还可以使用非成员函数的形式对运算符函数进行重载：

```c++
// non-member operator overloads
#include <iostream>
using namespace std;

class CVector {
  public:
    int x,y;
    CVector () {}
    CVector (int a, int b) : x(a), y(b) {}
};

CVector operator+ (const CVector& lhs, const CVector& rhs) {
  CVector temp;
  temp.x = lhs.x + rhs.x;
  temp.y = lhs.y + rhs.y;
  return temp;
}

int main () {
  CVector foo (3,1);
  CVector bar (1,2);
  CVector result;
  result = foo + bar;
  cout << result.x << ',' << result.y << '\n';
  return 0;
}
```

大多数运算符都可以通过成员或非成员函数进行重载，非成员函数不是由对象调用的，它使用的所有值（包括对象）都是显式参数。

对运算符的重载存在几个限制：

1. 重载后的运算符必须至少有一个操作数是用户定义的类型这将防止用户为标准类型重载运算符。
2. 使用运算符时不能违反运算符原来的句法规则，比如，不能修改运算符的优先级。
3. 不能创建新的运算符。
4. 有些运算符不可重载，有一部分可重载的运算符不可通过非成员函数重载。

#### 友元

C++控制对类的对象私有部分的访问。通常公有类方法提供唯一的访问途径，但是有时候这种限制太严格，以致于不适合特定的编程问题。所以C++提供了另外一种形式的访问权限：友元。

友元有三种：

- 友元函数
- 友元类
- 友元成员函数

通过让函数成为类的友元，可以赋予该函数与类的成员函数相同的访问权限。

这里先介绍友元函数，另外两种友元将在后面的章节进行介绍。

> 这里可能会有疑惑，既然要让函数与类的成员函数具有相同的访问权限，那么为什么不让这个函数称为类的成员函数呢？
>
> 这是因为，类的成员函数只能通过对象来调用方法。这在有些情况下并不太方便。

创建一个友元函数的第一步是在类的声明中添加友元函数的原型，并在原型的前面添加关键字friend。

这里需要注意的两点是：

- 虽然友元函数是在类声明中声明的，但它不是成员函数，因此不能使用成员运算符来调用。并且也不要在定义友元函数的时候添加作用域解析符。
- 虽然友元函数不是成员函数，但它的访问权限与成员函数相同。

下面代码演示了如何创建并使用一个友元函数：

```c++
// friend functions
#include <iostream>
using namespace std;

class Rectangle {
    int width, height;
  public:
    Rectangle() {}
    Rectangle (int x, int y) : width(x), height(y) {}
    int area() {return width * height;}
    friend Rectangle duplicate (const Rectangle&);
};

Rectangle duplicate (const Rectangle& param)
{
  Rectangle res;
  res.width = param.width*2;
  res.height = param.height*2;
  return res;
}

int main () {
  Rectangle foo;
  Rectangle bar (2,3);
  foo = duplicate (bar);
  cout << foo.area() << '\n';
  return 0;
}
```

在定义友元函数的时候不需要再添加关键字friend。

##### 重载<<运算符

这里需要指出的一点是，如果使用一个类的成员函数来重载运算符，那么在使用这个运算符的时候，这个类的对象将是第一个操作数。

如果需要用某个运算符处理这个类的对象，同时又满足这个类的对象不是第一个操作数的情况，那么需要用到友元函数。

所以可以这样重载<<运算符：

```C++
class Rectangle{
	private:
		int width, height;
	public:
		...
		friend void operator<<(ostream &, const Rectangle &);
};

void operator<<(ostream & os, const Rectangle & r){
	os << "width: " << r.width << ", height: " << r.height << endl;
}
```

然后可以使用`cout << rect; //where rect is Rectangle object`来打印数据。

调用`cout << rect;`应使用cout对象本身，而不是它的拷贝，因此该函数按引用（而不是按值）来传递该对象。

但是这样的版本存在一个问题是：

`cout << rect;`

这样的语句能够正常工作，但是下面的语句会出现问题：

`cout << "Information of rect: " << rect << "\n"; //can't work`

要理解出现这个问题的原因需要先了解一般形式的cout输出。

```c++
int x = 5, y = 8;
cout << x << y;
```

cout 从左至右读取输出语句，意味着它等同于`(cout << x) << y;`

按照iostream中的定义，<<运算符要求运算符的左侧是一个ostream对象。那么也就意味着上面的语句中的`(cout << x)`指向一个ostream对象的引用。

所以对重载<<运算符的友元函数也可以采用相同的方法：

```C++
class Rectangle{
	private:
		int width, height;
	public:
		...
		friend ostream & operator<<(ostream &, const Rectangle &);
};

ostream & operator<<(ostream & os, const Rectangle & r){
	os << "width: " << r.width << ", height: " << r.height << endl;
	return os;
}
```

这样，cout在输出完rect对象后依然还是cout对象，从而可以进一步继续输出。

#### 类的自动转换和强制类型转换

[Type conversions - C++ Tutorial](http://www.cplusplus.com/doc/tutorial/typecasting/)中对C++中的类型转换作了一个比较详细的总结。

而这里将是从两个方向来讲解C++中与类相关的类型转换。

##### other type to class

将一个标准类型变量赋值给另一种标准类型的变量时，如果这两种类型兼容，则C++会自动将这个值转换为接收变量的类型。

可以将类定义成与基本类型或另一个类相关，使得从一种类型转换成另一种类型是有意义的。在这种情况下，程序员可以指示C++如何自动进行转换，或通过强制类型转换来完成。

下面定义一个类，用于将磅转换为英石：

```c++
#include <iostream>
#include "stonewt.h"

using std::cout;

class Stonewt{
    private:
        enum {Lbs_per_stn = 14};
        int stone;
        double pds_left;
        double pounds;
    public:
        Stonewt(double lbs);
        Stonewt(int stn, double lbs);
        Stonewt();
        ~Stonewt();
};

Stonewt::Stonewt(double lbs){
    stone = int(lbs) / Lbs_per_stn;
    pds_left = int(lbs) % Lbs_per_stn + lbs - int(lbs);
    pounds = lbs;
}

Stonewt::Stonewt(int stn, double lbs){
    stone = stn;
    pds_left = lbs;
    pounds = stn * Lbs_per_stn;
}

Stonewt::Stonewt() {
    stone = pds_left = pounds = 0;
}

Stonewt::~Stonewt(){}
```

在C++中，只接受一个参数的构造函数为将类型与该参数相同的值转换为类提供了模板。

所以在上面的这段代码中`Stonewt(double lbs);`构造函数可用于将double类型的值转换称为Stonewt类型：

也就是说可以编写这样的代码：

```C++
Stonewt myCat;
myCat = 19.6;	//use Stonewt(double) to convert 19.6 to Stonewt
```

上面的代码中，程序使用构造函数`Stonewt(double)`构造一个临时的对象，并使用19.6初始化这个对象。随后，采用逐成员复试的方法将该临时的对象中的内容复制到myCat中。这一过程称为隐式转换，因为这个过程是自动进行的。

需要注意的是，只有只接受一个参数的构造函数才能作为转换函数。下面的构造函数中有两个参数所以不能用作转换函数：

`Stonewt(double, int);   //not a conversion function`

但是可以通过其中一个参数添加默认值的方式使其称为转换函数：

`Stonewt(double lbs = 0, int stn);	//int-to-Stonewt conversion`

使用构造函数用作自动类型转换函数在有些情况下会造成一些错误。因此，C++新增了一个关键字explicit用于关闭这种特性：

`explicit Stonewt(double);	//not a conversion function`

虽然关闭了这种自动的隐式转换，但是仍然可以通过强制类型转换，将一个double类型的值转换称为Stonewt类：

```c++
Stonewt myCat;
myCat = 19,6;			//not valid if Stonewt(double) is declared as explicit
myCat = (Stonewt) 19.6;	//ok, C-like form of explicit typecast
```

下面详细介绍最后一点。函数原型化提供的参数匹配过程，允许使用`Stonewt(double);`构造函数来转换其他数值类型。也就是说，下面两条语句都首先将int转换为double，然后再使用`Stonewt(double)`构造函数：

`Stonewt Jumbo(7000);`

`Jumbo = 7300;`

不过当且仅当转换不存在二义性时，才会进行这种而不转换。也就是说，如果这个类还定义了构造函数`Stonewt(long);`，则编译器将拒绝这些语句，可能指出，int可被转换为long或者double，因此调用存在二义性。

##### class to other type

前面所说的与类相关的类型转换都是将一个数值转换为对象。这里将介绍一种将对象转换为另一种类型的数据的方法。

构造函数只用于从某种类型到这个类所属的类型的转换。

如果要进行相反方向的转换，必须使用特殊的C++运算符函数——转换函数。

这里给出转换函数的通用格式：

*operator typeName();*

其中operator是一个关键字，用于指出这是一个运算符函数，typeName指出要转换成的类型。

在定义转换函数的时候需要注意下面几点：

1. 转换函数必须是类方法
2. 转换函数不能指定返回类型
3. 转换函数不能有参数

例如，将上面代码中Stonewt类转换称为double类型数值的转换函数的原型是`operator double();`

当然，为了确保转换函数不会修改对象，需要将转换函数定义为const成员函数。此外，虽然转换函数并没有返回类型，但是依然需要返回值：

```C++
class Stonewt{
	private:
		...
	public:
		...
		operator double() const;
};
...
Stonewt::operator double() const{
	return pounds;
}
```

在定义了转换函数后，可以使用隐式转换对属于这个类的对象进行转换，也可以使用强制转换进行转换：

```c++
Stonewt poppins(9, 2.8);
double p_wt = poppins;		//implicit conversion
cout << "p_wt" << endl;
cout << (double) poppins << end;	//explicit conversion
```

同样，将类所属的对象转换为其他类型的数据也会存在二义性问题，而导致编译出错。

如果不希望对象通过隐式转换为了其他形式的数据，同样可以将转换函数声明为explicit来组织这种隐式转换的发生：`explicit operator double() const;`

**总结**：

- 只有一个参数的类构造函数用于将类型与该参数相同的值转换为类定义的类型。
- 被称为转换函数的特殊类成员运算符函数，用于将类对象转换为其他类型。
- explicit可用于只有一个参数的类构造函数和转换函数，用于阻止隐式转换的发生。

### CH12. 类的动态内存分配

#### 动态内存和类

这一章节主要通过实现一个string类来描述对象的动态内存分配中存在的问题：

```C++
class StringBad{
    private:
        char * str;
        int len;
        static int num_strings;
    public:
        StringBad(const char * s);
        StringBad();
        ~StringBad();
        friend std::ostream & operator<<(std::ostream & os, const StringBad & st);
};
```

上面的这段声明，需要注意的有两个点：

首先，StringBad类使用char指针（而不是char数组）来存储字符串，这意味着类声明没有为字符串本身分配存储空间，而是在构造函数中使用new来为字符串分配空间。这避免了在类声明中预先定义字符串的长度。

其次，将num_strings声明为静态存储的变量。静态类成员有一个特点：无论创建了多少个对象，程序都只创建一个静态类变量副本。也就是说，类的所有对象共享一个静态成员。

> 关于静态类成员的初始化需要注意的是，不能再类声明中初始化静态成员变量，这是因为声明描述了如何分配内存，但并没有分配内存。
>
> 对于静态类成员，可以在类声明之外的地方使用单独的语句来进行初始化，这是因为静态类成员是单独存储的，而不是对象的组成部分。初始化语句需要指出静态类成员的类型并使用作用域运算符，但是不需要再次使用static关键字，比如上面声明中的num_strings的初始化语句为：
>
> `int StringBad::num_strings = 0;`

下面的代码用于实现上面声明中的类方法：

```c++
//stringbad.cpp -- StringBad class methods
#include <cstring>
#include "stringbad.h"

using std::cout;

int StringBad::num_strings = 0;

StringBad::StringBad(const char * s){
     len = strlen(s);
     str = new char[len + 1];
     std::strcpy(str, s);
     num_strings++;
     cout << num_strings << ": \" " << str << "\" object created\n";
}

StringBad::StringBad(){
    len = 4;
    str = new char[4];
    std::strcpy(str, "C++");
    num_strings++;
    cout << num_strings << ": \" " << str << "\" default object created\n";
}

StringBad::~StringBad(){
    cout << "\" " << str << "\" object deleted, ";
    --num_strings;
    cout << num_strings << " left\n";
    delete [] str;
}

std::ostream & operator<<(std::ostream & os, const StringBad & st){
    os << st.str;
    return os;
}
```

这里使用动态内存分配的方法实现了类的各种方法，并且为了在后面的实验中表现出一些特性，在类的构造函数和析构函数中输出了相关内容。

接下来，使用这个类，并且可以从输出结果中看出一些问题：

```c++
#include <iostream>
using std::cout;
#include "stringbad.h"

void callme1(StringBad &);
void callme2(StringBad);

int main(){
    using std::endl;
    {
        cout << "Starting an inner block.\n";
        StringBad headline1("Celery Stalks at Midnight");
        StringBad headline2("Lettuce Prey");
        StringBad sports("Spinach Leaves Bowl for Dollars");
        cout << "headline1: " << headline1 << endl;
        cout << "headline2: " << headline2 << endl;
        cout << "sports: " << sports << endl;
        callme1(headline1);
        cout << "headline1: " << headline1 << endl;
        callme2(headline2);
        cout << "headline2: " << headline2 << endl;
        cout << "Initialize one object to another:\n";
        StringBad sailor = sports;
        cout << "sailor: " << sailor << endl;
        cout << "Assign one object to another:\n";
        StringBad knot;
        knot = headline1;
        cout << "knot: " << knot << endl;
        cout << "Exiting the blocks.\n";
    }
    cout << "End of main()\n";
    return 0;
}

void callme1(StringBad & rsb){
    cout << "String passed by reference:\n";
    cout << " \"" << rsb << "\"\n";
}

void callme2(StringBad sb){
    cout << "String passed by value:\n";
    cout << " \"" << sb << "\"\n";
}
```

这里需要注意的是，在main()函数中使用代码块的目的是为了在程序推出前能看到对象调用析构函数的过程。

开始的时候这个程序函数正常运行的，构造函数指出自己创建了3个StringBad对象，并为这些对象进行了编号，然后程序使用重载运算符>>列出了这些对象。

然后接着，程序将headline1传递给callme1()函数：

`callme1(headline1);`

其输出结果为：

`String passed by reference:`

`"Celery Stalks at Midnight"`

并在调用后重现显示headline1：

`cout << "headlien1: " << headline1 << endl;`

其输出结果为：

`headline1: Celery Stalks at Midnight`

到目前为止，程序都是正常运行的。

随后在执行下面的代码，以headline2为参数调用callme2()函数：

`callme2(headline2);`

输出为：

`String passed by value:`

`"Lettuce Prey"`

`"Lettuce Prey" object deleted, 2 left`

然后再次打印headline2中的内容：

`cout << "headline2: " << headline2 << endl;`

其输出结果为：

`headline2: ▒P2▒`

这里，程序出现异常。

最后，在为每一个创建的对象自动调用析构函数时，出现了灾难性的结果：

```c++
Exiting the blocks.
" Celery Stalks at Midnight" object deleted, 2 left
" Spinach Leaves Bowl for Dollars" object deleted, 1 left
" Spinach Leaves Bowl for Dollars" object deleted, 0 left
"@g" object deleted, -1 left
"-|" object deleted, -2 left
```

这段程序有两个问题：

1. 输出中出现了非法内存访问的问题
2. 最后几行的输出的num_strings为负数，也就是说存在技术错误

##### 特殊成员函数

在弄清楚导致这些问题的原因前，需要知道几个类的特殊成员函数

如果没有给出定义，C++会自动提供下面这些成员函数：

- 默认构造函数

  如果没有提供任何构造函数，C++将创建默认构造函数。编译器将提供一个不接收任何参数，也不执行任何操作的构造函数（默认的默认构造函数）。带参数的构造函数也可以是默认构造函数，只要所有参数都有默认值。

- 默认析构函数

- 复制构造函数

- 赋值运算符

- 地址运算符

复制构造函数用于将一个对象复制到新创建的对象中，也就是说，它用于初始化过程中，以及按值传递参数的过程中，而不是常规的赋值过程中。复制构造函数的原型格式如下：

*Class_name(const Class_name &);*

它接受一个指向类对象的常量引用作为参数。

关于复制构造函数，需要知道两点，何时调用以及有何功能。

**何时调用复制构造函数？**

可以概括为使用一个对象初始化一个先创建的对象时，会调用复制构造函数，假设motto是一个已存在的对象，那么以下面三种方式用一个已存在对象初始化对象都会调用复制构造函数：

`StringBad ditto(motto);`

`StringBad ditto = motto;`

`StringBad ditto = StringBad(motto);	//call the copy constructor explicitly`

`StringBad * pStringBad = new StringBad(motto);`

除了初始化过程，按值传递意味着创建原始变量的一个副本。编译器生成临时对象时，也将使用复制构造函数。

**默认复制构造函数的功能：**默认的复制构造函数逐个复制非静态成员，复制的成员的值。这种复制方法也成为浅复制。所以在使用默认构造函数的情况下，下面的这行代码

`StringBad sailor = sports;`

等价于：

`StringBad sailor;`

`sailor.str = sports.str;	//pointer value assignment`

`sailor.len = sports.len;`

**默认的赋值运算符：**C++允许类对象赋值，这是通过自动为类重载赋值运算符实现的。赋值运算符函数的通用原型如下：

*Class_name & Class_name::operator=(const Class_name &);*

将已有的对象赋值给另一个对象时，将使用重载的赋值运算符。

与赋值构造函数相似，赋值运算符的隐式实现也对成员进行逐个复制。

##### 回到StringBad

了解类中的特殊成员函数后，就可以弄清上面那个程序的问题的原因所在了：

在将对象使用按值传递的方式传递给函数callme2()后，使用了默认构造函数，生成了一个临时的对象（形参），这个对象中的指针和实参中的指针指向同一块内存，在函数执行完毕后，临时变量调用析构函数，释放了内存，所以再次打印实参中的内容时，会显示乱码。

此外，使用默认的赋值运算符也是同样的道理，两个先创建的对象用两个原有的对象通过赋值运算符进行初始化，从而造成对象个数上的错误。

解决类设计中这种问题的方法是进行深度复制(deep copy)，即显示定义复制构造函数并且重载赋值运算符。

下面是改良后的StringBad类声明：

```c++
class StringBad{
    private:
        char * str;
        int len;
        static int num_strings;
    public:
        StringBad(const char * s);
        StringBad(const StringBad & sb);
        StringBad();
        ~StringBad();
        StringBad & operator=(const StringBad & sb);
        friend std::ostream & operator<<(std::ostream & os, const StringBad & st);
};
```

以及这段代码中的方法的实现：

```c++
#include <cstring>
#include "stringbad.h"

using std::cout;

int StringBad::num_strings = 0;

StringBad::StringBad(const char * s){
     len = strlen(s);
     str = new char[len + 1];
     std::strcpy(str, s);
     num_strings++;
     cout << num_strings << ": \" " << str << "\" object created\n";
}

StringBad::StringBad(){
    len = 4;
    str = new char[4];
    std::strcpy(str, "C++");
    num_strings++;
    cout << num_strings << ": \" " << str << "\" default object created\n";
}

StringBad::StringBad(const StringBad & sb){
    num_strings++;
    len = sb.len;
    str = new char[len + 1];
    std::strcpy(str, sb.str);
    cout << num_strings << ": \"" << str << "\" object created\n";
}

StringBad::~StringBad(){
    cout << "\" " << str << "\" object deleted, ";
    --num_strings;
    cout << num_strings << " left\n";
    delete [] str;
}

StringBad & StringBad::operator=(const StringBad & sb){
    if(this == &sb)
        return *this;
    delete [] str;          //free old string
    len = sb.len;
    str = new char [len + 1];
    std::strcpy(str, sb.str);
    return *this;
}

std::ostream & operator<<(std::ostream & os, const StringBad & st){
    os << st.str;
    return os;
}
```

在重载赋值运算符时，需要检查参数中的对象是否就是本对象（不排除用自己给自己赋值的情况），如果参数中的对象就是这个对象本身，那么返回this；否则，先使用new初始化对象自身中的char指针，然后将参数中的对象释放掉。 

> 静态类成员：可以将成员函数声明为静态的，在函数声明中班好关键字static，但是函数定义的时候不需要添加关键字static。关于静态成员函数，需要注意下面两个点：
>
> 首先，不能通过对象嗲用静态成员函数；实际上，静态成员函数甚至不能使用this指针，如果静态成员函数是在公有部分声明的，那么可以使用类名和作用域运算符来调用它。
>
> 其次，由于静态成员函数不与特定的对象关联，因此只能使用静态数据成员。

#### 在构造函数中使用new时应注意的事项

这一节的内容对类与动态内存分配中常见的问题进行了一个简单的总结：

1. 如果在构造函数中使用new来初始化指针成员，则应在析构函数中使用delete。

2. new和delete必须相互兼容。new对应于delete，new[]对应于delete[]

3. 如果有多个构造函数，则必须以相同的方式使用new，要么都带中括号，要么都不带。因为只有一个析构函数，所有构造函数都必须与它兼容。

   然而，可以在一个构造函数使用new初始化指针，而在其他构造函数中将指针初始化为空(0或nullptr)，这是因为delete不论带不带中括号都可以用于空指针

4. 应定义一个复制构造函数，通过深度复制将一个对象初始化为另一个对象。

5. 应定义一个赋值运算符，通过深度复制将一个对象复制给另一个对象。

#### 有关返回对象的说明

当成员函数或者独立的函数返回对象时，有几种返回方式可以选择：

1. 返回指向const对象的引用

   返回对象将调用复制构造函数，而返回引用则不会。因此，返回引用所作的工作更少，效率更高。不过需要注意的是，引用指向的对象应该在调用函数执行时存在。并且如果返回的引用参数都是const，那么返回的引用也必须是const。

2. 返回指向非const对象的引用

   返回引用可以避免调用复制构造函数，这样可以提高效率，并且如果类声明中不存在公有的复制构造函数，那么就必须返回引用。

3. 返回对象

   如果被返回的对象是被调用函数中的局部变量，则不应按引用的方式返回它，因为在被调用函数执行完毕时，局部对象将调用其析构函数。因此，当控制权回到调用函数时，引用指向的对象将不再存在。在这种情况下，应返回对象而不是引用。

4. 返回const对象

   假如错误的写出了这样的代码：`force1 + force2 = net;`，其中force1和force2以及net都是同一类的对象，并且这个类重载了+运算符。

   这样的代码是可行的，首先force1 + force2将创建一个临时的对象，然后net会通过复制运算符覆盖这个临时对象中的内容，当这条语句执行完毕后，临时对象会被删除，所有对象都保持不变。

   如果想要避免上面这种情况，最简单的方法是将+运算符函数的返回类型声明为const。

总之，

如果方法或函数要返回局部对象，则应返回对象，而不是指向对象的引用。在这种情况下，将使用复制构造函数来生成返回的对象。

如果方法或函数要返回一个没有公有复制构造函数的类的对象，它必须返回一个指向这种对象的引用。

最后，有些方法可以返回对象也可以返回对象的引用，这种情况下，首先选择引用，因为返回引用的效率更高。

#### 使用指向对象的指针

指针同样可以指向对象，一旦一个类完成声明，那么这个类就可以成为一种变量类型，所以可以使用这种类型的指针指向这种类型的对象。

和结构一样，由指针指向的对象可以使用->操作符来访问对象中的成员，下面这段代码演示了指向对象的指针的使用示例：

```c++
// pointer to classes example
#include <iostream>
using namespace std;

class Rectangle {
  int width, height;
public:
  Rectangle(int x, int y) : width(x), height(y) {}
  int area(void) { return width * height; }
};

int main() {
  Rectangle obj (3, 4);
  Rectangle * foo, * bar, * baz;
  foo = &obj;
  bar = new Rectangle (5, 6);
  baz = new Rectangle[2] { {2,5}, {3,6} };
  cout << "obj's area: " << obj.area() << '\n';
  cout << "*foo's area: " << foo->area() << '\n';
  cout << "*bar's area: " << bar->area() << '\n';
  cout << "baz[0]'s area:" << baz[0].area() << '\n';
  cout << "baz[1]'s area:" << baz[1].area() << '\n';       
  delete bar;
  delete[] baz;
  return 0;
}	
```

这里需要注意的是，如果对象是使用new创建的，那么只有在显式的使用delete删除对象时，其析构函数才会被调用。

还有一点，使用定位new运算符时，不能使用delete释放对应的内存。所以说如果使用定位new运算符给指向对象的指针分配内存，那么需要显式的调用对象的析构函数：

```c++
char * buffer = new char[BUF];
Rectangle pc = new (buffer) Rectangle(3, 4);
pc1 -> ~Rectangle();
```

对于使用定位new运算符创建的对象，应以与创建顺序相反的顺序进行删除。原因在于，晚创建的对象可能依赖于早创建的对象。

### CH13. 类继承

面向对象编程的主要目的之一时提供可重用的代码，代码的可重用可以通过类继承的属性来实现，通过类继承的属性，可以在原来的基类上添加一些新的数据或方法，也可以重写一些基类中的方法的实现。

#### 一个简单的基类

从一个类派生出另一个类时，原始类称为基类，继承类称为派生类。为了说明类继承，首先需要一个基类：

```c++
class TableTennisPlayer{
    private:
        string firstname;
        string lastname;
        bool hasTable;
    public:
        TableTennisPlayer(const string & fn = "none",
                const string & ln = "none", bool ht = false)
            : firstname(fn), lastname(ln), hasTable(ht) {}
        void Name() const;
        bool HasTable() const {return hasTable;}
        void ResetTable(bool v) {hasTable = v;}
};

void TableTennisPlayer::Name() const {
    std::cout<< lastname << ", " << firstname;
}
```

需要注意这里基类的构造函数采用了成员初始换列表的方法。其通用格式为：

*Constructor(parameter-list) : member1(parameter1), member2(parameter2), ... {}*

关于成员初始化列表需要注意的是，成员初始化列表只能用于构造函数，并且构造函数中的参数要么全都有默认值，要么全都没有默认值。

接下来可以由基类派生出另一个类：

`class RatedPlayer : public TableTennisPlayer{ ... };`

上面这段代码中，冒号用于指出，RatedPlayer的基类是TableTennisPlayer。而关键字public用于指出这个继承关系是公有继承，基类的公有成员将成为派生类的公有成员；基类的私有成员也会成为派生类的一部分，但是只能通过基类提供的接口进行访问。

在执行完上面的这行代码后，派生出的类RatedPlayer对象将存储基类的数据成员，并且派生类对象可以使用基类的方法。

在此基础上，还需要派生类添加自己的构造函数，而且派生类还可以根据需要添加额外的数据成员和成员函数。

这里需要强调的一点是，派生类并不呢个直接访问积累的私有成员，而必须通过基类方法进行访问。所以在派生类构造函数中初始化属于基类的数据成员时，需要调用基类的构造函数或者复制构造函数。

==这也就说明基类的构造函数至少可以有两种原型==

```c++
class RatedPlayer : public TableTennisPlayer{
	private:
	unsigned int rating;
	public:
    //using the constructor of the bass class
	RatedPlayer(unsigned int r = 0, const string & fn = "none", 
                const string & ln = "none", bool ht = false)
        : rating(r), TableTennisPlayer(fn, ln , ht){}
    //using the copy constructor of the base class
    RatedPlayer(unsigned int r, const TableTennisPlayer & tp)
        : rating(r), TableTennisPlayer(tp){}
    void ResetRating(unsigned int r) {rating = r;}
}
```

必须要在成员初始化列表中调用基类的显示的调用基类的构造函数，否则编译器将调用默认构造函数：

```c++
RatedPlayer::RatedPlayer(unsigned int r, const string & fn, 
                        const string & ln, bool ht) : rating(r) {}
//is equivalent to
RatedPlayer::RatedPlayer(unsigned int r, const string & fn,
                        const string & ln, bool ht) 
    : TableTennisPlayer(), rating (r) {}
```

而对于使用复制构造函数进行初始化的派生类构造函数，如果基类中并没有定义复制构造函数，那么编译器将生成一个默认的复制构造函数，采用成员复制。不过这里采用成员复制是没有问题的，因为成员复制将使用string类的复制构造函数类复制string成员。

**总之，有关派生类构造函数的要点如下**：

- 首先创建基类对象，也即先调用基类的构造函数
- 派生类的构造函数应通过成员初始化列表将基类信息传递给基类构造函数
- 派生类构造函数应初始化派生类新增的数据成员

此外，在派生类对象过期时，程序将采用和创建派生类对象时相反的顺序，即先调用派生类的析构函数，然后再调用基类的析构函数。

##### 派生类和基类之间的关系

派生类对象能够调用基类中的声明为public的方法：

```c++
RatedPlayer rplayer(1140, "Mallory", "Dack", true);
rplayer.Name();
```

此外，还有一个比较重要的关系是，基类的指针或者引用可以在不进行显示类型转换的情况下，之下派生类的对象。

然而，基类的指针或者引用只能调用基类的方法。

并且这种关系是单向的，也就是说不能使用派生类的指针或者引用指向基类的对象。

这种引用的兼容性使得可以使用基类对象初始化派生类对象。比如有下面这样的代码：

```c++
RatedPlayer olaf1(1840, "Olaf", "Loaf", true);
TableTennisPlayer olaf2(olaf1);
```

要初始化olaf2，匹配的构造函数原型如下：

`TableTennisPlayer(const RatedPlayer &);`

但是，基类中并没有定义这样的函数。但是基类中有这样的构造函数：

`TableTennisPlayer(const TableTennisPlayer &);`

形参是基类引用，基类的引用可以指向派生类的对象，因此，可以调用上面个的这个构造函数进行初始化。

同样，也可以使用赋值运算符将派生类对象赋值给基类对象：

```c++
RatedPlayer olaf(1840, "Olaf", "Loaf", true);
TableTennisPlayer winner;
winner = olaf;
```

在这种情况下，程序将使用隐式重载赋值运算符：

`TableTennisPlayer & operator=(const TableTennisPlayer &) const;`

同样，形参为基类的引用，所以实参可以是派生类对象。

#### 继承：is-a关系

公有继承是最常用的继承方式，它建立一种is-a关系，即派生类对象也是一个基类对象，可以对基类对象执行的任何操作，也可以对派生类对象执行。将公有继承的关系描述为is-a-kind-of关系可能更准确，但是通常使用术语is-a关系。

公有继承不建立has-a关系，这种关系通常由对象成员或者保护继承实现。关于has-a关系在后面会进行详细的介绍。

公有继承不能建立is-like-a关系，也就是说，它不采用明喻。

公有继承不建立is-implemented-as-a（作为...来实现）关系。例如，可以使用数组来实现栈，但从Array类派生出Stack类是不合适的，因为栈不是数组。

公有继承不建立uses-a关系。

#### 多态公有继承

前面所说的通过函数不同的特征标重载函数属于C++中的一种多态。

而在C++类继承中还有一种多态，就是说同一个方法在派生类和基类中的行为是不同的。换句话说，方法的行为取决于调用该方法的对象。

下面的两种机制用于实现多态公有继承：

- 在派生类中重新定义基类方法
- 使用虚方法

这里通过一个例子来更好地介绍多态公有继承中的一些概念，下面的代码定义了两个具有公有继承关系的两个类，基类Brass是一种基本的支票账户，BrassPlus是一个具有透支额度的支票账户：

```c++
class Brass{
    private:
        std::string fullName;
        long acctNum;
        double balance;
    public:
        Brass(const std::string & s = "Nullbody", long an = -1, double bal = 0.0)
            : fullName(s), acctNum(an), balance(bal) {}
        void Deposit(double amt);
        virtual void Withdraw(double amt);
        virtual void ViewAcct() const;
    	double Balance() const;
        virtual ~Brass() {};
};

class BrassPlus : public Brass{
    private:
        double maxLoan;
        double rate;
        double owesBank;
    public:
        BrassPlus(const std::string & s = "Nullbody", long an = -1,
                double bal = 0.0, double ml = 500, double r = 0.11125)
            : Brass(s, an, bal), maxLoan(ml), rate(r), owesBank(0.0) {}
        BrassPlus(const Brass & ba, double ml = 500, double r = 0.11125)
            : Brass(ba), maxLoan(ml), rate(r), owesBank(0.0) {}
        virtual void ViewAcct() const;
        virtual void Withdraw(double amt);
        void ResetMax(double m) {maxLoan = m;}
        void ResetRate(double r) {rate = r;}
        void ResetOwes() {owesBank = 0;}
};
```

上面这段代码中，派生类BrassPlus在基类Brass的基础上，新添了部分数据成员并重写了类方法ViewAcct()和Withdraw()。

这里需要注意关键字virtual，如果将一个类方法声明为virtual，那么如果使用一个基类的指针或者引用调用派生类对象中的virtual方法时，将使用派生类中定义的方法；如果没有将方法声明为virtual，那么使用基类的指针或者引用调用派生类对象的中的方法时，将使用基类中定义的方法。

方法在基类中被声明为virtual后，它在派生类中将自动称为虚方法。然而，在派生类声明中使用关键字virtual指出哪些方法是虚方法也不失为一种好习惯。

此外，还有一点需要注意的是，上面的类声明中，将基类的析构函数声明为virtual，这样做是为了确保释放派生类时，按正确的顺序调用析构函数。

下面对类中的方法进行实现：

```c++
#include <iostream>
#include "brass.h"
using std::cout;
using std::endl;
using std::string;

typedef std::ios_base::fmtflags format;
typedef std::streamsize precis;
format setFormat();
void restore(format f, precis p);

void Brass::Deposit(double amt){
    if(amt < 0)
        cout << "Negative deposit not allowed; deposit is cabcelled.\n";
    else
        balance += amt;
}

void Brass::Withdraw(double amt){
    format initialState = setFormat();
    precis prec = cout.precision(2);
    if(amt < 0)
        cout << "Withdraw must be positive;" << "Withdraw cancelled.\n";
    else if(amt <= balance)
        balance -= amt;
    else
        cout << "Withdraw amount of $" << amt << " exceeds your balance.\n" << "Withdraw cancelled.\n";
    restore(initialState, prec);
}

double Brass::Balance() const{
    return balance;
}

void Brass::ViewAcct() const{
    format initialState = setFormat();
    precis prec = cout.precision(2);
    cout << "Client: " << fullName << endl;
    cout << "Account Number: " << acctNum << endl;
    cout << "Balance: $" << balance << endl;
    restore(initialState, prec);
}

void BrassPlus::ViewAcct() const{
    format initialState = setFormat();
    precis prec = cout.precision(2);
    Brass::ViewAcct();
    cout << "Maximum loan: $" << maxLoan << endl;
    cout << "Owed to bank: $" << owesBank << endl;
    cout.precision(3);
    cout << "Loan Rate: " << 100 * rate << "%\n";
    restore(initialState, prec);
}

void BrassPlus::Withdraw(double amt){
    format initialState = setFormat();
    precis prec = cout.precision(2);

    double bal = Balance();

    if(amt <= bal)
        Brass::Withdraw(amt);
    else if(amt <= bal + maxLoan - owesBank){
        double advance = amt - bal;
        owesBank += advance * (1.0 + rate);
        cout << "Bank advance: $" << advance << endl;
        cout << "Finance charge: $" << advance * rate << endl;
        Deposit(advance);
        Brass::Withdraw(amt);
    }
    else
        cout << "Credit limit exceeded. Transaction cancelled.\n";
    restore(initialState, prec);
}

format setFormat(){
    return cout.setf(std::ios_base::fixed, std::ios_base::floatfield);
}

void restore(format f, precis p){
    cout.setf(f, std::ios_base::floatfield);
    cout.precision(p);
}
```

>[ios_base - C++ Reference](http://www.cplusplus.com/reference/ios/ios_base/)，这是C++ reference中关于ios_base的介绍。

基类和派生类中具有两个版本的ViewAcct()定义表明将有两个独立的方法定义。在同一个作用域（这里是派生类的作用域）中使用限定名对这两种方法进行区别，即基类方法的限定名是Brass::ViewAcct()，派生类方法的限定名是BrassPlus::ViewAcct()。如果作用域不存在冲突那么就不需要使用限定名称，在派生类的作用域中，程序会默认使用派生类的方法。

还有一点比较复杂的就是virtual关键字。如果方法是通过引用或指针而不是对象调用的，在这种情况下，如果没有使用关键字virtual，程序将根据引用类型或指针类型选择方法；如果使用了关键字virtual，程序将根据引用或指针指向的对象的类型来选择方法。

最后一点就是基类中将析构函数声明为virtual，其原因正是virtual关键字的作用：如果析构函数不是虚的，则将只调用对应于指针类型的析构函数。如果析构函数是虚的，将调用相应对象类型的析构函数。

#### 静态联编和动态联编

联编(binding)：或者称为绑定，就是将源代码中的函数调用解释为执行特定的函数代码块的过程。

在编译过程中进行联编被称为静态联编或者早期联编。

但是对于虚函数，编译器必须生成能够在程序运行时选择正确的虚方法的代码，这被称为动态联编，又被称为晚期联编。

如果没有将方法声明为虚方法，则程序根据指针类型进行联编，这一过程可以在编译时完成，所以采用静态联编。

如果将方法声明为虚方法，那么只有在运行时才知道指针指向的对象类型，所以只能在运行时完成联编，即动态联编。

虽然动态联编能够在程序运行时进行决策，但这种联编方法必须采取一些方法来跟踪基类指针或引用指向的对象类型，这增加了额外的处理开销。

> 虚函数的工作原理：
>
> 编译器处理虚函数的方法是：给每个对象添加一个隐藏成员。隐藏成员中保存了一个指向函数地址数组的指针。这种数组称为虚函数表(virtual function table, vtbl)。虚函数表中存储了为类对象进行声明的虚函数的地址。
>
> 基类对象包含一个指针，该指针指向基类中所有虚函数的地址表。派生类对象将包含一个之想独立地址比啊的指针，如果派生类提供了虚函数的新定义，该虚函数表将保存新函数的地址；如果派生类没有重新定义虚函数，该vtbl将保存函数原始版本呢的地址。如果派生类定义了新的虚函数，则该函数的地址也将被添加到vtbl中。
>
> 调用虚函数是时，程序将查看存储在对象中的vtl地址，然后转向相应的函数地址表。

关于虚函数，还需要知道的是：

- 构造函数不能是虚函数，因为这样做并没有什么意义；
- 友元函数也不能是虚函数，因为友元不是成员函数，虚函数必须是成员函数。
- 如果函数没有被重新定义，那么就不必将其声明为虚函数。

重新定义继承的方法并不是重载。如果重新定义派生类中的函数，将不只是使用相同的函数参数列表覆盖基类声明，无论参数列表是否相同，这样的操作都将隐藏所有同名的基类方法。

所以这里引出两条经验规则：

1. 如果重新定义继承的方法，应确保与原来的圆形完全相同，但如果返回类型是基类指针或引用，则可以修改为指向派生类的引用或者指针。即重新定义继承的方法时可以修改方法返回的类型，这称为返回类型协变：

   ```c++
   class Dwelling{
   	public:
   	virtual Dwelling & build(int);
   	...
   };
   
   class Hovel : public Dwelling{
   	public:
   	virtual Hovel & build(int);		//same function signature
   	...
   };
   ```

   这里允许方法的返回类型随着类类型的变化而变化。

2. 如果基类声明中的方法被重载，则应在派生类中重新定义所有的基类版本。

   ```c++
   class Dwelling{
   	public:
   	virtual void showperks(int) const;
   	virtual void showperks(double) const;
   	virtual void showperks() const;
   	...
   };
   
   class Hovel : public Dwelling{
   	public:
   	virtual void showperks(int) const;
   	virtual void showperks(double) const;
   	virtual void showperks() const;
   	...
   };
   ```

   如果只重新定义一个版本，则另外两个版本将被隐藏，派生类对象将无法使用它们。需要注意的是，如果不需要修改，则新定义可直接调用基类版本

   `void Hovel::showperks() const {Dwelling::showperks();}`

#### 访问控制

介绍了类的继承后，可以进一步了解类成员各种访问权限的区别了，下面的表格给出了三种访问权限之间的区别：

| Access                      | public | protected | private |
| --------------------------- | ------ | --------- | ------- |
| member of the same class    | yes    | yes       | yes     |
| member of the derived class | yes    | yes       | no      |
| not members                 | yes    | no        | no      |

这里所说的"not member"是指类作用域之外的地方。

#### 抽象基类

如果一个类派生出多个派生类，这些派生类都共有同一个方法，但是不同的派生类的方法的具体实现不同，所以在基类并不能给出具体的实现。

C++通过使用纯虚函数(pure virtual function)提供为实现的函数。纯虚函数的声明在虚函数声明的基础上结尾处为"=0"。

当类声明中包含纯虚函数时，则不能创建该类的对象。包含纯虚函数的类只用作基类。

有一点需要注意的是，可以在方法文件中给出纯虚函数的定义，也可以不进行定义。

下面代码用于演示一个抽象基类(abstract base class, ABC)的定义，头文件

```c++
//acctabc.h -- bank account classes
#ifndef ACCTABC_H_
#define ACCTABC_H_
#include <iostream>
#include <string>

class AcctABC{
    private:
        std::string fullName;
        long acctNum;
        double balance;
    protected:
        struct Formatting{
            std::ios_base::fmtflags flag;
            std::streamsize pr;
        };
        const std::string & FullName() const {return fullName;}
        long AcctNum() const {return acctNum;}
        Formatting SetFormat() const;
        void Restore(Formatting & f) const;
    public:
        AcctABC(const std::string & s = "Nullbody", long an = -1, double bal = 0.0)
            : fullName(s), acctNum(an), balance(bal) {}
        void Deposit(double amt);
        virtual void Withdraw(double amt) = 0;
        double Balance() const {return balance;}
        virtual void ViewAcct() const = 0;
        virtual ~AcctABC() {}
};

class Brass : public AcctABC{
    public:
        Brass(const std::string & s = "Nullbody", long an = -1, double bal = 0.0)
            : AcctABC(s, an, bal) {}
        virtual void Withdraw(double amt);
        virtual void ViewAcct() const;
        virtual ~Brass() {}
};

class BrassPlus : public AcctABC{
    private:
        double maxLoan;
        double rate;
        double owesBank;
    public:
        BrassPlus(const std::string & s = "Nullbody" ,long an = -1, double bal = 0.0,
                double ml = 500, double r = 0.10)
            : AcctABC(s, an, bal), maxLoan(ml), rate(r), owesBank(0.0) {}
        BrassPlus(const Brass & ba, double ml = 500, double r = 0.1)
            : AcctABC(ba), maxLoan(ml), rate(r), owesBank(0.0) {}
        virtual void ViewAcct() const;
        virtual void Withdraw(double amt);
        void ResetMax(double m) {maxLoan = m;}
        void ResetRate(double r) {rate = r;}
        void ResetOwes() {owesBank = 0.0;}
};
#endif
```

这段代码的背景还是上面提到过的支票账户的场景。上面的声明中，将抽象基类AcctABC中的方法withdraw()声明为纯虚函数，因为对于有透支额度的账户和无透支额度的账户，他们取钱操作的实现是不一样的。

下面这方法文件：

```c++
//acctabc.cpp -- bank acount class methods
#include <iostream>
#include "acctabc.h"
using std::cout;
using std::ios_base;
using std::endl;
using std::string;

void AcctABC::Deposit(double amt){
    if(amt < 0)
        cout << "Negative deposit not allowed;" << "deposit is cancelled.\n";
    else
        balance += amt;
}

void AcctABC::Withdraw(double amt){
    balance -= amt;
}

AcctABC::Formatting AcctABC::SetFormat() const {
    Formatting f;
    f.flag = cout.setf(ios_base::fixed, ios_base::floatfield);
    f.pr = cout.precision(2);
    return f;
}

void AcctABC::Restore(Formatting & f) const{
    cout.setf(f.flag, ios_base::floatfield);
    cout.precision(f.pr);
}

void Brass::Withdraw(double amt){
    if(amt < 0)
        cout << "Withdrawl amount must be positive;" << "withdrawl canceled.\n";
    else if(amt <= Balance())
        AcctABC::Withdraw(amt);
    else
        cout << "Withdrawl amount of $" << amt
            << " exceeds your balance.\n" << "Withdrawl canceled.\n";
}

void Brass::ViewAcct() const {
    Formatting f = SetFormat();
    cout << "Brass Client: " << FullName() << endl;
    cout << "Account Number: " << AcctNum() << endl;
    cout << "Balance: $" << Balance() << endl;
    Restore(f);
}

void BrassPlus::ViewAcct() const {
    Formatting f = SetFormat();

    cout << "BrassPlus Client: " << FullName() << endl;
    cout << "Account Number: " << AcctNum() << endl;
    cout << "Balance: $" << Balance() << endl;
    cout << "Maximum loan: $" << maxLoan << endl;
    cout << "Owed to bank: $" << owesBank << endl;
    cout.precision(3);
    cout << "Loan Rate: " << 100 * rate << "%\n";
    Restore(f);
 }

void BrassPlus::Withdraw(double amt){
    Formatting f = SetFormat();
    double bal = Balance();
    if(amt <= bal)
        AcctABC::Withdraw(amt);
    else if(amt <= bal + maxLoan - owesBank){
        double advance = amt - bal;
        owesBank += advance * (1.0 + rate);
        cout << "Bank advance: $" << advance << endl;
        cout << "Finance charge: $" << advance * rate << endl;
        Deposit(advance);
        AcctABC::Withdraw(amt);
    }
    else
        cout << "Credit limit exceeded. Transaction cancelled.\n";
    Restore(f);
}
```

方法文件中给出的抽象基类中的纯虚函数withdraw()的定义，并且由抽象基类派生出的类可以通过作用域运算符调用抽象基类中的方法，只要给出了定义，也能调用抽象基类中的纯虚函数。

C++中的抽象基类其实就是前面介绍的面向对象编程中抽象理念的具体实现。即设计ABC之前，首先应开发一个模型，用于指出编程问题所需的类以及它们之间的相互关系。

可以将ABC看作是一种必须实施的接口。ABC要求具体派生类覆盖其纯虚函数——迫使派生类遵循ABC设置的接口规则。

#### 继承和动态内存分配

在基类使用了动态内存的前提下，这里分两种情况进行讨论：派生类不使用new的情况，和派生类使用new的情况

##### 派生类不使用new

- 析构：派生类的默认析构函数执行的操作：执行自身的代码后调用基类析构函数。
- 复制：在复制类成员或继承的类组件时，使用的是该类的复制构造函数完成的。所以，派生类的默认复制构造函数使用基类的复制构造函数来复制派生类对象中的基类部分。
- 赋值：派生类的默认赋值运算符将自动使用基类的赋值运算符来对基类组件进行赋值。

所以如果派生类中不使用new，那么也不需要重新定义析构函数、复制构造函数以及赋值运算符。

##### 派生类使用new

先说结论，如果派生类使用了new运算符，那么必须为派生类定义显式析构函数、复制构造函数和赋值运算符。

- 析构：派生类析构函数自动调用基类的析构函数，所以在派生类的构造函数中使用new分配动态内存，那么就需要在派生类的析构函数中释放这些内存。

- 复制：派生类的复制构造函数必须调用基类的复制构造函数，如下

  ```c++
  hasDMA::hasDMA(const hasDMA & hs) : baseDMA(hs){
  	style = new char [std::strlen(hs.style) + 1];
      std::strcpy(style, hs.style);
  }
  ```

  上面这段代码中，hasDMA是继承至基类baseDMA的派生类，派生中有一个字符串style作为数据成员。

- 赋值：派生类的显式赋值运算符也要负责为所有的基类成员赋值，这项工作通过显式调用基类的赋值运算符函数来完成：

  ```c++
  hasDMA & operator=(const hasDMA & hs){
  	if(this == &hs)
  		return *this;
      baseDMA::operator=(hs);
      delete [] style;
      style = new char [std::strlen(hs.style) + 1];
      std::strcpy(style, hs.style);
      return *this;
  }
  ```

  需要注意的是，调用基类的赋值运算符时必须使用函数表示法，因为在派生类作用域中，基类的赋值运算符和派生类的赋值运算符时冲突了的，只有通过函数表示法才能使用作用域解析运算符进行区分。

### CH14. C++中的代码重用

前面介绍了公有继承体现的是一种is-a关系，而除了is-a关系，类与类之间还有一种has-a关系。通常，包含、私有继承和保护继承用于实现has-a关系，即新的类将包含另一个类的对象。

#### 包含对象成员的类

这里通过设计一个名为Student的类来介绍包含对象成员的类，Student类使用string类成员存储学生的姓名，然后使用valarray类的对象存储学生一系列考试的分数。

> 这里简单介绍一下valarray类的使用，valarray类是由头文件valarray支持的，是一个模板类，而模板特性也就意味着在声明对象的时候需要指出具体的数据类型。
>
> 下面是valarray类的一些方法：
>
> - operator[]()：利用索引访问数组中的元素
> - size()：返回包含的元素个数
> - sum()：返回所有元素的总和
> - max()：返回最大的元素
> - min()：返回最小的元素

上面的对象成员声明为私有的，这是为了满足数据隐藏的目的，而这也意味着Student类的成员函数可以使用string和valarray类的公有接口来访问和修改这两个对象。但在类的外面则只能通过Student类的公有接口访问这两个对象。

下面代码是Student类的部分声明：

```c++
class Student{
	private:
    //define the ArrayDb as std::valarray[double], 
    //then we can use ArrayDb in the member function
	typedef std::valarray<double> ArrayDb;
    std::string name;
    ArrayDb scores;
    ...
    public:
    Student() : name("Null student"), scores(){}
    //forbidden implicit type conversion from string to Student
    explicit Student(const std::string & s) : name(s), scores() {}
    //forbidden implicit type conversion from int to Student
    explicit Student(int n) : name("Null student"), scores(n) {}
    Student(const std::string & s, const ArrayDb & a)
        : name(s), scores(a) {}
    //using a constant string and a array of double to initialize the object
    Student(const char * str, const double * pd, int n)
        : name(str), scores(pd, n) {}
    ...
};
```

这里需要注意在构造函数中初始化对象成员时所使用的语法，这和在派生类中初始化基类部分的数据成员所使用的语法不一样。在派生类中初始化基类部分的数据成员时在初始化列表中使用的是类名，而初始化对象成员时在初始化列表中使用的对象名。

C++要求在构建对象的其他部分呢之前，先构建继承的类组件和所有成员对象。因此，如果不在初始化列表中初始化基类组件和成员对象，那么C++将使用基类或者成员对象所属的默认构造函数。

作为成员的对象的接口不是公有的，但可以在类方法中使用这些接口，比如下面的代码定义一个返回学生平局分数的函数：

```c++
double Student::Average() const{
	if(scores.size() > 0)
		return scores.sum() / scores.size();
	else
		return 0;
}
```

同样，可以定义一个使用string版本的<<运算符的友元函数：

```C++
ostream & operator<<(const ostream & os, const Student & stu){
	os << "Scores for" << stu.name << ":\n";
	...
	return os;
}
```

因为stu.name是一个对象，所上面的代码将调用函数`operator<<(const ostream & os, const string & s);`，这个函数是string类中的一个友元函数。

#### 私有继承

使用私有继承，基类的公有成员和保护成员都见称为派生类的私有成员，这意味着基类方法将不会成为派生对象公有接口的一部分，但可以在派生类的成员函数中使用他们。

公有继承与私有继承的区别：使用公有继承，基类的公有方法将成为派生类的公有方法，派生类将继承基类的接口，这是is-a关系的一部分；使用私有继承，基类的公有方法将成为派生类的私有方法，派生类不继承基类的接口，这种不完全的继承就是has-a关系的一部分。

私有继承和包含之间的区别：包含将对象作为一个命名的成员对象添加到类中，而私有继承将对象作为一个未被命名的继承对象添加到类中。

这里将通过包含或者继承添加的对象称为子对象(subobject)。

要进行私有继承，那么需要使用关键字private而不是public来定义类（实际上，private是默认值，因此省略访问限定符也将导致私有继承）。

下面的代码演示了私有继承的定义方式，还是上面那个Student类的例子：

```c++
class Student : private std::string, private std::valarray<double>{
	private:
    typedef std::valarray<double> ArrayDb;
    ...
    public:
    Student() : std::string("Nully Student"), ArrayDb() {}
    explicit Student(const std::string & s) : std::string(s), ArrayDb() {}
    explicit Student(int n) : std::string("Nully Student"), ArrayDb(n) {}
    Student(const std::string & s) : std::string(s), ArrayDb(n) {}
    Student(const char * str, double * pd, int n)
        : std::string(str), ArrayDb(pd, n) {}
    ...
};
```

使用多个基类的继承被称为多重继承(multiple inheritance, MI)。

注意上面构造函数中的初始化列表，通过调用基类的构造函数进行初始化。

私有方法通过基类名称和作用域解析运算符调用基类中的方法：

```C++
double Student::Average() const {
	if(ArrayDb::size() > 0)
		return ArrayDb::sum() / ArrayDb::size();
	else
		return 0;
}
```

如果要访问基类的对象或者基类中的友元函数，那么需要使用强制转换，将派生类强制转换为基类：

```c++
const string & Student::Name() const{
	return (const string &) *this;
}

ostream & operator<<(const ostream & os, Student & stu){
	os << "Scores for " << (const string &) stu << ":\n";
	...
	return os;
}
```

之所以要进行强制转换是因为派生类中并没有为基类对象进行命名，所以要通过强制类型转换访问基类对象。而友元函数也并不属于基类，所以无法通过基类名称和作用域解析运算符调用基类中的友元函数。

stu引用无法自动转换为string引用，==因为在私有继承中，未进行显式类型转换的派生类引用或指针，无法防护之给基类的引用或指针==。

不过在这个例子中，即使使用公有继承，也必须进行显式的类型转换，因为`os << stu;`会导致递归调用。而另一个原因是，公有继承中虽然派生类的指针或引用或自动转换为基类的指针或引用，但是这里是多重继承，编译器无法确定要将派生类转换成哪个基类，如果两个基类都提供了函数`operator<<()`那么将存在二义性。

> 使用包含还是使用私有继承？
>
> 因为这两种方法都能够建立has-a关系，所以这里列出两种方法各自的优势
>
> 使用包含：
>
> 1. 易于理解
> 2. 继承可能会引发问题，由其是多重继承
> 3. 包含可以与一个类的多个对象建立has-a关系，而继承只能与一个类的一个对象建立关系。
>
> 使用继承：
>
> 1. 能够提供更多的特性
> 2. 可以重新定义基类中的虚函数

#### 保护继承

使用保护继承时，基类的公有成员和保护成员都将成为派生类的保护成员。和私有继承一样，基类的接口在派生类中也是可用的，但在继承层次结构之外是不可用的。当使用派生类派生出另一个类时，私有继承和保护继承之间的主要区别就体现出来了。

使用私有继承时，第三代派生类将不能使用基类的接口，这是因为基类的公有方法在派生类中将变成私有方法；使用保护继承时，基类的公有方法在第二代中将变成保护方法，因此第三代派生类中可以使用这些方法。

下面给出基类中各种访问限制的成员在不同的继承方法中的变化：

|                  | 公有继承             | 保护继承               | 私有继承             |
| ---------------- | -------------------- | ---------------------- | -------------------- |
| 公有成员变成     | 派生类的公有成员     | 派生类的保护成员       | 派生类的私有成员     |
| 保护成员变成     | 派生类的保护成员     | 派生类的保护成员       | 派生类的私有成员     |
| 私有成员变成     | 只能通过基类接口访问 | 只能通过基类接口访问   | 只能通过基类接口访问 |
| 能否隐式向上转换 | 能                   | 能（但只能在派生类中） | 不能                 |

这里的隐式向上转换就是派生类的指针或引用自动转换称为基类的指针或引用。

> 使用using重新定义访问权限：
>
> 在使用私有继承或者保护继承定义派生类时，基类的公有成员将成为保护成员或私有成员。如果要使基类的方法在类的外面也可以使用，一种方法是在派生类中定义一个使用基类方法的函数：
>
> ```c++
> double Student::sum() const{
> 	return std::valarray<double>::sum();
> }
> ```
>
> 而另一种方法就是使用一个using声明来指出埃圣雷可以使用特定的基类成员，即使采用的是私有继承。
>
> ```c++
> class Student : private std::string, private std::valarray[double]{
> 	...
> 	public:
> 	using std::valarray<double>::min;
> 	using std::valarray<double>::max;
> 	...
> }
> ```
>
> 需要注意的是，using声明只使用成员名——没有圆括号、函数特征标或返回类型。
>
> 使用了using声明过后，就可以在类的外面使用这些方法了，就像这些方法是Student的公有方法一样：
>
> `cout << "high score: " << ada[i].max() << endl;`

#### 多重继承

前面在介绍私有继承时使用了多重继承的方式，但是在这里更加详细的讨论一些多重继承中的问题。

多种继承种最主要的两个问题是，从两个不同的基类继承同名方法；从两个或更多相关基类那里继承同一个类的多个实例。

这里通过一个祖先相同的MI例子来介绍解决这些问题的方法：

![祖先相同的MI](C:\Users\19021\Pictures\MI.png)

先定义一个抽象基类Worker，然后由这个抽象基类派生出另外两个类Singer和Waiter，然后再利用这两个派生出的类使用多重继承派生出SingingWaiter类。

这里部分代码：

```c++
class Worker{
    private:
        std::string fullname;
        long id;
    public:
        Worker() : fullname("no one"), id(0L) {}
        Worker(const std::string & s, long n)
            : fullname(s), id(n) {}
        virtual ~Worker() = 0;		//pure virtual deconstructor
        virtual void Set();
        virtual void Show() const;
};

class Waiter : public Worker{
    private:
        int panache;
    public:
        Waiter() : Worker(), panache(0) {}
        Waiter(const std::string & s, long n, int p = 0)
            : Worker(s, n), panache(p) {}
        Waiter(const Worker & w, int p =0)
            : Worker(w), panache(p) {}
        void Set();
        void Show() const;
};

class Singer : public Worker{
    private:
    ...
    public:
        Singer() : Worker(), voice(other){}
        Singer(const std::string & s, long n, int v = other)
            : Worker(s, n), voice(v) {}
        Singer(const Worker & w, int v = other)
            : Worker(w), voice(v) {}
        void Set();
        void Show() const;
};

class SingingWaiter : public Singer, public Waiter{
    ...
};
```

因为Singer和Waiter都继承了一个Worker组件，因此SingingWaiter中将包含两个Worker组件。

所以这回导致问题，通常可以将派生类对象的地址赋给基类指针，但是在这里将出现二义性问题：

```c++
SingingWaiter ed;
Worker * pw = &ed;	//ambiguous
```

这种赋值将把基类指针设置为派生对象中的基类对象的地址，但在ed中包含两个Worker对象，有两个地址可供选择，所以需要使用类型转换来指定对象：

```c++
Worker * pw1 = (Waiter *) &ed;
Worker * pw2 = (Singer *) &ed;
```

或者可以通过虚基类的方法使SingingWaiter对象中只包含一个Worker对象。

虚基类使得从多个类（它们的基类相同）派生出的对象只继承一个祖先基类对象。通过在声明中使用关键字virtual，可使Worker被用作Singer或Waiter的虚基类（virtual和public的次序无关紧要）：

```c++
class Singer : virtual public Worker {...};
class Waiter : public Worker {...};
...
class SingingWaiter : public Singer, public Waiter {...};
```

通过这样的声明，SingingWaiter的对象中将只包含一个Worker对象的副本。

使用虚基类时，需要对构造函数采用一种新的方法。因为虚基类中，派生过程中的信息自动传递将不起作用，比如，在通常情况下，可以通过信息自动传递将一些信息传递给基类：

```c++
class A{
	int a;
    public:
    A(int n = 0) : a(n){}
	...
};

class B{
    int b;
    public:
    B(int m = 0, int n = 0) : A(n), b(m) {}
    ...
};

class C{
    int c;
    public:
    C(int q = 0, int m = 0, int n = 0) : B(m, n), c(q) {}
    ...
};
```

但在这里的这个虚基类例子中，这种信息自动传递将不起作用，例如对于下面的SingingWaiter的构造函数：

```c++
SingingWaiter(const Worker & wk, int p = 0, int v = Singer::other)
    : Waiter(wk, p), Singer(wk, v) {}
```

C++在基类是虚的时，禁止信息通过中间类自动传递给基类。因此上述构造函数将初始化成员panache和voice，但wk参数中的信息将不会传递给子对象Waiter。在这种情况下，Waiter子对象将使用Worker的默认构造函数进行初始化。

如果不希望使用Worker的默认构造函数，那么可以显式的调用所需的基类构造函数。因此，SingingWaiter的构造函数应该是这样的：

```c++
SingingWaiter(const Worker & wk, int p = 0, int v = Singer::other)
    : Worker(wk), Waiter(wk, p), Singer(wk, v) {}
```

此外，在多重继承中还有一个问题就是调用方法的歧义性。

在上面的例子中SingingWaiter的基类Singer和Waiter中都包含同名的方法。不过这个问题可以通过作用域解析运算符来澄清编程者的意图。

```c++
SingingWaiter newhire("Elise Hawks", 2005, 6, soprano);
//newhire.Show();				ambiguous
newhire.Singer::Show();		//calls the version of Singer::show()
```

然而更好的方法是在SingingWaiter中重新定义Show()，并指出要使用哪个Show()：

```c++
void SingingWaiter::Show(){
	Singer::Show();
}
```

总而言之，在祖先相同的情况下，使用MI必须引入虚基类，并修改构造函数初始化列表的规则。另外，如果在编写这些类方法的时候没有考虑到MI，则还可能需要重新编写它们。

#### 类模板

继承（公有、私有或保护）和包含并不总是能够满足重用代码的需要。还有一种能够满足代码重用的方法是泛型编程。

泛型编程常用于容器类，先使用通用的代码生成存储不同类型值的容器类，然后将具体的类型作为参数传递给这个类，这样就可以使用通用代码生成存储不同类型值的容器。

最简单的一种处理方式是：使用typedef满足这种需求：

```C++
typedef unsigned long Item;

class Stack{
    ...
};
```

然而这种方式却存在很多不方便的地方，比如每次使用都需要修改一次头文件；并且每次只能修改一次，即一个程序中的Item只能代表一种类型，无法满足需要多种具体类型容器类的程序需求。

和模板函数一样，模板类以下面这样的代码开头：

`template <class Type>`

关键字template告诉编译器，将要定义一个模板。尖括号中的内容相当于函数的参数列表。可以把关键字class看作是参数的类型名，该参数接受具体类型作为其值，把Type看作是参数的名称。

这里使用class并不意味着Type必须是一个类，而只是表明Type是一个通用的类型说明符，在使用模板时，将使用实际的类型替换它。

```c++
template <class Type>
class Stack{
    private:
    enum{MAX = 10};
    Type items[MAX];
    int top;
    ...
};
```

当模板被调用时，Type将被具体的类型值（如int或string）取代。在模板定义中，可以使用类形参数名来标识要存储在容器类中的类型。

同样，可以使用模板成员函数替换原有类的类方法。每个函数头都将以相同的模板声明打头：

`template <class Type>`

另外，还需将类限定符从`Stack::`改为`Stack<Type>::`，比如：

```c++
template <class Type>
bool Stack<Type>::push(const Type & itme){
    ...
}
```

不过如果在类声明中定义了方法（内联定义），则可以省略模板前缀和类限定符。

在完成模板类的定义后，可以通过对模板类进行实例化来使用模板类。例如，下面的代码创建两个栈，一个用于存储int，一个用于存储string对象：

```c++
Stack<int> kernels;
Stack<string> colonels;
```

看到了上面的声明后，编译器将按`Stack<Type>`模板来生成两个独立的类声明和两组独立的类方法。类声明`Stack<int>`将使用int替换模板中所有的Type，而类声明`Stack<string>`将使用string来替换Type。当然使用的算法必须与类型一致。

##### 模板多功能性

递归使用模板：`ArrayTP< ArrayTP<int, 5>, 10> twodee;`，创建一个二维数组。因为在模板类ArrayTP中重载了`operator[]()`，所以可以使用[]运算符编译这个二维数组。

使用多个类型参数：模板可以包含多个类型参数：

```C++
template <class T1, class T2>
class Pair{
	private:
	T1 a;
	T2 b;
	...
};
```

类模板的另一个新特性是，可以为类型参数提供默认值：

`template <class T1, class T2 = int>class Topo {...};`

这里需要注意的是，虽然可以为类模板类型参数提供默认值，但不能为函数模板的类型参数提供默认值。

##### 模板的具体化

- 隐式实例化，声明一个或多个对象，指出所需的类型，而编译器使用通用模板提供的处方生成具体的类定义：

  `Stack<int> stuff; 	//implicit instantiation`

- 显式实例化，使用关键字template并指出所需类型来声明类时，编译器将生成类声明的显式实例化：

  `template class ArrayTP<string, 100>;	//generate ArrayTP<string, 100> class`

- 显式具体化，和函数模板一样，如果需要为特殊类型的模板进行修改，使其行为不同，可以创建显式具体化：

  `template <> class Stack<string> { ... };`
  
- 部分具体化，C++还允许部分具体化，即限制模板的部分通用性，其实就是指定了部分类型参数的显式具体化：

  ```c++
  //general template
  template <class T1, class T2> class Pair { ... };
  //specialization with T2 set to int
  template <class T1> class Pair<T1, int> { ... };
  ```



