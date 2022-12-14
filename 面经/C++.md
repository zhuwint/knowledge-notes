# 基本

## 变量的声明和定义有什么区别

变量的定义为变量分配地址和存储空间， 变量的声明不分配地址。一个变量可以在多个地方声明， 但是只在一个地方定义。 加入extern 修饰的是变量的声明，说明此变量将在文件以外或在文件后面部分定义。

很多时候一个变量，只是声明不分配内存空间，直到具体使用时才初始化，分配内存空间， 如外部变量。

```go
int main() 
{
   extern int A;
   //这是个声明而不是定义，声明A是一个已经定义了的外部变量
   //注意：声明外部变量时可以把变量类型去掉如：extern A;
   dosth(); //执行函数
}
int A; //是定义，定义了A为整型的外部变量
```

## 写出int 、bool、 float 、指针变量与 “零值”比较的if 语句

```go
//int与零值比较 
if ( n == 0 )
if ( n != 0 )
 
//bool与零值比较 
if   (flag) //   表示flag为真 
if   (!flag) //   表示flag为假 
 
//float与零值比较 
const float EPSINON = 0.00001;
if ((x >= - EPSINON) && (x <= EPSINON) //其中EPSINON是允许的误差（即精度）。
//指针变量与零值比较 
if (p == NULL)
if (p != NULL)
```

## 结构体可以直接赋值吗

声明时可以直接初始化，同一结构体的不同对象之间也可以直接赋值，但是当结构体中含有指针“成员”时一定要小心。

## sizeof与strlen的区别

* sizeof是一个操作符，strlen是库函数。
* sizeof的参数可以是数据的类型，也可以是变量，而strlen只能以结尾为‘\0’的字符串作参数。
* 编译器在编译时就计算出了sizeof的结果，而strlen函数必须在运行时才能计算出来。并且sizeof计算的是数据类型占内存的大小，而strlen计算的是字符串实际的长度。
* 数组做sizeof的参数不退化，传递给strlen就退化为指针了

## C 语言的关键字 static 和 C++ 的关键字 static 有什么区别

在 C 中 static 用来修饰局部静态变量和外部静态变量、函数。而 C++中除了上述功能外，还用来定义类的成员变量和函数。即静态成员和静态成员函数。

编程时 static 的记忆性，和全局性的特点可以让在不同时期调用的函数进行通信，传递信息，而 C++的静态成员则可以在多个对象实例间进行通信，传递信息。

## C语言的malloc和C++中的new有什么区别

* new 、delete 是操作符，可以重载，只能在C++ 中使用。
* malloc、free 是函数，可以覆盖，C、C++ 中都可以使用。
* new 可以调用对象的构造函数，对应的delete 调用相应的析构函数。
* malloc 仅仅分配内存，free 仅仅回收内存，并不执行构造和析构函数
* new 、delete 返回的是某种数据类型指针，malloc、free 返回的是void 指针。

## volatile关键字

C/C++ 中的 volatile 关键字和 const 对应，用来修饰变量，通常用于建立语言级别的 memory barrier。

volatile 关键字是一种类型修饰符，用它声明的类型变量表示可以被某些编译器未知的因素更改，比如：操作系统、硬件或者其它线程等。遇到这个关键字声明的变量，编译器对访问该变量的代码就不再进行优化，从而可以提供对特殊地址的稳定访问。

## 一个参数可以既是const又是volatile吗

可以，用const和volatile同时修饰变量，表示这个变量在程序内部是只读的，不能改变的，只在程序外部条件变化下改变，并且编译器不会优化这个变量。每次使用这个变量时，都要小心地去内存读取这个变量的值，而不是去寄存器读取它的备份。

## 全局变量和局部变量有什么区别

* 全局变量是整个程序都可访问的变量，谁都可以访问，生存期在整个程序从运行到结束（在程序结束时所占内存释放）；
* 而局部变量存在于模块（子程序，函数）中，只有所在模块可以访问，其他模块不可直接访问，模块结束（函数调用完毕），局部变量消失，所占据的内存释放。
* 操作系统和编译器，可能是通过内存分配的位置来知道的，全局变量分配在全局数据段并且在程序开始运行的时候被加载.局部变量则分配在堆栈里面。

## 简述C、C++程序编译的内存分配情况

* 从静态存储区域分配：

内存在程序编译时就已经分配好，这块内存在程序的整个运行期间都存在。速度快、不容易出错， 因为有系统会善后。例如全局变量，static 变量，常量字符串等。

* 在栈上分配：

在执行函数时，函数内局部变量的存储单元都在栈上创建，函数执行结束时这些存储单元自动被释 放。栈内存分配运算内置于处理器的指令集中，效率很高，但是分配的内存容量有限。大小为2M。

* 从堆上分配：

即动态内存分配。程序在运行的时候用 malloc 或new 申请任意大小的内存，程序员自己负责在何 时用free 或delete 释放内存。动态内存的生存期由程序员决定，使用非常灵活。如果在堆上分配了空间，就有责任回收它，否则运行的程序会出现内存泄漏，另外频繁地分配和释放不同大小的堆空间将会产生 堆内碎块。

一个C、C++程序编译时内存分为5 大存储区：堆区、栈区、全局区、文字常量区、程序代码区。

## 简述strcpy、sprintf 与memcpy 的区别

* 操作对象不同，strcpy 的两个操作对象均为字符串，sprintf 的操作源对象可以是多种数据类型， 目的操作对象是字符串，memcpy 的两个对象就是两个任意可操作的内存地址，并不限于何种数据类型。
* 执行效率不同，memcpy 最高，strcpy 次之，sprintf 的效率最低。
* 实现功能不同，strcpy 主要实现字符串变量间的拷贝，sprintf 主要实现其他数据类型格式到字 符串的转化，memcpy 主要是内存块间的拷贝。

## C语言的指针和引用和c++的有什么区别

* 指针有自己的一块空间，而引用只是一个别名；
* 使用sizeof看一个指针的大小是4，而引用则是被引用对象的大小；
* 作为参数传递时，指针需要被解引用才可以对对象进行操作，而直接对引 用的修改都会改变引用所指向的对象；
* 可以有const指针，但是没有const引用；
* 指针在使用中可以指向其它对象，但是引用只能是一个对象的引用，不能 被改变；
* 指针可以有多级指针（**p），而引用止于一级；
* 指针和引用使用++运算符的意义不一样；
* 如果返回动态内存分配的对象或者内存，必须使用指针，引用可能引起内存泄露。

## typedef和define有什么区别

* 用法不同：typedef 用来定义一种数据类型的别名，增强程序的可读性。define 主要用来定义 常量，以及书写复杂使用频繁的宏。
* 执行时间不同：typedef 是编译过程的一部分，有类型检查的功能。define 是宏定义，是预编译的部分，其发生在编译之前，只是简单的进行字符串的替换，不进行类型的检查。
* 作用域不同：typedef 有作用域限定。define 不受作用域约束，只要是在define 声明后的引用 都是正确的。
* 对指针的操作不同：typedef 和define 定义的指针时有很大的区别。

## 指针常量和常量指针的区别

指针常量是指定义了一个指针，这个指针的值只能在定义时初始化，其他地方不能改变。常量指针 是指定义了一个指针，这个指针指向一个只读的对象，不能通过常量指针来改变这个对象的值。 指针常量强调的是指针的不可改变性，而常量指针强调的是指针对其所指对象的不可改变性。

## C语言的结构体和C++有什么区别

* C语言的结构体是不能有函数成员的，而C++的类可以有。
* C语言的结构体中数据成员是没有private、public和protected访问限定的。而C++的类的成员有这些访问限定。
* C语言的结构体是没有继承关系的，而C++的类却有丰富的继承关系。

## 如何避免野指针

* 指针变量声明时没有被初始化。解决办法：指针声明时初始化，可以是具体的地址值，也可让它指向NULL。
* 指针p被free或者delete之后，没有置为NULL。解决办法：指针指向的内存空间被释放后指针应该指向NULL。
* 指针操作超越了变量的作用范围。解决办法：在变量的作用域结束前释放掉变量的地址空间并且让指针指向NULL。

## 句柄和指针的区别和联系是什么

句柄和指针其实是两个截然不同的概念。Windows系统用句柄标记系统资源，隐藏系统的信息。你只要知道有这个东西，然后去调用就行了，它是个32it的uint。指针则标记某个物理内存地址，两者是不同的概念。

## extern“C”

extern "C"的主要作用就是为了能够正确实现C++代码调用其他C语言代码。加上extern "C"后，会指示编译器这部分代码按C语言（而不是C++）的方式进行编译。由于C++支持函数重载，因此编译器编译函数的过程中会将函数的参数类型也加到编译后的代码中，而不仅仅是函数名；而C语言并不支持函数重载，因此编译C语言代码的函数时不会带上函数的参数类型，一般只包括函数名。

这个功能十分有用处，因为在C++出现以前，很多代码都是C语言写的，而且很底层的库也是C语言写的，为了更好的支持原来的C代码和已经写好的C语言库，需要在C++中尽可能的支持C，而extern "C"就是其中的一个策略。

## C++中struct和class的区别

* 默认继承权限不同，class继承默认是private继承，而struct默认是public继承
* class还可用于定义模板参数，像typename，但是关键字struct不能同于定义模板参数 C++保留struct关键字，原因
* 保证与C语言的向下兼容性，C++必须提供一个struct
* C++中的struct定义必须百分百地保证与C语言中的struct的向下兼容性，把C++中的最基本的对象单元规定为class而不是struct，就是为了避免各种兼容性要求的限制
* 对struct定义的扩展使C语言的代码能够更容易的被移植到C++中

## C++类内可以定义引用数据成员吗

可以，必须通过成员函数初始化列表初始化

## 什么是右值引用，跟左值有什么区别

在C++11中可以取地址的、有名字的就是左值，反之，不能取地址的、没有名字的就是右值。

左值一定在内存中，右值有可能在内存中也有可能在寄存器。

右值引用和左值引用都是属于引用类型。无论是声明一个左值引用还是右值引用，都必须立即进行初始化。而其原因可以理解为是引用类型本身自己并不拥有所绑定对象的内存，只是该对象的一个别名。左值引用是具名变量值的别名，而右值引用则是不具名（匿名）变量的别名。

左值引用通常也不能绑定到右值，但常量左值引用是个“万能”的引用类型。它可以接受非常量左值、常量左值、右值对其进行初始化。不过常量左值所引用的右值在它的“余生”中只能是只读的。相对地，非常量左值只能接受非常量左值对其进行初始化。

```C++
int &a = 2;       # 左值引用绑定到右值，编译失败
int b = 2;        # 非常量左值
const int &c = b; # 常量左值引用绑定到非常量左值，编译通过
const int d = 2;  # 常量左值
const int &e = c; # 常量左值引用绑定到常量左值，编译通过
const int &b =2;  # 常量左值引用绑定到右值，编程通过
```

## C++中的cast转换

1、const_cast

* 用于将const变量转为非const

2、static_cast

* 用于各种隐式转换，比如非const转const，void*转指针等, static_cast能用于多态向上转化，如果向下转能成功但是不安全，结果未知；

3、dynamic_cast

用于动态类型转换。只能用于含有虚函数的类，用于类层次间的向上和向下转化。只能转指针或引用。向下转化时，如果是非法的***对于指针返回NULL，对于引用抛异常***。要深入了解内部转换的原理。

* 向上转换：指的是子类向基类的转换
* 向下转换：指的是基类向子类的转换

它通过判断在执行到该语句的时候变量的运行时类型和要转换的类型是否相同来判断是否能够进行向下转换。

4、reinterpret_cast

* 几乎什么都可以转，比如将int转指针，可能会出问题，尽量少用；

5、为什么不使用C的强制转换？

* C的强制转换表面上看起来功能强大什么都能转，但是转化不够明确，不能进行错误检查，容易出错。

## 智能指针

* unique_ptr（替换auto_ptr）

unique_ptr实现独占式拥有或严格拥有概念，保证同一时间内只有一个智能指针可以指向该对象。它对于避免资源泄露(例如“以new创建对象后因为发生异常而忘记调用delete”)特别有用。

```C++
unique_ptr<string> p3 (new string ("auto"));   //#4
unique_ptr<string> p4；                       //#5
p4 = p3;//此时会报错！！
```

* shared_ptr

shared_ptr实现共享式拥有概念。多个智能指针可以指向相同对象，该对象和其相关资源会在“最后一个引用被销毁”时候释放。从名字share就可以看出了资源可以被多个指针共享，它使用计数机制来表明资源被几个指针共享。可以通过成员函数use_count()来查看资源的所有者个数。除了可以通过new来构造，还可以通过传入auto_ptr, unique_ptr,weak_ptr来构造。当我们调用release()时，当前指针会释放资源所有权，计数减一。当计数等于0时，资源会被释放。

shared_ptr 是为了解决 auto_ptr 在对象所有权上的局限性(auto_ptr 是独占的), 在使用引用计数的机制上提供了可以共享所有权的智能指针。

* weak_ptr

weak_ptr 是一种不控制对象生命周期的智能指针, 它指向一个 shared_ptr 管理的对象. 进行该对象的内存管理的是那个强引用的 shared_ptr. weak_ptr只是提供了对管理对象的一个访问手段。weak_ptr 设计的目的是为配合 shared_ptr 而引入的一种智能指针来协助 shared_ptr 工作, 它只可以从一个 shared_ptr 或另一个 weak_ptr 对象构造, 它的构造和析构不会引起引用记数的增加或减少。weak_ptr是用来解决shared_ptr相互引用时的死锁问题,如果说两个shared_ptr相互引用,那么这两个指针的引用计数永远不可能下降为0,资源永远不会释放。它是对对象的一种弱引用，不会增加对象的引用计数，和shared_ptr之间可以相互转化，shared_ptr可以直接赋值给它，它可以通过调用lock函数来获得shared_ptr。


# 面向对象

## 面向对象的3大特征

* 封装性：将客观事物抽象成类，每个类对自身的数据和方法实行 protection （private ， protected ， public ）。
* 继承性：广义的继承有三种实现形式：实现继承（使用基类的属性和方法而无需额外编码的能力)、可 视继承(子窗体使用父窗体的外观和实现代码)、接口继承(仅使用属性和方法,实现滞后到子类实现)。
* 多态性：是将父类对象设置成为和一个或更多它的子对象相等的技术。用子类对象给父类对象赋值 之后，父类对象就可以根据当前赋值给它的子对象的特性以不同的方式运作。

## 拷贝构造函数与赋值运算符的区别

* 拷贝构造函数生成新的类对象，而赋值运算符不能。
* 由于拷贝构造函数是直接构造一个新的类对象，所以在初始化这个对象之前不用检验源对象 是否和新建对象相同。而赋值运算符则需要这个操作，另外赋值运算中如果原来的对象中有内存分配要先把内存释放掉。
