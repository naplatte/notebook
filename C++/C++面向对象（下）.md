## C++面向对象（下）

### 1.转换函数

- 转换函数是一种特殊的成员函数，用于将一个 **类的对象转换为其他（你认为合理的）类型**
  - 比如一个分数 class，可以想到将其转化为 double 类型
  - 也可以将其转换为字符串类型，只要你认为这么做合理即可
- 这样的好处就是可以让 **自定义的类对象像内置数据类型一样使用**
- 格式：**关键字 operator 后直接跟转换类型，相当于转换类型作为函数名，因为函数名本身就是返回类型，因此函数不需要声明返回类型**
- 一般需要声明为 const，不会修改当前对象

```cpp
class Fraction {
public:
    Fraction(int n,int d) : numerator(n),denominator(d){}
    operator double() const {
        return (double)numerator / denominator;
    }
    
private:
    int numerator; //分子
    int denominator;//分母
};

int main (){
    Fraction f(1,2);
    double res = 3 + f;
    cout << res << endl;//3.5
    return 0;
}
```

- 程序执行 **`double res = 3 + f`** 的过程
  - 因为执行的是 int 型 + 类的对象，C++本身无法提供这种加法，所以进行以下操作
  - 首先看是否有关于 **① 运算符+的重载**，找是否有 `double operator + (Fraction f,double d){}`
  - 没找到后再去看类成员函数中是否有 **② 转换函数**，即是否可以将类对象转换为其他类型

### 2.non-explicit one argument constructor 非显示单参数构造函数

- 如果想实现 `Fraction res = f + 3`，即一个分数类对象 + 整数，返回分数类对象，编译器在执行时，执行顺序如下
  - 首先找是否有分数类对象+整数的重载 - 没找到
  - 其次尝试是否可将**右操作数**(3)转换为分数 - 找到非显示单参数构造
- 编译器会 ==优先保证左操作数的类型不变==，所以不会先考虑去找转换函数将左操作数 f 转为 int

```c++
class Fraction {
public:
    Fraction(int n,int d = 1) : numerawtor(n),denominator(d){}
    //加法重载
    Fraction operator + (const Fraction& f) {
        //实现两个分数相加的动作，下面实现过程不重要
        Fraction res = Fraction(1);
        return res;
    }
private:
    int numerawtor;
    int denominator;
};

int main() {
    Fraction f(1,2); // 1/2
    Fraction res = f + 3;
    return 0;
}
```

- 但如果（非显示单参数构造+加法重载）和（转换函数）并存，此时执行 `Fraction res = 3 + f`，就会报错
  - 前者是将整数-> 分数类对象，后者将分数类对象-> double，理论上都可完成 3+f 的操作
  - 编译器面对多种可行方案时，就会报错（这点很模糊很难，先放一下）

```c++
//加法重载
Fraction operator + (const Fraction& f) {
    //实现两个分数相加的动作，下面实现过程不重要
    Fraction res = Fraction(1);
    return res;
}
//转换函数
operator double() const {
    return (double) numerawtor / denominator;
}
```

- 如果给构造函数加上关键字 `explicit`，表示不允许构造函数自动执行像刚刚一样 3 转换为 $\frac{3}{1}$ 的操作

```c++
//显示单参数构造函数
explicit  Fraction(int n,int d = 1) : numerawtor(n),denominator(d){}
```

### 3.pointer like classes 像指针一样的类(对象)

- ==本质上是一个类，但类里面会有一个真正的指针==

<img src="https://raw.githubusercontent.com/mumushu1/Pictures/main/7222a7089d8c60698acd7e8a9001f3be.png" alt="image-20250523094705779" style="zoom:80%;" />

- 关于 **智能指针**，本质是 **”自动管理资源的类“**，标准库的智能指针都是一个个 **类模板**，是 **封装了原始指针并重载操作符的类模板**
  - 下面的例子中，`T* px` 是类中真正的指针
  - `share_ptr<Foo> sp (new Foo)`
    - `(new Foo)` 即调用类构造函数，表示 Foo 类型对象的指针赋予为指针 px
    - 创建类的对象 sp，指定 T 为 Foo 结构体类型，即对象的 px 指针指向一个 Foo 类型的结构体
  - `Foo f(*sp)`
    - `*sp` 是调用类中对 *的重载，返回当前对象的* px 指针，而 px 在上文提到是一个指向 Foo 结构体类型的指针， *px 就是指向的结构体
    - 所以 `Foo f(*sp)` 相当于是进行了一步 **拷贝构造**，创建了一个新的结构体对象 f，f 的“值”就是*sp 指向的结构体
  - `sp->method()`
    - sp 是类对象，sp-> method()就是类对象调用类成员函数
    - 只不过在类中对 `->` 做了重载，会返回 px 指针，而 `->` 会继续往下执行 ==（操作符的链式调用）==，即相当于执行 `px->method()`
    - 而 px 是指向 Foo 结构体的指针，当然可以调用 method()

```cpp
template<class T>
    
class share_ptr {
public:
    T& operator *() const{
        return *px;
    }
    T* operator ->() const {
        return px;
    }
    share_ptr(T* p) : px(p) {}

private:
    T* px;
    long* pn;
};
struct Foo {
    void method() {
		cout << "method已被调用" << endl;
    }
};
int main() {
    share_ptr<Foo> sp (new Foo);
    Foo f(*sp);

    sp->method();

    return 0;
}
```

- 关于 **迭代器**
  - 下面是一个迭代器的简单用法
  
  ```cpp
  list<int> p = {1,2,3,4,5};
  for (list<int>::iterator it = p.begin(); it != p.end(); it++) {
      cout << *it << " " ;
  }
  ```
  
  - 迭代器也和上面一样，都在类中做了操作符重载，在*it 时，我们希望直接取到容器中的 data，而不是 next 或 prior 指针
  - `return &(operator *());`
    - operator *()调用上面的*()函数，即返回 data 的引用
    - &(operator *())：取 data 的引用的地址，* *会得到实际 data 的地址**，因为引用没有独立的地址
    - 所以返回的指针，这个指针就是指向 data
  
  ```CPP
  //通过迭代器获取当前节点的值
  reference operator *() const {
      return (*node).data;
  }
  
  //返回指向当前data的指针
  pointer operator ->() const {
      return &(operator *());
  }
  ```

### 4.function like class 像函数一样的类 （仿函数）

- 仿函数本质上是 **重载了()的类 or 结构体**，为什么会有仿函数，这个以后再说，先关注语法层面

- 一个小例子：调用仿函数时，直接`对象+()即可`

```cpp
template<typename T>

class Add {
public:
    T operator () (T a,T b) {
        return a + b;
    }
};
int main() {
    Add<int> add_int;
    cout << add_int(3,4);

    Add<float> add_float;
    cout << add_float(2.2,4.2);

    return 0;
}
```

### 5.模板

#### ① 类模板&函数模板

- 类模板：类的成员变量可以有不同的类型
- 函数模板：一个模板函数，函数的参数可以有很多类型，但一次调用时，只能指定一种类型，比如本例中的 min1 函数，只能两个 int 比或两个 double 比，不能 int 和 double 比，这样 **模版推导会失败**
  - 如果需要两个不同类型的参数，异或是更多，需要写成两个或多个模板参数 `template <typename T1, typename T2>`

- **class VS typename**
  - C++中 `class` 和 `typename` 在模板参数列表中，**完全等价**，`template<class T>` 和 `template<typename T>` 是一样的
  - **除了** 在模板内部的 **嵌套依赖类型中**，必须使用 `typename`，这部分以后再聊，现阶段知道二者大多情况无区别


```cpp
template<typename T>

class Stone {
private:
    T weight;
    T height;
public:
    Stone(int w,int h) : weight(w),height(h){}

    bool operator < (const Stone &sother) const{
        return this->height < sother.height;
    }

    T get_height() const{
        return this->height;
    }
};

template<typename T>
inline const T& min1(const T& a,const T& b) {
    return b < a ? b : a;
}

int main() {
    Stone<int> s1(1,3);
    Stone<int> s2(1,4);
    //模板函数要求两个参数类型一样
    auto more_min = min1(s1,s2);
    cout << more_min.get_height();
    return 0;
}
```

- 类模板和函数模板总结
  - 类模板用于写 **通用的数据结构类，** 函数模板用于写 **通用算法函数**
  - 类模板在创建对象时必须 **指定** 类型，函数模板在调用时 **自动推导类型**

#### ③ 成员模板

> 这里相当模糊，等之后深造吧

- 定义：**一个类（或结构体）中的成员函数是模板函数**
- 下面的代码：先常创建 <iPhone,MacBook> 类型的 pair 对象 Apple，之后使用 Apple 创建了 baseCombo，这个 baseCombo，类型是 <Phone,Laptop> 但其值还是 Apple 的值，即 **内容是子类，类型是父类**
- 这样的作用：支持不同类型对象之间的拷贝（只要他们的类型可以转换：子类可转换为父类，但父类不能转换为子类），**可以用类型不同的对象去构造另一个模板对象，只要他们类型可以转换**

```cpp
class Phone {
public:
    void call() const {cout << "Calling" << endl;}
};

class Laptop {
public:
    void code() const {cout << "Coding" << endl;}
};

template <typename T1,typename T2>
struct Combo {
    T1 item1;
    T2 item2;
    Combo(){}//默认构造
    //成员模版构造
    template <typename U1,typename U2>
    Combo(const Combo<U1,U2>& other) : item1(other.item1),item2(other.item2){}
};

class iPhone : public Phone {};
class MacBook : public Laptop {};

int main() {
    Combo<iPhone,MacBook> Apple;//一个关于苹果公司的手机+笔记本产品组合
    Combo<Phone,Laptop> baseCombo(Apple);//用“苹果组合”构造出基础组合（手机+笔记本组合）
    //basepack的类型是<Phone,Lapop>类型，但是basepack的值还是iPhone和MacBook的值直接拷贝过来的，也就是内容是子类，但类型是父类！
}
```

#### ④模板特化

- 是什么：在编写泛型代码时，有时想为**某些特定类型定义不一样的实现**，就需要用到模板特化

```cpp
template<typename T>
struct MyPrint { //模板类 - 泛化
    void print() {
        cout << "generic type" << endl;
    }
};
template<> //注意这里没有参数
struct MyPrint<int> { //特化模板（只适用于int） - 特化
    void print() {
        cout << "int type" << endl;
    }
};

int main() {
    MyPrint<float> m1;
    MyPrint<int> m2;
    m1.print();//"generic type"
    m2.print();//"int type"
    return 0;
}
```

- 好处：为某些类型提供更高效的实现；对特定类型特殊处理；避免写多个重复的类/函数

#### ⑤ 模板偏特化

- 是什么：只对模板的一部分参数进行特化，**保留其他参数的泛型性**，上面的特化是全特化，与之对应就是现在的偏特化，偏特化是介于“通用模板”和“全特化”之间的一种灵活方案
- ==个数上的偏特化==：即多个参数时，特化部分参数

```cpp
template<typename T1,typename T2> //泛型模板
struct Type {
    static void print() {
        cout << "general template" << endl;
    }
};

template<typename T> //第一个参数任意类型，指定第二个参数为int类型
struct Type<T,int> {
    static void print() {
        cout << "偏特化：第二项为int类型" << endl;
    }
};

int main() {
    Type<double,string> t1;
    Type<double,int> t2;
    t1.print();//"general template"
    t2.print();//"偏特化：第二项为int类型"
    return 0;
}
```

- ==范围上的偏特化==：没有具体指定某个参数特化，而是给出一个**范围**，比如对指针类的所有类型都匹配这个特化模板

```cpp
//通用版本
template<typename T>
struct C {
    void print() const{
        cout << "非指针的输出" << endl;
    }
};

//偏特化版本
template<typename U>
struct C<U*> {
    void print() const{
        cout << "指针的输出" << endl;
    }
};

int main() {
    C<int> c1;
    C<int*> c2;
    c1.print();//非指针的输出
    c2.print();//指针的输出
    return 0;
}
```

#### ⑥ 模板模板参数

- 是什么：一个模板的参数列表中，允许将另一个模板当做参数传入，下面通过一个例子理解
- 一个容器模板

```cpp
template<typename T>
class Container {
public:
    void add(const T& x){}
    T get(int i) const {}
};
```

- 下面针对这个容器模板设置一个管理者模板，其本身也是个模板，需要的参数为
  - 一个模板参数：即需要管理的容器模板，比如`vector`,`list`
  - 一个类型模板：容器中存放的元素类型，比如`int`,`stirng`
  - `template<template<typename> class C,typename T>`语法记一下

```cpp
//容器模板Container
template<typename T>
class Container {
public:
    void add(const T& x){}
    T get(int i) const {}
};

//管理者模板Manger
template<template<typename> class C,typename T>// “C 是一个模板，它本身接受一个类型参数”
class Manger {
    C<T> data;//用传入的容器模板C来实例化一个对象
public:
    void add(const T& x) {data.add(x);}
    T get(int i)const {return data.get(i);}
};

int main() {
    Manger<Container,int> m1;//用自定义的Container存int
    Manger<vector,int> m2;//用vector存int
    Manger<list,int> m3;//用list存int
}
```

### 6.值、指针和引用

#### ①概念初窥

```cpp
int x = 5;
int* p = &x;//&x是对x取地址
int& r = x;

cout << "p = " << p << endl;//p = 0x8b2cfff744
cout << "*p = " << *p << endl;//*p = 5
cout << "&p= " << &p << endl;//&p= 0x8b2cfff738
cout << "r = " << r << endl;//r = 5
```

- 指针也是老生常谈了，p是指针 也是个地址，*p即p指向的值，&p即对指针取地址，也就是指针的地址，下面主要谈下引用
- **定义引用时必须要设初值**，且设完之后就不能绑定到其他对象上了（对比指针可以随意换人绑定）

```cpp
int y = 3;
r = y;//这里不是让r绑定到y上，上面说了引用不能换人，这里相当于是将y的值赋给x⭐⭐⭐
cout << x;//可以看到x的值已经变为3了，这里很像函数的参数传入引用，改引用就相当于改其绑定的对象
```

- 引用和值是**一体的两面**，以上面x和r为例，x的地址和r的大小是一样的，地址也是一样的==（全都是假象）==

```cpp
cout << "&x = " << &x << endl;//x的地址 &x = 0x12c01ff66c
cout << "&r = " << &r << endl;//r的地址 &r = 0x12c01ff66c
```

#### ②假象的由来

- **语言层面的真相：引用是一个别名，而不是一个对象**
  - 即不占用新的存储空间，所以上面引用和变量的大小一样，地址一样，引用r就是x，x就是r
  - 所以对r的任何操作都将作用在x上
- **编译器的实现层面：引用通常表现为常量指针**：浅色箭头，所以引用的实际大小应该是4个字节（32位系统下），和指针大小一致

<img src="https://raw.githubusercontent.com/mumushu1/Pictures/main/651a8f4725abb46c4d0402f93631ddb3.png" alt="image-20250529101317956" style="zoom:80%;" />	

#### ③引用的常见用途

- 函数传参：最好是传引用或常量引用，因为速度更快
  - 解释一下速度问题：在上篇说过速度更快，但上文又提到引用的大小和地址与其绑定的对象是一样的，那为什么更快
  - 上文提到大小和地址一样是假象了，在传参时，如果传引用的话本质上是传了一个指针，对比如果是传值的话，需要将整个值压栈（值很大就很占时空），而指针的大小是固定4B(32位系统下)
  - 引用一般不拿来声明变量or定义对象，一般就是参数传递的时候去用
- 返回类型：能返回引用就返回引用

#### ④函数名相同，传参方式不同的两个函数

- 以下两个函数是不能并存的，在语法层面上，引用就是值的别名
  - 在调用func时，不管是传值还是传引用，调用方式都是`func(a)`，如果二者可以并存，调用`func(a)`时编译器无法分清是调用的哪个函数

```cpp
void func(int a) {}
void func(int& a){}
```

- 复习一下const，相同的两个函数，一个加const ， 一个不加const，二者是可以并存的 

```cpp
void func() const{}
void func() {}
```

### 7.语法层面看不到的底层原理

#### ①虚指针和虚表

- 子类继承父类时，子类的对象也会有内存去存储父类private成员，即不能访问 != 不存储，子类不能访问父类private成员，但创建子类对象时，父类private成员也会占内存
- **虚函数、虚表与虚指针**
  - 当类中有虚函数时，对象（内存）中会有一个指向**虚函数表(vtable)**的指针，成为**虚表指针(vtpr)**，既然是指针，位数和地址位数就是一样的，32位系统下占4B
  - 每个含虚函数的类都有一个虚表，用于支持运行时多态
  - ==虚指针和虚表就相当于页表基址寄存器和页表，不太确切，大概这个意思==
  - 每个类的虚函数表，包含自己的虚函数，以及从父类那继承来的虚函数；如果自己重写了父类的虚函数，那么只存储重写后的即可，父类原本的虚函数就不会存储

```cpp
class A {
public:
    virtual void vfun1();
    virtual void vfunc2();
    void func1();
    void func2();
private:
    int m_data1;
    int m_data2;
};

class B : public A {
public:
    virtual void vfunc1();
    void func2();
private:
    int m_data3;
};

class C : public B {
public:
    virtual void vfunc1();
    void func2();
private:
    int m_data4;
};
```

![image-20250531160620045](https://raw.githubusercontent.com/mumushu1/Pictures/main/1bcc529a24c2e6ecdd3059299f3a68e8.png)

- 如果一个指针p指向C类的对象，通过指针调用C类中的虚函数时，编译器发生的动作，用C语言可以表示为`*(p->vptr)[n]`，即通过指针找到虚指针，取其地址就是虚表的地址，`[n]`即为调用虚表中第n个虚函数
- **静态绑定与动态绑定**
  - 绑定：程序在运行时决定调用哪个函数的过程
  - 静态绑定：在**编译期**就决定调用哪个函数，对应汇编中的`call`指令，`call 0x 1A234B7F`，默认情况下，非虚函数都是静态绑定；效率高，但缺乏多态性
  - 动态绑定：在**运行时**才决定调用哪个函数，用于虚函数，**程序在运行时会查虚函数表**，来决定调用哪个类的虚函数，比如上图中如果C对象调用`vfunc1()`，编译器在运行时才查到`vfucn1()`是C类对象的才去调用
  - 动态绑定的三个条件
    - 必须通过指针去调用
    - 指针**向上转型**（以上图为例，就是定义指向A对象的指针，其引用C，即`A* p1 = &c`，子类C->父类A）
      - 类比生活中这很合理，比如狗(子类)->动物(父类)，但动物不能->狗，向上转型是安全的
    - 虚函数

#### ②再谈this

- **简单说：this是一个指针，通过对象调用函数，对象的地址就是this**

```cpp
CDocument::
OnFileOpen(){
    ...
    Serialize();
    ...
}
virtual Serialize();

class CMyDoc : public CDocument{
    virtual Serialize(){...}
};

int main(){
    CMyDoc myDoc;//子类对象
    myDoc.OnFileOpen();
}
```

- 从main函数出发去看，定义了一个子类对象`myDoc`，子类对象调用父类的`OnFileOpen()`
  - 这一步当然是合理的，因为子类继承父类的成员函数
  - 对于成员函数，都有一个默认的参数（this指针），**当`myDoc`调用函数时，这个函数的默认参数是子类对象的this指针**
  - 所以调用此函数的流程是，首先会执行父类的`onFileOpen()`，执行到`Serialize()`时，实际上是`this->Serialize()`，这个this是子类对象的this，`this->Serialize()`即为`*(this->vptr)[n](this)`：前一部分就是动态绑定找虚函数，后面的this是默认参数
- 回顾上面动态绑定的三个条件：首先是指针调用（这里是this指针），其次是向上转型（调用是子类的对象调用），最后是虚函数（完全满足）

#### ③关于动态绑定(Dynamic Binding)

- 前面说过，对于一个静态绑定，对应一个汇编中的call指令，而且**call的一定是一个确定的地址**
- 而对于动态绑定，也是call，但call的内容不同，这里汇编的含义我们就不去了解了，其实现的功能就是`*(p->vptr)[n]`

<img src="https://raw.githubusercontent.com/mumushu1/Pictures/main/b4bd5a0c226347f78a0dfa043e1dfe2d.png" alt="image-20250605094650985" style="zoom:50%;" />	

### 8.再谈const

- 不要出现，定义的对象是const，但其调用的成员函数没加const，会报错
- ==在实现一个成员函数时，就需要考虑要不要加const，这是一个可以很早期完成的工作==

|                                             | 对象是const（成员变量不能改变） | 对象不是const（成员变量可以改变） |
| ------------------------------------------- | ------------------------------- | --------------------------------- |
| **调用的函数是const（保证不改变data）**     | ✔️                               | ✔️                                 |
| **调用的函数不是const（不保证不改变data）** | ❌                               | ✔️                                 |

- 加const和不加const，算作“函数签名”的一部分，即加不加const是两个函数，可以共存（对比函数的返回类型，不属于签名的部分）
- **C++规则怪谈：当成员函数的const版本和non-const版本同时存在时，const对象只能调用const版本的，non-const对象只能调用non-const版本的**，即表格右上角的情况在此时不会出现

### 9.详解new和delete

#### ①基本认知

- 对于类的对象而言，new会调用构造函数，delete会调用析构函数
- `new、delete`：分配/释放一个对象
- `new[]、delete[]`：分配/释放一组对象
- new的底层原理
  1. 调用`operator new(size)`分配内存（底层就是malloc）
  2. 在分配好的内存上调用构造函数
- delete的底层原理
  1. 先调用析构函数
  2. 调用`operator delete(ptr)`释放内存（底层就是free(ptr)），ptr指向这片空间的首地址
  3. free通过查找隐藏的头部得知释放的内存块大小
     - delete[]如何知道元素个数：new[]额外分配了空间，在数组前存储了元素个数（用户看不到）
- new需要知道分配多大内存，需要一个size，而delete

- 可以通过重载`operator`来自定义分配器

#### ②重载operator new、operator delete、operator new[]、operator delete[]

- 下面这段代码影响无远弗届，但目前尚未参透，留得之后学习

```cpp
//封装 malloc/free 的通用内存分配器,可以用来分配任何类型的内存
//*void:通用指针类型，可以指向任何类型
//size_t:无符合整数类型
void* myAlloc(size_t size) {
    return malloc(size);
}

void myFree(void* ptr) {
    return free(ptr);
}

inline void* operator new(size_t size) {
    cout << "这是重载的new" << endl;
    return myAlloc(size);
}

inline void* operator new[](size_t size) {
    cout << "这是重载的new[]" << endl;
    return myAlloc(size);
}

inline void operator delete(void* ptr) {
    cout << "这是重载的delete" << endl;
    return myFree(ptr);
}

inline void operator delete[](void* ptr) {
    cout << "这是重载的delete[]" << endl;
    return myFree(ptr);
}
```

- 也可以对类成员函数new、new[]、delete、delete[]进行重载，用做内存池，这里有待继续研究

#### ③示例

