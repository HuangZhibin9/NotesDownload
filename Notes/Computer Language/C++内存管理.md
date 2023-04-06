# C++内存管理

## primitives 基础工具

### 2 内存分配的每一层面

![image-20230401135745985](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230401135745985.png)

![image-20230401135809673](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230401135809673.png)

### 3 四个层面的基本用法

```c++
void* p1 = malloc(512);
free(p1);

 int* p2 = new int;
delete p2;

void* p3 = ::operator new(512);
::operator delete(p1);

//3种allocator调用方式
//VC
int* p4 = allocator<int>().allocate(3,(int*)0);
allocator<int>().deallocate(p4,3);

//borlandc
int* p4 = allocator<int>().allocate(3);
allocator<int>().deallocate(p4,3);

//gnuc
void* p4 = alloc::allocate(512);
alloc::deallocate(p4,512);
//4.9改成了_pool_alloc
```

### 4 基本构件之一newdelete expression

- new 和 delete是表达式
- new后面接类名，同时也能进行初始化，这也意味着实际上new可以分为三步
  - 1、调用operator new函数（注意这个函数是可以重载的，它的底层就是malloc函数）
    - `void* p1 = operator new(sizeof(...))`
    - operator new 的底层是一个malloc循环，如果内存申请成功，就返回内存地址的void指针，申请失败就调用你个我们自己设置的内存释放函数然后再循环看能不能申请成功
  - 2、把void*进行转型
  - 3、调用构造函数`p1->Class1::Class1(1,2)`,但是实际上我们不可以这样直接调用构造函数，只有编译器可以这样，但是在VC6.0编译通过了，而GCC就会报错说不能直接调用构造函数
- delete 后面放一个要释放的指针
  - 1、先调用析构函数`p1->~Class()`（注意这个我们是可以调用的，而new里那样的构造函数就不行）
  - 2、调用operator delete(p1)

### 7 Array new

- 在malloc获得内存是，内存实际上会有一个头部，cookie，上面记录了你malloc了多少内存，而如果是array new，还会记录数组的大小
- 所以在delete的时候使用`delete []`，就会根据cookie里记录的大小，释放对应的内存，然后还会根据数组的大小，调用对应次数的析构函数，虽然delete 不加[]也能释放掉内存，但是如果数组内容里的对象存在指针，就有可能发生内存泄漏
- 如果对象没有non-trivial dtor，就不会有记录要几次析构函数

### 8 placement new

- placement new允许我们将对象构建于已经分配的内存中
- 没有placement delete，因为placement new 并没有分配内存

```c++
#innclude <new>
char* buf = new char[sizeof(Complex)*3];
Complex* pc = new(buf)Complex(1,2);
...
delete buf;
```

- placement new 分解为三步
  - 第一步`void* men = operator new(sizeof(),buf)`
    - 这句的new源码是`void* operator new(size_t,void* loc) { return loc;}`,相当于什么都没做，返回loc地址
  - 第二步，转型
  - 第三步调用构造函数

### 9 重载

- new和delete是表达式，不能改变和重载
- 但是operator new 和 operator delete 可以重载，可以重载全局，也可以重载成员函数，但是一般不会重载全局，因为影响太大

### 10 重载示例

- 我们可以重载不同类型的operator new 作为placement new的调用函数
- ![image-20230401171626102](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230401171626102.png)![image-20230401171819323](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230401171819323.png)

- 我们也可以重载对应的delete，但是只有在对应的operator new时构造函数出错才会调用
- back_string使用new(extra) 扩充申请量，string创建字符串的时候会多申请一个extra的内存，用来存放一些东西，

### 12 Per class allocator

- 虽然其实malloc很快，但是减少malloc的次数总是好的
- 降低cookie的用量
- 内存管理两个目的，减少浪费，提高速度
- 通过重载operator new 和 delete来把申请内存掌控在自己手里

### 14 Static allocator

```c++
#include <iostream>
#include <string>
#include <vector>

using namespace std;

class myallocator
{
private:
	//寻找下一块内存的指针
	struct obj
	{
		obj* next;
	};
public:
	//声明两个函数
	void* allocate(size_t size);
	void deallocate(void* ptr);
private:
	//指向下一个可使用的自由内存的指针
	//初始为空
	obj* freestore = nullptr;
	//一次分配5个内存
	const int CHUNK = 5;
};
void* myallocator::allocate(size_t size) {
	//存放返回地址的变量
	obj* p;
	//如果没有下一块自由内存，就请求分配
	if (!freestore) {
		//需要的内存大小
		size_t chunk = CHUNK * size;
		//用p存放得到的内存地址，将其转型为obj*，则可以使用next指针
		p = (obj*)malloc(chunk);
		//将其设置为自由内存
		freestore = p;

		//把得到的大块内存切割，并将它们的next连接
		for (int i = 0; i < (CHUNK - 1); ++i) {
			p->next = (obj*)((char*)p + size);
			p = p->next;
		}
		//最后一个的next指向null
		p->next = nullptr;
	}
	//返回得到的内存地址p，并将自由内存设置到下一个
	p = freestore;
	freestore = p->next;
	return p;
}
void myallocator::deallocate(void* ptr) {
	//把需要释放的内存插入到当前自由区的前面
	((obj*)ptr)->next = freestore;
	freestore = (obj*)ptr;
}
class Foo
{
public:
	long L;
	//string str;
	static myallocator myalloc;
	Foo(long l) : L(l) {}
	static void* operator new(size_t size) {
		return myalloc.allocate(size);
	}
	static void operator delete(void* ptr) {
		return myalloc.deallocate(ptr);
	}
};
//类外声明静态成员变量
myallocator Foo::myalloc;


int main() {
	Foo* p[100];
	//一个Foo的大小
	cout << "sizeof(Foo)" << sizeof(Foo) << endl;

	//new 23个Foo对象
	for (int i = 0; i < 23; ++i) {
		p[i] = new Foo(i);
		//查看地址
		cout << p[i] << ' ' << p[i]->L << endl;
	}
	//delete 对象
	for (int i = 0; i < 23; ++i) {
		delete p[i];
	}

    system("pause");
}
```



### 15 Macro for static allocator

### 16 New Handler

- 前面讲到了`operator new`会循环调用malloc，如果malloc失败，就会调用一个new handler，由你自己设定的，然后再malloc看能不能成功
- 设计良好的new handler ：
  - 让更多的memory可用
  - 调用abort()或exit()
- =default, -delete

## std::alocator

### 18 VC6标准分配器之实现

- VC6的allcator没有特殊的设计，就是用operator new 和delete实现

- 是以元素为单位，比如是int就分配512个int空间，double就512个double空间，这样的话不同类型分配的空间大小就不一样

- 为了去除cookie就要保证分配的空间大小一样，这样我们就不需要cookie来记录申请了多少空间

- alloc的单位是字节,alloc里面有静态成员函数allocate和deallocate，

  - ```c++
    void* p = alloc::allocate(512);
    //或者
    alloc().allocate(512);
    alloc::deallocate(p,512);
    
    ```

- 当我们使用的时标准的没有其他机制的allocator，为3个double分配内存，在把它们的地址打印出来就可以看到两个之间差16个字节，说明除了double8个字节之外，还有头尾两个4字节的cookie总共8个字节，就意味着如果我们有一百万个元素，就会有八百万字节浪费在cookie上，

- 而如果使用的是alloc，为3个double分配内存，在把它们的地址打印出来，就可以看到两个之间只差差8个字节，说明cookie那段被节省下来了

### 23 G2 9std alloc

- 里面维护一条链表，里面有16个指针
- 一次申请20*2，但是只使用前20，后面做储备
- 嵌入式指针，借用前4个字节作为指针
- 自己使用allocator归还的时候要写归还多少，但是由于allocator不是给我们使用的，而是给容器使用的，容器里面的元素大小用的时候就已经记录了，所以对容器来说每次归还都写换多少不是难事 



- 运行过程：
  - 申请需要32字节，查找alloc中list#3下没有空间，pool里也没有内存，所以就向pool注入`32*20*2 + RoundUp(0>>4)`的内存,其中1块返回给客户，19块划给list#3，剩下20块留作备用池
    - 累计申请量：1280
    - pool大小：640
  - 申请64字节，由于pool有余量，所以取pool划分`640/64 = 10`个区块（从pool上切下来的分块数量必须小于20，超过也只分20出来），第一块给客户，剩下九块给list#7
    - 累计申请量：1280
    - pool大小：0
  - 申请96字节，由于list#11里没有内存，同时pool也没有，所以又申请`96*20*2 + RoundUp(1280>>4) = 5200 `,其中1个还给客户，19块给#11，余2000备用，注意追加量的设计
    - 累计申请量：5200
    - pool大小：2000
  - 当pool里的内存连一个也不够，就会变成碎片，这个碎片的处理是把它挂到对应链表下，比如还剩80字节，就把这八十字节当作一块挂到#9上，然后继续申请下一块内存，要注意追加量
  - 当剩下的内存不足以支撑20*2块内存空间，malloc失败，会把当前pool当碎片先处理，然后朝右边一个链表借一个内存块来使用

- 最大的缺陷，没办法delete，申请之后就再也不会还给操作系统了，在前面讲的当pool里没有空间时，我们会malloc一段大内存，在这个内存上才有cookie记录了申请了多少，delete的时候需要一个指向这块内存的指针，但是在使用的时候，我们已经丢失这个指针了。