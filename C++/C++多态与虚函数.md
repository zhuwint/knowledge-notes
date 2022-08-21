## 编译时多态与运行时多态

编译时多态  

* 主要是方法的重载，通过参数列表的不同来区分不同的方法。

运行时多态  

* 也叫作动态绑定，一般是指在执行期间（非编译期间）判断引用对象的实际类型，根据实际类型判断并调用相应的属性和方法。主要用于继承父类和实现接口时，父类引用指向子类对象。

## 构造函数调用顺序

调用顺序：基类构造函数–>派生类成员变量的构造函数–>自身构造函数

对于一个类X进行 **初始化** ：

1. 按照继承声明顺序，**初始化**基类对象
2. **初始化**X的成员对象
3. 调用X构造函数

析构函数调用顺序相反

```cpp
#include <iostream>
using namespace std;

class A
{
public:
    A() { cout << "A()" << endl; }
    ~A() { cout << "~A()" << endl; }
};

class B
{
public:
    B() { cout << "B()" << endl; }
    ~B() { cout << "~B()" << endl; }
};

class Test : public A, public B // 派生列表
{
public:
    Test() { cout << "Test()" << endl; }
    ~Test() { cout << "~Test()" << endl; }

private:
    B ex1;
    A ex2;
};

int main()
{
    Test ex;
    return 0;
}
//A()   
//B()    
//B()
//A()
//Test()
//~Test()
//~A()
//~B()
//~B()
//~A()
```

## 虚函数

通过基类指针只能访问派生类的成员变量，但是不能访问派生类的成员函数。为了消除这种尴尬，让基类指针能够访问派生类的成员函数，C++ 增加了 **虚函数（Virtual Function）** 。使用虚函数非常简单，只需要在函数声明前面增加 virtual 关键字。

```cpp
#include <iostream>
using namespace std;

//基类People
class People{
public:
    People(char *name, int age);
    virtual void display();  //声明为虚函数
protected:
    char *m_name;
    int m_age;
};
People::People(char *name, int age): m_name(name), m_age(age){}
void People::display(){
    cout<<m_name<<"今年"<<m_age<<"岁了，是个无业游民。"<<endl;
}

//派生类Teacher
class Teacher: public People{
public:
    Teacher(char *name, int age, int salary);
    virtual void display();  //声明为虚函数
private:
    int m_salary;
};
Teacher::Teacher(char *name, int age, int salary): People(name, age), m_salary(salary){}
void Teacher::display(){
    cout<<m_name<<"今年"<<m_age<<"岁了，是一名教师，每月有"<<m_salary<<"元的收入。"<<endl;
}

int main(){
    People *p = new People("王志刚", 23);
    p -> display();

    p = new Teacher("赵宏佳", 45, 8200);
    p -> display();

    return 0;
}
```

有了虚函数，基类指针指向基类对象时就使用基类的成员（包括成员函数和成员变量），指向派生类对象时就使用派生类的成员。换句话说，基类指针可以按照基类的方式来做事，也可以按照派生类的方式来做事，它有多种形态，或者说有多种表现方式，我们将这种现象称为 **多态（Polymorphism）** 。

多态是面向对象编程的主要特征之一，C++中虚函数的唯一用处就是构成多态。

> C++提供多态的目的是：可以通过基类指针对所有派生类（包括直接派生和间接派生）的成员变量和成员函数进行“全方位”的访问，尤其是成员函数。如果没有多态，我们只能访问成员变量。
>

### 注意事项

1. 只需要在虚函数的声明处加上 virtual 关键字，函数定义处可以加也可以不加。
2. **虚函数必须实现，否则编译器会报错**。（如果不生成对象则不报错）
3. 为了方便，你可以只将基类中的函数声明为虚函数，这样所有派生类中具有遮蔽关系的同名函数都将自动成为虚函数。关于名字遮蔽已在《[C++继承时的名字遮蔽](http://c.biancheng.net/view/2271.html)》一节中进行了讲解。
4. 当在基类中定义了虚函数时，如果派生类没有定义新的函数来遮蔽此函数，那么将使用基类的虚函数。
5. 只有派生类的虚函数覆盖基类的虚函数（函数原型相同）才能构成多态（通过基类[指针](http://c.biancheng.net/c/80/)访问派生类函数）。例如基类虚函数的原型为`virtual void func();`，派生类虚函数的原型为`virtual void func(int);`，那么当基类指针 p 指向派生类对象时，语句`p -> func(100);`将会出错，而语句`p -> func();`将调用基类的函数。
6. **构造函数不能是虚函数。对于基类的构造函数，它仅仅是在派生类构造函数中被调用，这种机制不同于继承。也就是说，派生类不继承基类的构造函数，将构造函数声明为虚函数没有什么意义。**
7. 析构函数可以声明为虚函数，而且有时候必须要声明为虚函数。

### 构成多态的条件

* 必须存在继承关系；
* 继承关系中必须有同名的虚函数，并且它们是覆盖关系（函数原型相同）。
* 存在基类的指针，通过该指针调用虚函数。

### 什么时候声明虚函数

首先看成员函数所在的类是否会作为基类。然后看成员函数在类的继承后有无可能被更改功能，如果希望更改其功能的，一般应该将它声明为虚函数。如果成员函数在类被继承后功能不需修改，或派生类用不到该函数，则不要把它声明为虚函数。

## 虚析构函数

防止内存泄露，定义一个基类的指针p，在delete p时，

* 如果基类的析构函数是虚函数，这时只会看p所赋值的对象，如果p赋值的对象是派生类的对象，就会调用派生类的析构函数（毫无疑问，在这之前也会先调用基类的构造函数，再调用派生类的构造函数，然后调用派生类的析构函数，基类的析构函数，所谓先构造的后释放）；
* 如果p赋值的对象是基类的对象，就会调用基类的析构函数，这样就不会造成内存泄露。

如果基类的析构函数不是虚函数，在delete p时，调用析构函数时，只会看指针的数据类型，而不会去看赋值的对象，这样就会造成内存泄露。

> 为什么c++默认析构函数不是虚函数？
>
> 因为虚函数需要虚表和虚表指针，会占用额外内存。如果一个类没有子类，就没有必要将析构函数设为虚函数
>

## 纯虚函数与抽象类

纯虚函数没有函数体，只有函数声明，在虚函数声明的结尾加上`=0`，表明此函数为纯虚函数。

包含纯虚函数的类称为抽象类（Abstract Class）。之所以说它抽象，是因为它无法实例化，也就是无法创建对象。原因很明显，纯虚函数没有函数体，不是完整的函数，无法调用，也无法为其分配内存空间。

抽象类通常是作为基类，让派生类去实现纯虚函数。派生类必须实现纯虚函数才能被实例化。

```cpp
#include <iostream>
using namespace std;

//线
class Line{
public:
    Line(float len);
    virtual float area() = 0;
    virtual float volume() = 0;
protected:
    float m_len;
};
Line::Line(float len): m_len(len){ }

//矩形
class Rec: public Line{
public:
    Rec(float len, float width);
    float area();
protected:
    float m_width;
};
Rec::Rec(float len, float width): Line(len), m_width(width){ }
float Rec::area(){ return m_len * m_width; }

//长方体
class Cuboid: public Rec{
public:
    Cuboid(float len, float width, float height);
    float area();
    float volume();
protected:
    float m_height;
};
Cuboid::Cuboid(float len, float width, float height): Rec(len, width), m_height(height){ }
float Cuboid::area(){ return 2 * ( m_len*m_width + m_len*m_height + m_width*m_height); }
float Cuboid::volume(){ return m_len * m_width * m_height; }

//正方体
class Cube: public Cuboid{
public:
    Cube(float len);
    float area();
    float volume();
};
Cube::Cube(float len): Cuboid(len, len, len){ }
float Cube::area(){ return 6 * m_len * m_len; }
float Cube::volume(){ return m_len * m_len * m_len; }

int main(){
    Line *p = new Cuboid(10, 20, 30);
    cout<<"The area of Cuboid is "<<p->area()<<endl;
    cout<<"The volume of Cuboid is "<<p->volume()<<endl;
  
    p = new Cube(15);
    cout<<"The area of Cube is "<<p->area()<<endl;
    cout<<"The volume of Cube is "<<p->volume()<<endl;

    return 0;
}
```

抽象基类除了约束派生类的功能，还可以实现多态。请注意第 51 行代码，指针 p 的类型是 Line，但是它却可以访问派生类中的 area() 和 volume() 函数，正是由于在 Line 类中将这两个函数定义为纯虚函数；如果不这样做，51 行后面的代码都是错误的。我想，这或许才是C++提供纯虚函数的主要目的。

> 一个纯虚函数就可以使类成为抽象基类，但是抽象基类中除了包含纯虚函数外，还可以包含其它的成员函数（虚函数或普通函数）和成员变量。
>
> 只有类中的虚函数才能被声明为纯虚函数，普通成员函数和顶层函数均不能声明为纯虚函数。
>

### 虚函数与纯虚函数的区别

* 定义一个函数为虚函数，不代表函数为不被实现的函数。
* 定义他为虚函数是为了允许用基类的指针来调用子类的这个函数。
* 定义一个函数为纯虚函数，才代表函数没有被实现。
* 定义纯虚函数是为了实现一个接口，起到一个规范的作用，规范继承这个类的程序员必须实现这个函数。

> 对于实现纯虚函数的派生类，该纯虚函数在派生类中被称为虚函数，虚函数和纯虚函数都可以在派生类中重写、
>

## 动态绑定和静态绑定

当函数名被调用时，编译器将选择应该执行的代码，此时即称编译器绑定了函数名。换句话说，当函数被调用时，编译器就会将该该函数的名称和它的定义绑定在一起。

静态绑定发生在编译时，并将名称绑定到一个固定的函数定义，然后在每次调用该名称时执行该定义。例如，编译器使用静态绑定将以下语句中的 getName 绑定到 Person 类（而不是其他类）中的 getName 定义：

```cpp
for (int k = 0; k <people.size (); k++)
{
    cout << people [k] ->getName () << endl;
}
```

在静态绑定中，编译器使用编译时可用的类型信息。如果代码在继承层次结构中的不同类的对象上运行，则编译器可用的唯一类型信息将是用于访问所有对象的基类[指针](http://c.biancheng.net/c/80/)类型。因此，静态绑定将始终使用基类版本的成员函数。

相反，动态绑定发生在运行时。动态绑定仅在编译器可以确定运行时子类对象所属的确切类时才起作用。编译器然后使用这个运行时类型信息来调用该类中定义的函数版本。

为了使动态绑定成为可能，编译器将运行时类型信息存储在具有虚函数的类的每个对象中。动态绑定始终使用对象的实际类中的成员函数版本，而无视用于访问对象的指针的类。
