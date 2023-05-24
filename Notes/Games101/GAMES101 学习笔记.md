# kaGAMES101 - 学习笔记

## 一、计算机图形学概述

![image-20230518145916670](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230518145916670.png)

### 1、计算机图形学是什么

- 用计算机技术合成视觉信息

### 2、主要方向

- 光栅化
  - 把三维空间的形体，呈现在屏幕上
  - 把投影出来的基本图形分解成片元fragments（像素 pixels）
  - 在游戏中使用（实时渲染 Real-time）

- 曲线和网格
  - 如何在计算机图形中表示几何（曲线）

- 光线追踪
  - 从摄像机发射光线穿过每一个像素
    - 计算相交与阴影
    - 持续反射光线直到命中光源（模拟光线）
  - 在动画和电影中使用（离线渲染 Offline）
    - 相比实时渲染花费时间更长，但是效果好，真实
- 动画/模拟
  - 关键帧动画
  - 布料、物理模拟、特效

### 3、计算机视觉和计算机图形学区别

![image-20230514164020477](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230514164020477.png)

- 把三维模型转化为二维屏幕图形是 计算机图形学
- 从图形推断模型是计算机视觉

## 二、线性代数复习

![image-20230518150818744](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230518150818744.png)

### 1、向量（Vector）

- 向量的定义

- 标准化

- 向量求和

- 坐标表示

- 长度表示

$$
在图形学中通常用列向量表示一个向量\ \ A=
\left(
\begin{matrix}
x\\
y
\end{matrix}
\right)
,
A^T=
\left(
\begin{matrix}
x&
y
\end{matrix}
\right)
$$

#### 点乘（Dot product）

- 点乘的定义

- 点乘的性质

  - 交换律、结合律、分配律

- 点乘的运算法则 

- 点乘的作用：

  - 找到两个向量之间的夹角：$\cos\theta=\frac{\vec{a}\cdot\vec{b}}{|\vec{a}|\cdot|\vec{b}|}$
  - 找到一个向量在另一个向量的投影 
  
  ![image-20230515104843893](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515104843893.png)
  
  - 判断两个向量是否同向
  
    ![image-20230515105003802](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515105003802.png)

#### 叉乘（Cross product)

- 叉乘的定义

![image-20230515105151928](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515105151928.png)



- 方向：右手螺旋定则（OpenGL 左手坐标系）
  - 通过右手螺旋定则确定的坐标系即为右手坐标系
- 作用：建立三维坐标系
- 性质：

![image-20230515105412254](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515105412254.png)

- 坐标运算：<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515105614705.png" alt="image-20230515105614705" style="zoom: 50%;" /><img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515105644951.png" alt="image-20230515105644951" style="zoom: 50%;" />

- 作用：

  - 判断左右：用叉乘的方向反映两个向量的左右关系

    <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515105907525.png" alt="image-20230515105907525" style="zoom:50%;" />

  - 判断内外：若点在三条边的同一方向则在内部

  <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515105958178.png" alt="image-20230515105958178" style="zoom:67%;" />

  

  

### 2、矩阵（Matrices）

- 矩阵的定义
- 矩阵的乘法
- 性质
  - 没有交换律
  - 结合律、分配律
- 单位矩阵
- 转置矩阵
- 逆矩阵

## 三、变换（二维与三维）

![image-20230518151840763](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230518151840763.png)

### 变换的应用

- 位置移动
- 旋转缩放
- 光栅成像

### 二维变换

#### 缩放变换（Scale）

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515161940437.png" alt="image-20230515161940437" style="zoom: 67%;"/>  

#### 翻转变换（Reflection）

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515162242920.png" alt="image-20230515162242920" style="zoom:67%;"/> 

#### 剪切变换（Shear）

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515162700151.png" alt="image-20230515162700151" style="zoom:67%;" /> 

#### 旋转变换（Rotate）

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515162747396.png" alt="image-20230515162747396" style="zoom:67%;" />

​                                                      <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/707A30BA888E6C0755E14F14795C4BEB.png" alt="img" style="zoom: 67%;" />

- 转置矩阵与逆矩阵相等的矩阵为正交矩阵

#### 总结 ：线性变换 = 矩阵

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515163109416.png" alt="image-20230515163109416" style="zoom: 67%;" />

### 齐次坐标

#### 引入齐次坐标的原因

- 用矩阵形式线性变换无法表示平移（非线性变换）

​                                          <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515163258294.png" alt="image-20230515163258294" style="zoom:67%;" /> 

- 即引入齐次坐标，将线性变换和平移统一在同一个矩阵中

#### 二维齐次坐标

- 二维点    ：$(x,y,1)^{T}$
  - $(x,y,w)^{T} = (\frac{x}{w},\frac{y}{w},1)^{T}$
- 二维向量：$(x,y,0)^{T}$
- 点与向量不同的原因，向量具有平移不变性
  - 向量 + 向量 = 向量 （向量和）
  - 点 - 点 = 向量 （两点相减表示向量）
  - 点 + 向量 = 点 （点在向量方向移动）
  - 点 + 点 = 中点 
    - 如：$A = (x_1,y_1,1)^{T},B=(x_2,y_2,1)^{T},则A+B=(x_1+x_2,y_1+y_2,2)^{T},即(\frac{x_1+x_2,}{2},\frac{y_1+y_2,}{2},1)^T$

#### 二维放射变换

- 放射变换 = 线性变换 + 平移

​                                ![image-20230515164547506](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515164547506.png) 

- 引入齐次坐标（先线性，再平移）

​                             ![image-20230515164617030](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515164617030.png) 

- 缩放矩阵：

​                   <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515164753554.png" alt="image-20230515164753554" style="zoom:67%;" /> 

- 旋转矩阵：

​                     <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515164822351.png" alt="image-20230515164822351" style="zoom:67%;" /> 

- 平移矩阵：

​                    <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515164851203.png" alt="image-20230515164851203" style="zoom:67%;" /> 

### 逆变换

- 逆变换即逆矩阵 $M^{-1}$

![image-20230515165059897](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515165059897.png)

### 组合变换

- 一个复杂变换可以拆解若干个简单变换，即若干个简单变换可以组合成一个复杂变换
  - $A_n...A_3A_2A_1\left[ \matrix{x\\y\\z} \right]$
- 矩阵乘法没有交换律，即变换顺序很重要，从右到左

### 三维变换

- 三维点    ：$(x,y,z,1)^{T}$
  - $(x,y,z,w)^{T} = (\frac{x}{w},\frac{y}{w},\frac{z}{w},1)^{T}$
- 三维向量：$(x,y,z,0)^{T}$

- 引入齐次坐标：

​                      <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515170150486.png" alt="image-20230515170150486" style="zoom:67%;" /> 

- 缩放矩阵：

​                  ![image-20230515194031718](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515194031718.png) 

- 平移矩阵：

​                  ![image-20230515194101812](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515194101812.png) 

- 旋转矩阵

  - X轴：

    ​    ![image-20230515194320497](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515194320497.png) 

  - Y轴：

       ![image-20230515194356388](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515194356388.png) 

  - Z轴：

       ![image-20230515194433534](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515194433534.png) 

- Y轴特别的原因：

  - $\vec{x}\times\vec{y}=\vec{z},\vec{y}\times\vec{z}=\vec{x},\vec{z}\times\vec{x}=\vec{y}$
  - 也就是z向x旋转，才是正y，但是坐标系中绕y旋转的方向是x到y，所以矩阵是负的

![image-20230515194847228](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515194847228.png)

- 任意绕原点旋转可以分解为三个轴向旋转：

![image-20230515195129667](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515195129667.png)

- 绕任意轴$\vec{n}$旋转$\alpha$角度——罗德里格斯旋转公式

![image-20230515195300289](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515195300289.png)

推导：![img](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/F39CFD1889E151AED0ECFDE90D83A9D5.png)

- 默认旋转轴过原点，若不过，则先平移至原点再平移回去。

## 四、变换（模型、视图、投影）

![image-20230518152840743](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230518152840743.png)

### MVP变换

- MVP变换是用来将虚拟世界三维物体映射（变换）至二维屏幕坐标上

- MVP变换分为三步：
  - 模型变换
  
  - 视图变换
  
  - 投影变换

#### 模型变换

模型变换（model transformation）

- **模型变换的部分主要负责的就是将模型从局部坐标系转到世界坐标系下的过程**，同时对于模型的摆放也会在转换完成之后，对于模型的世界坐标进行转换。因为每个模型都有自己的局部坐标系，所以在对于不同的模型进行移动的时候，将所有模型转化到一个坐标系下，进行的各种仿射变换会比较容易。
- 对于从模型空间转换到世界空间的过程，实际上就是对于模型上的点进行仿射变换将其在模型空间内的坐标转换到在世界空间下的坐标。具体的变换过程其实也是在总体上对于原本的模型空间的坐标乘以一个变换矩阵，从而使其转化为世界空间下的坐标，也就是***模型矩阵（Model matrix）***：

![image-20230515215951419](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515215951419.png)

- 而这里的模型矩阵又可以被拆分成为具体的缩放旋转平移三部分的矩阵：

![image-20230515222257735](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515222257735.png)

- 通俗地说就是，找个好的地方，把所有人集合在一起，摆个pose。



#### 视图变换

视图变换（view transformation）

- **视图变换的部分主要负责的就是将模型从世界空间坐标系转到观察空间坐标系或者视图坐标系下的过程**

- 通俗的说就是，找到一个放相机的位置，往某一个角度去看。

- 变换矩阵的求解方法是把摄像机变换为坐标系中心的过程

  ​                              <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515235639634.png" alt="image-20230515235639634" style="zoom: 67%;" /> 

  - 定义摄像机
    - 位置$\vec{e}$
    - 观察方向$\vec{g}$
    - 上方up$\vec{t}$
  - 把 $\vec{e}$ 移动至原点：$T_{view}=\left[ \matrix{1&0&0&-x_e\\0&1&0&-y_e\\0&0&1&-z_e\\0&0&0&1} \right]$(因为是朝原点移动，所以为负)
  - 旋转$\vec{g}$到-Z，$\vec{t}$到Y，$\vec{g}\times\vec{t}$到X：$R_{view}=\left[ \matrix{x_{\vec{g}\times\vec{t}}&y_{\vec{g}\times\vec{t}}&z_{\vec{g}\times\vec{t}}&0\\x_t&y_t&z_t&0\\x_{-g}&y_{g}&z_{-g}&0\\0&0&0&1} \right]$

  > 推导过程：
  >
  > ​        这个过程是旋转X到$\vec{g}\times\vec{t}$,，Y到$\vec{t}$，Z到$-\vec{g}$的逆过程。所以可以先求这个简单的旋转矩阵，在求其逆矩阵，又因为旋转矩阵是正交矩阵，则逆矩阵即转置矩阵
  >
  > ![image-20230516000939969](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230516000939969.png)![image-20230516001734493](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230516001734493.png)

- 变化矩阵：

$$
\begin{align}
M_{view}&=R_{view}T_{view}\\
&=\begin{bmatrix}x_{\vec{g}\times\vec{t}}&y_{\vec{g}\times\vec{t}}&z_{\vec{g}\times\vec{t}}&0\\x_t&y_t&z_t&0\\x_{-g}&y_{g}&z_{-g}&0\\0&0&0&1\end{bmatrix}
\begin{bmatrix}1&0&0&-x_e\\0&1&0&-y_e\\0&0&1&-z_e\\0&0&0&1\end{bmatrix}
\end{align}
$$



#### 投影变换

投影变换（Projection）

- 关于投影变换的定义，**其实就是将三维坐标下的点，经过矩阵变换，投射到2D坐标空间的过程，也就是将观察空间转换到裁剪空间**。
- 通俗的说就是，摄像机按下快门，把三维空间成像在二维屏幕上。

- 而投影变换包括两种不同的投影方式，**正交投影变换（Orthographic Projection）**和**透视投影变换（Perspective Projection）**

![image-20230516111305004](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230516111305004.png)

##### 视锥体

- 正交投影变换和透视投影变换主要区别是视锥体形状的不同。
- 视锥体的定义：
  - **视锥体指的是摄像机在场景中的一个可见范围，可以用一个虚拟的锥体将整个可见范围包裹起来**，而在视锥体之外的物体将无法被摄像机看到，从而进行剔除操作。而视锥体由六个面组成，分别为上下平面，左右平面，以及**近裁剪平面（Near Clipping Plane）和远裁剪平面（Far Clipping Plane）**

![image-20230516112405533](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230516112405533.png)



##### 正交投影

- 变换矩阵：

$$
\begin{align}
M_{ortho}&=\begin{bmatrix}\frac{2}{r-l}& 0& 0& 0\\ 0& \frac{2}{t-b}& 0& 0\\ 0& 0& \frac{2}{n-f}& 0\\ 0& 0& 0& 1\end{bmatrix}
\begin{bmatrix}1& 0& 0& -\frac{r+L}{2}\\ 0& 1& 0& -\frac{t+b}{2}\\ 0& 0& 1& -\frac{n+f}{2}\\ 0& 0& 0& 1\end{bmatrix}\\\\
&=\begin{bmatrix}\frac{2}{r-l}& 0& 0& -\frac{r+l}{r-l}\\ 0& \frac{2}{t-b}& 0& -\frac{t+b}{t-b}\\ 0& 0& \frac{2}{n-f}& -\frac{n+f}{n-f}\\ 0& 0& 0& 1\end{bmatrix}
\end{align}
$$



- 正交投影的核心：用一个立方体框住物体，$[l,r]\times[b,t]\times[f,n]$，然后把这个立方体变换至标准正方体$[-1,1]^3$中。
  - 由于是右手系，需要注意f和n的大小关系
- 变换顺序：先平移中心至原点，再缩放。

![image-20230516112716689](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230516112716689.png)

##### 透视投影

- 透视投影的特性：

  - 是计算机图形、艺术、视觉系统上最经常用的投影

  - 近大远小

  - 平行线不在平行而是汇聚在一个点上

- 变换矩阵：

$$
\begin{align}
M_{per}&=M_{ortho}M_{persp\rightarrow ortho}\\\\&=

\begin{bmatrix}\frac{2}{r-l}& 0& 0& -\frac{r+l}{r-l}\\ 0& \frac{2}{t-b}& 0& -\frac{t+b}{t-b}\\ 0& 0& \frac{2}{n-f}& -\frac{n+f}{n-f}\\ 0& 0& 0& 1\end{bmatrix}

\begin{bmatrix}n& 0& 0& 0\\ 0& n& 0& 0\\ 0& 0& n+f& -nf\\ 0& 0& 1& 0\end{bmatrix}\\\\&=

\begin{bmatrix}\frac{2n}{r-l}& 0& \frac{l+r}{l-r}& 0\\ 0& \frac{2n}{t-b}& \frac{b+t}{b-t}& 0\\ 0& 0& \frac{n+f}{n-f}& \frac{2fn}{f-n}\\ 0& 0& 1& 0\end{bmatrix}

\end{align}
$$

- 透视投影的核心：用“远平面”和“近平面”框住物体，先把“远平面”挤压成与“近平面”相同，然后做一次正交投影。

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230516112405533.png" alt="image-20230516112405533" style="zoom:67%;" />

- 即透视投影分为两步：
  - 将Frustum挤压为cuboid
  - 对cuboid进行正交投影

- 对于挤压，规定：

  - 挤压过程中，近平面和远平面的Z值不发生变化
  - 挤压过程中，远平面中心点不发送变化

- 推导过程

  - 挤压过程中的比例关系：

    ![image-20230516115529713](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230516115529713.png)

  - x同理：          $y'=\frac{n}{z}y,x'=\frac{n}{z}x$

  - 用齐次坐标，描述视锥体中任意一点的变换：
    $$
    \begin{bmatrix}
    x\\
    y\\
    z\\
    1
    \end{bmatrix}
    \rightarrow
    \begin{bmatrix}
    \frac{nx}{z}\\
    \frac{ny}{z}\\
    unknown\\
    1
    \end{bmatrix}
    \rightarrow
    \begin{bmatrix}
    nx\\
    ny\\
    z\times unknown\\
    z
    \end{bmatrix}
    $$

  - 用矩阵乘法表示为：
    $$
    M(4\times4)\begin{bmatrix}
    x\\
    y\\
    z\\
    1
    \end{bmatrix}
    =\begin{bmatrix}
    nx\\
    ny\\
    z\times unknown\\
    z
    \end{bmatrix}
    $$
    
  - 根据矩阵乘法，可推导出M大致形式：
    $$
    M=\begin{bmatrix}
    n&0&0&0\\
    0&n&0&0\\
    ?&?&?&?\\
    0&0&1&0
    \end{bmatrix}
    $$
  
  - 代入规定：
  
    - 近平面上的点经过变换之后不变：
      $$
      M(4\times4)\begin{bmatrix}
      x\\
      y\\
      n\\
      1
      \end{bmatrix}
      =\begin{bmatrix}
      nx\\
      ny\\
      n^2\\
      n
      \end{bmatrix},得:
      M=\begin{bmatrix}
      n&0&0&0\\
      0&n&0&0\\
      0&0&A&B\\
      0&0&1&0
      \end{bmatrix},A\times n+B=n^2
      $$
  
    - 远平面上的点经过变换Z不变，这个点我们选择中心点简化计算：
      $$
      M(4\times4)\begin{bmatrix}
      0\\
      0\\
      f\\
      1
      \end{bmatrix}
      =\begin{bmatrix}
      0\\
      0\\
      f^2\\
      f
      \end{bmatrix},得:
      M=\begin{bmatrix}
      n&0&0&0\\
      0&n&0&0\\
      0&0&A&B\\
      0&0&1&0
      \end{bmatrix},A\times f+B=f^2
      $$
  
    - 解得：$A=n+f,B=-nf$，即：$M_{persp\rightarrow ortho}=\begin{bmatrix} n&0&0&0\\ 0&n&0&0\\ 0&0&n+f&-nf\\ 0&0&1&0\end{bmatrix}$
  
    - 所以
      $$
      \begin{align}
      M_{per}&=M_{ortho}M_{persp\rightarrow ortho}\\\\&=
      
      \begin{bmatrix}\frac{2}{r-l}& 0& 0& -\frac{r+l}{r-l}\\ 0& \frac{2}{t-b}& 0& -\frac{t+b}{t-b}\\ 0& 0& \frac{2}{n-f}& -\frac{n+f}{n-f}\\ 0& 0& 0& 1\end{bmatrix}
      
      \begin{bmatrix}n& 0& 0& 0\\ 0& n& 0& 0\\ 0& 0& n+f& -nf\\ 0& 0& 1& 0\end{bmatrix}\\\\&=
      
      \begin{bmatrix}\frac{2n}{r-l}& 0& \frac{l+r}{l-r}& 0\\ 0& \frac{2n}{t-b}& \frac{b+t}{b-t}& 0\\ 0& 0& \frac{n+f}{n-f}& \frac{2fn}{f-n}\\ 0& 0& 1& 0\end{bmatrix}
      
      \end{align}
      $$
      

#### 视口变换

##### 视锥

![image-20230516201601394](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230516201601394.png)<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230516201612541.png" alt="image-20230516201612541" style="zoom:67%;" />

- 定义视锥：

  - 宽高比（Aspect ratio）
  - 垂直的视野角度 fovY

- 利用视锥得到物体长宽高：

  ​     <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230516201813300.png" alt="image-20230516201813300" style="zoom:67%;" /> 

##### 屏幕（Screen）

- 本质是二维数组，数组元素为像素
- 典型的光栅成像设备

##### 光栅（Raster）

- 是德语中的屏幕的意思
- Rasterize-光栅化，意思就是把物体画在屏幕上，即成像

##### 像素（Pixel）

- Pixel = picture element
- 可抽象的认为像素是一个颜色均匀的小正方形，是屏幕成像的最小单位，在一个像素中不再细分颜色变化
- 具体颜色有红绿蓝混合而成

##### 屏幕空间

- 规定：
  - 像素坐标以每个像素左下角的（x，y）形式表示，xy为整数，
  - 所有像素都在(0,0)到(width-1,height-1)之间
  - 像素的中心：(x+0.5,y+0.5)
  - 整个屏幕覆盖(0,0) 到 (width,height)
- 认为左下角为原点，向右X，向上Y，如OpenGL。（在不同地方有不同规定，DirectX 为左上原点，向右X，向下Y）

##### 视口变换

- 把MVP变换后得到的标准立方体$[1,1]^3$映射到屏幕空间上（暂时不考虑Z轴，之后会了解）即：
  $$
  [-1,1]^2\rightarrow [0,width]\times [0,height]\\
  M_{viewport}=\begin{bmatrix}
  \frac{width}{2}&0&0&\frac{width}{2}\\
  0&\frac{height}{2}&0&\frac{height}{2}\\
  0&0&1&0\\
  0&0&0&1
  \end{bmatrix}
  $$
  
- 总结：把虚拟世界的物体转换到屏幕：
  $$
  M=M_{viewport}M_{per}M_{view}M_{model}
  $$
  

## 五、光栅化（三角形的离散化）

### 三角形网格

- 三角形的性质与优点
  - 最基础的多边形
    - 其他图形都可以拆解为三角形
  - 三角形一定是一个平面
  - 内外的定义很明确（其他多边形可能还涉及凹凸）
  - 定义三个顶点之后，三角形内可以根据插值做渐变

### 光栅化（Rasterization）

- 光栅化的关键
  - 判断一个像素是否在三角形内（像素中心点与三角形的关系）

#### 采样（Sampling）

- 对一个函数在某点上求值，就是采样

- 通过采样，可以离散化一个函数

  ```c++
  for(int x = 0; x < x_max; ++x) {
  	output[x] = f(x);
  }
  ```

- 光栅化采样：利用像素中心对变换后得到的屏幕空间进行采样各种信息

- 光栅化采样的目的：判断像素是否在三角形内

![image-20230517104404587](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517104404587.png)

![image-20230517104533108](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517104533108.png)

```c++
for(int x = 0; x < x_max; ++x) {
	for(int y = 0; y < y_max; ++y) {
		image[x][y] = inside(tri, x + 0.5, y + 0.5);
	}
}
```

- 判断是否在三角形内
  - **叉乘**

​                     <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517104813039.png" alt="image-20230517104813039" style="zoom:50%;" /> 

- 特殊点

  - 不做处理

  - 做特殊处理（OpenGL等）

    ​       <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517104918348.png" alt="image-20230517104918348" style="zoom:50%;" /> 

#### 优化——Bounding Box

​              ![image-20230517105009418](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517105009418.png) 

#### 走样（锯齿）——Aliasing (Jaggies)

![image-20230517105137413](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517105137413.png)

![image-20230517105147839](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517105147839.png)

## 六、光栅化（抗锯齿与深度测试）

### 采样与走样

- 采样的对象：
  - 在位置上采样——照片
  - 在时间上采样——视频

#### 采样的瑕疵（Artifacts）

Artifacts(Erros/ Mistakes/ Inaccuracies)

- 走样的表现形式
  - 锯齿
  - 摩尔纹
  - 车轮效应
  - ...

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517204650646.png" alt="image-20230517204650646" style="zoom:33%;" /> <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517204710999.png" alt="image-20230517204710999" style="zoom:33%;" /><img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517204724152.png" alt="image-20230517204724152" style="zoom:30%;" />

#### 走样的原因

- 信号频率太快（变化太快），而采样频率太慢

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517205011269.png" alt="image-20230517205011269" style="zoom: 67%;" />

#### 反走样的大致方向

- 在采样前进行模糊（预滤波）

![image-20230517205202510](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517205202510.png)

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517204650646.png" alt="image-20230517204650646" style="zoom: 45%;" /><img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517205302556.png" alt="image-20230517205302556" style="zoom:45%;" />

![image-20230517205347876](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517205347876.png)

- 注意顺序，必须在采样前模糊才能达到反走样(Antialiasing)的效果，采样后模糊只能模糊锯齿，并不能达到反走样效果

![image-20230517205732868](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517205732868.png)

### 频域分析

#### 频域的定义

- 频域(Frequency Domain)：
  - 是描述信号在频率方面特性时用到的一种坐标系。
    自变量是频率，即**横轴是频率，纵轴是该频率信号的幅度**，也就是通常说的频谱图

#### 傅里叶变换

- 任何周期函数都可以用正弦函数和余弦函数表示

![image-20230517214030430](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517214030430.png)

- 作用：把信号分解成频率，即从时域转换到频域

![image-20230517214415552](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517214415552.png)

#### 滤波

- 高通滤波
  - 高频信息表示边界，信号变化较大的地方需要高频率的正弦波模拟

![image-20230517214953131](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517214953131.png)

- 低通滤波
  - 低频信号表示大面积色块，也是照片的主要信息

#### 卷积

##### 卷积的定义

- 在图形学上，卷积简单的认为就是滤波，就是平均，在原始信号的任意一个位置，取其周围平均

  ![image-20230517215513406](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517215513406.png)

##### 卷积定理

- 时域卷积 = 频域相乘 （反过来一样）
- 卷积的两中方式：
  - 图和滤波器直接在时域上进行卷积
  - 先进行傅里叶变换到频域上，在相乘，最后逆变换回时域

![image-20230517215811423](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517215811423.png)

##### 卷积盒

- 卷积盒即低通滤波器

​                      <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517215835312.png" alt="image-20230517215835312" style="zoom:67%;" /> 

- 左为时域，右为频域

​                         <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517215906514.png" alt="image-20230517215906514" style="zoom:50%;" /> 

- 更大的卷积盒，意味着更低的低通滤波
  - 原因是，卷积盒的尺寸越大，模糊的程度越高

​                       <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517220011941.png" alt="image-20230517220011941" style="zoom:50%;" /> 

#### 采样与频率

- 采样就是才频域上的重复

![image-20230517220206544](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517220206544.png)

### 反走样

#### 走样的本质：

- 采样不够快(时域) -> 中间间隔小(频域) -> 信号在频域上的卷积，发生重叠

​                                 <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517220447845.png" alt="image-20230517220447845" style="zoom:67%;" /> 

#### 反走样的方法

- 设备上的方法（不算反走样）：增加采样率（增加分辨率） -> 像素点更多更小 -> 采样频率高 -> 频谱间隔大
- 反走样：先模糊 -> 再采样

![image-20230517205202510](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517205202510.png)

![image-20230517222842860](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517222842860.png)

- 反走样的解释：就是通过低通滤波截去高频信息，从而使剩下的低频信号不会发生重叠

![image-20230517223011927](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517223011927.png)

#### MSAA（Multi Sampling Anti-Aliasing）

- 多重采样：
  - 把一个像素分成更小的像素，每个像素也有各自中心点，再通过小像素在三角形的中的个数得到覆盖率，算出像素对应的颜色。实际上解决的就是对图像进行模糊的这个操作，通过增加像素点进行平均一个像素在与不在三角形内的部分。

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517223722857.png" alt="image-20230517223722857" style="zoom:33%;" /> <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517223736286.png" alt="image-20230517223736286" style="zoom:33%;" /><img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517223758224.png" alt="image-20230517223758224" style="zoom:33%;" />

![image-20230517223844461](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517223844461.png) 

#### FXAA（Fast Approximate Anti-Aliasing）

- 快速近似抗锯齿：
  - 与采样无关，在图像层面上进行处理，处理过程是先找到三角形的边界，把有锯齿的边界替换从没有锯齿的边界，处理起来非常快。

#### TAA（Temporal Anti-Aliasing）

- 时间抗锯齿
  - 非常快速，将静态的图片在时间上进行采样，相连两帧的图像相同，但是利用同一个像素不同位置来感知是否在三角形内，计算时考虑前一帧的结果，相当于MSAA中多计算的点，分布在时间上，每一帧没有额外操作。

#### DLSS（Deep Learning Super Sampling）

- 深度学习超级采样
  - 依赖深度学习，使用低分辨率的图像生成高分辨率的图像，在把高分辨律的图像缩回去，得到抗锯齿图像。

### 可见度/遮挡

#### 油画家算法（Painter's Algorithm）

- 先排序好物体的前后顺序，在由后往前光栅化，后光栅化的物体会覆盖前面的物体，从而实现遮挡效果

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230518192902246.png" alt="image-20230518192902246" style="zoom:67%;" />

- 但是当遇到前后关系复杂的物体则难以处理

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230518192938331.png" alt="image-20230518192938331" style="zoom:50%;" />

#### 深度缓冲（Z-Buffer）

- 在画家算法中，深度信息以物体为单位，就会遇到复杂深度的问题；深度缓冲则把深度信息以像素为单位，这个算法也得到了广泛运用
- 核心思想：每次采样都要把当前三角面的点的Z值与Z-buffer中的Z值作比较，根据远近判断是否被遮挡，从而决定是否改变当前像素的颜色
- 算法步骤：
  - Z-Buffer算法需要维持一个深度数组记为zbuffer，对应每一个像素位置，其每个位置初始值置为无穷大（即离摄像机无穷远）。
  -  随后我们遍历每个三角形面上的每一个像素点[x,y]，如果该像素点的深度值z，小于zbuffer[x,y]中的值，则更新zbuffer[x,y]值为该点深度值z，并同时更新该像素点[x,y]的颜色为该三角形面上的该点的颜色。

- 伪代码如下：

```c++
for(each triangle T){
	for(each sample(x,y,z) in T) {
		if(z < z_buffer[x,y]) {
			frame_buffer[x,y] = rgb;
			z_buffer[x,y] = z;
		}
	}
}
```

![image-20230518194129378](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230518194129378.png)

## 七、着色（光照与基本着色模型）

### 着色之前

- 模型变换 -> 视图变换 -> 投影变换 -> 视口变换 -> 光栅化

![image-20230518194520206](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230518194520206.png)

### 着色

- 为物体上色，在图形学中也就是对物体应用**材质**的过程

#### 简单光照模型（BPR）

##### 定义

- Blinn-Phong Reflectance Model
- 人看到物体，即人眼接收到了来自物体反射的光，物体反射的光分为三个：
  - 高光（Specular）
  - 漫反射光（Diffuse）
  - 环境光（Ambient）

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230518195328372.png" alt="image-20230518195328372" style="zoom:67%;" />

##### 规定

- 观测方向 $\vec{v}$
- 物体表面法线 $\vec{n}$
- 光照方向 $\vec{l}$
- 表面参数（颜色，shininess等）
- 暂时只考虑局部这个点，不考虑其他物体（不考虑阴影）

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230518195652101.png" alt="image-20230518195652101" style="zoom:67%;" />

##### 环境光（Ambient lighting）

- 泛光反射即环境光，这里做出假设：任何一个点接受环境光相同。
- 可以得到理想经验模型:

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520145217177.png" alt="image-20230520145217177" style="zoom:67%;" />

- 即环境光是一个常数

![image-20230520145429115](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520145429115.png)

##### 漫反射（Diffuse Reflection）

- 漫反射使物体有体积感（因为考虑了表面法线）
- 漫反射是光从一定角度入射后，从入射点向四面八方反射，且每个方向反射的光强度相等（假设）

![image-20230518195926848](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230518195926848.png)

- 两个理论

  - Lambert余弦定理：光照强度取决于 $\vec{l}$ 和 $\vec{n}$ 的夹角，所以应该将光强乘以$cos\theta$（$cos\theta = l*n$）

    <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230518200345199.png" alt="image-20230518200345199" style="zoom:67%;" /> 

  - 能量衰退定理：点光源，半径越大，能量越小，即光强与$r^2$成反比

​                                <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230518200655069.png" alt="image-20230518200655069" style="zoom:50%;" /> 

- 得到Lambert漫反射模型 ：$L_d =k_d(I/r^2)max(0,\vec{n}\cdot\vec{l})$

  ​            <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230518201016664.png" alt="image-20230518201016664" style="zoom:67%;" />

  - $k_d$是漫反射系数，表示为一个vector，即rgb通道，定义了颜色
  - $I$为入射光强，r为点光源到入射点的距离
  - $\vec{n},\vec{l}$为法向量和光入射方向
  - max剔除夹角大于90度的光

- Lambert漫反射模型为经验模型，与实际物理存在差异

![image-20230518201432481](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230518201432481.png)

##### 高光（Specular Highlights）

###### Phong反射模型

​                   <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520143310166.png" alt="image-20230520143310166" style="zoom: 50%;" /> 

$\vec{R}$为镜面反射方向

- 产生高光的原因：

  - 光滑的表面
  - 观察方向接近经面反射方向（$\vec{v}和\vec{R}$足够接近）

- 如何判断$\vec{v}和\vec{R}$是否接近 ： $cos\alpha = \vec{v}\cdot \vec{R}$

- 得到Phong反射模型：
  $$
  L_s=k_s(I/r^2)max(0,cos\alpha)^p=k_s(I/r^2)max(0,\vec{v}\cdot \vec{R})^p
  $$

- $k_s$为镜面反射系数，经验模型简化忽略能量损失，$I$为入射光强，$r$为光源到入射点的距离，$max$剔除负数

  - 指数p的作用：控制反射光的大小，一般为100-200

![image-20230520144130693](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520144130693.png)

​            <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520144153271.png" alt="image-20230520144153271" style="zoom:67%;" /> 

- 但是，Phong模型中的 $\vec{R}$ 不方便计算，于是改进得到了Blinn-Phong反射模型

###### Blinn-Phong反射模型

​                    <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520144514422.png" alt="image-20230520144514422" style="zoom: 50%;" /><img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520143310166.png" alt="image-20230520143310166" style="zoom: 50%;" /> 

- 核心思想：引入半程向量$\vec{h}$，当 $\vec{h}和\vec{n}$ 接近时，即 $ \vec{v}和\vec{R}$ 接近

$$
\begin{align}
\hat{h}&=bisector(\hat{v},\hat{l})\\
&=\frac{\hat{v}+\hat{l}}{||\hat{v}+\hat{l}||}
\end{align}
$$

- 得到：

$$
L_s=k_s(I/r^2)max(0,\vec{v}\cdot \vec{R})^p
$$

![image-20230520145102961](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520145102961.png)



## 八、着色（着色频率、图形管线、纹理樱色）

### 着色频率

- 即以什么为单位进行着色
- 对应分别在平面、顶点、像素进行着色的效果

![image-20230520150129355](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520150129355.png)

#### 平面着色（flat shading）

- 对每个三角形平面进行着色，即法线为平面的法线
- 计算快，但效果不好
- 法线定义：两条边叉乘得到

![image-20230520150502048](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520150502048.png)

#### 顶点着色（Gouraud Shading）

- 对每个顶点进行着色，即法线为顶点的法线，三角形内部着色通过插值

- 法线定义：顶点的法线为相邻面法线的（加权面积）平均

![image-20230520151113326](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520151113326.png)

![image-20230520150803046](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520150803046.png)

#### 像素着色（Phong Shading）

- 对每个像素进行着色，即法线为每个像素对应的法线
- 法线定义：像素法线通过顶点法线插值得到

![image-20230520151142945](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520151142945.png)

![image-20230520151014099](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520151014099.png)

#### 对比

![image-20230520151039149](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520151039149.png)

### 渲染管线

- 图形渲染管线 Graphics(Real-time Rendering) Pipeline

![image-20230520151255116](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520151255116.png)

#### 顶点处理

![image-20230520151411353](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520151411353.png)

- 顶点处理的作用是对所有顶点数据进行MVP变换，最终得到投影到二维平面的坐标信息（同时为了Zbuffer保留深度z值）。超出观察空间的会被剪裁掉。

#### 三角形阶段

- 将所有顶点按照原有的几何信息，处理成三角面，每个面由三个顶点组成

#### 光栅化

![image-20230520151536363](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520151536363.png)

- 得到前面的三角形后，对三角形进行光栅化

#### 片元阶段

![image-20230520151751151](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520151751151.png)

- 光栅化得到了三角形的哪些点可以被显示，也就是将顶点数据转化成立片元（像素）（片元可能比像素更小，比如MSAA操作后的采样点），片元阶段将进行着色，这一阶段需要进行深度测试。
- 着色不仅在片元阶段，顶点阶段也有涉及，比如顶点着色

![image-20230520153017656](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520153017656.png)

- 着色还需要进行的就是**纹理映射**（Texture Mapping）

![image-20230520153052882](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520153052882.png)

#### Framebuffer处理

- 最后得到像素颜色信息输送给显示设备加以显示，即完成了图形渲染管线

### Shader 和 GPU

#### Shader

- 能在硬件上执行的语言
- 每个顶点/像素执行一次（不需要写for循环）
- 顶点操作 -> 顶点着色器（Vertex Shader）
- 片元操作 -> 片元着色器（Fragment Shader）

![image-20230520153605033](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520153605033.png) 

#### GPU（Graphics Processing Unit）

- 独显/核显

- gpu可以理解成高度并行化的处理器（核心数量理解为并行线程的数量）
- gpu并行度惊人，远超过cpu

### 纹理映射（Texture Mapping）

- 纹理可以定义任意一个点的不同属性，让每一个点可以显示自己的颜色

![image-20230520153847658](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520153847658.png)

#### 纹理（Texture）

- 表面是2D的，任何三维物体的表面都可以映射到一个2D图像（Texture）

![image-20230520154004202](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520154004202.png)

- 如果拥有这个映射关系，则可以把每一个对应点的颜色信息（漫反射系数）存储在2D的Texturn上，每次光照模型计算时，查找对应的颜色系数，即可实现不同点不同颜色，即把Texture贴在模型上

![image-20230520154200632](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520154200632.png)

#### 纹理坐标（u,v）

- 表示Texture的映射关系，(u,v)范围一般为(0,1)

![image-20230520154312705](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520154312705.png)

- 即计算出模型上每一个点对应的UV坐标，即可到Texture查询漫反射系数

​             <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520154458912.png" alt="image-20230520154458912" style="zoom:50%;" /><img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520154507339.png" alt="image-20230520154507339" style="zoom:50%;" />

#### 特殊纹理 tile

- 具有上下左右重复连续，无缝拼接的特性

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520154611826.png" alt="image-20230520154611826" style="zoom:50%;" /><img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520154507339.png" alt="image-20230520154507339" style="zoom:50%;" />



## 九、着色（插值、高级纹理映射）

### 插值

#### 重心坐标

- 定义
  - 三角形ABC平面中任意一点(x,y)都可以写成这三个点的线性组合形式，即：$(x,y)=\alpha A+\beta B+\gamma C$
  - 其中$\alpha +\beta+\gamma = 1$，这种形式的 $(\alpha,\beta,\gamma)$ 称为 $(x,y)$ 的重心坐标
  - 例：A的重心坐标为 $(1,0,0)$

​                               <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520181129448.png" alt="image-20230520181129448" style="zoom:50%;" /> 

- 几何定义：利用面积比求重心坐标

​                        <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520181317999.png" alt="image-20230520181317999" style="zoom:50%;" /> 

- 重心坐标公式：

​            <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520181412894.png" alt="image-20230520181412894" style="zoom:67%;" /> 

#### 插值

- 插值的作用
  - 知道三个顶点的属性，可以得到内部任意一点的平滑过渡
  - 如纹理坐标、颜色、法向量等等
- 插值的方法
  - 利用重心坐标进行插值

​                        <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520181655075.png" alt="image-20230520181655075" style="zoom:67%;" /> 

- V 可替换成任意需要插值的属性
- 但是重心坐标不能保证在投影变换中保持不变，需要在三维中找到重心坐标进行插值，而不能在投影后进行插值

#### 透视矫正插值

![img](https://pic1.zhimg.com/80/v2-a84f9a4ee21dbc176605d96975efe508_720w.webp)

- 由上图可得，在投影变换后，c通过插值得到的intensity为0.5，然而在实际它并不等于0.5，因此重心坐标同理不能保证在投影变换中保持不变，而考虑到这种情况的插值方法即**透视矫正插值**

- [透视矫正插值](https://zhuanlan.zhihu.com/p/144331875)
- [图形学 - 关于透视矫正插值那些事](https://zhuanlan.zhihu.com/p/403259571)

### 纹理

#### 纹理过小

- 纹理过小会导致失真，也就是走样
  - 屏幕空间上好几个像素对应的UV坐标都是同一个，映射的都是同一个uv坐标的属性
  - 所以如下图红点，不能简单的取某个uv点作为映射值

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520200424748.png" alt="image-20230520200424748" style="zoom:67%;" />

##### 双线性插值

- 缓解因纹理过小带来走样现象的方法
  - 取最近的四个点
  - 先水平做两次插值，在上下做一次插值（或者先上下两次，再左右一次）

![image-20230520200807640](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520200807640.png)

##### 双三次插值

- 取周围16个点，根据一个三次方程，求每一个点的权重，乘以颜色最后求和得到

![image-20230520202214203](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520202214203.png)

##### 对比

- 最近邻插值、双线性插值、双三次插值

![image-20230520202305080](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520202305080.png)

#### 纹理过大

- 纹理过大时，一个像素通过映射对应一大块uv坐标，导致采样频率不足，出现摩尔纹和锯齿的走样现象

![image-20230520202605745](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520202605745.png)

![image-20230520211150940](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520211150940.png)

- footprint：一个像素大小映射到纹理坐标的大小
- 由进到远，同样一个像素映射的纹理范围大小越来越大，导致走样现象

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520202756632.png" alt="image-20230520202756632" style="zoom:67%;" />

- 如：同样一个大小的圆圈内，对应到纹理坐标中的空间大小不同

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520202912218.png" alt="image-20230520202912218" style="zoom: 80%;" />

##### 超采样（Super Sampling）

- 类似抗锯齿的思想，把一个像素分为更小的sample，提高采样频率，从而解决走样问题，**但是开销太大**

​                            ![image-20230520203119116](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520203119116.png) 

##### Mipmap

![image-20230520204129986](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520204129986.png)

- Mipmap核心思想：
  - 提前生成不同level的mipmap
  - 根据远近footprint的不同，查询对应level的mipmap
- level
  - level 0代表的是原始texture，也是精度最高的纹理，随着level的提升，每提升一级将4个相邻像素点求均值合为一个像素点，因此越高的level也就代表了更大的footprint的区域查询。
  - level的层数：$log_2n$
  - 需要的存储空间（与原来的做比较）：$\frac{4}{3}$
- Mipmap的特点：快（离线查询）、近视（并不是准确值）、**只能查询正方形**

- 查询方法：

  - 利用屏幕空间相邻像素点对应的uv，估算footprint大小确定level等级，使用对应的mipma

    ​              <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520205031188.png" alt="image-20230520205031188" style="zoom:67%;" /> 

  - 在屏幕空间取当前像素点的右方和上方的相邻像素点（四个都取也行），分别查询得到对应UV坐标，计算当前像素点与右方像素点和上方像素点的UV距离。

    ​              <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520205345399.png" alt="image-20230520205345399" style="zoom:67%;" /> 

  - 两个中取最大值，作为正方形的边长，再求得level ：$D=log_2L$

    ​             <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520205456155.png" alt="image-20230520205456155" style="zoom:67%;" /> 

  - 这样得到的D不是整数，若是四舍五入求得最近邻的D值，则会出现过渡突兀的边界 

  - 所以选择使用**三线性插值**，再D level与D+1 level进行一次线性插值

  - 即：在D level的纹理中进行一次双线性插值得到一个颜色属性，在D+1 level的纹理中进行一次双线性插值得到第二个颜色属性，对这两个颜色属性在进行一次线性插值得到最终的颜色属性

- 对比：

  <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520210325429.png" alt="image-20230520210325429" style="zoom: 50%;" /><img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520210346033.png" alt="image-20230520210346033" style="zoom:47%;" /><img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520210359389.png" alt="image-20230520210359389" style="zoom:50%;" />

##### 各向异性滤波Ripmaps

- 由于Mipmap只能预处理正方形区域的均值，这会导致远处过度模糊

- 真实情况屏幕空间上的像素对应纹理空间中的footprint不一定是正方形

  ![image-20230520210703531](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520210703531.png)

- 解决方法：各向异性过滤

  - 即不仅生成等比例的缩小纹理，也生成长方形缩小纹理

    ​                                  ![image-20230520210842506](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520210842506.png)

  - 允许对长条区域进行范围查询，但斜着区域和Mipmap一样

  - 空间开销是原来的三倍

  - 各向异性nX指的是压缩几倍，也就是从左上到右下有几层，这个并不会对硬件（帧数造成多大的影响），只要开七各向异性使用的计算量相差不大，只是对显存要求不同，但是最大也不会超过原先三倍

##### EWA过滤

- 把任意不规则的形状拆分成很多个不同的圆形，去覆盖这个形状，然后多次查询进行插值，但是耗时大

![image-20230520211831351](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520211831351.png)

#### 纹理应用

- 纹理的理解：Texture = 一块内存上的数据，不必只存储颜色，也可以是高度、法线等

##### 环境光映射（Environment Map）

- 将环境光存储在一个贴图上。想象这样一个情形，光照离我们的物体的距离十分遥远，因此对于物体上的各个点光照方向几乎没有区别，那么唯一的变量就是人眼所观察的方向了，因此各个方向的光源就可以用一个球体进行存储,即**任意一个3D方向**，都**标志着一个texel**

![image-20230521154411435](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521154411435.png)

![image-20230521154433648](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521154433648.png)

- 未看到物体时，根据视线直接映射到的uv查询对应的环境光
- 或者看到镜面，通过视线与法线的反射方向查询对应的环境光

![image-20230521154701222](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521154701222.png)

- 但是用一个球体来存储环境光有一个比较明显的缺点，仔细观察上文当中展开的那副Texture图可以观察看到，**上方和下方均有较为严重的扭曲**，因此另外一种存储的方法就是Cube Map，也就是天空盒:

![image-20230521154802456](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521154802456.png)

![image-20230521154743214](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521154743214.png)



##### 凹凸映射（Bump Mapping）

- 记录逻辑上的相对高度（可为负值），表现物体表面凹凸不平的性质
- 对每一个pixel做一个“扰动”
- 利用高度差重新计算法线

- 二维计算方法：

  - 原平面的法线为(0,1)
  - 凹凸映射中在该点的 梯度(微分) 为 $dp = c\cdot [h(p+1)-h(P)]$
  - 则实际得到的法线为：$(-dp,1).normalized()$

  ​         ![image-20230521161211373](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521161211373.png) 

- 三维计算方法：

  - 原平面的法线为(0,0,1)
  - 凹凸映射中的梯度微分：
    - $du = c1\cdot [h(u+1)-h(u)] $
    - $dv = c2\cdot [h(v+1)-h(v)] $
  - 则实际得到的法线为$(-du,-dv,1).normalized()$

- 但是要注意以上都以原法线在（0，0，1）为标准，即在切线空间中计算，即最后结果需要左乘(t,b,n)。

##### 法线贴图（Normal Maps）

- 凹凸映射只是记录相对高度，从而计算出对应的法线，而法线贴图则是直接记录法线
- 可以使用低精度模型，套用高精度模型的法线信息，使渲染效果提升

![img](https://pic2.zhimg.com/80/v2-22ea9aecaf66e4ac7c1579a944c55d79_720w.webp)

- 若直接存储模型空间中的法线坐标，好处是可以转换到世界坐标直接使用，但是一旦模型进行了不等比缩放（如各种动画），则法线将不再垂直表面。

![img](https://img-blog.csdnimg.cn/20190702113455414.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xsc2Fuc3Vu,size_16,color_FFFFFF,t_70)

- 所以解决方法是，存储切线空间中的法向量坐标。
- 切线空间：
  - Z轴由原来的法线n构成，X轴为切线tangent，Y轴由法线叉乘切线得到

##### 位移贴图（Displacement Maps）

- 与Bump Mapping类似，但是记录的相对高度会实际改变三角形顶点的位置，而凹凸映射并不会改变实际几何形状
- 代价：要求模型精度高，有足够的点来改变
  - DirectX中的动态曲面细分：先用粗糙的三角形，在应用Texture时，检测是否需要细化三角形

![image-20230521162731460](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521162731460.png)

##### 三维纹理

- 定义空间内任意一个点的纹理

![image-20230521171745013](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521171745013.png)

##### 阴影纹理（Shadow Maps）

- 即计算好阴影信息，写在Texture中

![image-20230521171914092](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521171914092.png)

- 在光栅化阶段中，我们知道shading无法解决物体之间的阴影关系，而阴影贴图可以一定程度上解决这个问题

- 阴影的本质 -> 光源照射不到，即光源“看不到”的地方

- 阴影贴图的原理：

  - 把光源作为摄像机去看，将整个场景渲染一遍，从而得到光源视角的深度缓冲 Z-buffer（这个Z buffer实际上就是shadow maps）

    ​                   <img src="https://pic4.zhimg.com/v2-f5886aa8095af1e5299086829521685f.webp" alt="img" style="zoom:50%;" /> 

  - 转到真正的摄像机对场景进行渲染

    ​               <img src="https://pic3.zhimg.com/v2-e0019ee4acef3077ef76fc458abe1206.webp" alt="img" style="zoom:50%;" /> 

  - 对摄像机视角下的每一个点，转换到光源视角下，与光源得到的深度缓冲做比较，若相等则可以照亮，即不在阴影中，若深度值（若负数则是小于）大于深度缓冲中的值，则意味着物体前面（光源视角）由东西挡着，即在阴影中

    ​                      <img src="https://pic1.zhimg.com/v2-b3487114affa81587ca0edc1350f3b94.webp" alt="img" style="zoom:50%;" /> 

  - 这样得到了每个点是否在阴影中，若在则不计算Blinn-Phong模型中的高光和漫反射。效果如下：

    ![img](https://pic1.zhimg.com/80/v2-944b0bd40587eb9586cefcb83de35060_720w.webp) 

  - 对应可视化的深度缓冲，距离光源越近越黑

    ![img](https://pic3.zhimg.com/80/v2-5dcc99e0f3e043355f8dde360747b3ee_720w.webp) 

- 这样得到的阴影有几个细节：
  - 浮点数难以判断相等，所以一般会有一个tolerance
  - shadow maps查询时**不采用双线性插值**，只寻找最近的点，因为倘若插值发生在物体边缘时，与邻接点的深度差距很大，会导致插值结果会有很大的误差
  - 属于硬阴影，只适用于点光源

## 十、几何（基本表示方式）

### 表示方式

#### 隐式曲面特点

- 隐式曲面不会告诉你任何点的信息，只会说明曲面上的每个点满足的关系：$f(x,y,z)=0$
- 好处：很容易判断点是否在曲面上
- 坏处：无法直接看出表示的曲面是什么

![image-20230521181917197](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521181917197.png)

#### 显示曲面特点

- 对于显式曲面来说是与隐式曲面相对应的，所有曲面的点被**直接给出**，或者可以**通过映射关系直接得到**

​                       <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521182045612.png" alt="image-20230521182045612" style="zoom:67%;" /> 

- 没有直接给出点的数据x，y，z，但是拥有u，v的取值范围以及从$(u,v)\rightarrow (x,y,z)$的映射关系，那么只需要将所有的u，v代入自然就可求得x，y，z。
- 优缺点与隐式曲面恰好相反

#### 区别

- 区别隐式曲面与显示曲面的关键就在于**是否可以直接表示出所有的点**

### 隐式几何

#### 代数几何

- 通过代数表达式获得的曲面

- 难以表示复杂的实际图形

![image-20230521182355723](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521182355723.png)

#### 构造固体几何

- Constructive Solid Geometry （CSG）
- 指的是对不同的几何做布尔运算，如并、交、差

![image-20230521182531245](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521182531245.png)

#### 距离函数

- 符号距离函数（Signed Distance Function）
- 描述一个点到物体表面的**最短距离**，外表面为正，内表面为负。
- 可以通过距离函数来得到几何形体混合的效果，如下图:

​     ![image-20230521182806910](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521182806910.png) 

- 对于符号距离函数来说本质上就是一种**定义距离的函数**。如有空间任意一点到各个几何物体表面的距离，对这些距离**做各种各样的运算操作**最后得到的一个函数就是最终的距离函数了，举一个简单的例子:
  - 对于这样一个二维平面的例子，定义空间中每一个点的SDF为该点到阴影区域右边界的垂直距离，在阴影内部为负，外部为正，因此对于A和B两种阴影来说的SDF分别如上图下半部分所示。
  - 有了SDF(A),SDF(B)之后对这两个距离函数选择性的**做一些运算**得到最终的距离函数，这里采用最简单的SDF = SDF(A)+SDF(B)来举例
  - 最终得到的SDF为零的点的集合即为blend之后曲面，对该例子来说，就是两道阴影之间中点的一条线:

![image-20230521182929067](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521182929067.png)

#### 水平集

- 水平集 Level Set

- 水平集的方法其实与SDF很像(像是SDF的一种特殊形式)，也是**找出函数值为0的地方作为曲线**，但不像SDF会空间中的每一个点有一种严格的数学定义，而是**对空间用一个个格子去近似一个函数**，如下:

![image-20230521183155106](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521183155106.png)

- 对该面内的每一个点利用已经定义好的格子值**进行双线性插值**就可以得到任意一点的函数值，**找出所有=0的点作为曲面**。

- 该方法的好处是对于SDF，我们可以更加**显示的**区空间曲线的形状。该方法广泛的运用在医学成像和物理模拟之中:

![image-20230521183312416](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521183312416.png)

![image-20230521183321296](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521183321296.png)

#### 分型几何

- 分型几何 Fractals

- 分型几何是指许许多多**自相似的形体**最终所组成的几何形状。
- 如雪花是一个六边形，放大之后会发现每一个边上又是一个六边形，再放大六边形边上的六边形边上又是六边形，就这样无限套娃，有点递归的意思。

![image-20230521183453947](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230521183453947.png)

### xian'shi
