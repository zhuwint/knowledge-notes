## explicit关键字

> 作用：用来声明类构造函数是显示调用的，而非隐式调用，可以阻止调用构造函数时进行隐式转换。只可用于修饰单参构造函数，因为无参构造函数和多参构造函数本身就是显示调用的，再加上 explicit 关键字也没有什么意义。
>

C++中，**一个参数的构造函数(或者除了第一个参数外其余参数都有缺省值的多参构造函数)**，承担了两个角色。

* 用于构建单参数的类对象
* 隐含的类型转换操作符（转换构造函数）

**隐式类型转换** ( **构造函数的隐式调用** )

先来看一下这种隐式类型转换是怎么发生的吧.

```cpp
#include <iostream>
using namespace std;

class Point {
public:
    int x, y;
    Point(int x = 0, int y = 0)
        : x(x), y(y) {}
};

void displayPoint(const Point& p) 
{
    cout << "(" << p.x << "," 
         << p.y << ")" << endl;
}

int main()
{
    displayPoint(1);
    Point p = 1;
}
```

我们定义了一个再简单不过的`Point`类, 它的构造函数使用了默认参数. 这时主函数里的两句话都会触发该构造函数的隐式调用. (如果构造函数不使用默认参数, 会在编译时报错)

显然, 函数`displayPoint`需要的是`Point`类型的参数, 而我们传入的是一个`int`, 这个程序却能成功运行, 就是因为这隐式调用. 另外说一句, 在对象刚刚定义时, 即使你使用的是赋值操作符`=`, 也是会调用构造函数, 而不是重载的`operator=`运算符.

这样悄悄发生的事情, 有时可以带来便利, 而有时却会带来意想不到的后果. `explicit`关键字用来避免这样的情况发生.

**explicit关键字**

[C++ 参考手册如下解释](https://link.zhihu.com/?target=https%3A//zh.cppreference.com/w/cpp/language/explicit)

1. 指定构造函数或转换函数 (C++11起)为显式, 即它不能用于[隐式转换](https://link.zhihu.com/?target=https%3A//zh.cppreference.com/w/cpp/language/implicit_conversion)和[复制初始化](https://link.zhihu.com/?target=https%3A//zh.cppreference.com/w/cpp/language/copy_initialization).
2. explicit 指定符可以与常量表达式一同使用. 函数若且唯若该常量表达式求值为 true 才为显式. (C++20起)

这篇文章我们关注的就是第一点. 构造函数被`explicit`修饰后, 就不能再被隐式调用了. 也就是说, 之前的代码, 在`Point(int x = 0, int y = 0)`前加了`explicit`修饰, 就无法通过编译了.

**能用就用**

如果我们能预料到某种情况的发生, 就不要把这个情况的控制权交给编译器. 之前的代码, 以前我都觉得它无法通过编译. 在不知道`explicit`关键字的情况下, 我也是每次都使用`Point(1)`做一个类型转换再传入给`displayPoint`函数.

Effective C++中也写:

> 被声明为`explicit`的构造函数通常比其 non-explicit 兄弟更受欢迎, 因为它们禁止编译器执行非预期 (往往也不被期望) 的类型转换. 除非我有一个好理由允许构造函数被用于隐式类型转换, 否则我会把它声明为`explicit`. 我鼓励你遵循相同的政策.
>

```cpp
// 加了explicit之后的代码
#include <iostream>
using namespace std;

class Point {
public:
    int x, y;
    explicit Point(int x = 0, int y = 0)
        : x(x), y(y) {}
};

void displayPoint(const Point& p) 
{
    cout << "(" << p.x << "," 
         << p.y << ")" << endl;
}

int main()
{
    displayPoint(Point(1));
    Point p(1);
}
```

> `explicit`关键字只对有一个参数的类构造函数（或其它参数都有缺省值）有效, 如果类构造函数参数大于或等于两个时, 是不会产生隐式转换的, 所以`explicit`关键字也就无效了。
>
> **拷贝构造函数最好不要声明为explicit**
>


## constexpr关键字

**constexpr 关键字的功能是使指定的常量表达式获得在程序编译阶段计算出结果的能力，而不必等到程序运行阶段**

### constexpr函数

constexpr函数与编译期计算有关，要是constexpr函数所使用的变量其值能够在编译时就确定，那么constexpr函数就能在编译时执行计算。另一方面，要是constexpr函数所使用的变量其值只能在运行时确定，那么constexpr就和一般的函数没区别。

C++11 要求constexpr函数不能多于一条语句，但是碰到 if-else 语句时，但可以巧妙地使用条件操作符来替代，C++14 中则放松了这个要求。

### constexpr变量

const变量的值可以在编译时或运行时确定，与const相比，constexpr的限制更多，因为 **constexpr变量的值必须在编译时就能确定** 。

### 与const的区别

const修饰变量，表示这个变量是不可修改的，因此const变量必须初始化，一经初始化就不可修改。

1. 如果const变量的初始化值是在编译时就可以确定，则在编译时初始化；
2. 如果const变量的初始化值是在运行时才确定，则在运行时初始化；

传统const的问题在于“双重语义”，既有“只读”的含义，又有“常量”（不可改变）的含义，而constexpr严格定义了常量。

```cpp
constexpr int sqr1(int arg){
    return arg*arg;
}
const int sqr2(int arg){
    return arg*arg;
}
array<int,sqr1(10)> mylist1;//可以，因为sqr1时constexpr函数
array<int,sqr2(10)> mylist1;//不可以，因为sqr2不是constexpr函数
```


## mutable关键字

`mutable` 从字面意思上来说，是「可变的」之意。

若是要「顾名思义」，那么这个关键词的含义就有些意思了。显然，「可变的」只能用来形容变量，而不可能是「函数」或者「类」本身。然而，既然是「变量」，那么它本来就是可变的，也没有必要使用 `mutable` 来修饰。那么，`mutable` 就只能用来形容某种不变的东西了。

C++ 中，不可变的变量，称之为常量，使用 `const` 来修饰。然而，若是 `const mutable` 联用，未免让人摸不着头脑——到底是可变还是不可变呢？

事实上，`mutable` 是用来修饰一个 `const` 示例的部分可变的数据成员的。如果要说得更清晰一点，就是说 `mutable` 的出现，将 C++ 中的 `const` 的概念分成了两种。

* 二进制层面的 `const`，也就是「绝对的」常量，在任何情况下都不可修改（除非用 `const_cast`）。
* 引入 `mutable` 之后，C++ 可以有逻辑层面的 `const`，也就是对一个常量实例来说，从外部观察，它是常量而不可修改；但是内部可以有非常量的状态。

> 当然，所谓的「逻辑 `const`」，在 C++ 标准中并没有这一称呼。这只是为了方便理解，而创造出来的名词。
>

显而易见，`mutable` 只能用来修饰类的数据成员；而被 `mutable` 修饰的数据成员，可以在 `const` 成员函数中修改。

### lambda表达式中的mutable

C++11 引入了 Lambda 表达式，程序员可以凭此创建匿名函数。在 Lambda 表达式的设计中，捕获变量有几种方式；其中按值捕获（Caputre by Value）的方式不允许程序员在 Lambda 函数的函数体中修改捕获的变量。而以 `mutable` 修饰 Lambda 函数，则可以打破这种限制。

```cpp
int x{0};
auto f1 = [=]() mutable {x = 42;};  // okay, 创建了一个函数类型的实例
auto f2 = [=]()         {x = 42;};  // error, 不允许修改按值捕获的外部变量的值
```

需要注意的是，上述 `f1` 的函数体中，虽然我们给 `x` 做了赋值操作，但是这一操作仅只在函数内部生效——即，实际是给拷贝至函数内部的 `x` 进行赋值——而外部的 `x` 的值依旧是 `0`。


## final与override

我们在写虚函数时，想让派生类中的虚函数覆盖掉基类虚函数，有时我们会不小心写错，造成了隐藏，这不是我们想要看到的结果。所以C++ 11新标准中我们可以使用override关键字来说明派生类中的虚函数。

如果我们使用override标记了某个函数，但该函数并没有覆盖已存在的虚函数，此时编译器将报错。

```cpp
class B {
public:
    virtual void f1(int) const;

    virtual void f2();

    void f3();
};

class A : public B {
public:
    void f1(int) const override;   // 正确
    void f2(int) override; // 错误：B没有形如f2(int)的函数
    void f3() override;    // 错误：f3不是虚函数
    void f4() override;     // 错误：B没有名为f4的函数
};
```

使用override是希望能覆盖基类中的虚函数，如果不符合则编译器报错。

我们还能把某个函数指点为 **final** ，意味着任何尝试覆盖该函数的操作都将引发错误：

```cpp
class B {
public:
    virtual void f1(int) const;

    virtual void f2();

    void f3();
};

class D : public B {
    void f1(int) const final;  // 不允许后续的其它类覆盖f1(int)
};


class E : public D {
    void f2();            // 正确：覆盖从间接类B继承而来的f2
    void f1(int) const;   // 错误：D已将f2声明为final
};
```

## volatile

volatile 的作用：

* 当对象的值可能在程序的控制或检测之外被改变时，应该将该对象声明为 violatile，告知编译器不应对这样的对象进行优化。
* volatile不具有原子性。
* volatile 对编译器的影响：使用该关键字后，编译器不会对相应的对象进行优化，即不会将变量从内存缓存到寄存器中，防止多个线程有可能使用内存中的变量，有可能使用寄存器中的变量，从而导致程序错误。

```cpp
volatile int i=10;
int a = i;
int b = i; 
// volatile 指出 i 是随时可能发生变化的，每次使用它的时候必须从 i的地址中读取，
// 因而编译器生成的汇编代码会重新从i的地址读取数据放在 b 中。
// 而优化做法是，由于编译器发现两次从 i读数据的代码之间的代码没有对 i 进行过操作，它会自动把上次读的数据放在 b 中。
// 而不是重新从 i 里面读。这样一来，如果 i是一个寄存器变量或者表示一个端口数据就容易出错，
// 所以说 volatile 可以保证对特殊地址的稳定访问。
```

使用 volatile 关键字的场景：

* 当多个线程都会用到某一变量，并且该变量的值有可能发生改变时，需要用 volatile 关键字对该变量进行修饰；
* 中断服务程序中访问的变量或并行设备的硬件寄存器的变量，最好用 volatile 关键字修饰。
* volatile 关键字和 const 关键字可以同时使用，某种类型可以既是 volatile 又是 const ，同时具有二者的属性。

> 总结：volatile保证了不同线程对该变量操作的内存可见性（一个线程对共享变量的修改，能够被其它线程看到），并禁止指令重排序。当一个变量被volatile修饰时，那么对它的修改会立刻刷新到主存，当其它线程需要读取该变量时，会去内存中读取新值。而普通变量则不能保证这一点。
>

### volatile指针

* 修饰由指针指向的对象、数据是 const 或 volatile 的：

```cpp
const char* cpch;
volatile char* vpch;
```

* 指针自身的值——一个代表地址的整数变量，是 const 或 volatile 的：

```cpp
char* const pchc;
char* volatile pchv; 
```
