## 指针

### 常量指针与指针常量

常量指针`（const int * p）`：指向常量（const int）的指针，可以切换到指向别的常量（const int）；

指针常量`（int * const p）`：指针的指向不可修改。

指向常量的指针常量`（const int * const p）`: p的指向不可修改，p所指的内存区域中的值也不可修改（是常量）。

> 从右往左读：遇到 p 替换成 "p is a"，遇到 * 替换成 "point to"
>

### 函数指针

如果在程序中定义了一个函数，那么在编译时系统就会为这个函数代码分配一段存储空间，这段存储空间的首地址称为这个函数的地址。而且函数名表示的就是这个地址。既然是地址我们就可以定义一个指针变量来存放，这个指针变量就叫作函数指针变量，简称函数指针。

那么这个指针变量怎么定义呢？虽然同样是指向一个地址，但指向函数的指针变量同我们之前讲的指向变量的指针变量的定义方式是不同的。例如：

```cpp
int (*p)(int, int);
```

这个语句就定义了一个指向函数的指针变量 p。首先它是一个指针变量，所以要有一个\*，即（\*p）；其次前面的 int 表示这个指针变量可以指向返回值类型为 int 型的函数；后面括号中的两个 int 表示这个指针变量可以指向有两个参数且都是 int 型的函数。所以合起来这个语句的意思就是：定义了一个指针变量 p，该指针变量可以指向返回值类型为 int 型，且有两个整型参数的函数。p 的类型为 int(*)(int，int)。

**指向函数的指针变量没有 ++ 和 -- 运算**

```cpp
# include <stdio.h>
int Max(int, int);  //函数声明
int main(void)
{
    int(*p)(int, int);  //定义一个函数指针
    int a, b, c;
    p = Max;  //把函数Max赋给指针变量p, 使p指向Max函数
    printf("please enter a and b:");
    scanf("%d%d", &a, &b);
    c = (*p)(a, b);  //通过函数指针调用Max函数
    printf("a = %d\nb = %d\nmax = %d\n", a, b, c);
    return 0;
}
int Max(int x, int y)  //定义Max函数
{
    int z;
    if (x > y)
    {
        z = x;
    }
    else
    {
        z = y;
    }
    return z;
}
```

## 引用

引用（Reference）是 C++ 相对于C语言的又一个扩充。引用可以看做是数据的一个别名，通过这个别名和原来的名字都能够找到这份数据。引用类似于 Windows 中的快捷方式，一个可执行程序可以有多个快捷方式，通过这些快捷方式和可执行程序本身都能够运行程序；引用还类似于人的绰号（笔名），使用绰号（笔名）和本名都能表示一个人。

```cpp
#include <iostream>
using namespace std;

int main() {
    int a = 99;
    int &r = a;
    cout << a << ", " << r << endl;
    cout << &a << ", " << &r << endl;

    return 0;
}
// 99, 99
// 0x28ff44, 0x28ff44
```

注意，引用在定义时需要添加`&`，在使用时不能添加`&`，使用时添加`&`表示取地址。

### 引用作为参数

在定义或声明函数时，我们可以将函数的形参指定为引用的形式，这样在调用函数 时就会将实参和形参绑定在一起，让它们都指代同一份数据。如此一来，如果在函数体中修改了形参的数据，那么实参的数据也会被修改，从而拥有“在函数内部影响函数外部数据”的效果。

### 引用作为函数返回值

```cpp
#include <iostream>
using namespace std;

int &plus10(int &r) {
    r += 10;
    return r;
}

int main() {
    int num1 = 10;
    int num2 = plus10(num1);
    cout << num1 << " " << num2 << endl;

    return 0;
}
// 20 20
```

在将引用作为函数返回值时应该注意一个小问题，就是**不能返回局部数据（例如局部变量、局部对象、局部数组等）的引用**，因为当函数调用完成后局部数据就会被销毁，有可能在下次使用时数据就不存在了，C++ 编译器检测到该行为时也会给出警告。

## 引用和指针的区别和联系

1. 引用只能在定义时初始化一次，之后不能改变指向其它变量（从一而终）；指针变量的值可变。
2. 引用必须指向有效的变量，指针可以为空。
3. sizeof指针对象和引用对象的意义不一样。sizeof引用得到的是所指向的变量的大小，而sizeof指针是对象地址的大小。
4. 指针和引用自增(++)自减(--)意义不一样。
5. 相对而言，引用比指针更安全。

**不同点：**

1. 指针是一个实体，而引用仅是个别名；
2. 引用使用时无需解引用(*)，指针需要解引用；
3. 引用只能在定义时被初始化一次，之后不可变；指针可变；
4. 引用没有 const，指针有 const；const修饰的指针不可变；
5. 引用不能为空，指针可以为空；
6. “sizeof 引用”得到的是所指向的变量(对象)的大小，而“sizeof 指针”得到的是指针本身(所指向的变量或对象的地址)的大小；
7. 指针和引用的自增(++)运算意义不一样；

8.从内存分配上看：程序为指针变量分配内存区域，而引用不需要分配内存区域。

**相同点:**  
两者都是地址的概念，指针指向一块儿内存，其内容为所指内存的地址；引用是某块儿内存的别名。

## 函数参数传递中值传递、地址传递、引用传递有什么区别？

(1)  **值传递** ，会为形参重新分配内存空间，将实参的值拷贝给形参，形参的值不会影响实参的值，函数调用结束后形参被释放；

(2)  **引用传递** ，不会为形参重新分配内存空间，形参只是实参的别名，形参的改变会影响实参的值，函数调用结束后形参不会被释放；

(3)  **地址传递** ，形参为指针变量，将实参的地址传递给函数，可以在函数中改变实参的值，调用时为形参指针变量分配内存，结束时释放指针变量。
