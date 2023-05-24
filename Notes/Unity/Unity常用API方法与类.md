# Unity常用的方法和类

> Started	2022 9 20
>
> Author	黄智斌 HuangZhibin9
>
> 笔记下载：[HuangZhibin9/NotesDownload (github.com)](https://github.com/HuangZhibin9/NotesDownload)

- **本篇旨在熟悉unity基本操作后，进一步了解unity常用的方法和类**



![101246e27d7b586714](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/101246e27d7b586714.png)

## 脚本API文档与用户手册



自主学习Unity相关代码使用：

- [Unity脚本API文档](https://docs.unity3d.com/cn/2020.2/ScriptReference/index.html)  **(查询具体代码)**
- [Unity用户手册](https://docs.unity3d.com/cn/2020.2/Manual/index.html)**（查询模块功能及方法）**





## 打印函数

### 打印函数的测试功能

```c#
//打印函数有'print'与'Debug'两种
//打印函数的结果会在Unity的console窗口显示出来

void Start()
{
    print("正式进入API课程的学习");
    Debug.Log("Unity常用的方法和类");
    //1.测试
    Calculate();
}

//用于测试和占位
//如我有一个新的功能函数，暂时没有填入功能代码，可以先用打印函数进行测试与占位
private void Calculate()
{
    //Debug.Log("计算");
    Add();
    Subtract();
}
private void Add()
{
    Debug.Log("加法");
}
private void Subtract()
{
    Debug.Log("减法");
}
```



### 打印函数的检错功能

```c#
void Start()
{
    //2.检错
    Debug,Log("2+3的计算开始了");
    int a = 1;
    Debug,Log("a的数值为：" + a);
    int b = 3;
    Debug,Log("b的数值为：" + b);
    int num = a + b;
    Debug,Log("2+3的结果为" + num);
    //通过给每一个加上一个打印函数，就可以检测出错误出现在a的赋值错误
}
```



### 两种打印方法的区别

```c#
//'print'方法继承自MonoBehaviour
//'Debug'方法则是独立封装的类，Log方法为静态方法不需要实例

public class TestDebugAndPrintClass
{
    private void TestPrint()
    {
        print("测试");
        //此处会出错，显示没有print这个函数，因为该类没有继承自MonoBehaviour
    }
    
    private void TestDebug()
    {
        Debug.Log("测试");
        //此处能成功运行，因为Debug是独立封装的类
    }
}

public class TestPrintClass:MonoBehaviour
{
    private void TestPrint()
    {
        print("测试");
        //此处则能成功运行
    }
}
```

## 事件函数

![](https://raw.githubusercontent.com/HuangZhibin9/HzbImage/master/%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E5%87%BD%E6%95%B0.jpg)

### Reset 函数

* 调用情况
  + 只能在编辑器状态下调用（不运行）。

* 调用时间、次数与作用

  + 当脚本第一次挂在到对象时或者使用了Reset命令时调用。

  + 一般用来初始化脚本的各个默认值，Reset常用于检视面板中设置好的默认值。

```c#
public float attack = 10.0;

private void Reset()
{
    Debug.Log("调用了Reset函数");
    Debug.Log("攻击值设为默认值" + attack);
}
```

![](https://raw.githubusercontent.com/HuangZhibin9/HzbImage/master/Reset.png)

![](https://raw.githubusercontent.com/HuangZhibin9/HzbImage/master/%E6%89%93%E5%8D%B0%E7%BB%93%E6%9E%9C.png)





### Awake 函数

* 调用情况

  + 在加载场景时初始化包含脚本激活状态的***GameObject***时

  + ***GameObject***或者***组件***由非激活转变为激活状态

  + 在初始化使用***Instantiate***创建的***GameObject***后

* 调用时间、次数与作用

  + 在脚本实例的生存期内，**Awake仅调用一次**。脚本的生存期持续至包含它的场景结束。

  + Unity调用每个***GameObject***的***Awake***的顺序时不确定的。

  + 一般用Awake代替构造函进行初始化。

```c#
private void Awake()
{
    Debug.Log("调用了Awake方法");
}
```

* 注意此处***Awake***函数挂载的物体必须是激活的，而该脚本无论激活与否都会执行***Awake***

![脚本未激活](https://img-blog.csdn.net/20180621133844745?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3BpeGVsX25wbGFuY2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

- 并未激活脚本，但还是执行了***Awake***



### OnEnable 函数

* 调用情况

  + 游戏物体被激活

  + 脚本组件被激活

* 调用时间、次数与作用

  + 每次游戏物体或脚本被激活都会调用一次（对比***Awake***函数）

  + 用于重复变为初始状态

```c#
private void OnEnable()
{
    currentHP = 100;
    Debug.Log("当前血量为："+currentHP);
}

//比如我们杀死一个敌人之后，敌人的HP为0了，若是我们想让这个怪物复活的同时初始化他的HP我们就用OnEnable，多次调用
//而此时若用Awake，则只能调用一次
```



### Start 函数

* 调用情况

  + 游戏物体被激活

  + 脚本组件被激活

* 调用时间、次数与作用
  + 在脚本实例激活时，在第一帧的***Update***之前，在***Awake***和***OnEnable***之后

```c#
void Awake()
{
    //加载UI所需的图片资源
}

void Start()
{
    //显示UI
}

//因为Start在Awake之后执行，所以上述代码能保证UI显示时能正确显示图片
```



### 关于变量赋值顺序的探究

- 在学习了上述函数之后，我们探究一下它们之间的顺序关系



```c#

public class TestClass : MonoBehaviour
{
    //在类头部赋值变量
    public float attackValue = 10;
    
    //在Awake中赋值变量
    void Awake()
    {
        attackValue = 1;
    }
    
    //在OnEnable中赋值变量
    void OnEnable()
    {
        attackValue = 2;
    }
    
    //在Start中赋值变量
    void Start()
    {
        attackValue = 3;
    }
}
```

同时在检视面板**inspection**中调整变量的值

![inspection](https://raw.githubusercontent.com/HuangZhibin9/HzbImage/master/inspection.png)



最后结论：

- 类头部声明变量（字段）并赋值->检视面板赋值->Awake->OnEnable->Start
- 从左到右为变量被赋值的顺序，即后赋值的把前面的结果覆盖
- 上述也是脚本中函数的调用顺序



### Update与LateUpdate函数

#### Update

- 调用情况
  - 游戏物体被激活
  - 脚本组件被激活
- 调用时间,次数与作用
  - 每帧都会调用，是最常见的函数，每秒大概调用60此左右（根据当前电脑的性能和状态）
  - 一般用于实时更新数据，接受输入数据

```c#
void Update()
{
    //ToDo
}
```

#### LateUpdate

- 调用情况
  - 游戏物体被激活
  - 脚本组件被激活
- 调用时间,次数与作用
  - 与***Update***一样每帧都会调用，每秒大概调用60此左右（根据当前电脑的性能和状态）
  - 但是***LateUpdate***一定会在***Update***之后进行调用
  - 比如摄像机跟随人物，一定是人先走然后才是摄像机跟随，即：人物移动在***Update***中，摄像机跟随在***LateUpdate***中

```c#
void LateUpdate()
{
    //ToDo
}
```

#### FixedUpdate

- 由于每台电脑的性能不同，Update每秒的调用次数不一样，若是涉及到物体运动的代码放在Update中，则可能出现不同电脑，物体移动的速度不同，因此引出FixedUpdate——执行间隔的时间固定，与帧数无关

```c#
void FixedUpdate()
{
	//ToDo
}
```

- 两者的异同
  - 同：当MonoBehaviour启用时，其在每一帧被调用。都是用来更新的
  - 异：
    - FixedUpdate()每帧与每帧之间相差的时间是固定的，而Update()不是固定的
    - Update受当前渲染的物体影响，这与当前场景中正在被渲染的物体有关（比如人物的面数，个数等），有时快有时慢，帧率会变化，Update被调用的时间间隔就会发生变化。
      但是FixedUpdate则不受帧率的变化影响，它是以固定的时间间隔来被调用。
  - 在官网文档中也提到说： 处理Rigidbody时，需要用FixedUpdate代替Update。例如:给刚体加一个作用力时，你必须应用作用力在FixedUpdate里的固定帧，而不是Update中的帧。(两者帧长不同)。正是因为FixedUpdate是以固定的时间间隔来被调用，所以人物运动起来比较平滑（运动起来不会有忽快忽慢的感觉，像是跳帧似的），这也就是为什么处理Rigidbody时要用FixedUpdate了（当然还有其他一些原因）。



- 总结
  - Update是在每次渲染新的一帧的时候才会调用
  - FixedUpdate是在固定的时间间隔执行，不受游戏帧率的影响
    FixedUpdate的时间间隔可以在项目设置中更改，点击 Edit - Project Setting - time  找到 Fixed timestep，就可以修改了。
  - LateUpdate是晚于所有Update执行的。





### OnDisable 函数

- 调用情况
  - 游戏物体被禁用
  - 脚本组件被禁用
  - 游戏物体被销毁
- 调用时间、次数与作用
  - 每次满足调用情况时都会调用一次（对比***OnEnable***）
  - 用于一些对象的状态重置，资源回收与清理

```c#
void OnDisable()
{
    //ToDo
}
```



### OnApplicationQuit 函数 

- 调用情况
  - 在程序退出之前，所有的游戏对象都会调用这个函数
  - 在编辑器终止运行模式时调用
- 调用时间、次数与作用
  - 在上述情况满足时，调用一次
  - 一般用于处理一些游戏退出后的逻辑

```c#
void OnApplicationQuit()
{
    //ToDo
}
```

### OnDestroy 函数

- 调用情况
  - 场景或游戏结束
  - 游戏物体被销毁
  - 当前脚本被移除
- 调用时间、次数与作用
  - 满足上述情况时调用一次
  - 一般用于物体销毁时相关的代码

```c#
void OnDestroy()
{
	//ToDo
}
```



### OnDisable 与 OnDestroy 

- ***OnDisable***
  -  当对象变为不可用或非激活状态时-此函数被调用
  -  即当这个物体不可用或非激活时-此函数被调用，也有当游戏结束运行时-此函数被调用
- ***OnDestroy***
  -  当***MonoBehaviour***将被销毁时，这个函数被调用
  -  即当游戏结束运行时-此函数被调用





## 游戏物体

### 游戏物体的创建

- 创建方式
  - 使用构造函数（声明+实例化），创建空物体
  - 根据现有的预制体或者场景已有的游戏物体，来实例化
  - 使用一些特别的API创建一些基本的几何体

```c#
public GameObject grisPrefab;//声明一个游戏物体，可在inspector面板拖入预制体

void Start()
{
	GameObject myGo = new GameObject("MyGameObject");//声明+实例化
    
    GameObject.Instantiate(grisPrifab);//以grisPrefab预制体创建游戏物体
    
    GameObject.CreatePrimitive(PrimitiveType.Plane);//创建一个平面
}
```

![image-20221009105713198](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221009105713198.png)





### 游戏物体的查找和获取

- 已有物体引用，如自身或者已创建公共字段且在Hierarchy中拖入对象

```c#
//自身
this.gameObject;
//其他
public GameObject myGo;//创建公共字段

void Start()
{
    myGo//可以直接使用
}
```

- 无直接引用，对其它物体的查找

```c#
//a.通过名称查找
GameObject mainCameraGo = GameObject.Find("Main Camera");//找到名字为"Main Camera"的物体

//b.通过标签查找
GameObject mainCameraGo = GameObject.FindGameObjectWithTag("MainCamera");//找到标签为"MainCamera"的物体

//c.通过类型查找
Camera camera = GameObject.FindGameObjectWithType<Camera>();//找到所有物体中的一个Camera组件引用

//d.多数查找与获取
GameObject[] enemyGos= GameObject.FindGameObjectsWithTag("Enemy");//找到所有标签为"Enemy"的游戏物体
BoxCollider[] colliders= GameObject.FindObjectsOfType<BoxCollider>();//找到所有物体中的所有Camera组件引用
```

- 通过类型查找
  - 这种查找传回的是你查找的组件类型，如上例，查找“Camera”则传回的是“Camera”引用，“BoxCollider”传回的是“BoxCollider”引用，而不是对应的游戏物体。
  - 若是需要通过查找有此组件的物体，可先找到该组件的引用，如“Camera”，再使用`camera.gameObject`得到游戏物体。
- 多数查找中返回的是一个数组。



### Component的查找和获取

- 查找自身物体上的组件

```c#
//获取此物体上Camera组件
void Start()
{
    Camera camera = GetComponent<Camera>();
}
```

![image-20221011104754629](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221011104754629.png)

- 获取其他物体上的组件

```c#
//先获取游戏物体，在查找组件
//游戏物体可通过拖拽得到引用，也可以通过前面的Find方法
void Start()
{
    GameObject cameraGo = GameObject.Find("Main Camera");//获取游戏物体
    Camera camera = cameraGo.GetComponent<Camera>();//获取组件
}
```



## Transform组件

### Transform组件的访问和获取

- 方法同前面获取`Component`
  - 获取自身的`Transform`
  - 获取其他物体的`Transform`

### Transform的位置和旋转信息

```c#
void Start()
{
	Debug.Log("Gris的世界坐标位置信息：" + this.transform.position);
    Debug.Log("Gris的四元数旋转信息：" + this.transform.rotation);
}
```

![image-20221011111336874](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221011111336874.png)

- 此处的`Rotation`是四元数，而inspector面板是欧拉角的形式

![image-20221011111437482](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221011111437482.png)

```c#
void Start()
{
    Debug.Log("Gris的欧拉旋转角：" + this.transform.eularAngle);//输出的是inspector的欧拉角
}
```

- 在unity中若是一个物体有子物体，则在inspector上子物体会显示相对坐标，获取方法：

  ```c#
  void Start()
  {
      Debug.Log("Gris相对与父对象的位置坐标是：" + this.transform.localPosition);
      Debug.Log("Gris相对与父对象的选择信息是：" + this.transform.localeulerAngle);
  }
  ```

- 缩放与正方向

```c#
void Start()
{
	Debug.Log("Gris相对于父对象的变换缩放是："+grisTrans.localScale);
    Debug.Log("Gris的自身坐标正前方（Z轴正方向）是："+grisTrans.forward);
    Debug.Log("Gris的自身坐标正右方（X轴正方向）是：" + grisTrans.right);
    Debug.Log("Gris的自身坐标正上方（Y轴正方向）是：" + grisTrans.up);
}
```

### Transform的查找方法

```c#
void Start()
{
    Debug.Log("当前脚本挂载的游戏对象下的叫Gris的子对象身上的Transform组件是："+transform.Find("Gris"));
    Debug.Log("当前脚本挂载的游戏对象下的第一个（0号索引）子对象的Transform引用是："+transform.GetChild(0));
    Debug.Log("Gris当前在此父对象同级里所在的索引位置："+ grisTrans.GetSiblingIndex());
}
```

## Vector2结构体

###  Vector2静态变量

- 世界坐标方向

```c#
void Start()
{
    print(Vectot2.down);
    print(Vectot2.up);//Y轴正方向
    print(Vectot2.left);
    print(Vectot2.right);//X轴正方向
    print(Vector2.one);//单位向量（1，1）
    print(Vector2.zero);//零向量（0，0）
}
```

![image-20221011132937032](C:\Users\Adam\AppData\Roaming\Typora\typora-user-images\image-20221011132937032.png)

### Vector2的构造函数和成员变量

```c#
	void Start()
    {
        //构造函数
        Vector2 v2 = new Vector2(2,2);
        print("V2向量是：" + v2);
        
        //模长
        print("V2向量的模长：" + v2.magnitude);
        print("V2向量的模长的平方：" + v2.sqrMagnitude);
        print("v2向量的单位向量" + v2.normalized);  
    }
```

![image-20221011140555731](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221011140555731.png)

```c#
void Start()
{
    print("V2向量的XY值分别是：" + v2.x + "," + v2.y);
    print("V2向量的XY值分别是（使用索引器形式访问）：" + v2[0] + "," + v2[1]);
}
```

![image-20221011140844107](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221011140844107.png)

### Vector2的公共函数

#### 比较向量是否相等

```c#
void Start()
{
    bool equal = v2.Equals(new Vector2(1, 1));
    print("V2向量与向量（1,1）是否相等？" + equal);
    Vector2 v2E = new Vector2(1, 3);
    bool equalv2E = v2E.Equals(new Vector2(3, 1));
    print("v2E向量与向量（3,1）是否相等？" + equalv2E);
}
```

![image-20221011141338491](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221011141338491.png)

- (1,3)和(3,1)是不相等的



#### Vector2的单位化

```c#
void Start()
{
    print("V2向量是：" + v2);
    print("V2向量的单位化向量是：" + v2.normalized + "但是V2向量的值还是：" + v2);
    v2.Normalize();
    print("V2向量是：" + v2);
}
```

- 前面讲到的`v2.normalized`是Vector2的一个变量，不会改变v2的值；而此处的`v2.Normalize();`是一个方法，会改变v2的值

#### 设置及使用Vector2的值

```c#
void Start()
{
    //设置v2的值
    v2.Set(5, 9);
    print("V2向量是：" + v2);
    //将v2的值使用在设置位置
    transform.position = v2;
    transform.position = new Vector(2,2);
    
    //但是我们无法直接设置其中的x或者y
    transform.position.x = 4;//会报错！
}
```

- 无法修改position某个值的原因：
  - Transform中的position是属性(换成方法也一样，因为属性的实现本质上还是方法)而不是公有字段
  - position的类型是Vector的，而Vector是Struct类型
  - Struct之间的赋值是拷贝而不是引用
