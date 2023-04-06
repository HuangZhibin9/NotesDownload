# C++标准库-体系结构与内核分析

### 1 认识headers、版本、重要资源

- 标准库与header files形式呈现，不带副档名（.h）
- 新式headers内的足交封装于namespace “std”
- 旧式headers内的组件不封装于std

### 2 STL体系结构基础介绍

- 六大部件
  - **容器**
  - 分配器
  - **算法**
  - 迭代器
  - 适配器
  - 仿函数

![image-20230329094351500](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230329094351500.png)

```c++
#include <vector>
#include <algorithm>
#include <functional>
#include <iostream>

using namespace std;

int main() {
    int ia[6] = {27,210,12,47,109,83};
    vector<int,allocator<int>> vi(ia,ia+6);
    
    cout << count_if(vi.begin(),vi.end(),
                    not1(bind2nd(less<int>(),40)));
    return 0;
}
```

- ”前闭后开“区间——begin指向第一个，但是end指向最后一个的下一个

![image-20230329095418086](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230329095418086.png)

- c++ 11 range-based for statement 新语法

```c++
for ( decl : coll ) {
	statement
}

for (int i : {2, 3, 4, 5, 6}) {
    cout << i << endl;
}

vector<int> vec;
...
for (auto elem : vec) {
    cout << elem << endl;
}

for (auto& elem : vec) {
    elem *= 3;
}
```

- auto自动推断类型

### 3 容器之分类与各种测试

- vector
  - 两倍或1.5倍扩展
- list
  - 标准库有一个sort，list自身有一个sort

- deque
  - 双向开口，分段连续，每次扩充一个buffer
  - stack和queue都是只开放一部分功能的deque，且不提供iterator
- multiset

### 7 分配器之测试

- 不同编译器的分配器可能不同，
- allocate是一个模板类，可以直接使用，但是没有意义，里面只有两个函数，allocate，deallocate本质也是malloc与free
  - deallocate不仅需要填指针还要写归还多少，而delete与free就不需要

### 8 源代码之分布 VC, GCC

- D:\VS\下载缓存\VC\Tools\MSVC\14.29.30133\include

### 9 OOP 面向对象编程 vs GP 泛型编程）

- OOP 数据和操作放在一起；GP将datas和methods分开来
- GP里容器和算法团队可以各自造车，然后通过迭代器沟通
- 所有算法最后设计元素的操作就是比大小，就要去看是否重载符号

### 10 技术基础：操作符重载and模板泛化, 全特化, 偏特化

- 四个符号不能重载 `::`、`.`、`.*`、`?:`
- 迭代器是一种泛化的指针，与指针相关的操作符需要重载

- 类模板、函数模板、成员模板

### 11 分配器

- 最终大家都是到malloc和free
- operator new（）会调用malloc

![image-20230330094038673](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230330094038673.png)

- 默认使用`allocator`在头文件<xmemeory>里面，里面有个`_Allocator()`函数，里面实际上调用的是`operator new()`函数
- `deallocate`调用`operator delete()`，但是额外有要求，不仅要求归还的指针，还要写归还多少内存
- 就是说默认`allocator`并没有多的设定，会带来额外的开销，就是如果你需要的内存块小，overhead的占比就大，cookie前后都有八个字节。
- 所以实际上你可以直接调用这个`allocator`的函数

![image-20230330094533599](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230330094533599.png)

- 但是存在另一个更好的分配器`alloc`,G2.9版，但是在4.9又恢复到原来的没有特殊机制的`allocator`,但是`alloc`也还在，但是不在std的命名空间

![image-20230330095657280](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230330095657280.png)

### 12 容器之间的实现关系与分类

![image-20230330100244121](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230330100244121.png)

- 早期的标准库比较少用继承，大多是复合，而容器之间也存在关系比如`set`里面有一个（has-a）`rb-tree`
- ![image-20230330100839623](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230330100839623.png)

### 13 深度探索list

- 在2.9版中，list是一个类，里面有一个指针，指向另一个类，节点类，里面有两个指针（前后），和一个数据，所以list的size只有4字节，但是4.9版很多复杂的类，甚至有一个继承了`alloc`，大小是8字节
- 所有容器都是前闭后开，最后一个元素的下一个是end()，虽然是环状但是最后一个是end
- 在2.9上分配器使用`alloc`
- 迭代器，一大堆typedef，操作符重载，在2.9的时候前后指针都是void*，还要转型，在后面的版本优化了

### 15 迭代器的设计原则和Iterator Traits的作用与设计

- `iterator`需要遵循的原则，算法会需要迭代器提供的信息
  - 迭代器的分类
  - 迭代器之间的距离
  - 迭代器里值的类型
  - point（源码未使用过）
  - reference（源码未使用过） 
- 算法会需要这些东西，迭代器必须定义这些东西，但是这样看就不需要traits机器（提取这五个这点）?

![image-20230330135420774](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230330135420774.png)![image-20230330135427742](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230330135427742.png)

- 但是因为使用算法的时候，有可能传递进去的是一个指针（指针是退化的迭代器），就用萃取机，提取它的五个定义，所以算法不能直接问迭代器，需要借助traits来问，traits设计的时候使用了偏特化，传进来是迭代器，就正常提取五个定义，如果是指针或者常量指针，就用另外的版本

### 16 vector深度探索

- 实际上是一个能自动扩容的数组
- 2.9中分配器也是alloc，vector本身只是一个类，大小是三个指针，12字节，2.9版本；4.9也变成很多类，而且里面有个类还是public继承了allocator，按理来说这样的意思是vector isa allocator，但是实际上它继承只是为了用到allocator里面的两个函数

![image-20230330140621835](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230330140621835.png)

- 每次扩容就需要重新拷贝，就需要调用大量的构造函数
- 迭代器，与list相比就是用了一个指针来作为迭代器，走的是traits里指针偏特化的代码

### 17 array、forward list深度探索

- `array<int,10> myArray`，要传入大小，如果为0，源码中自动将其改为1，没有写构造函数和析构函数
- 迭代器直接是指针，因为内存空间是连续的
- 单向链表与list差不多，少了一个指针，并且没有重载`--`

### 18 deque、queue和 stack深度探索

- deque，双端数组，是分段存储，对外号称连续，这个靠迭代器实现，实际上是一个个缓冲区，然后有一个vector组件，存放每个缓冲区的指针，向后扩充时，就是申请一段新的缓冲区，在把这个缓冲区的指针放到vector的后面
- deque类里面有四个元素，两个迭代器start、finish、map指针、map_size，大小16*2+4+4 = 40字节，模板里面可以填写三个模板参数，deque存放什么类型的数据，选择什么分配器，一个buffer的大小，这个是旧版可以自己设置的，新版不允许，默认是512除以一个元素的大小，代表一个buffer能放几个元素，map指针指向一个控制中心，控制中心记录各个buffer的位置，是vector容器，数据从中间开始记录，扩容时再把数据存储再中段
- ![image-20230330144620796](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230330144620796.png)
- deque的迭代器是一个类，里面有四个元素node指向map，表示这个迭代器是哪一个buffer，last指向这个buffer的最后一个，first第一个，cur指向这个buffer里的其中一个元素，标志这个迭代器所指向的元素

![image-20230330143851141](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230330143851141.png)

- 比如deque的insert()函数，在某个迭代器的位置插入一个元素。离头比较近，往前推，里尾比较近，往后推，先判断是否是头尾，是头交给`push_front`，是尾交给`push_back`，否则在头尾先新生成要插入的元素，在将元素搬运到指定位置
- 如何模拟连续空间，全是迭代器的功劳，首先要重载`-`模拟连续空间指针相减，两个迭代器相减重载为，计算两个迭代器中间有几个buffer乘以每个buffer多大，在计算起始位置和结束位置的差值，然后是`++`和``--`就要去判断是否在这个buffer到达边界，然后因为是随机访问迭代器，还要重载`+  -`，来考虑到加完减完是否要跨越缓冲区，算出要跨越几个缓冲区，退回到map里，调整node指向哪个buffer，选择最后落到的缓冲区，在计算curren应指向哪个元素。最后还要重载`[]`,应为连续空间的数组应该是支持下标查找





- queue、stack不允许遍历，没有迭代器，看他们的源码，就可以知道模板参数其实有两个，第一个是存储的元素类型，第二个就有意思的，是一个底层结构类，默认是deque。
- 这意思是他们都是内含一个deque，然后封住某些功能 ，利用里面的deque转发使用它的功能（adapter 适配器 ），其实只要能满足queue和stack源代码里功能的要求，就能作为它们的底层结构，除了deque之外，其实list也能完成，也就是说我们第二个模板参数可以填list，也能创建出一个queue或者stack对象，但是一定是deque比较快
- 另外stack也能选择vector作为底层结构，但是queue因为队列需要push队头元素，而vector没有一个push_front函数，所以不行，但是我们知道编译器对于模板类是没有一个检查过程的，所以编译是能通过的，不过在使用queue.pop功能是就会报错。另外其实利用两个stack，也能做成一个queue

### 20 RB tree 深度探索

- 红黑树是一个高度平衡二叉搜索树，有利于查找，rb_tree提供遍历，且是排序好的状态
- 不应该修改rb-tree的元素值，会破坏红黑树严谨的排列结构，但是实际上编程层面上并没有阻止我们修改，只是因为map中的虽然key不更改，但是它的data是可以改掉的
- rb-tree提供两种插入，第一种unique的，如果重复就不会插入，第二种是equal，重复的话插入到相邻的位置
- 红黑树有五个模板参数 key的类型，value的类型，如何从value中取出key，比较方法，分配器

- Handle&Body 

### 21 set、multiset深度探索

- set能遍历，也有iterator，但是我们不能改变元素值，因为对set来说Key就是value
- set插入，调用insert_unique，multiset调用insert_equal
- 三个模板参数，key、比较方法、alloc，里面有一个红黑树，其中的两个模板参数已经确定，value的类型就是key的类型，取得key的方法在gnu c 中是自带的identity函数，在VC中是成员函数，之所以无法更改值，在源码中可以看到它的迭代器是一个const迭代器，无法修改值，
- set的所有操作都是转调用红黑树的操作，所以set也是一个container adapter，与stack queue一样

### 22 map、multimap深度探索

- set能遍历，也有iterator，但是我们不能改变key，但是可以改变data
- map有四个模板参数，key的类型，data的类型，比较方法，alloc，注意第二个是data的类型，在map中会自动写为

​		pair<const key,data>作为红黑树的第二个模板参数，取key的方法就是select1st()，也是gnu c独有的，VC中也是一个类的成员仿函数

- 它的迭代器就是红黑树的迭代器，但是上面的key已经是const无法改变
- map独特的`[]`重载，返回map里key为【】值的data值，如果没有，则会创建一个key为【】值的pair存入map，同时返回data值，所以可以利用这个东西进行insert

![image-20230330213243588](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230330213243588.png)

### 23 hashtable深度探索

- gnu c默认hashtable的大小是53，当元素超过53，扩容至53的倍数的附近的质数，这是2.9版
- hashtable有六个模板参数，value的类型、key的类型，HashFcn(元素如何转换至一个编号)、ExtraKey（怎么从value里提取key），EqualKey（key如何比大小），alloc
- 类中有三个函数对象，3个字节，还有一个vector<node*>对象，buckets篮子，里面存的就是具体的元素，vector包含三个指针12字节，还有一个size_type，4个字节，总共19，被调整至20个字节

- 迭代器由两个指针组成，一个指向具体元素，一个指向hashtable本身，这样就能在访问某个key值的拉链时 ++越界，能返回hashtable访问下一个bucket 

### 25 hash set、hash multiset, hash map、hash multimap概念

### 26 unordered容器概念

- c++11后hash_全部换成unordered
- 篮子一定大于元素个数

### 27 算法的形式

- 算法是一个函数模板 ，算法看不见容器，对其一无所知，他需要的一切信息必须从迭代器中获得，也就是说算法要询问迭代器一些问题，迭代器要能够回答，通过这些信息选择一个最好的方法。

### 28 迭代器的分类（category）

- 随机访问（array、vector连续存储，deque假装自己是连续的，所以它也支持随机）
- 双向访问（list、set、map）
- 单向访问
- stack、queue没有迭代器，unordered需要看每个bucket里的是单向还是双向链表

### 29 迭代器分类（category）对算法的影响

- 比如distance算法求两个迭代器之间的距离，会先通过traits去询问迭代器是什么类型，如果是随机访问，说明它肯定重载了`-`，所以直接返回两个迭代器相减，而如果不是随机访问迭代器，则需要通过一个一个移动来数两个之间的距离；又比如advance函数前进，也需要知道迭代器是什么种类，来选择是一个一个前进还是直接跳过去
- 总之算法对迭代器要求知道五个信息或者说特质，这个是借助traits萃取机来得到

### 30 算法源代码剖析（11个例子）

- accumulate累计，两个版本，第一个版本就是累加，三个参数，头，尾，初值，第二个版本参数，头，尾 ，初值，二元计算方法，就是按你给的方法来累计算
- `for_each(it first,it last, fun f)`,对每个元素，做一个fun f操作
  - c++11新东西 `for (decl : coll ) { todo }`

### 31 仿函数和函数对象

- 是我们最容易写出来的有可能融入标准库的东西
- 算术类
- 逻辑运算类
- 相对关系类

- 两个父类，unary_function一元，binary_function二元，自己写的仿函数没有继承这两个父类之一，就没有融入STL

### 32 存在多种Adapter

- 根据要改造的东西分为容器适配器、仿函数适配器、迭代器适配器
- 一般都会借助一个辅助函数来实现，因为如果是类的话，一个模板类的模板参数是要显示写出来的，而如果是一个模板函数的话他就可以通过推断得到你写入的两个参数的类型，辅助函数就是一个模板参数来辅助得到类型，在进去模板类里面调用具体的实现
- 所有适配器都是使用复合而不是继承
- stack和queue本质上就是一个容器适配器，改造deque，减少了接口，改变了接口名字等等
- 第二个是函数适配器，`binder2nd`绑定第二参数 

- not1取否定

- 迭代器适配器
  - 比如`copy(v1.begin(),v1.end(),inserter(v2,it))`，把v2的迭代器改造成插入迭代器，通过重载`=`从而把copy函数里的复制变成插入



### c++11 新容器 Tuple

- 存储任意数量的容器



- 

- - 