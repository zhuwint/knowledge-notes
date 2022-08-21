## 基本使用

```cpp
#include <iostream>
using namespace std;

class complex{
public:
    complex();
    complex(double real, double imag);
public:
    //声明运算符重载
    complex operator+(const complex &A) const;
    void display() const;
private:
    double m_real;  //实部
    double m_imag;  //虚部
};

complex::complex(): m_real(0.0), m_imag(0.0){ }
complex::complex(double real, double imag): m_real(real), m_imag(imag){ }

//实现运算符重载
complex complex::operator+(const complex &A) const{
    complex B;
    B.m_real = this->m_real + A.m_real;
    B.m_imag = this->m_imag + A.m_imag;
    return B;
}

void complex::display() const{
    cout<<m_real<<" + "<<m_imag<<"i"<<endl;
}

int main(){
    complex c1(4.3, 5.8);
    complex c2(2.4, 3.7);
    complex c3;
    c3 = c1 + c2;
    c3.display();
 
    return 0;
}
```

运算符重载其实就是定义一个函数，在函数体内实现想要的功能，当用到该运算符时，编译器会自动调用这个函数。也就是说，运算符重载是通过函数实现的，它本质上是函数重载。

## 重载规则

* 并不是所有的运算符都可以重载。长度运算符`sizeof`、条件运算符`: ?`、成员选择符`.`和域解析运算符`::`不能被重载。
* 重载不能改变运算符的优先级和结合性
* 重载不会改变运算符的用法，原有有几个操作数、操作数在左边还是在右边，这些都不会改变。例如`~`号右边只有一个操作数，`+`号总是出现在两个操作数之间，重载后也必须如此。
* 运算符重载函数不能有默认的参数，否则就改变了运算符操作数的个数，这显然是错误的。
* **运算符重载函数不仅可以作为类的成员函数，还可以作为全局函数**。将运算符重载函数作为类的成员函数时，二元运算符的参数只有一个，一元运算符不需要参数。之所以少一个参数，是因为这个参数是隐含的。将运算符重载函数作为全局函数时，二元操作符就需要两个参数，一元操作符需要一个参数，而且其中必须有一个参数是对象，好让编译器区分这是程序员自定义的运算符，防止程序员修改用于内置类型的运算符的性质。如果有两个参数，这两个参数可以都是对象，也可以一个是对象，一个是C ++内置类型的数据
* 将运算符重载函数作为全局函数时，一般都需要在类中将该函数声明为友元函数。原因很简单，该函数大部分情况下都需要使用类的 private 成员。

## 重载数学运算符

```cpp
#include <iostream>
#include <cmath>
using namespace std;

//复数类
class Complex{
public:  //构造函数
    Complex(double real = 0.0, double imag = 0.0): m_real(real), m_imag(imag){ }
public:  //运算符重载
    //以全局函数的形式重载
    friend Complex operator+(const Complex &c1, const Complex &c2);
    friend Complex operator-(const Complex &c1, const Complex &c2);
    friend Complex operator*(const Complex &c1, const Complex &c2);
    friend Complex operator/(const Complex &c1, const Complex &c2);
    friend bool operator==(const Complex &c1, const Complex &c2);
    friend bool operator!=(const Complex &c1, const Complex &c2);
    //以成员函数的形式重载
    Complex & operator+=(const Complex &c);
    Complex & operator-=(const Complex &c);
    Complex & operator*=(const Complex &c);
    Complex & operator/=(const Complex &c);
public:  //成员函数
    double real() const{ return m_real; }
    double imag() const{ return m_imag; }
private:
    double m_real;  //实部
    double m_imag;  //虚部
};

//重载+运算符
Complex operator+(const Complex &c1, const Complex &c2){
    Complex c;
    c.m_real = c1.m_real + c2.m_real;
    c.m_imag = c1.m_imag + c2.m_imag;
    return c;
}
//重载-运算符
Complex operator-(const Complex &c1, const Complex &c2){
    Complex c;
    c.m_real = c1.m_real - c2.m_real;
    c.m_imag = c1.m_imag - c2.m_imag;
    return c;
}
//重载*运算符  (a+bi) * (c+di) = (ac-bd) + (bc+ad)i
Complex operator*(const Complex &c1, const Complex &c2){
    Complex c;
    c.m_real = c1.m_real * c2.m_real - c1.m_imag * c2.m_imag;
    c.m_imag = c1.m_imag * c2.m_real + c1.m_real * c2.m_imag;
    return c;
}
//重载/运算符  (a+bi) / (c+di) = [(ac+bd) / (c²+d²)] + [(bc-ad) / (c²+d²)]i
Complex operator/(const Complex &c1, const Complex &c2){
    Complex c;
    c.m_real = (c1.m_real*c2.m_real + c1.m_imag*c2.m_imag) / (pow(c2.m_real, 2) + pow(c2.m_imag, 2));
    c.m_imag = (c1.m_imag*c2.m_real - c1.m_real*c2.m_imag) / (pow(c2.m_real, 2) + pow(c2.m_imag, 2));
    return c;
}
//重载==运算符
bool operator==(const Complex &c1, const Complex &c2){
    if( c1.m_real == c2.m_real && c1.m_imag == c2.m_imag ){
        return true;
    }else{
        return false;
    }
}
//重载!=运算符
bool operator!=(const Complex &c1, const Complex &c2){
    if( c1.m_real != c2.m_real || c1.m_imag != c2.m_imag ){
        return true;
    }else{
        return false;
    }
}

//重载+=运算符
Complex & Complex::operator+=(const Complex &c){
    this->m_real += c.m_real;
    this->m_imag += c.m_imag;
    return *this;
}
//重载-=运算符
Complex & Complex::operator-=(const Complex &c){
    this->m_real -= c.m_real;
    this->m_imag -= c.m_imag;
    return *this;
}
//重载*=运算符
Complex & Complex::operator*=(const Complex &c){
    this->m_real = this->m_real * c.m_real - this->m_imag * c.m_imag;
    this->m_imag = this->m_imag * c.m_real + this->m_real * c.m_imag;
    return *this;
}
//重载/=运算符
Complex & Complex::operator/=(const Complex &c){
    this->m_real = (this->m_real*c.m_real + this->m_imag*c.m_imag) / (pow(c.m_real, 2) + pow(c.m_imag, 2));
    this->m_imag = (this->m_imag*c.m_real - this->m_real*c.m_imag) / (pow(c.m_real, 2) + pow(c.m_imag, 2));
    return *this;
}

int main(){
    Complex c1(25, 35);
    Complex c2(10, 20);
    Complex c3(1, 2);
    Complex c4(4, 9);
    Complex c5(34, 6);
    Complex c6(80, 90);
   
    Complex c7 = c1 + c2;
    Complex c8 = c1 - c2;
    Complex c9 = c1 * c2;
    Complex c10 = c1 / c2;
    cout<<"c7 = "<<c7.real()<<" + "<<c7.imag()<<"i"<<endl;
    cout<<"c8 = "<<c8.real()<<" + "<<c8.imag()<<"i"<<endl;
    cout<<"c9 = "<<c9.real()<<" + "<<c9.imag()<<"i"<<endl;
    cout<<"c10 = "<<c10.real()<<" + "<<c10.imag()<<"i"<<endl;
   
    c3 += c1;
    c4 -= c2;
    c5 *= c2;
    c6 /= c2;
    cout<<"c3 = "<<c3.real()<<" + "<<c3.imag()<<"i"<<endl;
    cout<<"c4 = "<<c4.real()<<" + "<<c4.imag()<<"i"<<endl;
    cout<<"c5 = "<<c5.real()<<" + "<<c5.imag()<<"i"<<endl;
    cout<<"c6 = "<<c6.real()<<" + "<<c6.imag()<<"i"<<endl;
   
    if(c1 == c2){
        cout<<"c1 == c2"<<endl;
    }
    if(c1 != c2){
        cout<<"c1 != c2"<<endl;
    }
   
    return 0;
}
```

## 重载<<和>>运算符

```cpp
istream & operator>>(istream &in, complex &A){
    in >> A.m_real >> A.m_imag;
    return in;
}

ostream & operator<<(ostream &out, complex &A){
    out << A.m_real <<" + "<< A.m_imag <<" i ";
    return out;
}
```

## 重载下标运算符

下标运算符`[ ]`必须以成员函数的形式进行重载

* 返回值类型 & operator[ ] (参数);

或者：

* const 返回值类型 & operator[ ] (参数) const;

使用第一种声明方式，`[ ]`不仅可以访问元素，还可以修改元素。使用第二种声明方式，`[ ]`只能访问而不能修改元素。在实际开发中，我们应该同时提供以上两种形式，这样做是为了适应 const 对象，因为[通过 const 对象只能调用 const 成员函数](http://c.biancheng.net/view/2232.html)，如果不提供第二种形式，那么将无法访问 const 对象的任何元素。

```cpp
#include <iostream>
using namespace std;

class Array{
public:
    Array(int length = 0);
    ~Array();
public:
    int & operator[](int i);
    const int & operator[](int i) const;
public:
    int length() const { return m_length; }
    void display() const;
private:
    int m_length;  //数组长度
    int *m_p;  //指向数组内存的指针
};

Array::Array(int length): m_length(length){
    if(length == 0){
        m_p = NULL;
    }else{
        m_p = new int[length];
    }
}

Array::~Array(){
    delete[] m_p;
}

int& Array::operator[](int i){
    return m_p[i];
}

const int & Array::operator[](int i) const{
    return m_p[i];
}

void Array::display() const{
    for(int i = 0; i < m_length; i++){
        if(i == m_length - 1){
            cout<<m_p[i]<<endl;
        }else{
            cout<<m_p[i]<<", ";
        }
    }
}

int main(){
    int n;
    cin>>n;

    Array A(n);
    for(int i = 0, len = A.length(); i < len; i++){
        A[i] = i * 5;
    }
    A.display();
   
    const Array B(n);
    cout<<B[n-1]<<endl;  //访问最后一个元素
   
    return 0;
}
```

## 重载++和--运算符

```cpp
#include <iostream>
#include <iomanip>

using namespace std;

//秒表类
class stopwatch {
public:
    stopwatch() : m_min(0), m_sec(0) {}

public:
    void setzero() {
        m_min = 0;
        m_sec = 0;
    }

    stopwatch run();  // 运行
    stopwatch operator++();  //++i，前置形式
    stopwatch operator++(int);  //i++，后置形式
    friend ostream &operator<<(ostream &, const stopwatch &);

private:
    int m_min;  //分钟
    int m_sec;  //秒钟
};

stopwatch stopwatch::run() {
    ++m_sec;
    if (m_sec == 60) {
        m_min++;
        m_sec = 0;
    }
    return *this;
}

stopwatch stopwatch::operator++() {
    return run();
}

stopwatch stopwatch::operator++(int n) {
    stopwatch s = *this;
    run();
    return s;
}

ostream &operator<<(ostream &out, const stopwatch &s) {
    out << setfill('0') << setw(2) << s.m_min << ":" << setw(2) << s.m_sec;
    return out;
}

int main() {
    stopwatch s1, s2;

    s1 = s2++;
    cout << "s1: " << s1 << endl;
    cout << "s2: " << s2 << endl;
    s1.setzero();
    s2.setzero();

    s1 = ++s2;
    cout << "s1: " << s1 << endl;
    cout << "s2: " << s2 << endl;
    return 0;
}
```

## 重载new和delete

内存管理运算符 new、new[]、delete 和 delete[] 也可以进行重载，其重载形式既可以是类的成员函数，也可以是全局函数。一般情况下，内建的内存管理运算符就够用了，只有在需要自己管理内存时才会重载。

```cpp
// 以成员函数的形式重载 new 运算符：
void * className::operator new( size_t size ){
    //TODO:
}

// 以全局函数的形式重载 new 运算符：
void * operator new( size_t size ){
    //TODO:
}
```

两种重载形式的返回值相同，都是`void *`类型，并且都有一个参数，为`size_t`类型。在重载 new 或 new[] 时，无论是作为成员函数还是作为全局函数，它的第一个参数必须是 size_t 类型。size_t 表示的是要分配空间的大小，对于 new[] 的重载函数而言，size_t 则表示所需要分配的所有空间的总和。

当然，重载函数也可以有其他参数，但都必须有默认值，并且第一个参数的类型必须是 size_t。

```cpp
// 以类的成员函数的形式进行重载：
void className::operator delete(void *ptr){
    //TODO:
}

// 以全局函数的形式进行重载：
void operator delete(void *ptr){
    //TODO:
}
```

两种重载形式的返回值都是 void 类型，并且都必须有一个 void 类型的[指针](http://c.biancheng.net/c/80/)作为参数，该指针指向需要释放的内存空间。

> 如果将new方法和delete方法都重载为私有，那么就无法在堆上创建对象，而只能在栈上创建。
>

## 重载强制类型转换符

在 C++ 中，类型的名字（包括类的名字）本身也是一种运算符，即类型强制转换运算符。

类型强制转换运算符是单目运算符，也可以被重载，但只能重载为成员函数，不能重载为全局函数。

```cpp
#include <iostream>
using namespace std;
class Complex
{
    double real, imag;
public:
    Complex(double r = 0, double i = 0) :real(r), imag(i) {};
    operator double() { return real; }  //重载强制类型转换运算符 double
    // 重载强制类型转换运算符时，不需要指定返回值类型，因为返回值类型是确定的，就是运算符本身代表的类型
};
int main()
{
    Complex c(1.2, 3.4);
    cout << (double)c << endl;  //输出 1.2
    double n = 2 + c;  //等价于 double n = 2 + c. operator double()
    cout << n;  //输出 3.2
}
```
