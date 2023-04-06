# C++ 侯捷视频学习笔记

## 基于对象

### 构造函数

- 尽量使用初始化列表
  - 一个变量 有两个阶段 ： 初始化、赋值
  - 而构造函数的初始化列表属于初始化，速度快
  - 如果没用初始化列表，而在函数体内实现，则视为放弃初始化，进行赋值，速度慢

### 参数传递与返回值

- 常量成员函数 
  - 函数后面加`const`，表示该函数不可以改动值

- pass 值传递和引用传递
  - 尽量不要传值，如果值太大了很慢，传引用很快
  - 为了传的快，而使用传引用，不希望他改动，使用`const`

- return 返回传递
  - 也尽量使用引用
- `friend`友元
  - 可以直接取得`private`中的值
  - `calss`的各个`object`互为`friends`
- `return by reference`
  - 检查要返回的东西放在什么地方
  - 局部变量不能传引用

### 函数重载与临时对象

- 操作符重载（成员函数）
- 操作符重载（非成员）
- 临时对象 typename (),直接加小括号
- 内联函数 `inline`
  - 在类外声明时需要显式书写
  - 类内可以隐藏，即在类内声明的函数自动视为内联函数。
  - 会在编译时，直接在调用处展开函数体
  - 不会调用函数，没有函数栈帧的开销，用空间换时间
  - 但是当代码量大，使用循环和递归，不宜使用内联，即使使用内联，编译器也会自行判断为普通函数

### 内存管理

- `new`和 `delete`
  - `new`分为三步：创建内存（malloc）*void、类型转换、调用构造函数
  - `delete`分为两步：调用析构函数、删除内存（free）
- `new` 和`delete`是不能重载，但是分解后的里面那个`operator new`和`operator delete`可以重载，涉及到内存池
- `new`一个数组的时候，内存会多一个头记录数组多长（大小为int 4字节）
  - `delete[] p`由于会根据上面的头的数值，唤起对应次数的析构函数，所以如果不加[]，则只有第一个调用了析构函数，但是最后其实申请的空间都会被释放，因为空间申请时的头部记录了申请了多少空间，所以实际上内存泄漏时因为析构函数没调用（如果这个类并没有指针，即析构函数没有重要的东西，则其实不会发生什么，但是良好习惯应该加上【】）
  - ![image-20230328141945567](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230328141945567.png)

### 扩展补充

- 函数只有一份，函数不属于对象

  - ```c++
    class C{
    public:
        int a;
        void fun();
    }
    C c1;
    C c2;
    c1.fun();
    c2.fun();
    
    //这样写并不是意味着分别调用c1和c2的fun函数
    //fun函数只有一份，转化成下面这样更加直观
    C::fun((&c1));
    C::fun((&c2));
    //这里面的&c1值得是把c1的地址传给函数的this指针
    ```

- 静态变量只有一份，不属于对象

- 静态函数没有this指针，只能处理静态变量

  - 可以通过对象调用
  - 可以通过类名调用 

- namespace

  - ```c++
    using namespace std;//全部打开
    using std::cout;//cout打开，不需要std::cout
    ```

    

## 面向对象

### 类与类的关系 复合、委托、继承（is-a）

- 复合（has-a）（有另一个class的对象）

  - ![image-20230328153114434](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230328153114434.png)
  - 比如`queue`其实底层用`deque`实现，只开放了部分功能

  - ![image-20230328153132818](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230328153132818.png)
  - 构造由内向外，先构造组件，在构造自己
  - 析构由外而内，先析构自己，在析构组件

- 委托 有另一个class的指针
  - 编译防火墙，Handle/Body

- 继承 

## 其他拓展

### 转换函数

- 把class转换成其他类型

- 语法

  - 没有返回类型

  - 通常不改动，加上`const`

  - ```c++
    class Fraction
    {
    public:
        
        Fraction(int num, int den=1)
            :m_numerator(num), m_denominator(den) {}//初始化列表
        
        //转换函数，把class转换为double
        operator double() const {
            return (double) (m_numerator / m_denominator);
        }
    private:
        int m_denominator;
        int m_numerator;
    }
    
    Fraction f(3,5);//创建了一个对象：三分之五
    double d = f + 4;//调用转换函数，把f转换为double
    ```

- `non-explicit-noe-argument`——能把其他转为该class

  - ```c++
    class Fraction
    {
    public:
        
        Fraction(int num, int den=1)
            :m_numerator(num), m_denominator(den) {}//初始化列表
        
        Fraction operator+(const Fraction& f) {
            return Fraction(......);
        }
        
        //转换函数，把class转换为double
        operator double() const {
            return (double) (m_numerator / m_denominator);
        }
    private:
        int m_denominator;
        int m_numerator;
    }
    
    Fraction f(3,5);//创建了一个对象：三分之五
    Fraction d2 = 4;//会将4变为4/1
    Fraction d2 = f + 4;// [Error] ambiguous
    
    ```

- `explicit`只加在构造函数前面，说明在构造的时候调用，不进行类型转换

### 智能指针

- class设计出来像一个指针
  - 里面一定装着一个真正的指针
  - 需要对`*`和`->`进行重载
  - `*`会消耗掉`->`不会

```c++
template<class T>
class shared_ptr
{
public:
    T& operator*() const
    { return *px; }
    T* operator->() const
    { return px;  }
    
    shared_ptr(T* p) : px(p) {}
    
private:
    T* px;
    long* pn;
}
```

- 迭代器
  - 除了`*` `->`还需要处理++、--等等

### 仿函数

- class设计出来像一个函数

### namespace 经验谈

### 类模板

### 函数模板

### 成员模板

### 特化

### 偏特化

- 个数
- 范围

### C++11 

- 模板参数个数不限

### VPTR、VTBL

- 3个条件
- 多态

### const

- 放在成员函数的后面，不改data

![image-20230328203508739](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230328203508739.png)

