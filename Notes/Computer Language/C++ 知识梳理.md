## 1、多态、虚函数
### 多态是什么

- 多态的意思字面上理解就是一个函数名有多种形态或者说多种状态，我们可以根据需求使用对应的那种形态。
- 多态在c++中分为**静态多态**和**动态多态**。
- **静态多态**，也可以说**编译时多态**，也就是在**编译时就设计了多个状态**，比如说**模板**、**函数重载**都是在编译时就设计好的多态。
- 对应的**动态多态**也就是**运行时多态**，是在程序运行时才产生的多态，是通过**继承和虚函数**来实现的，利用**指针指向不同对象**，进而使函数有不同的实现，这也是**程序设计模式**里最重要的技巧。
### 虚函数的实现机制

- 在说明虚函数的实现机制前，需要先说明的是类继承虚函数实现**多态的前提条件有三个**，**子类继承父类**，**父类有虚函数**，子类对虚函数进行重写，**父类指针指向子类对象**，这样的话父类指针的虚函数才能在运行时动态绑定我们设定的子类对象里的函数，然后是虚函数的实现机制。
- **虚函数**是通过**虚函数指针**和**虚函数表**实现的，一个包含有虚函数的类，当创建这个类对象的时候，除了它自身的字段以外，它的内存还会**多一个四字节的指针**，在VS上你可以通过调试模式看到**这个指针vfptr是放在对象的最前面的**，它指向一个**指针数组**，这个数组就是**虚函数表**，里面存放的**指针指向对应的虚函数**。当我们调用一个对象的虚函数时，它会先通过虚函数指针先找到这个类的虚函数表，在表里找到你需要的那个虚函数。值得一提的时，**虚函数指针是属于类对象**的，存放在对象空间里，而**虚函数表是属于类**的，也就是说**同一个类的所有对象共享这个虚函数表**，它是在**编译**的时候生成的，放在**只读数据段**，也就是**常量区**。
- 然后就是虚函数能做到多态的原因，首先是假设父类有一个虚函数A，子类重写了这个虚函数A，这两个函数都会在编译的时候存放在代码段里，而子类继承父类的同时，也会继承父类的虚函数表，只不过在父类的表里写的是父类虚函数A的地址，而子类改成了重写之后的虚函数A地址。所以当我们通过指向子类对象的父类指针去调用函数时，会先通过虚函数指针找到虚函数表，然后再虚函数表上找到对应的虚函数指针，只不过这个虚函数指针指向的不再是父类的那个 虚函数而是子类的，这就实现的多态。
### 虚函数调用是在编译时确定还是运行时确定的？如何确定调用哪个函数？

- 运行时确定的，通过虚函数表查找对应的虚函数的地址，进而调用对应的函数
### 虚函数是存在类中还是类对象中（即是否共享虚表）？

- **虚函数本身时存放在代码段中的**，而**虚函数指针是属于类对象**的，在VS中我们创建一个包含虚函数的类对象是，可以用sizeof查看它的大小会发现比正常的大四个字节，就是虚函数指针，在调试模式中可以看到创建类对象的时候，会自动在类对象空间的起始地方生成一个虚函数指针VFPTR。而虚函数表是属于整个类的，也就是说同一个类的所有对象共享一个虚函数表，这个虚函数表是在编译的时候生成的，所以应该放在的是**只读数据区，也就是常量区**。这个在VS的调试模式下也可以观察到，比如我写一个带有虚函数的类，同时**设置一个const 常量**，创建类对象后中断，可以看到**常量的地址和虚函数指针的地址只相差四个字节。**

![](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230409205440692.png#id=tAhpn&originHeight=116&originWidth=195&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
### 基类的析构函数不是虚函数会带来什么问题

- 我们使用**父类指针指向子类对象**的时候，**父类析构函数不是虚函数**，在我们delete掉父类指针时，**只会调用父类的析构函数**，此时如果子类对象中有指针，则可能造成内存泄漏，而如果把父类的析构函数设置为虚函数，就会先调用子类的析构函数在调用父类的析构函数。
### **在(基类的)构造函数和析构函数中调用虚函数会怎么样**？

- 在VS上试过，会调用父类的虚函数而不是子类的，这里是因为在**编译时已经静态绑定**了，并没有通过虚函数指针调用这个虚函数。
- 语法上是没有问题的，但是如果我们使用父类指针指向子类对象，在父类的构造函数里调用虚函数时，会调用子类的虚函数，但是因为编译器是先对父类进行构造，在对子类进行构造，此时子类的特有成分还未初始化，如果虚函数使用到了这些成分则一定会出错；同理子类先进行析构，然后父类析构时调用虚函数，但是子类已经被析构掉了，特有成分已经销毁。
## 2、内存模型
### 内存区域

-  C++内存分为5个区域，**堆区**、**栈区**、**全局/静态存储区**、**常量存储区**、**代码区** 
-  栈区： 
   - 是由编译器在需要的时候分配，也由编译器自动释放的区域。用来存放局部变量，函数参数等。存放在栈区的数据只在当前函数或者下一层函数有效，一旦函数返回了，数据就释放了。
-  堆区： 
-  这是C的说法，在C++应该说是**自由存储区**，只不过**默认是堆区**，**也可能是内存池**这就涉及到分配器和我们自己设置的内存管理的问题了。堆区是由new来分配内存块的，但是编译器不会管理内存块的释放，需要使用者自行delete，如果没有释放则会造成内存泄漏，或者在程序结束时由系统自动回收。 
-  全局/静态存储区： 
   - 用来存放全局变量或者静态变量的，里面具体也有两个部分，**初始化过的放在一起.data段**，而**没有初始化的放在一起.bss段**，这部分在程序运行前会清零，也就是你没有初始化的全局变量，其实编译器也会帮你初始化为0，另外静态成员变量在初始化以前是没有分配内存的。
-  常量只读区 
   - **存放全局的const常量**，这个数据区里的东西是**不允许修改**，虚函数表的存放位置也就是常量区，这一点可以通过VS调试看到虚函数指针指向的地址就在常量区。另外**局部的const常量本质还是变量，是存放在栈上的**，虽然也是只读，但是还是有特殊手段可以修改的，虽然没什么意义。
-  代码段： 
   - 存放代码，比如函数的地方。
### 堆和栈的区别

- 管理方式 
   - 堆需要用户手动申请手动释放，容易产生内存泄漏
   - 栈由系统自动分配自动回收，
- 大小和生长方向不同 
   - **堆地址由下向上生长，栈由上往下**。堆的空间比栈大得多，**32位上堆的内存能到4G，**window的**默认栈大小应该是1M**
- 碎片产生 
   - **堆**中内存的分配和释放**会产生内存碎片**，而**栈因为后进先出的机制，不会产生内存碎片**
### 内存对齐

-  为何要有内存对齐? 
   - 1、**可移植性**。有些CPU可以访问任意地址上的任意数据，而有些CPU只能在特定的地址上访问，如果在编译的时候将分配的内存对齐，这样就具有平台的可移植性。
   - 2、**提升性能**。CPU每次寻址都需要花费时间，且访问是按照字长为单位来到，而且如果访问为对齐的内存，处理器需要做两次内存访问，比如CPU某个字的时候，数据从第二个字节开始存储，只存储了一部分，CPU还需要访问下一个字来得到剩下的数据。
-  类对象的对齐规则 
   - 首先不同平台不同编译器有不同的对齐系数，**默认的对齐系数一般是4个字节。**
   - 在类中，第一个成员位于偏移量为0的位置，当然如果有虚函数，0的地方就会是虚函数指针，这就是另外的情况了，从0往后，每个成员的首地址都必须是自身大小和默认对齐系数中比较小的那一个的整数倍，编译器会在中间空余的地方填充字节，一般是填充0。举个例子设置一个类，里面包含3个bool类型的变量和一个int类型的变量，bool类型的大小为1个字节，所以每个bool变量的首地址都要是1的倍数，也就是地址0-1是bool A，1-2是bool B，2-3是bool C，然后是int变量了，由于它的对齐数应该为4，所以它的内存空间应该是地址4-8，而地址3-4则会被系统填充为0。所以一个这样的类的对象大小为8个字节，中间有一个字节填充用于字节对齐。
   - 而对于整个类来说，最后**整个类的地址必须是对齐系数的整数倍**，同时**也要是类中元素最小对齐数的整数倍**就比如前面的例子3个bool和一个int，类的大小必须是对齐系数4和类中最小对齐数1的整数倍，8刚好是就不需要填充，否则也会填充0。
   - 另外变量内存对齐分布是跟顺序有关的，同样是3个bool和一个int，若干先声明两个bool在声明一个int最后声明一个bool，此时两个bool分布在0-2，3-4填充，int分布在4-8，而最后的bool又要放在9上，因此最后类的大小就变成了9，同时又因为类也要对齐为4的倍数，说如果用sizeof查看这个类的大小会是12个字节。
-  空结构体的内存对齐 
   - 空的类对象本身是不占空间的，但是**为了表示占位**，我们如果用sizeof查看空类大小会是一个字节，但是我们如果在一个类中放**一个空类对象，和其他的字段在一起**，空类对象的**大小就会变成0。**
   - 但是需要注意的是如果**放在类中其他字段的最后面**，则**还会是1个字节**，因为如果放在最后为0的话，则我们用指针存放空对象的地址就在类的外面了此时我们销毁类，但是这个空类对象不会被销毁。
### 内存布局

- 单一继承（含成员变量+虚函数+虚函数覆盖） 
   - 一个包含虚函数的类对象中的内存布局，最前面是一个4字节的虚函数指针，指向这个类的虚函数表，虚函数表中存放的是类中虚函数的地址，然后才是类自身的字段。
   - 如果有子类继承这个父类的话，也会有虚函数指针，指向另一个属于这个子类的虚函数表，如果没有虚函数重写，则表中的地址和父类相同，如果有重写的话，地址会修改成重写之后的虚函数地址。指针后面是父类的字段，最后是自己的字段，值得说的是，如果在父类中权限被设置为private的字段，**子类是无法访问的，但是子类的内存空间仍会有这个字段。**
```cpp
class Parent {
public:
    int iParent;
    virtual void func1() {
        std::cout << "Parent" << std::endl;
    }
    virtual void func2() {
        std::cout << "Parent" << std::endl;
    }
    virtual void func3() {
        std::cout << "Parent" << std::endl;
    }
};
class Son : public Parent {
public:
    int iSon;
    virtual void func1() {
        std::cout << "Son" << std::endl;
    }
    virtual void func2() {
        std::cout << "Son" << std::endl;
    }
    virtual void func3() {
        std::cout << "Son" << std::endl;
    }
};
class GrandSon : public Son {
public:
    int iGrandSon;
    virtual void func1() {
        std::cout << "GrandSon" << std::endl;
    }
    virtual void func2() {
        std::cout << "GrandSon" << std::endl;
    }
    virtual void func3() {
        std::cout << "GrandSon" << std::endl;
    }
};
```
![](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230410195815777.png#id=NesFz&originHeight=185&originWidth=267&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

- 多继承关系 
   - 基本与单继承相同，不过会有两个虚函数指针，分别指向从父类1和父类2继承下来的两个虚函数表，这个顺序取决于继承书写的先后顺序。如果父类1和父类2有同名的虚函数f1，子类重写了f1，看虚函数表可以发现只有第一个虚函数表有重写之后的f1，而第二个虚表上f1的位置上会写着**goto第一张虚表**，也就是会让你去第一种虚表找f1函数。
   - 另外如果子类有**新增加的虚函数，会放在第一张虚函数表里面**
```cpp
#include <iostream>
using namespace std;

class Parent1 {
public:
    int iParent1;
    virtual void func1() {
        std::cout << "Parent1" << std::endl;
    }
    virtual void func2() {
        std::cout << "Parent1" << std::endl;
    }
    virtual void func3() {
        std::cout << "Parent1" << std::endl;
    }
};

class Parent2 {
public:
int iParent2;
    virtual void func1() {
        std::cout << "Parent2" << std::endl;
    }
    virtual void func2() {
        std::cout << "Parent2" << std::endl;
    }
    virtual void func3() {
        std::cout << "Parent2" << std::endl;
    }
};

class Son : public Parent2,public Parent1 {
public:
int iSon;
    virtual void func1() {
        std::cout << "Son" << std::endl;
    }
    virtual void func2() {
        std::cout << "Son" << std::endl;
    }
    virtual void func3() {
        std::cout << "Son" << std::endl;
    }
};
```

![](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230410200459761.png#id=sTttc&originHeight=206&originWidth=271&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)![](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230410203725885.png#id=tTjpT&originHeight=234&originWidth=312&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

- 菱形继承（钻石继承） 
   - 基类A 有两个派生类 B、C，然后有一个类D同时继承B、C，看D的内存布局可以看到D继承了B和C的内容，在B和C空间里面分别有一个A，也就是D包含了两份A，一份来自B，一份来自C

![](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230410210008533.png#id=kr7fd&originHeight=302&originWidth=326&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

- 菱形继承（钻石继承）虚继承 
   - B、C虚继承A，D继承B、C，在内存空间上第一部分为B，VBtable指针，指向的是一个虚表，上面记录了B的虚基类的偏移量也就是虚基类的位置，但是**虚函数指针就没有了，放到了后面的虚基类里面**，但是如果**B有新的虚函数**，这里就还会**有一个B的虚函数指针**，然后是B的成员，同理第二部分是C，然后是D自己的成员，最后是虚基类，前面说的VBtable指针指向的虚表上面记录的就是这个虚基类到前面的偏移量。
   - 然后就是C中的偏移量，记录的也是和B相同的虚基类，意思就是虽然B和C都有找到虚基类的指针，只不过指向的是同一个虚基类，也就只有一个虚基类，这避免了菱形继承重复基类的问题。

![](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230410213105540.png#id=RLXTX&originHeight=365&originWidth=259&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
### 钻石继承存在什么问题

- 会有二义性问题，假设A有两个子类B和C，然后D同时继承B和C，观察内存分布会看到D中第一部分是B、第二部分是C，然后这两个里面都会有一个A，也就是存在两个A，此时如果你要调用A的函数，编译器就会不知道调用B中的A还是C中的A。
- 解决办法 虚继承
## 3、内存管理
### C++和C分别使用什么函数来做内存的分配和释放？有什么区别，能否混用？

- C使用`malloc`和`free`来进行内存的分配和释放，需要指定多大的内存
- C使用`new`和`delete`来进行内存的分配和释放，不需要指定多大内存，只需要声明类型。准确的说`new`和`delete`是C里的**关键字**，若是要深究是哪个函数来实现内存管理的话应该是`operator new`和`operator delete`这两个函数
- 想探究`new`和`malloc`之间的区别，就需要解释一下C++中`new`和`delete`到底做了什么 
   - 首先我们new一个类型，实际上编译器会做**3个操作。** 
      - 第一个就是调用`operator new`函数，这个函数在做的事情就是在循环调用malloc申请空间，申请成功就返回空间的首地址，是一个void指针，申请失败就会调用一个new handler，一个解决内存不足的办法，然后再循环回去看看能不能malloc成功，如果最后还是没有申请得到空间，就会抛出异常。值得一提的是，这个operator new函数是可以重载的，这就涉及到后面内存池或者分配器的设计了。
      - 第一个操作是调用operator new，第二个操作就是转型了，再C中malloc回来的是一个void指针我们需要进行强制转型，在刚刚说的operator new 返回的也是一个void指针，只不过转型操作由系统自动进行了。
      - 转型完成之后就是第三个操作，就是调用类的构造函数。这也是和malloc很大的不同，我们new出来的类对象系统会调用构造函数。当然这里说的调用构造函数也只有编译器能做到，我们是不能直接调用一个类的构造函数。只想调用类的构造函数而不申请空间，就是另一个placement new能做到的事了
   - 以上是我们使用new时，编译器做的事情，而当我们使用delete销毁一个内存的时候，系统也做了两个操作。 
      - 第一个就是调用类对象的析构函数，刚刚说的构造函数我们不能显式调用，但析构函数我们是可以显示调用的。
      - 第二个操作就是调用operator delete函数，这个函数内部就是一个free函数，释放掉对应的内存。
   - 刚刚忘记说了，虽然我们new 一个类对象的时候不需要说明多大的空间，但其实operator new 的第一个参数还是要多少字节和malloc一个，但是编译器会计算这个类型的大小有多大，所以我们new的时候不用写申请多少空间。另外它其实还有第二个的参数是一个指针，这是placement new才会用到的东西。
   - operator delete 的参数也和free一样是一个指针，指向你要释放的首地址，之所以delete和malloc为什么知道要释放首地址往后多少空间的内存，就跟这个内存头部的cookie有关了。
- 以上就是我的回答
### 简单内存池的设计

- 内存池的设计思想，每次获取空间时，先看内存池中有没有自由内存，如果有就把自由内存返回，如果没有，就在一次性向堆中malloc一大块内存，分割成很多的小块，返回第一个小块，剩下的作为内存池备用。这样即减少了malloc的次数，也减少的因为cookie的浪费。
```cpp
class myAllocator {
private:
	//寻找下一块内存的嵌入式指针
    struct obj{
        obj* next;
    };
public:
    //分配内存的函数
    void* allocate(size_t size);
    //回收内存的函数
    void deallocate(void* ptr);
    private:
    //指向自由内存的指针，初始为空
    obj* freestore = nullptr;
    //一次性申请的内存大小
    const int CHUNK = 10;
};
//分配的设计
void* myAllocator::allocate (size_t size) {
    //存放返回地址的指针
    obj* p;
    //把这个大块内存分割成小块，并用嵌入式指针把每一块连起来
    if(!freestore) {
        //总共需要申请的大小
        size_t chunk = size * CHUNK;
        //先用freestore保存这块内存的首地址
        p = (obj*)malloc(chunk);
        freestore = p;
        //分割串联
        for(int i = 1;i <= CHUNK-1; ++i) {
            //next指针指向下一个小块内存空间
            p->next = (obj*)((size_t)p + size);
            p = p->next;
        }
        //最后一个小块的next指向空
        p->next = nullptr;
    }
    //把第一块返回，freestore指向下一块
    p = freestore;
    freestore = p->next;
    return p;
}
//回收的设计
void myAllocator::deallocate(void* ptr) {
    //把需要回收的那块内存块放在内存池的开头
    ((obj*)ptr)->next = freestore;
    freestore = (obj*)ptr;
}

//使用自定义分配器的类
class Foo {
public:
    long L;
    Foo(long x = 1) : L(x) {}
    //定义一个静态的自定义分配器对象
    static myAllocator myalloc;
    //重载静态operator new
    void* operator new(size_t size) {
        return myalloc.allocate(size);
    }
    //重载静态operator delete
    void operator delete(void* ptr) {
        return myalloc.deallocate(ptr);
    }
};
myAllocator Foo::myalloc;
```
### STL标准模板库中的alloc

- 在gnuC2.9的版本中使用的容器的分配器就是一个非常经典的内存池设计。在前面说的自定义分配器中是以元素为单位，比如int类型就是分配4字节，double就分配9字节。而在gnuc 2.9中的分配器alloc，则是以字节为单位进行分配的。
- 在分配器里面维护了一条链表，里面装了16个指针，分别对应8字节，16字节，24字节最后到128字节，这些指针指向对应的自由内存，当系统要求分配某个字节，就到对应的指针里去看有没有内存。除了没有指针指向的自由内存，还有一个备用池也会有自由内存。这样吧，我举个具体的申请例子来讲述这个分配器的运行过程。
- 运行过程： 
   - 首先假如我需要申请32字节的空间，分配器就会先去看链表里面第四个也就是对应32字节的位置下面有没有内存，如果没有，就看备用池有没有，如果也没有，就向系统一次性malloc 20 _ 2 _ 32字节的内存，也就是40块 32字节的内存，但是只使用前20个，返回第一块给客户，然后19块放到刚刚说的链表第四个指针下面，剩下20个放在备用池里面。这样下一次如果在申请32字节大小的内存，就会到链表第四个指针下找刚刚放上去的空间。
   - 然后加入我又要申请64字节，刚刚是32字节，现在是64字节，分配器也是会先到对应链表里面第八个指针看上面有没有自由空间，没有，就看备用池里面有没有，备用池里面有刚刚额外申请的20块32字节的空间，这个时候就会把这些空间先分成每块64字节，应该是10块把，返回第一块给客户，剩下9块挂在第八个指针下面。
   - 大概的运行过程就是这样，当然还有一些细节，就是刚刚说的每次malloc会额外申请20块内存作为备用池的自由内存，细节就是这个申请还有一个追加量，会根据你累计申请空间量，动态调整追加量，越申请，每次追加量就越多。这其实就和vector容器每次扩容是倍增的原理一样，总共申请的越多说明之后可能还会要求更多。
   - 当然还有其他细节，比如备用池最多分出20块对应的内存，还有当备用池不够作为一块内存时会先把这个碎片放到对应的链表上在申请空间等等，当时总之alloc内存池的设计大概是这样，当然我只是学习其中原理，并没有手动实现过这个内存池。最后要说的是，这个优秀的内存池设计背后在gnuC 4.9 被放弃了专用没有任何特殊设计的普通分配器了，我也不知道具体的原因。
## 4、类型转换
### 隐式转换

- 当一个值拷贝给另一个兼容类型的时候，隐式转换会自动进行，有编译器操作。
- C++面向对象的多态特性，本质就是通过父类指针实现对子类指针的封装，这其中也发生了隐式转换。除此之外，各种数值类型的转换等等，都给开发带来了不小便利。
- 隐式转换的原则：低精度 -> 高精度，子类 -> 父类
- 发生的条件： 
   - 混合类型的运算中
   - 不同类型的赋值操作
   - 参数传递
   - 返回值传递
- 隐式转换的风险一般在于自定义的类构造函数中，如果类的构造函数只有一个参数，编译器会默认规定这也是一个隐式的类型转换，可能会把参数类型的数据隐式转换成这个类类型。
```cpp
class Test{
public:
    Test(int i) : x(i) {}
    int x;
};

int main () {
    Test t(10);
    t = 20;//编译通过,20隐式转换为了Test(20)
}
```

-  另外可以用 `operator Typename () const {return (Typename) xx}`将该类类型隐式转换成`Typename`类型 
-  在一个参数的构造函数前加上`explicit`可以禁止隐式转换 
### 显示转换

- 类C型的显示转换，即在前面加上`(Typename)`，虽然也能满足需求，但是有风险，因为如果在指针上使用这种强制转换成其他类型的指针，编译器不会报错，但是运行的时候可能会出错。比如有两个指针分别指向A、B两个类，我把B的指针强制转换为A类型并赋给A的指针，如果我们用这个指针调用A的函数，此时编译不会报错，但是运行的时候就会崩溃
#### `dynamic_cast`

-  `dynamic_cast`只能用于指向类的指针和引用或者`void*`，目的是确保类型转换的结果指向目标指针类型的有效完整对象 
-  `dynamic_cast`可以将父类指针转换为子类的指针，如果此时父类指针确实指向的是一个子类对象，则转换成功，否则转换失败，指向null。但是要求父类必须要有虚函数，因为`dynamic_cast`是使用了RTTI信息在运行时推导父类指针实际的对象 
```cpp
class Test {
public:
    virtual void test (){
        cout << "ss" << endl;
    }
};
class Test2 : public Test{

};

class Test3{
public:
    virtual void test() {
        cout << "ss" << endl;
    }
};

int main() {
    Test* t1 = new Test2; 
    Test* t2 = new Test;
    Test3* t3 = new Test3;

    Test2* tt;
    tt = dynamic_cast<Test2*>(t1);
    cout << tt << endl;
    tt = dynamic_cast<Test2*>(t2);
    cout << tt << endl;
    tt = dynamic_cast<Test2*>(t3);
    cout << tt << endl;
}

//结果为
//00C6F820
//00000000
//00000000
//只有第一个转换成功
```
#### `static_cast`

- 相当于隐式转换，可以实现C++中内置基本数据类型之间的相互转换，enum、struct、 int、char、float等，能进行类层次间的向上类型转换和向下类型转换（向下不安全，因为没有进行动态类型检查）。它不能进行无关类型(如非基类和子类)指针之间的转换，也不能作用包含底层const的对象；
#### `const_cast`

- const_cast: 把const属性去掉，即将const转换为非const（也可以反过来），const_cast只能用于指针或引用.
#### `reinterpret_cast`

- 没啥场景，类似C语言中的强制类型转换，什么都可以转，万不得已不要使用，一般前三种转换方式不能解决问题了使用这种强制类型转换方式。
## 5、智能指针
### C++中的智能指针有哪些，各自有什么作用?

- 智能指针主要解决的是内存释放的问题，原本我们使用new申请一块自由存储区的空间时，最后都需要自己用delete回收，而C11引入了智能指针就是为了解决这个问题，它的主要原理就是将回收的程序放在智能指针的析构函数中，然后利用智能指针是创建在栈空间上的对象，他会在生命周期结束的时候有系统自动调用析构函数并回收，这样就做到了自动delete释放内存。C的智能指针具体有四种，分别是auto_ptr、unique_ptr、shared_ptr和weak_ptr，其中auto_ptr已经废弃了，基本上被独占指针unique_ptr取代了，当然你还是能使用的。这些智能指针本质上就是一个长得像指针的模板类，就有点像仿函数的概念，只要知道这几种指针具体的实现方式我们自己也可以写出这三个智能指针出来。
### auto_ptr原理及仿写

- 不允许隐式构造
- 不能将同一个裸指针赋值给多个智能指针，因为他会重复释放
- 释放空间使用的是delete
```cpp
template<class T>
class My_auto_ptr {
public:
    //传入指针的构造函数，默认值为nullptr，禁止隐式转换
    explicit My_auto_ptr(T* ptr = nullptr) : m_ptr(ptr) { 
        //把原指针置空，否则原指针仍然能用
        ptr = nullptr; 
    }
    //拷贝构造函数，把原对象置空
    My_auto_ptr(const My_auto_ptr& src) {
        m_ptr = src.m_ptr;
        src.m_ptr = nullptr;
    }
    //拷贝赋值函数
    My_auto_ptr& operator=(const My_auto_ptr& src) {
        //检查是否是自身
        if (src == *this) return *this;
        m_ptr = src.m_ptr;
        src.m_ptr = nullptr;
        return *this;
    }
    //析构函数
    ~My_auto_ptr() {
        //在析构函数中释放指针
        delete m_ptr;
        m_ptr = nullptr;
    }
    //get函数，返回内部指针地址
    T* get() {
        return m_ptr;
    }
    //将智能指针释放，退化为裸指针返回
    T* release() {
        T* ptr = m_ptr;
        m_ptr = nullptr;
        return ptr;
    }
    //将智能指针原内存释放，并重置为新指针，默认为nullptr
    void reset(T* ptr = nullptr) {
        delete m_ptr;
        m_ptr = ptr;
        ptr = nullptr;
    }
    //重载*，返回指针内容
    T& operator*() const {
        return *m_ptr;
    }
    //重载->返回指针
    T* operator->() const {
        return (&**this);
    }
private:
	T* m_ptr;
};
```
### unique_ptr的原理及仿写

- 独占指针只允许有一份指针指向这份数据,不能将同一个裸指针赋值给多个智能指针
- 也就是在这个模板类里面，拷贝构造和拷贝赋值函数都被删除了，不允许调用，智能移动构造和移动赋值
- 独占指针不允许隐式构造
- 提供了bool运算符重载
- 释放内存使用的是deleter（删除器），里面会判断delete或者delete[]
```cpp
template<class T>
class MyUnique_ptr {
private:
    //私有的内部实际指针
    T* m_ptr;
public:
    //以裸指针构造独占指针，默认为nullptr
    //禁止隐式构造
    explicit MyUnique_ptr(T* ptr = nullptr) : m_ptr(ptr) { 
        ptr = nullptr;
    }
    //因为独占指针只允许有一个指针占有实际内存
    //因此删除拷贝构造和拷贝赋值函数
    MyUnique_ptr(const MyUnique_ptr& src) = delete; 
    MyUnique_ptr& operator=(MyUnique_ptr& src) = delete;
    //移动构造函数
    MyUnique_ptr(MyUnique_ptr&& src) {
        m_ptr = src.m_ptr;
        src.m_ptr = nullptr;
    }
    //移动赋值函数
    MyUnique_ptr& operator=(MyUnique_ptr&& src) {
        if (&src == this) return *this;
        m_ptr = src.m_ptr;
        src.m_ptr = nullptr;
        return *this;
    }
    //析构函数，释放指针
    ~MyUnique_ptr() {
        delete m_ptr;
        m_ptr = nullptr;
    }
    //返回内部指针地址
    T* get() {
        return m_ptr;
    }
    //销毁独占指针，取出裸指针
    T* release() {
        T* ptr = m_ptr;
        m_ptr = nullptr;
        return ptr;
    }
    //重置独占指针
    void reset(T* ptr = nullptr) {
        delete m_ptr;
        m_ptr = ptr;
        ptr = nullptr;
    }
    //交换两个指针，成员函数
    void swap(MyUnique_ptr<T>& src) {
        T* temp;
        temp = src.m_ptr;
        src.m_ptr = m_ptr;
        m_ptr = temp;
    }
    //重载*
    T& operator*() const {
        return *m_ptr;
    }
    //重载->
    T* operator->() const {
        return (&**this);
    }
    //转换函数，重载bool，用在if(u_ptr){}
    operator bool() const {
        return m_ptr != nullptr;
    }
    //声明友元函数
    template<class U>
    friend void swap(MyUnique_ptr<U>& a, MyUnique_ptr<U>& b);
};
//全局函数，用指针make_MyUnique
template<class T>
MyUnique_ptr<T> make_MyUnique(T* ptr = nullptr) {
    return MyUnique_ptr<T>(ptr);
}
//全局函数，有右值引用make_MyUnique
template<class T>
MyUnique_ptr<T> make_MyUnique(MyUnique_ptr<T>&& src) {
    return MyUnique_ptr<T>(forward<MyUnique_ptr<T>>(src));
}
//交换两个独占指针,全局函数
template<class T>
void swap(MyUnique_ptr<T>& a, MyUnique_ptr<T>& b) {
    T* temp;
    temp = a.m_ptr;
    a.m_ptr = b.m_ptr;
    b.m_ptr = temp;
}
```
### shared_ptr的原理及仿写

- 共享指针允许多个指针指向同一份数据，因为它使用了引用计数，每多一个指针指向这个数据，引用技术加一，每销毁一个指针，引用技术减一，如果引用计数为0，则delete这个数据。
- 但是共享指针也不能将同一个裸指针赋值给多个智能指针，因为这样会是两个独立的共享指针，它们会分别计数，也就是意味着到时候它们会重复释放。
- 第一个没有计数器设计的版本：
```cpp
template<typename T>
class MyShared_ptr {
private:
    //内部指针
    T* m_ptr;
    //计数器
    long* m_count;
public:
    //以裸指针构造共享指针，默认为nullptr
    //禁止隐式构造
    explicit MyShared_ptr(T* ptr = nullptr) : m_ptr(ptr) {
        //计数器初始化为1
        m_count = new long(1);
    }
    //以MyUnique_ptr右值构造共享指针
    MyShared_ptr(MyUnique_ptr<T>&& src) {
        //销毁独占指针，取出裸指针
        m_ptr = src.release();
        //计数器初始化为1
        m_count = new long(1);
    }
    //拷贝构造函数
    MyShared_ptr(const MyShared_ptr& src) {
        m_ptr = src.m_ptr;
        //拷贝计数器指针
        m_count = src.m_count;
        //计数器加一
        (*m_count)++;
    }
    //拷贝赋值
    MyShared_ptr& operator=(const MyShared_ptr& src) {
        if (&src == this) return *this;
        //如果自身不为空，先调用析构函数
        if (m_ptr != nullptr) {
            this->~MyShared_ptr();
        }
        m_ptr = src.m_ptr;
        m_count = src.m_count;
        (*m_count)++;
        return *this;
    }
    //以MyUnique_ptr右值 拷贝赋值
    MyShared_ptr& operator=(const MyUnique_ptr<T>&& src) {
        if (m_ptr != nullptr) {
            this->~MyShared_ptr();
        }
        //销毁独占指针，取出裸指针
        m_ptr = src.release();
        m_count = new long(1);
        return *this;
    }
    //析构函数
    ~MyShared_ptr() {
        //计数器减一
        (*m_count)--;
        //如果计数器为0，则释放指针空间和计数器空间
        if (0 == *m_count) {
            delete m_ptr;
            delete m_count;
            m_ptr = nullptr;
            m_count = nullptr;
        }
    }
    //获得内部指针地址
    T* get() {
        return m_ptr;
    }
    //重置指针
    void reset(T* ptr = nullptr) {
        //先调用析构函数
        this->~MyShared_ptr();
        m_ptr = ptr;
        m_count = new long(1);
    }
    //返回当前引用计数
    long use_count() {
        return *m_count;
    }
    //转换函数 bool
    operator bool() const {
        return m_ptr != nullptr;
    }
    //当前共享指针是否唯一
    bool unique() {
        if (1 == *m_count) return true;
        else return false;
    }
    //成员函数交换指针和计数器
    void swap(MyShared_ptr<T>& src) {
        T* temp_ptr;
        long* temp_count;
        temp_ptr = src.m_ptr;
        temp_count = src.m_count;
        src.m_ptr = m_ptr;
        src.m_count = m_count;
        m_ptr = temp_ptr;
        m_count = temp_count;
    }
    //重载*
    T& operator*() const {
        return *m_ptr;
    }
    //重载->
    T* operator->() const {
        return (&**this);
    }
};
//全局函数，有右值引用make_MyShared
template<class T>
MyShared_ptr<T> make_MyShared(MyShared_ptr<T>&& src) {
    return MyShared_ptr<T>(forward<MyShared_ptr<T>>(src));
}
```

- 有计数器设计的版本
```cpp
//计数器设计
template<typename T>
class MyCount {
private:
    //分别存储引用计数和弱指针计数
    long use_count;
    long weak_count;
public:
    //计数器的初始化
    MyCount(long u,long w) : use_count(u), weak_count(w) {}
    //弱指针计数++
    void Increase_weak_count() {
        ++weak_count;
    }
    //弱指针计数--
    void Decrease_weak_count() {
        --weak_count;
    }
    //引用计数++
    void Increase_use_count() {
        ++use_count;
    }
    //引用计数--
    void Decrease_use_count() {
        --use_count;
    }
    //获得引用计数
    long get_use_count() {
        return use_count;
    }
    //释放传入对象的内存
    void DestroyPtr(T* ptr) {
        delete ptr;
    }
    //若没有弱指针，则释放计数器
    void DestroyThis() {
        if(0 == weak_count)
            delete this;
    }
};

template<typename T>
class MyShared_ptr {
private:
    //内部指针
    T* m_ptr;
    //计数器指针
    MyCount<T>* m_count;
public:
    //以裸指针构造共享指针
    explicit MyShared_ptr(T* ptr = nullptr) : m_ptr(ptr) {
        //初始化计数器为 引用1 弱指针0
        m_count = new MyCount<T>(1,0);
    }
    //以独占指针构造共享指针
    MyShared_ptr(MyUnique_ptr<T>&& src) {
        //销毁独占指针并取出裸指针
        m_ptr = src.release();
        //初始化计数器为 引用1 弱指针0
        m_count = new MyCount<T>(1, 0);
    }
    //拷贝构造
    MyShared_ptr(const MyShared_ptr& src) {
        m_ptr = src.m_ptr;
        m_count = src.m_count;
        //引用计数++
        m_count->Increase_use_count();
    }
    //拷贝赋值
    MyShared_ptr& operator=(const MyShared_ptr& src) {
        if (&src == this) return *this;
        if (m_ptr != nullptr) {
            this->~MyShared_ptr();
        }
        m_ptr = src.m_ptr;
        m_count = src.m_count;
        //引用计数++
        m_count->Increase_use_count();
        return *this;
    }
    //拷贝独占指针赋值
    MyShared_ptr& operator=(const MyUnique_ptr<T>&& src) {
        if (m_ptr != nullptr) {
            this->~MyShared_ptr();
        }
        m_ptr = src.release();
        m_count = new MyCount<T>(1, 0);
        return *this;
    }
    //析构函数
    ~MyShared_ptr() {
        //引用计数--
        m_count->Decrease_use_count();
        //如果引用计数为零
        if (0 == m_count->get_use_count()) {
            //释放指针
            m_count->DestroyPtr(m_ptr);
            //销毁计数器（计数器内部判断有无弱指针）
            m_count->DestroyThis();
            m_ptr = nullptr;
            m_count = nullptr;
        }
    }
    //获得内部指针地址
    T* get() {
        return m_ptr;
    }
    //重置共享指针为新指针
    void reset(T* ptr = nullptr) {
        this->~MyShared_ptr();
        m_ptr = ptr;
        m_count = new MyCount<T>(1, 0);
    }
    //返回引用计数
    long use_count() {
        return m_count->get_use_count();
    }
    //转换函数
    operator bool() const {
        return m_ptr != nullptr;
    }
    //是否唯一
    bool unique() {
        if (1 == m_count->get_use_count()) return true;
        else return false;
    }
    //交换指针和计数器
    void swap(MyShared_ptr<T>& src) {
        T* temp_ptr;
        MyCount<T>* temp_count;
        temp_ptr = src.m_ptr;
        temp_count = src.m_count;
        src.m_ptr = m_ptr;
        src.m_count = m_count;
        m_ptr = temp_ptr;
        m_count = temp_count;
    }
    //重载*
    T& operator*() const {
        return *m_ptr;
    }
    //重载->
    T* operator->() const {
        return (&**this);
    }
    //设置弱指针类为友元类
    template<typename T>
    friend class MyWeak_ptr;
};

//全局函数，有右值引用make_MyShared
template<class T>
MyShared_ptr<T> make_MyShared(MyShared_ptr<T>&& src) {
    return MyShared_ptr<T>(forward<MyShared_ptr<T>>(src));
}
```
### weak_ptr的原理及仿写

- 弱指针的引入是为了解决共享指针循环引用导致，假设有A、B两个类，A中有一个指向B对象的共享指针，B中也有一个指向A对象的共享指针。然后现在有一段程序，在这段程序中初始化了两个共享指针，ptr_a和ptr_b分别指向一个堆上的A、B对象，这个时候指针a和指针b的引用计数都是1，然后我们在用这两个指针分别拿去初始化对方的内部共享指针，这样指针a和指针b的计数器就是2了，但我们这段程序结束是，栈上的指针a和b自动析构，让引用计数都减一，此时两个引用计数都还是1，没有到0，所以不会调用delete，但是其实这两个指针已经找不到了，也就是这两个堆上的空间发生了内存泄漏。
- 而弱指针的作用就是，指向一个对象，但是不会引起计数器增加，同时这个弱指针并没有重载右箭头，也就是弱指针无法调用这个对象的东西，也就是这个弱指针只能用来判断这个对象是否还存在，如果想要通过弱指针来调用对象，就必须把弱指针先转化成共享指针。
- 弱指针只能由其他弱指针或者共享指针构造
```cpp
template<class T>
class MyWeak_ptr {
private:
    //内部存放一个指向共享指针的指针
    MyShared_ptr<T>* m_s_ptr = nullptr;
    //存放计数器
    MyCount<T>* m_count = nullptr;
public:
    //默认构造函数，初始化计数器为 引用0 弱指针1
    MyWeak_ptr() {
        m_count = new MyCount<T>(0, 1);
    }
    //拷贝构造 以弱指针
    MyWeak_ptr(const MyWeak_ptr<T>& ptr) {
        //把原来计数器释放
        this->~MyWeak_ptr();
        m_s_ptr = ptr.m_s_ptr;
        m_count = ptr.m_count;
        //弱指针计数++
        m_count->Increase_weak_count();
    }
    //拷贝构造 以共享指针
    MyWeak_ptr(const MyShared_ptr<T>& ptr) {
        this->~MyWeak_ptr();
        //把该共享指针的地址存入m_s_ptr
        m_s_ptr = &ptr;
        m_count = ptr.m_count;
        m_count->Increase_weak_count();
    }
    //析构函数
    ~MyWeak_ptr() {
        //弱指针计数--
        m_count->Decrease_weak_count();
        //释放计数器（内部会自己判断弱指针计数是否为0）
        m_count->DestroyThis();
        m_s_ptr = nullptr;
        m_count = nullptr;
    }
    //拷贝赋值
    MyWeak_ptr& operator=(const MyWeak_ptr<T>& ptr) {
        if (&ptr == this) return *this;
        this->~MyWeak_ptr();
        m_s_ptr = ptr.m_s_ptr;
        m_count = ptr.m_count;
        m_count->Increase_weak_count();
        return *this;
    }
    //拷贝赋值 
    MyWeak_ptr& operator=(MyShared_ptr<T>& ptr) {
        this->~MyWeak_ptr();
        m_s_ptr = &ptr;
        m_count = ptr.m_count;
        m_count->Increase_weak_count();
        return *this;
    }
    //重置
    void reset() {
        this->~MyWeak_ptr();
        m_count = new MyCount<T>(0, 1);
    }
    //交换
    void swap(MyWeak_ptr<T>& ptr) {
        MyShared_ptr<T>* temp_ptr = ptr.m_s_ptr;
        ptr.m_s_ptr = m_s_ptr;
        m_s_ptr = temp_ptr;
        MyCount<T>* temp_count = ptr.m_count;
        ptr.m_count = m_count;
        m_count = temp_count;
    }
    //判断弱指针是否有对象，没有返回true
    bool expired() {
        return 0 == m_count->get_use_count();
    }
    //返回引用计数
    long use_count() {
        return m_count->get_use_count();
    }
    //将弱指针转化为共享指针，若没有对象，则共享指针内部指针为nullptr
    MyShared_ptr<T> lock() {
        if (expired()) return MyShared_ptr<T>();
        else return *m_s_ptr;
    }
};
```

## 6、各种关键字
### const的作用

-  const作用，总结起来就是我希望被const修饰的东西不允许更改，也就是是只读 
-  const修饰变量 
   - const修饰变量的意思就是**不允许修改这个变量**，也就是定义一个常量，这个常量在**程序运行中不允许修改**，所以const修饰变量时，**必须进行初始化**。
   - const修饰变量也分为const修饰**成员变量**和const修饰**全局变量**，虽然他们在程序中都作为常量使用，但是就像刚刚所说的，本质上他们其实都是变量，所以const修饰的**成员变量内存仍然是放在栈上**的，而const**全局变量则放在常量只读区**，这意味着在栈上的const成员变量还是有特殊办法改变的，而全局const变量就没办法改变了，虽然这其实没有意义，只不过是我学习过程中的一个有意思的点而已。
-  const修饰引用 
   - 引用就是对一个对象的别称，当const修饰一个引用时候，和修饰变量有所不同的是，修饰引用时，并不意味着这个对象是常量，只是代表我们**不能通过这个别称修改这个对象**，也就是**这个别称是只读的**。
   - 比较经典的用法就是我们在使用**函数传递参数**时，基本上能选择传递引用就传递引用，因为效率更高嘛，但是如果我不希望在函数中不小心更改了这个参数，加上const限定就保证在函数中不能修改这个参数了，另外一个例子就是，我们定义了一个int a，和一个对它的常量引用b，我们修改b会报错，因为他是常量引用，而我们修改a，则b也会跟着修改，就非常好的说明了，const引用是只读的意思。
   - 此外还有一点就是**常量引用能引用左值和右值**，，但是普通引用也就是左值引用只能引用左值
-  const修饰指针 
   - const修饰指针具体要分成**常量指针和指针常量**，具体就看const的位置，比如定义一个常量指针，const int _ a，const放在最前面，实际上const作用于后面的_a，也就是 _a只读不能更改， _a代表的就是a这个指针指向的内容，也就是这个内容的值是不能更改的，但是a本身是可以改的，也就是指针指向可以更改。
   - 而如果是int* const a 的话，const具体修饰的就是a，意思就是a本身不能更改，也就是a的指向不能更改，但是a指向的内容的值可以更改。
-  const修饰函数 
   - const修饰函数的本质就是**const修饰this指针**，也就是我们不能改动这个对象的成员，当我们对一个函数加上const时，就意味着我们保证在这个函数中不会修改成员。而一个类的const对象，只能调用它的const函数，因为const对象不允许修改成员，只有const函数保证不修改成员，而一个类的普通对象是可以调用const与非const函数的。
   - 此外一个函数**加不加const他们的函数签名**是不同的，也就是**能形成重载**的，而如果两个函数同时存在，则const对象调用const版本，而非const对象调用非const版本
### static的作用

- static可以对变量和函数进行修饰**分为三种情况** 
   - 在用于文件域中时，也就是我们在定义全局变量时，加上static意味着这个变量限定在了这个文件中使用，其他文件调用不到。
   - 在用于函数域中时，比如我们在函数体中定义并初始化了一个静态变量，同时对这个变量进行其他操作。这个静态变量是全局的，存放在静态区中，但是**只在这个函数中可见**，最重要的是这个变量只进行一次初始化，也就是说当我们重复调用这个函数的时候，初始化的那一句只有在最开始的时候执行一次分配内存并初始化，此后的函数不在调用，只会执行其他语句。
   - 在用于类中时，即静态成员变量和静态成员函数 
      - **静态成员变量**意味着这个变量不在属于类对象而**属于整个类**，也就是这个变量会存放在静态全局区，而不是每个类对象的空间上，而所有类调用的都是**同一份这个静态变量**，也就不允许在类中初始化，**必须在类外初始化**，避免每次创建类对象的时候重复定义这个静态变量。
      - 而**当static修饰类中的成员函数**时，就代表着这个函数**只能操作同样是static的成员变量**，而不能操作其他变量。具体解释的话就是静态成员属于整个类而不属于单独某个类对象，除了静态变量以外，其他变量都是属于各自类对象的，函数无法使用具体对象的成员，**只能使用属于整个类的成员**，底层的实现手段就是**函数调用时不传入this指针**，在调用普通类成员函数时，会传入this指针告诉函数是哪个对象在使用这个函数，操作成员变量时操作的是谁的成员变量，static函数就不传入this指针，也就是它不知道是谁在调用它，因此它就没办法使用具体谁的成员对象
### extern的作用

- extern修饰变量名或者函数名声明意思为**外部链接**，即去其他文件中寻找该变量或者函数的定义，当然如果定义变量前面加了**static**就会失败，因为static全局变量以为着只允许它那个文件中使用
- extern"C" 意思**是按照C语言的编译器风格翻译**，在函数定义中，C在**函数名上会加上参数**，因为C要是实现重载，而C不用，所以当C++**要去调用一个C风格的函数时就会报错**，就要用到extern"C"。
### explicit的作用

- 当类的**构造函数只有一个参数**的时候，编译器会把单独这个类型的数**隐式转换**成一个类对象，比如我们的**隐式构造**实际上就是运用了这个隐式转换
- 而当我们在这个构造函数前面加上**explicit**时，就限定这个构造函数**必须显示构造**，避免了某些隐式转换带来的问题。
### constexpr的作用

- 就是告诉编译器后面这个是一个**常量表达式**，要求它要**在编译期就计算出来**，而不是在运行的时候，放在函数前面就代表着这个函数本质上就是一个常量表达式，**通常是只能有一个return语句**，但是这个好像在最新的C++没有那么严格我没有具体去了解。
- 通过这个关键字可以在编译期让编译器去计算很多东西，就可以节省程序运行时候的性能，没记错的话我在stl的源码中看到过挺多这个关键字，只不过我个人使用的比较少，唯一用过的可能还是在**leetcode上耍的小聪明**，有一些题目在运行的时候计算要花费很多时间，但是如果有的函数或者表达式加上constexpr，就**能让编译器在编译阶段计算**，就能让最后的运行时间节省很多。
### volatile的作用？

- 跟编译器优化有关，告诉编译器每次操作该变量时一定要**从内存中真正取出**，而不是使用已经存在寄存器中的备份。
- 比如我们**定义了一个cosnt常量**，C++会进行优化把**这个常量放进符号表中**，当用到这个常量时**从符号表中查询**，而没有直接去这个常量的地址中查询，所以当我们用一些特殊手段修改了这个常量时，我们会发现，使用中它还是原来的值没有修改，而如果我们使用volatile修饰这个变量，编译器就会从内存中真正取出这个常量，就可以看到它改变了，
### mutable的作用？

- 保持常量对象中大部分数据成员仍然是“只读”的情况下，实现对个别数据成员的修改；
- 使类的**const函数可以修改对象的mutable数据成员**。
### auto和deltype的作用和区别？

- auto用于实现**类型的自动推导**，让编译器来操心变量的类型，但本质上auto并非一种类型的声明，而是一个类型声明时的占位符，编译器**会在编译时期将auto替换成实际类型。**
- 但是auto也有一些限制比如**不能推导数组**，**不能用于函数传参**，但是deltype可以解决这些问题。
## 7、左值右值，构造函数
### 什么是左值和右值？

-  简单的从名字出发来讲的话，左值，是**可以**出现在等号左边的，当然他也可以出现在右边，右值，是**只能**出现在等号右边的，是只能。 
-  从特点上来说的话，左值是**可以取地址**的，**有名字的**，用户**能够改变值**的量，比如说常见的变量或者类对象，左值**具有持久的状态**，在离开作用域的时候才销毁。 
-  而右值是**不能取地址**，**没有名字**的量，或者**即将销毁的量**，在C++11后具体定义了两种右值，第一种**纯右值**，**运算表达式**产生的临时变量、不和对象关联的**原始字面量**、**非引用返回的临时变量**都是纯右值；第二种是**将亡值**，也就是**即将要销毁的值**，比如**临时对象**，或者就是**我们自己认为的以后不会再用到的值**，但是这种值需要通过**move**手动转为右值。这两种都属于右值。 
-  但是在学习过程中也有遇到有意思的东西，比如标准库中的**string就不符合右值的定义**，比如前面说的右值是只能放在等号右边的，但是如果三个字符串s1，s2和s3，我却可以这样写**s1+s2=s3**，s1+s2是一个运算表达式，**返回是值传递**，应该是一个临时对象右值却能放在等号左边，我一开始以为编译器只能检查自带的类型是否为右值，但是对s1+s2取地址的时候，编译器却能提醒我这是一个右值，所以原因在其他，当然最后发现其实很简单，看string的源码就知道，甚至可以说不用看也能想到，string的**等于号肯定是重载过**的了，当我们写出s1+s2 = s3的时候，实际上应该翻译成(s1+s2).operator=(s3)，也就是**这个临时对象调用operator=这个函数**，参数是s3，临时对象调用函数当然没有左值右值的限制，所以编译成功，当然这只是我学习过程中遇到的一个小问题， 
### 什么是右值引用，为什么要引入右值引用？

- 在C++11以前是没有右值引用这个概念的，以前的引用就是左值引用，也就是引用只能引用一个左值，当你想给一个常量取别名的时候会报错，要想引用右值只能使用常量引用。
- 而在C++11引入了右值引用，专门引用右值，用法是用两个取地址符号来获得右值的引用，右值引用**是为了支持移动操作**而引出的一个概念，移动操作主要的目的是**避免了不必要的拷贝，提高性能**。通常如果类成员包含指针或者堆空间的申请时，我们会设计一个深拷贝，在拷贝的时候申请新的空间，防止两个指针指向同一个地方，重复delete，然而如果我们拷贝的对象本身就是一个右值，或者说是一个将亡值，一个临时对象，他自己的那个空间他以后也用不到了，所以直接让我们的指针指向它的那块内存，让我们接管那块内存，就**节省了一个开辟空间拷贝的过程**。其实说到底，移动构造**本质就是一个浅拷贝**，不过要注意的是，接管右值的内存是要记得把它的指针置空，不然就会出现浅拷贝的问题，重复delete。
- 所以右值引用的引入就是**为了解决冗余拷贝的问题**。
## 8、内联函数与宏
### 内联函数有什么作用？存不存在什么缺点？

- 首先在程序运行过程中调用一个函数时，需要**先将当前状态保存**，将**参数入栈**，函数**结束后出栈**，**恢复状态等**等开销，而内联函数的作用就是**节省这些开销。**
- 内联函数就是让编译器在函数调用的地方，**直接将函数的代码展开**，就地计算，而**不是开辟栈空间调用函数**，这样就可以避免函数调用的开销，但是内联函数会在每一个函数调用的地方都展开，这样也就**会有很多的代码冗余**，也就是会造成**代码膨胀**，尤其是递归的函数，会有大量内存的开销，本质上就是一个**用空间换时间**的手段。
- 但是实际上我们在函数前的inline声明也**只是对编译器的建议**，编译器会**自己判断函数**是否复杂是否应该内联，甚至其实**类中的成员函数都是默认内联**的，只有在类外定义才需要我们加上inline，借侯捷老师的话就是，直接**在每个函数前面都加上inline**，把是否内联的问题交给编译器来解决。
### 内联函数和宏有什么区别，有了宏为什么还需要内联函数?

- 宏是在**预处理阶段对命令进行替换**，而内联函数是在**编译阶段在调用点展开**
- 也就是说**宏命令**是**不会进行类型检查**的，可能会出现类型安全问题，而**内联函数会进行类型检查。**
