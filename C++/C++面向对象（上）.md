## C++面向对象（上）

### 1.为什么会出现面向对象

- C 语言：面向过程，定义的数据所有函数都可以访问
- 面向对象：可以将 **数据** 及 **使用这些数据的函数** 放在一起（类） - 每个函数只可以访问它需要访问的数据，不会混杂在一起

![image-20250515112557763](https://raw.githubusercontent.com/mumushu1/Pictures/main/11122e55a971b4d6748066a57f6409f0.png)	

### 2.头文件防卫式声明

- 作用：若一个头文件被包含多次，防止 **重复定义**
- 比如 **`include "a.h"`** 和 **`include "b.h"`** ，如果 b.h 里面又引用 a.h，在编译时 a.h 就会被编译两次

```cpp
#ifndef CPP_STL_COMPLEX_H
#define CPP_STL_COMPLEX_H

class complex {

};

#endif //CPP_STL_COMPLEX_H
```

- 另一种方式： `#param once`，告诉编译器“这个文件只包含一次”

```cpp
#param once
class Person{};
```

### 3.内联函数 - inline

- 函数的调用过程
- 存在即合理：一些函数体较小，比如只进行 **简单** 的加法并返回，执行这种函数时，在 **参数传递、保存上下文** 等函数调用的操作上花费的时间甚至大于执行函数本身的时间
- 内联做了什么：编译器会在调用处 **直接展开函数代码**，而不是走一次“调用+返回”的流程。
- 🎯 **在类内定义的函数，默认就是 `inline` 的，** 类外定义的函数，如果想内联，需要加关键字 inline

```cpp
inline int add(int a,int b) {
    return a + b;}
```

- inline 只是给编译器建议，最后是否为内联编译器自有定夺
- ① 不适合内联：函数复杂、有递归，虚函数 ② 适合内联：短小，简单

### 4.访问级别

- public：可以被类外的函数访问，如果想让类中的函数被外界使用，就将这些函数放在 public 中
- private
    - 只有类内的函数才可访问，**一般存放类中定义的数据**，这也是面向对象本来的目的，将数据及访问数据的函数放在一个类中
    - 当然如果想定义一个只能在类中才能使用的函数，也可以将这个函数放在 private 中
- protect：暂未涉及
- 将成员变量设为 `private` 是为了封装和安全，将访问接口 `real()` 和 `imag()` 设为 `public` 是为了受控地提供读取功能。二者不矛盾，而是配合良好。

```cpp
class complex {
public:
    complex(double r = 0,double i = 0) : re(r),im(i){}
    
    double real() const {
        return re;
    }
    double imag() const {
        return im;
    }

private:
    double re,im;//实部和虚部
};

int main() {
    complex obj(1,2);
    obj.re = 2;
    //使用类内函数访问类中私有变量
    double re = obj.real();
    double im = obj.imag();
    cout << "re = " << re << " im = " << im << endl;

    //使用类外函数访问类中私有变量（错误）
    cout << obj.re;

    return 0;
}
```

### 5.构造函数 & 重载，单例设计模式

- 构造函数的 **初始列 `complex(double r,double i) : re(r),im(i) {}`**
- 一个类可以有多个构造函数，因为会存在 **多种不同的定义实例的方式** ，虽然每个构造函数的名称都相同，但 **编译器对于这些函数的标记是不同的**，编译器可以根据名称、返回值、参数等把一个函数赋予一个唯一的标识
- 将 **构造函数放在 private 中，并在 public 提供静态实例，可形成单例设计模式**  - 一个类不允许外界创建实例，只对外提供唯一实例

```cpp
class A {
public:
    static A& getInstance();

private:
    A();
    A(const A& rhs);
};

A& A :: getInstance() {
    static A a;
    return a;
}
```

### 6.常量函数

- 即在函数参数列表之后，函数体之前，加入 const 关键字
- **只适用于类的成员函数，加上const表示此函数不会通过this指针改变类成员变量的值，而对于全局函数，因为没有this指针和成员变量，所以自然也没有加从const这个说法**

```cpp
  double real() const {return re;}
  double imag() const {return im;}
  
  const complex obj(1,2);
```

- 作用： **若函数会改变变量的值，则不加 const，否则加 const**
- 如果这个函数没有改变变量的值，以上面的 real()和 imag()为例，如果它们没有加 const，在创建对象时，就无法创建 const 类型的对象
    - 直观理解就是：你在创建对象时希望这个对象是 const，也就是 **不可修改的，** 但你的 **函数** 没加 const，即函数认为是可修改的，这就会矛盾；
    - 所以如果你不创建 const 类型的对象，对于不会修改值的函数不加 const 也不会报错，但不推荐！
- **写代码的习惯：对于不会修改变量值的函数，一律写成常量函数**

### 7.参数传递：传值、传引用、传 const 引用

- 值传递：就是把需要传递的变量一整个搬过去，压到栈中，不论这个参数有多大，比如值传递一个大小为 100 的整型数组，那么 400B 的数据就会被压栈
- 引用传递：传引用就理解为传指针，也就是传这个参数的 **地址**
- 写代码的大方向：所有的参数传递都传引用，省空间，效率更高
- 都传引用的问题：我们知道，值传递在函数内部是不能修改参数值的，而引用传递可以，那 **如何在传引用的前提下，还要求函数不改变参数的值？答案就是传 const 引用**，比如之前写过的按 pair 的 key 排序的函数

```cpp
auto cmp = [](const pair<int,int> &pair1 , const pair<int,int> &pair2) {
    return pair1.first < pair2.first;
};
```

- cosnt 表示即使传的是引用，但是在函数内部也不能修改这个参数的值
- 函数返回类型也有返回值以及返回引用，写代码时，如果 **可以返回引用**，那就返回引用，即尽量返回引用
    - 什么时候不能返回引用：返回局部对象的引用 ❌，返回临时对象的引用 ❌
    - 什么时候可以返回引用：当函数返回的是一个 **在函数外仍然有效的对象，** 则可以返回引用

### 8.友元

- ==友元函数可以使用类中 private 内的变量（protect 和 public 自然也可以），友元函数的权限比子类要更强一点，子类只能访问 public 和 protect==
- 这其实是打开了 C++封装的一个大门
- 如果不使用友元，则只能使用类中 public 中提供的方法去拿 private 中的变量，本例中就是 **`real()`** 和 **`imag()`** 两个函数
- **同一个 class 的不同对象之间互为友元**

```cpp
class complex {
public:
    complex() : re(0),im(0){}
    complex(double r,double i) : re(r),im(i) {}

    int add(const complex& obj) {
        return obj.re + obj.im;
    }

    double real() const {return re;}
    double imag() const {return im;}

private:
    double re,im;//实部和虚部
};
int main() {
    complex c1(1,2);
    complex c2 (2,3);
    int res = c1.add(c2); //c1 可以访问 c2 的 private 变量
    cout << "res = " << res;

    return 0;
}
```

### 9.操作符重载 1 - 成员函数

- 任何成员函数，都有一个隐藏的参数 - this 指针（除了静态函数），谁调用这个函数，this 指针就指向谁的地址
- this 是一个指针类型，所以如果函数的参数有 this，则参数类型需要为指针类型而不是引用类型
- **操作符重载：实际上就是一个函数，有返回值，有参数，函数名就是需要重载的操作符，除此之外，在操作符前加上关键字 *operator***
- **`*ths`** 本身在表达式语义上是一个左值（lvalue），可以安全地返回引用。关键在于：**这个对象还在外部“活着”**，生命周期没结束！

```cpp
class complex {
public:
    complex() : re(0),im(0){}
    complex(double r,double i) : re(r),im(i) {}
    double real() const {return re;}
    double imag() const {return im;}

private:
    double re,im;//实部和虚部

public:
		//ths 是一个指针，*ths 是一个 complex 类的对象，返回* ths 就相当于返回了一个对象，所以返回值是 complex&
    complex& __doapl(complex* ths,const complex& r) {
        ths->re += r.re;
        ths->im += r.im;
        return *ths;
    }
    //this 是一个指针，所有上面必须传一个指针，不能传引用
    complex& operator += (const complex& r) {
        return __doapl(this,r);
    }
};
int main() {
    complex c1(1,2);
    complex c2 (2,3);

    c1 += c2;
    cout << c1.real() << "+" << c1.imag();//3 + 5
    return 0;
}

```

- 调用函数的对象，不需要知道函数的返回值是什么 ，比如在执行 c1 += c2 时，c1 无需知道返回值是 complex&类型的，如果仅执行 c1+= c2 的操作，操作符重载对应函数的返回值也可以是 void，但需要考虑使用操作符的多种情况，比如实现 c1 += c2 += c3，就需要要求返回值是 complex&类型

### 10.操作符重载 2 - 非成员函数

- 和成员函数操作符重载的不同：成员函数可以使用 this 指针

```cpp
//class 之外 ①：全局函数
inline double img(const complex& x) {
    return x.imag();
}
inline double real(const complex& x) {
    return x.real();
}
//class 之外 ②：操作符重载
inline complex operator + (const complex& c1,const complex& c2) {//复数 + 虚数
    return complex(real(c1) + real(c2),img(c1) + img(c2)); 
}
inline complex operator + (double x,const complex& c1) {//复数 + 实部
    return complex(real(c1) + x, img(c1));
}
inline complex operator + (const complex& c1,double y) {
    return complex (real(c1),img(c1) + y);
}

```

- 共轭复数 & 重载 << 输出
    - 现代 C++能够 cout 各种类型的数据，实际上就是对 << 做了很多操作符重载 string
    
    ```cpp
    //共轭复数
    inline complex conj (complex& x) {
        return complex(real(x),-img(x));
    }
    //重载 <<，输出复数
    inline ostream & operator << (ostream& os,const complex& x) {
        return os << '(' << real(x) << ',' << img(x) << 'i' << ')';
    }
    ```
    

### 11.临时对象(temp object)

- 用法： **`typename + ()` , typename 可以是任何类型，当然也可以是类**
- 比如上面的三个函数返回 **`complex(real(c1)_+x,img(c2))` ，** 就是创建了一个临时对象，这个对象的 **生命周期在本行代码执行完就结束**
- 所以上面三个函数一定不能返回引用

### 12.复数类总结 - 在写一个类时需要注意的地方

- 构造函数：学会使用 **初始列**
- 函数的参数传递：引用传递，是否需要加 const
- 函数的返回值：是否可以 **return by reference**
- 函数定义：是否需要标记为 **const** ⭐
- 操作符重载：定义在类内还是类外

### 13.拷贝构造、拷贝赋值、析构（big three）

- 字符串类的设计：使用一个指针指向字符串存储的地址，而非直接使用数组存字符串 - 因为字符串长度有大有小，如果在类中设定一个数组成员存字符串，不知道需要设定多大。
- 所以字符串类是一个带指针的类 ，对于带指针的类，一定要自己写出 **拷贝构造&拷贝** 赋值

- 如果不自己写，使用编译器提供的拷贝构造和拷贝复制会怎样：以下面为例，如果使用编译器的复制，会使 b 的指针指向 a 指向的地方，导致 b 原来指向的东西没有指针指向了 **（内存泄漏）**，b 和 a 共同指向一个位置，其中一个析构也会出现问题，这种拷贝称为 **浅拷贝（只拷贝指针），** 我们后面自己实现的拷贝复制，可以实现 **深拷贝**

<img src="https://raw.githubusercontent.com/mumushu1/Pictures/main/4e53f407045d57fe54e97c245dc6d5f7.png" alt="image-20250515112654042" style="zoom: 67%;" />	

```cpp
//构造函数
inline String ::String(const char* str) {
    if (str) {
        m_data = new char[strlen(str) + 1];
        strcpy(m_data,str);
    }
    else {
        m_data = new char[1];
        *m_data = '\0';
    }
}
//拷贝构造函数：直接取目标 str 的内容作为自己的内容（深拷贝）
inline String ::String(const String &str) {
    m_data = new char[strlen(str.m_data) + 1];
    strcpy(m_data,str.m_data);//strcpy 将指针指向的内容复制
}
//拷贝赋值
inline String &String::operator=(const String &str) {
    //检测自我赋值：这一步的必要的，如果自己拷贝自己，没有这一步直接执行 delete [] m_data，会将自己删掉，无法进行后面的拷贝工作
    if (this == &str) {
        return *this;
    }
    delete[] m_data;
    m_data = new char[strlen(str.m_data) + 1];
    strcpy(m_data,str.m_data);
    return *this;
}
//析构函数
inline String :: ~String() {
    delete[] m_data;
}
```

### 14.static

- 静态数据&静态函数：一个类只有一个
    - 静态函数没有 this 指针作为参数（其他成员函数默认有 this 指针作为参数）
    - **静态函数只能处理静态数据**
- 调用静态函数&静态变量的两种方式
    - 通过对象调用（和普通成员变量&成员函数调用方法相同）
    - 通过类名直接调用 **`Account::m_rate`**
- 需要使用静态的场景：比如账户类，每个成员可以有自己的存款、利息，但每个人的利率是相同的，也就是一个类中只需要一个利率，那么就把这个利率设为静态

```cpp
class Account {
public:
    static double m_rate;//利率

    static void set_rate(const double& x) {
        m_rate = x;
    }
};
double Account::m_rate = 9.0;//如何赋值

int main() {
    Account obj;
    cout << obj.m_rate << endl;

    //直接使用类名访问静态成员函数和静态成员变量
    Account::set_rate(5.0);
    cout << Account::m_rate;
}
```

### 15.模板

- 类模板：类的成员变量可以有不同的类型

```cpp
template<typename T>

class lr_template {
public:
    lr_template(const T& r,const T& i) : re(r),im(i){}
    T get_re() const {return re;}
    T get_im() const {return im;}

private:
    T re;
    T im;
};

int main() {
    lr_template<int> c1(12,3);
    lr_template<float> c2(2.1,32.4);
}
```

- 函数模板

```cpp
class Stone {
public:
    Stone(int w,int h,int weight) : w(w),h(h),weight(weight){}

    bool operator < (const Stone& sto) const {
        return this->weight < sto.weight;
    }

    int get_weight() const {
        return this->weight;
    }

private:
    int w,h,weight;
};

template<typename T>
inline const T& min_cl(const T& a,const T& b) {
    return a < b ? a : b;
}

int main() {
    Stone s1(1,1,2);
    Stone s2(1,1,3);
    //调用时自动推导参数类型
    auto res = min_cl(s1,s2);
    cout << "较小的weight为:" << res.get_weight() << endl;

    return 0;
}
```

- 类模板和函数模板总结
    - 类模板用于写 **通用的数据结构类，** 函数模板用于写 **通用算法函数**
    - 类模板在创建对象时必须 **指定** 类型，函数模板在调用时 **自动推导类型**

### 16.命名空间

- 作用：避免命名冲突，在同一命名空间中，不能出现名字相同功能不同的函数（除了重载），不同命名空间中则可以
- C++的标准库定义在 std 命名空间中

```cpp
#include <iostream>
namespace MySpace{
    void printf() {
        std::cout << "This is my namespace" << std::endl;
    }
}

int main() {
	using namespace MySpace;
  printf();//This is my namespace
}
```

- 三种指定命名空间的方法

```cpp
//法一
using namespce std;

//法二：仅使用命名空间中的某几个函数
using std::cout;// from xx import xx

//法三：直接在使用前声明
std::cout << "hello" << std::endl;
```

以上内容是关于 **基于对象的设计**，即设计单一的类，无论这个类是否带指针，下面的内容是关于 **面向对象的设计**，即类与类之间具有 **关系** 

### 17.类与类之间的关系

- composition（复合）
- inheritance（继承）
- delegation（委托）

### 18.composition 复合（has-a 的关系）

- 复合是指 **一个类中有另一个类的对象作为成员变量**
- 比如队列 queue 和双端队列 deque，一个类的成员函数直接使用另一个类的成员函数
- 形成的设计模式：***adapter 适配器***，使用复合来实现“行为转发“，即下面 queue 类中 deque 类的对象调用自己的函数
  
    <img src="https://raw.githubusercontent.com/mumushu1/Pictures/main/b90145630b1d47c4f27e408c59f96b90.png" alt="image-20250515112731756" style="zoom:80%;" />		
    
- 下面的例子就类似于 DS 中的邻接表数据结构，在一个 struct ALGraph 中存在 struct VNode 类型的对象

<img src="https://raw.githubusercontent.com/mumushu1/Pictures/main/9a6802ef0b67068f34c39becba764933.png" alt="image-20250515112754358" style="zoom: 80%;" />	

```cpp
typedef struct ArcNode{
	int data;
	struct ArcNode* next;
}ArcNode;

typedef struct VNode{
	int data;
	struct ArcNode* first;
}VNode;

typedef struct{
	struct VNode* List[maxsize];
	int numv,nume;
}ALGraph;
```

- 复合关系下的构造和析构（编译器执行的顺序），以 queue 和 deque 为例
    - 构造由内而外：先调用 deque 的 **默认构造函数**，再调用 queue 的构造函数（这个应该不难理解）
    - 析构由外而内：先调用 queue 的析构函数，再调用 deque 的构造函数（想象成剥洋葱，先拿掉外面的瓣再拿里面的瓣）
- **类之间的复合关系，就是一个类作为另一个类的“成员变量”，构成“整体–部分”的结构，复合对象随宿主类一起创建和销毁，生命周期绑定在一起。**

### 19.delegation 委托

- 概念：一个对象 **将某个操作的实现委托给另一个对象来完成**，即“功能代理”。
- 如何理解委托：即 **composition by reference，** 也是一个类中含有另一个类的东西，只不过含有的不是另一个类的对象，而是另一个类的指针
- 这样做的意义：主类给外界提供接口，至于主类的函数，都是另一个类实现的，主类中包含这个类的指针来调用自己的函数，**接口与实现严格地分开**

<img src="https://raw.githubusercontent.com/mumushu1/Pictures/main/5d811b776eb5846182c42f82be2c77ce.png" alt="image-20250515112828966" style="zoom:80%;" />	

- 复合 VS 委托
    - **复合** 是“我有一个你”
    - **委托** 是“我让你替我做这事”

### 20.inheritance 继承（is-a 的关系）

- 概念：子类继承父类的成员变量和成员函数
- 子类对象中有一部分是父类的东西（可以类比数据库中的子类 - 研究生和本科生的父类是学生，它们都具有学生的属性，加上他们自己独有的属性）
- 继承关系下的构造和析构（编译器的执行顺序）
    - 构造：由内（父类）而外（子类）
    - 析构：由外（子类）而内（父类）
- 进一步剖析继承关系下子类和父类的构造函数
    - 如果父类使用默认构造，则在子类的构造函数中自动调用父类的默认构造
    - **如果父类自己写了构造，则在子类的构造函数中必须显示调用父类的构造函数**

```cpp
class Person {
public:
    Person(string i,string n) : id(i),name(n){}
    void print() const {
        cout << "I'm a person" << endl;
    }
    string res_id() const {
        return this->id;
    }
    string res_name() const {
        return this->name;
    }
protected:
    string id;
    string name;
};

class Student : public Person {
public:
    ****//父类不使用默认构造，则子类的构造函数列表需要调用父类的构造函数
    Student(string id,string name,string school,int grade) : Person(id,name),school(school),grade(grade){}
    string res_school () const {
        return this->school;
    }
    int res_grade() const {
        return this->grade;
    }
private:
    string school;
    int grade;
};
int main() {
    Student s1("101","nap_latte","pku",500);
    cout << "学生的姓名：" << s1.res_name() << endl;
    cout << "学生的学校：" << s1.res_school() << endl;
    cout << "学生的成绩：" << s1.res_grade() << endl;
//    学生的姓名：nap_latte
//    学生的学校：pku
//    学生的成绩：500
    return 0;
}
```

- 三种继承方式
  
  
    | 继承方式 | 父类 public 成员在子类中变成 | 父类 protected 成员在子类中变成 | 变化 |
    | --- | --- | --- | --- |
    | public | public | protected | 不变（is-a 的关系） |
    | protected | protected | protected | 全变为 protect |
    | private | private | private | 全变为 private |

### 21.虚函数

- virtual 函数：希望子类重写（override）这个函数（这个函数在父类已有默认定义）
- pure virtual（纯虚函数）：子类必须重写这个函数（函数在父类中无默认定义）
- 普通函数：不希望子类重写的函数
- 抽象类：包含纯虚函数的类，比如下面的 Shape 类，抽象类只能被继承和实现，不能创建对象（实例化）

```cpp
class Shape {
public:
    virtual void draw() const = 0;//纯虚函数语法：函数 = 0
    virtual void error(const string& msg){//虚函数
        cout << msg << endl;
    }
};

class Rectangle : public Shape {
public:
    void draw() const override {
        cout << "Rectangle draw!" << endl;
    }
};
class circle : public Shape {
public:
    void draw() const override {
        cout << "circle draw!" << endl;
    }
};
int main() {
    Rectangle re;
    circle ci;
    re.draw();
    ci.draw();
    re.error("default error!");
//    Rectangle draw!
//    circle draw!
//    default error!
    return 0;
}
```

- 设计模式之 **Template Method**（模版方法）
    - **在父类中定义算法的骨架，而将其中的某些具体步骤推迟到子类实现**（子类通过重写父类的虚函数）
    - 比如下面的例子，我们需要完成打开文件这个算法，这些算法中有一些通用步骤，比如说检测路径名、文件名等等，但也有些特殊步骤，比如针对文件格式会有不同的处理方法，我们将实现这个方法的函数命名为 Serialize，在父类将其声明为虚函数，在子类中再重写实现具体步骤
    - 下面 main()函数的执行步骤
        1. 子类对象调用父类的 onFileOpen()函数
        2. 执行父类的 onFileOpen()函数，执行到 Serialize()时，跳转到子类对这个虚函数的实现
        3. 执行子类 override 的 Serialize()后回到父类，执行完剩余的 onFileOpen()函数后返回到 main()

<img src="https://raw.githubusercontent.com/mumushu1/Pictures/main/9a0f11a9b01bfd6b7c471dd8de1f205d.png" alt="image-20250515112857913" style="zoom:80%;" />	

- **复合关系 + 继承关系下，编译器执行构造函数的顺序**：① 父类构造函数 ② 子类包含的类的构造函数 ③ 子类构造函数

```cpp
class Shape {
public:
    Shape() {
        cout << "Shape（父类）的构造函数执行了" << endl;
    }
};
class special_rectangle {
public:
    special_rectangle() {
        cout << "special_rectangle（子类复合关系包含的类）的构造函数执行了" << endl;
    }
};

class Rectangle : public Shape {
public:
    special_rectangle sr;
    Rectangle() {
        cout << "Rectangle（子类）的构造函数执行了" << endl;
    }
};

Rectangle re;//子类实例化
```

<img src="https://raw.githubusercontent.com/mumushu1/Pictures/main/3e254ef4290246fe80ddbd5de54d2a11.png" alt="image-20250515112922558" style="zoom:80%;" />	

- 析构次序则完全相反

### 22.观察者模式

- 类似于 Notion 中数据库的不同视图，有表格视图，日历视图，状态栏视图等等
- 当一个对象(Subject)的状态发生变化时，其他所有依赖者(Observer 的子类)都会收到通知并自动更新
- `Observer` 是一个抽象基类，定义了统一的接口 `update()`，不同子类继承 Observer 并重写 update()实现不同视图的更新
- Subject 更新后，需要让所有观察者的数据也更新，但不同观察者更新数据的方式可能不同，因此 Subject 中设置了 Observe *类型的指针，将 update 的任务* *委托**给 Observer

```cpp
class Observer;

class Subject {
    int m_value;
    vector<Observer*> m_views;
public:
    //添加新视图
    void attach(Observer *obs) {
        m_views.push_back(obs);
    }
    //设置 Subject 的新值，并自动通知所有观察者
    void set_val(int value) {
        m_value = value;
        notify();
    }
    //遍历所有观察者，调用它们的 update() 接口，将 subject 对象和 m_value 的值传给观察者
    void notify();
};

class Observer {
public:
    virtual void update(Subject* sub,int value) = 0;//每个观察者都有自己的更新方式，因此设置为虚函数
};

inline void Subject::notify() {
    for (int i = 0; i < m_views.size(); i++)
        m_views[i]->update(this, m_value);
}
```

- 以 Notion 中数据库的不同视图大概举一个例子

```cpp
//被观察者，相当于上面的 subject
class Database {
	vector<Observer*> m_views;
}

class Observer {}//观察者的父类

class Table : public Observer{}//表格视图
class Calendar : public Observer{}//日历视图
class StatusBar : public Observer{}//状态栏视图
```

### 23.组合模式

- 可以让“组合对象”和“单个对象”具有 **一致接口** 进行操作，即可以像处理单个对象一样，统一处理复杂的组合结构
    - 组合对象：类似文件系统中的文件夹
    - 单个对象：类似文件系统中的单个文件

```cpp
class Component {
    int value;
public:
    Component(int val) {value = val;}
    virtual void add(Component*){}
};

//组合对象 - 类似文件系统中的文件夹
class Composite : public Component{
    vector<Component*> c;
public:
    Composite(int val) : Component(val){}
    void add(Component* elem) {//文件夹中可以加入文件
        c.push_back(elem);
    }
};

//叶子结点 - 类似文件系统中的文件
class Primitive : public Component {
public:
    Primitive(int val) : Component(val) {}
};

```