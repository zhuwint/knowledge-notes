## 函数模板

在C++中，数据的类型也可以通过参数来传递，在函数定义时可以不指明具体的数据类型，当发生函数调用时，编译器可以根据传入的实参自动推断数据类型。这就是类型的参数化。

所谓函数模板，实际上是建立一个通用函数，它所用到的数据的类型（包括返回值类型、形参类型、局部变量类型）可以不具体指定，而是用一个虚拟的类型来代替（实际上是用一个标识符来占位），等发生函数调用时再根据传入的实参来逆推出真正的类型。这个通用函数就称为 **函数模板（Function Template）** 。

```cpp
template<typename T>
void Swap(T *a, T *b) {
    T temp = *a;
    *a = *b;
    *b = temp;
}

template<typename T> 
void Swap(T &a, T &b){
    T temp = a;
    a = b;
    b = temp;
}
```

> template <typename 类型参数1 , typename 类型参数2 , ...> 
>
> 返回值类型  函数名(形参列表){  
>     //在函数体中可以使用类型参数  
> }
>

`typename`关键字也可以使用`class`关键字替代，它们没有任何区别。C++ 早期对模板的支持并不严谨，没有引入新的关键字，而是用 class 来指明类型参数，但是 class 关键字本来已经用在类的定义中了，这样做显得不太友好，所以后来 C++ 又引入了一个新的关键字 typename，专门用来定义类型参数。不过至今仍然有很多代码在使用 class 关键字，包括 C++ 标准库、一些开源程序等。

## 类模板

C++ 除了支持函数模板，还支持 **类模板（Class Template）** 。函数模板中定义的类型参数可以用在函数声明和函数定义中，类模板中定义的类型参数可以用在类声明和类实现中。类模板的目的同样是将数据的类型参数化。

```cpp
template<typename T1, typename T2>  //这里不能有分号
class Point{
public:
    Point(T1 x, T2 y): m_x(x), m_y(y){ }
public:
    T1 getX() const;  //获取x坐标
    void setX(T1 x);  //设置x坐标
    T2 getY() const;  //获取y坐标
    void setY(T2 y);  //设置y坐标
private:
    T1 m_x;  //x坐标
    T2 m_y;  //y坐标
};
```

一但声明了类模板，就可以将类型参数用于类的成员函数和成员变量了。换句话说，原来使用 int、float、char 等内置类型的地方，都可以用类型参数来代替。

使用类模板创建对象：

```cpp
Point<int, int> p1(10, 20);
Point<int, float> p2(10, 15.5);
Point<float, char*> p3(12.4, "东经180度");

Point<float, float> *p1 = new Point<float, float>(10.6, 109.3);
Point<char*, char*> *p = new Point<char*, char*>("东经180度", "北纬210度");
```
