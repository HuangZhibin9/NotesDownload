# C#基础入门


## 变量，操作符和表达式

### 理解语句

### 使用标识符

### 使用变量

### 使用基元数据类型

### 使用算术操作符

### 变量递增和递减

---

- **基础语法与C相识此处不做展开**

### 声明隐式类型的局部变量

- C#编译器可以迅速根据表达式判断变量类型，此处可使用**_var_**关键字

```c
var myVariable = 99;
var myOtherVariable = “Hello”;
```

- 在此例中**_myVariable_**和**_myOtherVariable_**分别被定义为int与string类型。

## 方法与作用域

### 创建方法

#### 方法的声明与调用

```c
returnType methodName (parameterList)
{
    //ToDo
}
```

- **_returnType_**(返回类型)是类型名称，方法返回的数据类型，不返回则使用**_void_**;
- **_methodName_**(方法名)是方法的名称。
- **_parameterList_**(参数列表)是传入的参数。此处为可选的，也可以放空白为不传入参数。

例：

```c
private void Test()
{
    Debug.Log(“示例”);
}

private void Start()
{
    Test();
}
```

#### 返回多个值

```c
(int,int) Test()
{
    int a = 1;
    int b = 2;
    return (a,b);
}

int c,d;
(c,d) = Test();
```

### 使用作用域

#### 定义局部作用域

- “{}”界定了方法的作用域
- 此处的num在该方法中存在，方法结束后消失。

```c
public void Test()
{
    int num;
}
```

#### 定义类作用域

- “{}”界定了类的作用域
- 类中定义的变量成为**字段**。字段在该类中的不同方法都可以共享

```c
public class TestClass()
{
    int num;
    
    public void test1()
    {
        num = 1;
    }
    
    public void test2()
    {
        num = 2;
    }
}
```

### 重载方法

- 同名方法在同作用域中的不同版本，依靠传入参数的不同辨别

```c
static void Main()
{
    Console.WriteLine(“Test”);
    Console.WriteLine(41):
}
```

```c
public void test(int a)
{
    //ToDo
}
public void test(int a,int b)
{
    //ToDo
}

public void main()
{
    int a = 1;
    int b = 2;
    test(a);
    test(a,b);
}
```

- 此处test方法会根据传入参数的不同重载使用不同的方法

### 使用可选参数和具名参数

#### 定义可选参数

```c
void testMethod(int a,double b = 0.0,string c = “Hello”)
{
    //ToDo
}
```

- 此处a为必需参数，b与c为可选参数，若没有传入b与c，则按照默认值使用
- 可选参数只能放在必需参数之后

```c
void main()
{
    testMethod(10,123.45,”World”);
    testMethod(10,123.45);
    testMethod(10);
}
```

#### 传递具名参数

- C#在方法调用中可以不按照顺序传入参数

```c
//任意顺序
testMethod(c:”World”,a:10,b:123.45);

//省略可选参数
testMethod(b:123.45,a:10);

//兼用顺序与名称指定参数
testMethod(10,c:”World”);
```

#### 消除可选参数和具名参数的歧义

- 当使用可选参数和方法重载时将可能遇到歧义

```c
//四个参数的方法
void testMethod(int a,double b = 1.23,char c = ‘c’,string d = “Hello”)
{
    //ToDO
}
//重载方法三个参数的版本
void testMethod(int a,double b = 1.23,char c = ‘c’)
{
    //ToDo
}

/*当只传入三个参数时，可能会是重载版本，也可能是省略第四个参数的版本，
但是编译器会认为三个参数的版本最符合，所以编译不会出错*/
testMethod(10,12.3,’B’);

//但是当只传入两个参数时，系统无法辨别那个版本更符合，所以编译会出错。
testMethod(10,12.3);
```

## 使用判断语句

### 声明布尔变量

```c
bool YesOrNo;
YesOrNo = true;
Console.WriteLine(YesOrNo);
```

### 使用布尔操作符
| 操作符 | 含义 |
| --- | --- |
| == | 等于 |
| ！= | 不等于 |
| < | 小于 |
| <= | 小于等于 |
| > | 大于 |
| >= | 大于等于 |
|  |  |
| ！ | 取反/否 |
| && | AND |
|  |  |


### 使用if语句做出判断

```c
if(bool)
{
    //ToDo
}
else if(bool)
{
    //ToDo
}
else if(bool)
{
    //ToDo
}
……
else 
{
    //ToDo
}
```

- 这里当**_if_**可以嵌套使用

### 使用switch语句

```c
switch (A)
{
    case a:
        {//ToDo}
        break;
    case b:
        {//ToDo}
        break;
    …
    default :
        {//ToDo}
        break;
    
}
```

- **_switch_**语句的控制表达式只能是某个整型（**_int,char,long_**等）或者**_string_**。其他任何类型（如：**_float_**和**_double_**）只能用**_if_**语句。
- **_case_**标签必须是常量表达式，如果希望在运行时计算标签的值，只能用**_if_**语句。
- **_case_**标签必须唯一，不能用两个相同的标签
- 可以连续写多个**_case_**标签，指定在多种情况下都运行相同的语句。

```c
//在这里当A等于a,b,c时都执行同一语句
switch (A)
{
    case a:
    case b:
    case c:
        {//ToDo}
        break;
}
```

## 使用复合赋值和循环语句

### 复合赋值操作符
| *= | /= |
| --- | --- |
| %= | += |
| -= |  |


### 编写while语句

```c
while (A)
{
    statement
}
```

先计算A，若结果为**_true_**，才执行**_statement_**，若第一次就不满足，则一次循环也不执行。

```c
int i = 0;
while (i<10)
{
    Console.WriteLine(i);
    i++;    
}
```

- 表达式Ａ必须是布尔表达式，且必须放在圆括号中
- 首次求值布尔表达式为**_false_**，语句不执行
- 要求在**_while_**的控制下执行两个或更多语句，必须用大括号将语句分组成代码快。

### 编写for语句

```c
//while 常见结构
initialization;//初始化
while (Boolean expression)//布尔表达式
{
    statement;//循环主体语句
    update control variable;//更新控制循环的变量
}
```

**_for_**语句提供了这种结构的更正式版本，以下为**_for_**语句的语法：

```c
for (initialization;Boolean expression;update control variable)
{
    statement;
}
```

```c
for (int i = 0;i<10;i++)
{
    Console.WriteLine(i);
}
```

- 初始化只发生一次，且一定发生
- 执行顺序：初始化->判断条件->循环主体->更新控制变量->判断条件->…
- 若一开始就不满足条件，只会执行初始化语句，就退出循环
- **_for_**语句三个部分都可以省略

```c
for (;;)
    statement;
```

- 若是在**_for_**的初始化中声明了变量，则该变量的作用域仅在**_for_**语句主体中，循环结束后变量消失

### 编写dowhile语句

```c
do
{
    statement;    
}while(A);
```

- 先执行**_do_**后的循环主体，若**_while_**满足，则再次循环，反之退出
- 与**_for_**和**_while_**不同，**_do_**语句若是第一次就不满足条件，也会执行一次循环主体。意味着无论如何，**_do_**语句至少执行一次循环
- 注意**_do_**语句的**_while_**后面一定要有分号

```c
int i = 0;
do
{
    Console.WriteLine(i);
    i++;
}while(i<10);
```

### break和continue语句

- **_break_**语句 
   - 在前面**_switch_**语句中，我们就用过**_break_**终止语句
   - **_break_**意思是直接终止当前代码段，直接跳出当前代码段或循环
- **_continue_**语句 
   - **_continue_**意思是停止当前该次循环，进入下一次循环
- **_break_**和**_continue_**常与**_if_**一起使用

```c
int i;
for(i=1;i<10;i++)
{
    if(i==5)
        break;
    Console.WriteLine(i);
}

Console.WriteLine(“/n”);
//换行

for(i=1;i<10;i++)
{
    if(i==5)
        continue;
    Console.WriteLine(i);
}
```

输出结果：
1 2 3 4
1 2 3 4 6 7 8 9

## 管理错误和异常

### 执行代码和捕捉异常

-  try 
   - 代码放到**_try_**中，代码运行时会先尝试执行**_try_**块内的所有语句
   - 如果没有任何语句产生异常，则这些语句将一个接一个运行至全部完成。
   - 但一旦出现异常，就跳出**_try_**块，进入一个**_catch_**处理程序执行
-  catch 
   - 紧接着**_try_**块，写一个或多个**_catch_**来处理可能发生的错误

```c
try
{
    int leftHandSide = int.Parse(lhsOperand.Text);
    result.Text = answer.ToString();
}
catch (FormatException)
{
    //处理异常
}
catch (OverflowException)
{
    //处理异常
}
```

- 此处的**_catch_**可以多个

### 使用checked和unchecked正数运算

```c
int number = int.MaxValue;
checked
{
    int willThrow = number++;
    Console.WriteLine(“永远执行不到这”)；
```

- checked代码段，会检查所有正数是否溢出，若溢出，抛出Overflow

```c
int number = int.MaxValue;
unchecked
{
    int willThrow = number++;
    Console.WriteLine(“永远会执行到这”)；
```

- unchecked，强制不检查溢出的代码段

## 创建并管理类和对象

### 理解分类与封装的目的

- 类，即分类（**_class_**），写程序时，很有必要对问题及其解决方案中固有的概念进行分类，然后用编程语言对这些类进行建模。
- 封装，是定义类的重要原则，其中心原则是：使用类的程序不用关心类的内部是如何工作，只需要创建类的实例并调用类的方法。封装也称为信息隐藏，他又两个实际目的： 
   - 将方法和数据合并到类中，也就是为了支持分类；
   - 控制对方法和数据的访问，也就是为了控制类的使用。

### 定义并使用类

- C#使用**_class_**定义新类。
- 以下**_Circle_**类包含方法（计算圆的面积）和数据（圆的半径）：

```c
class Circle
{
    int radius;
    double Area()
    {
        return Math.PI * radius * radius;
    }
}
```

类的主体包含的一般**方法**（如**_Area_**）和**字段**（**_radius_**）。

- 类中的变量称为**字段**
- 要使用类需要用到**_new_**关键字，进行实例化，关于实例化在后面章节学习

```c
Circle c;//创建Circle变量
c = new Circle();//实例化
```

### 控制可访问性

- 只能在类内部访问的方法或字段是私有的。声明私有方法或字段，需要在声明前添加**_private_**关键字。 
   - 若没有添加关键字，则默认为**_private_**
   - 前文**_Circle_**类里的字段和方法就是私有的，无法在其他类里访问
   - 私有字段和方法在自身的类中是可访问的，如**_Area_**方法就能调用**_radius_**
- 方法或字段如果既能从类的内部访问，也能从外部访问，就说它是公共的。声明公共方法或字段，需用**_public_**关键字

```c
class Circle
{
    private int radius;
    public double Area()
    {
        return Math.PI * radius * radius;
    }
}
```

-  此处**_radius_**被设置为私有字段，但是**_Area_**被设为公共方法，因此，在类的外部虽然不能直接调用私有的**_radius_**，但是可以调用**_Area_**，进而调用到**_radius_** 
-  命名建议： 
   - 公共标识符以大写字母开头
   - 非公共标识符（包括局部变量）以小写字母开头

### 构造器

上述的**_Circle_**的作用甚小，因为其中的**_radius_**，无法初始化，所以引入了构造器这一概念

#### 使用构造器

- 构造器一般用于初始化类
- 构造器是在创建类的实例时自动运行的方法。它与类同名，能获取参数，但不能返回任何值（void也不能加）。
- 每个类至少要有一个构造器，不提供构造器的话，编译器自动生成一个什么都不做的默认构造器。

```c
class Circle
{
    private int radius;
   
    public Circle()//构造器
    {
        radius = 0
    }
   
    public double Area()
    {
        return Math.PI * radius * radius;
    }
}
```

```c
Circle c;
c = new Circle();
double area = c.Area();//调用实例Circle类c中的Area方法
```

#### 重载构造器

```c
class Circle
{
    private int radius;
   
    public Circle()//默认构造器
    {
        radius = 0;
    }
    public Circle(int initialRadius)//重载构造器
    {
        radius = initialRadius;
    }
   
    public double Area()
    {
        return Math.PI * radius * radius;
    }
   
}
```

然后使用这两个构造器：

```c
Circle c;
c = new Circle(45);
```

此时因为我们实例化时，括号里有一个**_int_**，便会调用我们重载的那个构造器，让我们的**_radius_**初始化为45。

- C#的一个特点是，当我们为类写了任何构造器后，编译器不再自动生成默认构造器。
- 若是我们写了构造器之后，让它接受一个或多个参数，但是我们还想使用无参的构造器，就必须自己写一个**无参构造器**

#### 解构对象

- 构造器->创建并初始化对象（通常是填充它包含的字段）
- 解构器->检查对象并提取它的字段的值。

```c
class Point
{
    private int x,y;
   
    public void Deconstruct (out int x, out int y)
    {
        x = this.x;
        y = this.y;
    }
}
```

- 必须命名为Deconstruct。
- 必须是void方法。
- 必须获取一个或多个参数。这些参数要用对象中的字段的值填充。
- 参数用out修饰符加以标记。意味着这些值会传回给调用着。
- 方法主体代码向参数赋值。

```c
Point origin = new Point();
(int xVal,int yVal) = origin;
```

上述代码运行后，解构器会将Point类中的**_x_**,**_y_**的值传递出来

### 理解静态方法和数据

- 前文用过的**_Math_**类，我们用到圆周率时，直接用的**_Math.PI_**,这与我们自己建立的类的用法不一样，我们需要对类实例后才能使用，其中的不同就在于**_Math_**里的**PI**字段是静态字段，**_Math_**里的**_Sqrt_**是静态方法

```c
class Math
{
    public static double Sqrt(double d)
    {
        //ToDo
    }
}

class Test
{
    void test ()
    {
        //调用Math中的Sqrt方法
        double d = Math.Sqrt(42.24);
    }
}
```

- 静态方法不依赖类的实例
- 在其中只能访问标记为static的其他方法和字段

#### 创建共享字段

- 静态字段能在类的所有对象之间共享（非静态字段则局部于类的实例）。

```c
class Circle
{
    private int radius;
    public static int num = 0;
   
    public Circle()//默认构造器
    {
        radius = 0;
        num++;
    }
   
    public Circle(int initialRadius)
    {
        radius = initialRadius;
        num++;
    }
}
```

- 因为**_num_**由所有**_Circle_**对象共享，所以每次实例**_num++_**

```c
class Test
{
    void test()
    {
        Circle c1 = new Circle();
        Circle c2 = new Circle(2);
        Console.WriteLine(Circle.num);
    }
}
```

上述代码结果为：2

#### const关键字

- 用**_const_**声明的字段为常量字段，是一种特殊的字段，值永远不变，如前面用到的**_PI_**

```c
class Math
{
    public const double PI = 3.14159265358979;
}
```

#### 理解静态类

- C#允许声明静态类
- 静态类只包含静态成员

```c
public static class Math
{
    public static double Sin(double x) {……}
    public static double Cos(double x) {……}
    public static double Sqrt(double x) {……}
}
```

#### 静态using语句

- 在前面我们调用静态方法或静态字段时，我们都需要指明对应的类名，比如：**_Math.Sqrt_**,**_Console.WriteLine_**.
- 静态**_using_**将类引入作用域，这样在访问其静态成员时可以省略类名

```c
using static System.Math;
using static System.Console;
…
var root = Sqrt(99.9);
WriteLine($”asdasd”);
```

- 这里**_using_**类名前还写了**_System_**是因为要附加命名空间进行完全限定

## 理解值和引用

### 值类型和引用类型

-  值类型 
   - 大多数基元类型（如int,float,double和char等，但是不包括string）
   - 将变量声明为值类型，编译器会分配出能够储存容纳这种值的内存空间
-  引用类型 
   - 类是引用类型的一个例子
   - 比如前面的**_Circle_**类，我们声明一个**_Circle_**变量是，编译器不分配一个足够容纳一个**_Circle_**的内存空间，而是分配一小块空间，用于储存“真正存放**_Circle_**变量实例的内存地址”
   - 该地址称为对内存块的引用。
   - 而这块地址的存储发生在我们使用**_new_**对类进行实例化时发生

```c
int i = 42;
int copy = i;
i++;
```

- 声明i时，吸用为i分配了一块内存用于储存值，声明copy则分配了另一块内存，虽然我们将i复制给了copy，但是实际上它们是两块完全不同的内存块，即我修改i，对copy毫无影响

```c
Circle c = new Circle();
Circle refc = c;
```

- 声明c时，分配了一块用于存放地址的内存块，且在new时，将这个实例的地址存放于c；声明refc时，分配了一块内存用于存放地址，而将c复制给了refc，就意味着这时c和refc的内存块都存放了同一个实例的地址，此时我修改c的实例，因为它们是同一实例，则refc也被修改

### 理解null值和可空类型

- 对于值类型，我们都习惯于这样初始化：

```c
int i = 0;
double d = 0.0;
```

- 而对于引用类型的初始化

```c
Circle c = new Circle(42);
```

- 但是对于我不想一开始就实例化的引用类型，我们可以将其初始化为null（空）

```c
Circle c = new Circle(42);
Circle copy = null;
if (copy == null)
{
    copy = c;
}
```

#### 空条件操作符

在我们使用一个引用类型时，通常要检查其是否为空：

```c
if (c != null)
{
    Console.WriteLine($”The area is {c.Area()}”);
}
```

此时若c为null，则不会写入任何内容，我们还可以使用空条件操作符”?”

```c
Console.WriteLine($”The area is {c?.Area}”)
```

若c为null，则不会调用**_Area_**方法
输出为：The area is

#### 使用可空类型

- null本身是个引用，不可赋值给值类型

```c
int i = null;//错误
```

- 但是我们可使用“可空值类型”

```c
int? i = null;//正确
```

- 但是要注意即使我们给一个可空类型赋值了，它也不是值类型，就意味着一个希望接收值类型的方法，是不能使用可空变量的

```c
int? i = null;
int j = 99;
i = 100;//可以给可空变量赋值
i = j;//可以用值类型给可空变量赋值

j = i;//错误！不可以用可空变量给值类型赋值

private void test(int x)
{
    //ToDo
}

test(i);//错误！不可以把可空变量当作值类型传入
```

#### 理解可空类型的属性

- 可空类型公开了两个属性 
   - HasValue 可判断可空类型是包含一个值，还是包含null
   - Value 若可空类型包含值，可用Value获取该值

```c
int? i = null;
if(!i.HasValue)
{
    i = 99;//如果i为null就将99赋值给它
}
else
{
    Console.WriteLine(i.Value)//i部位null，就将它的值输出
}
```

### 使用ref和out参数

- 向方法传递实参是，方法内通常是使用的对实参的拷贝，形参。形参和实参是两块不同的内存，对形参的操作，并不会影响到原来的实参

```c
static void test(int num)
{
    num++;
}

static void Main()
{
    int i = 0;
    test(i);
    Console.WriteLine(i);
}
```

结果为：0

- 说明test方法对形参的操作，并没有影响到实参的值

但是，如果传入的是一个引用类型，对形参的改变却会影响到原来的实参的值，这是因为，实参将地址传入给了形参，而这时形参的引用对象和实参的是同一个，这就意味着，对形参的引用对象进行修改，实参的引用对象也会改变。

但是在C#中，我们更期望使用**_ref_**和**_out_**关键字

#### 创建ref参数

- 为参数（形参）添加**_ref_**前缀，作用与参数的所有操作都会作用于原始实参
- 但是在传入前，要先对形参初始化

```c
static void test(ref int num)
{
    num++;
}

static void Main()
{
    int i = 0;
    test(ref i);//传递实参也需要用ref前缀
    Console.WriteLine(i);
}
```

结果为：1

#### 创建out参数

- 使用**_ref_**时，实参要先初始化，但若是我想要在方法内部初始化实参，则需要用到**_out_**前缀
- 与**_ref_**相似，**_out_**也会让形参的任何操作应用于实参
- **_out_**，必须在方法内部对参数进行赋值

```c
static void test(out int num)
{
    num = 42;
}

static void Main()
{
    int i;
    test(out i);
    Console.WriteLine(i);
}
```

结果为：42
