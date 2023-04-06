# 《Unity Shader入门精要》学习笔记

> Started	2022.11.29
>
> Author	HuangZhibin9

## 第一章 欢迎来到Shader的世界

Shader更多地是面向GPU的工作方式，所以它的一些语法对我们来说不是那么直观。



## 第二章 渲染流水线

![渲染流水线](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/%E6%B8%B2%E6%9F%93%E6%B5%81%E6%B0%B4%E7%BA%BF.png)

渲染流水线的最终目的在于生成或者说是渲染一张二维纹理，即我们在电脑屏幕上看到的所有效果

### 2.1 综述

#### 2.1.1 什么是渲染流水线

渲染流水线的工作任务在于由一个三维场景出发、生成（或者说渲染）一张二维图像。

概念性阶段：**应用阶段（Application Stage）、几何阶段（Geometry Stage）、光栅化阶段（Rasterizer Stage）**

![image-20221129092847099](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221129092847099.png)

##### 应用阶段：

1. 这个阶段**由开发者主导**，由CPU负责实现。
2. 开发者有三个主要任务：
   1. 首先，**准备好场景数据**，如摄影机的位置、视锥体、模型、光源等。
   2. 其次，为提高渲染性能，往往需要**做一个粗粒度剔除（culling）工作**，把不可见的物体剔除出去。
   3. 最后，**设置好每个模型的渲染状态**，包括但不限于材质（漫反射颜色、高光反射颜色）、使用的纹理 、Shader等
3. 这一阶段最重要的输出是**渲染所需的几何信息**，即**渲染图元**。渲染图元可以是点、线、三角面等。这些渲染图元将会被传递给下一个阶段——几何阶段。

##### 几何阶段：

1. 几何阶段用于**处理所有和我们要绘制的几何相关的事情**。例如，决定需要绘制的图元是什么，怎样绘制，在哪里绘制。通常在GPU上进行。
2. 几何阶段负责和每个渲染图元打交道，进行**逐点、逐多边形**的操作。
3. 几何阶段的一个重要任务就是**把顶点坐标变换到屏幕空间中**，再交给光栅器进行处理。
4. 这一阶段输出的是**屏幕空间的二维顶点坐标**、每个顶点对应的**深度值、着色**等相关信息，并传递给光栅化阶段。

##### 光栅化阶段：

1. 这一阶段将会使用几何阶段传来的数据来产生屏幕上的像素，并渲染出最终图像
2. 光栅化的主要任务是决定每个渲染图元中的哪个像素应该被绘制再屏幕上。

### 2.2 CPU和GPU之间的通信

1. 渲染流水线的**起点是CPU**，即**应用阶段**。
2. 应用阶段可分为下面3个阶段：
   1. 把数据加载到**显存**中。
   2. 设置渲染状态。
   3. 调用`Draw Call`。

#### 2.2.1 把数据加载到显存中

1. 所有**渲染所需要的数据**，都需要从**硬盘**加载到**内存**中，然后**网格和纹理等数据**又被加载到**显存**中。
   1. 显存对显卡的方法**速度更快**。
   2. 大多数显卡没对直接访问RAM的**权限**。
2. 真实渲染中需要加载到显存中的数据复杂的多，如顶点的位置信息、法线方向、顶点颜色、纹理坐标等。
3. 一般来说，数据加载到显存之后，RAM中的数据就可以移除了，但有些时候，CPU仍需要访问它们（例如，我们希望CPU访问网格数据来进行碰撞检测），那我们就不希望它被移除，因为**从硬盘加载到内存的过程非常耗时**。
4. 开发者需要**通过CPU来设置渲染状态**，从而“指导”GPU如何进行渲染工作。

#### 2.2.2 设置渲染状态

**什么是渲染状态：**

​		通俗解释，这些状态定义了场景中的网格是怎么样被渲染的。

设置完成后，调用渲染命令`Draw Call`告诉GPU可以按照设置进行渲染了。

#### 2.2.3 调用Draw Call

1. `Draw Call`就是一个命令，**发起方是CPU，接收方是GPU。**

2. 这个命令**仅仅指向一个需要被渲染的图元列表**，而不包含材质信息（因为这个在上一个状态已经设置好了）。

3. 当给定一个`Draw Call`时，GPU就会**根据渲染状态和所有输入的顶点数据**进行计算，**最终输出成屏幕上显示的像素**。（这个计算过程就是下面的GPU流水线）。

   ![image-20221129162402791](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221129162402791.png)

### 2.3 GPU流水线

#### 2.3.1 概述

 对于几何阶段和光栅化阶段，开发者无法拥有绝对的控制权，但GPU也向开发者开放了许多权限。

![image-20221129164355113](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221129164355113.png)

##### 几何阶段

1. **顶点着色器**(`Vertex Shader`)是**完全可编程**的，通常用于实现**顶点的空间变换、顶点着色**等。
2. **曲面细分着色器**(`Tessellation Shader`)是一个**可选**的着色器，用于**细分图元**。
3. **几何着色器**(`Geometry Shader`)是一个**可选**的着色器，用于执行**逐图元的着色操作**，或者用于**产生更多的图元**。
4. **裁剪**(`Clipping`)是可配置的，用于将那些**不在摄像机视野内的顶点裁剪掉**，并**剔除掉某些三角图元的面片**。
5. **屏幕映射**(`Screen Mapping`)是**不可配置和不可编程**的，负责把每个图元的坐标转**换到屏幕坐标系**中。

##### 光栅化阶段

1. **三角形设置**(`Triangle Setup`)和**三角形遍历**(`triangle Traversal`)都是**固定函数**的阶段。
2. **片元着色器**(`Fragment Shader`)是**完全可编程**的，负责实现**逐片元的着色操作。**
3. **逐片元操作**(`Per-Fragment Operations`)是不可编程的，但是具有**很高的可配置性**，负责很多重要操作，如**修改颜色、深度缓冲、进行混合**等。

#### 2.3.2 顶点着色器（Vertex Shader）

1. 输入来自CPU的顶点数据，**处理单位是顶点**，**输入进来的每个顶点都会调用一次**顶点着色器。但是顶点着色器本身**不可以创建或者删除顶点，也无法获得顶点之间的关系。**

2. 顶点着色器需要完成的工作：

   1. **坐标变换**：把顶点坐标从模型空间转换到齐次裁剪空间,最终得到归一化的设备坐标（Normalized Device Coordinates,NDC）。

      ```c#
      o.pos = mul(UNITY_MVP,V.position);
      o.pos = UnityObjectToClipPos(v.vertex);
      ```
   
   2. **逐顶点光照**

#### 2.3.3 裁剪

1. 一个图元和摄像机视野的关系有三种：
   1. 完全在视野内的
   2. 部分在视野内的
   3. 完全在视野外的
2. 完全在视野内的图元，就继续传递给下一个流水线阶段，完全在视野外的图元就不会继续传递，而部分在视野内的就需要进行一些处理——裁剪。

![image-20221130084244786](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221130084244786.png)

#### 2.3.4 屏幕映射（Screen Mapping）

1. 屏幕映射（Screen Mapping）的任务是把每个图元的x和y坐标转换到屏幕坐标系（Screen Coordinates）下。

2. 屏幕坐标系OpenGL和DirectX的差异。

   ![image-20221130084817958](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221130084817958.png)

#### 2.3.5 三角形设置（Triangle Setup）。

1. 光栅化的第一个流水线阶段就是**三角形设置（Triangle Setup）。**
2. 上一个阶段输出的是三角网格的顶点数据，即我们得到的是三角网格每条边的两个端点。但如果要得到整个三角网格对像素的覆盖情况，我们就必须计算每条边上的像素坐标。
3. 这样一个**计算三角网格表示数据的过程**就是三角形设置。它的输出是为了下一个阶段做准备。

#### 2.3.6 三角形遍历（Triangle Traversal）                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             

1. 这一阶段会检查每个像素**是否被一个三角网格覆盖**，如果覆盖的话就会，就会生成一个**片元（fragment）**。
2. 找到哪些像素被三角网格覆盖的过程就是三角形遍历，也被称为**扫描变化（Scan Conversion）**。

#### 2.3.7 片元着色器（Fragment Shader）

1. **片元着色器（Fragment Shader）**是另一个非常重要的可编程着色器阶段，在DirectX中也叫**像素着色器（Pixel Shader）。**
2. **片元着色器**的输入是根据那些从**顶点着色器输出的数据**插值得到的，**它的输出是一个或多个颜色值。**

#### 2.3.8 逐片元操作（Per-Fragment Operations）

1. 在DirectX中也叫做输出合并阶段（Output-Merger）。
2. 这一阶段的主要任务：
   1. 决定每个片元的可见性。这涉及了很多测试工作，如深度测试、模板测试等
   2. 如果一个片元通过了所有测试，就需要把这个片元的颜色值和已经储存在颜色缓冲区中的颜色进行合并，或者说是混合。

![image-20221130091630368](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221130091630368.png)

![image-20221130091727154](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221130091727154.png)

## 第三章 Unity Shader 基础

### 3.1 Unity Shader 概述

#### 3.1.1 材质和Unity Shader

1. 在Unity中我们需要配合使用材质（Material）和Unity Shader 才能达到需要的效果。
2. 常见流程：
   1. 创建一个材质
   2. 创建一个Unity Shader ，并把它赋给上一步的材质
   3. 把材质赋给要渲染的对象
   4. 在材质面板调整Unity Shader 的属性

### 3.2 Unity Shader 的基础：ShaderLab

unity提供役种专门为Unity Shader 服务的语言——ShaderLab

### 3.3 Unity Shader 的结构

#### 3.3.1 给Shader取个名字

打开Shader文件的第一行就是Shader在材质面板的路径以及名字了

```c#
Shader "Book/Chap03/NameShader"
```

#### 3.3.2 材质和 Unity Shader 的桥梁：Properties

1. Properties 语义块中包含了一系列属性（property），这些属性会在材质面板中出现 。

2. 语义块的定义如下：

   ```c#
   Properties {
       Name ("display name", propertyType) = DefualtValue;
       Name ("display name", propertyType) = DefualtValue;
   }
   ```

   ![image-20221201153550646](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221201153550646.png)

   `Int`、`Float`、`Range`这些数字类型的属性，默认值就是一个单独的数字；

   对于`Color`和`Vector`这类属性，默认值是一个由圆括号包围的四维向量；

   对于`2D`、`Cube`、`3D`这三种纹理类型，默认值是通过一个字符串后跟一个花括号来指定的，其中，字符串要么是空，要么是内置的纹理名称，如"white" "black" "gray"。花括号的用处原本是用于指定一些纹理属性的，但在 Unity 5.0 以后的版本中，这些选项被移除了，如果我们需要类似的功能,就需要自己在顶点着色器中编写计算相应纹理坐标的代码。

   例：

   ```c#
   Properties
       {
           _MainTex("Texture", 2D) = "white" {}
           //Numbers and Sliders
           _Int("Int", int) = 2
           _Float("Float",float) = 1.5
           _Range("Range",Range(0.0, 0.5)) = 3.0
           //Color and Vector
           _Color("Color",Color) = (1,1,1,1)
           _Vector("Vector",Vector) = (2,3,6,1)
           //Textures
           _2D("2D",2D) = "" {}
           _Cube("Cube",Cube) = "white" {}
           _3D("3D",3D) = "black" {}
       }
   ```

   材质面板：

   ![image-20221201155011271](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221201155011271.png)

3. 为了让Shader中可以访问到这些属性，我们还需要在CG代码中定义和这些属性类型相匹配的变量。但是即使我们没有声明属性，依然可以直接定义变量。`Properties`语义块的作用仅仅是让这些属性可以出现在材质面板中。

#### 3.3.3 Subshader

1. 每一个Unity Shader文件中可以包含多个SubShader语义块，但最少要有一个。

2. 当Unity需要加载这个Unity Shader时，会扫描所有的SubShader，然后选择**第一个**能够运行的SubShader。

   ```c#
   SubShader {
       //可选的
       [Tags]
       
       //可选的
       [RenderSetup]
       
       Pass{
           //
       }
       //Other Passes
   }
   ```

3. SubShader定义了一些列的Pass和可选的状态[RenderSetup]和标签[Tags]。

4. ShaderLab 提供了一些列渲染状态的设置指令，例如是否开启回合/深度测试。

   ![image-20221202144230810](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221202144230810.png)

5. SubShader的标签[Tags]，是一个键值对，它的键和值都是字符串类型的。

   ![image-20221202144347062](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221202144347062.png)

   **【代码相关之后详细介绍，该处只进行了解】**

## 第四章 学习Shader所需要的数学基础

### 4.1 笛卡尔坐标系

![image-20221203152851741](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221203152851741.png)

### 4.2 左手右手坐标系

![image-20221203161028880](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221203161028880.png)

- Unity中模型空间和世界空间是左手坐标系，但对于观察空间来说（以摄影机为原点的坐标系），是右手坐标系。

### 4.3 点和矢量

基本概念略

- 点积

结果为标量，常用来计算投影、和两个矢量之间的角度。

- 叉积

结果为矢量，方向分别根据左右手坐标系使用左右手定则，常用来计算垂直一个平面的矢量和用于判断三角面片的方向。

### 4.4 矩阵

基本概念略

#### 4.4.1 特殊矩阵

1. 方块矩阵
2. 单位矩阵
3. 转置矩阵
4. 逆矩阵
5. 正交矩阵
   1. 一个矩阵和它的转置矩阵的乘积是单位矩阵

### 4.5 变换

#### 4.5.1 什么是变换

- 变换（transform），指的是我们把一些数据，如点、方向矢量甚至是颜色等，通过某种方式进行转换的过程。
- 线性变换：保留矢量加和标量乘的变换，如：缩放、旋转、错切、镜像、正交投影

![image-20221203163311375](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221203163311375.png)

- 除了线性变换之外还有平移变换

- 仿射变换：合并线性变换和平移变换的变换类型，用4x4的矩阵表示。

- 四维空间，齐次坐标空间。

  ![image-20221203163545275](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221203163545275.png)

#### 4.5.2 齐次坐标

- 3x3矩阵不能表示平移操作，所以我们将其扩展到了4x4的矩阵，把原本的三维矢量转换成了四维矩阵，也就是齐次坐标。

#### 4.5.3 分解基础变换矩阵

![image-20221203163848927](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221203163848927.png)
$$
M_{3\times3}用于表示旋转和缩放，t_{3\times1}用于表示平移。
$$

#### 4.5.4 平移矩阵

- 平移矩阵与逆矩阵

![平移矩阵](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221203164138889.png)![image-20221203164704313](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221203164704313.png)

#### 4.5.5 缩放矩阵

- 缩放矩阵与逆矩阵

  ![](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221205161810903.png)![image-20221203164808839](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221203164808839.png)

#### 4.5.6 旋转矩阵

![image-20221203165029237](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221203165029237.png)![image-20221203165041268](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221203165041268.png)![image-20221203165057637](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221203165057637.png)

### 4.6 坐标空间

#### 4.6.1 坐标空间的变换

- 假设，现在有父坐标空间P以及一个子坐标空间C。我们知道在父坐标空间中子坐标空间的原点位置以及3个单位坐标轴。
- 我们一般会有两种需求:
  - 一种需求是把子坐标空间下表示的点或矢量A转换到父坐标空间下的表示A，
  - 另一个需求是反过来，即把父坐标空间下表示的点或矢量B,转换到子坐标空间下的表示Be。
- 我们可以使用下面的公式来表示这两种需求:

![image-20221205201537099](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221205201537099.png)

![image-20221205203049466](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221205203049466.png)

![image-20221205201702539](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221205201702539.png)

#### 4.6.2 模型空间

- **模型空间（model space)**，也被称为**对象空间（object space)或局部空间（local space)**，左手。
- 每个模型都有自己独立的坐标空间，当**它移动或旋转的时候，模型空间也会跟着它移动和旋转**。把我们自己当成游戏中的模型的话，当我们在办公室里移动时，我们的模型空间也在跟着移动，当我们转身时，我们本身的前后左右方向也在跟着改变。

- 自然方向，在模型空间中，我们尝试用一些方向概念，如“前(forward)”、“后(back)”、“左(left)”、“右（right)”、“上(up)”、“下(down)”。

#### 4.6.3 世界空间

- **世界空间（world space）**是一个特殊的坐标系，他建立了我们所关心的最大的空间。
- 世界空间用于描述绝对位置，使用**左手坐标系**。
- 顶点变换的第一步，就是将顶点坐标从模型空间变换到世界空间中，通常叫做**模型变换**。

模型变换例：

![image-20221205210030615](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221205210030615.png)

#### 4.6.4 观察空间

- **观察空间(view space）**也被称为**摄像机空间（camera space)**。
- 摄像机决定了我们渲染游戏所使用的视角。在观察空间中，摄像机位于原点，同样，其坐标轴的选择可以是任意的，但由于本书讨论的是以Unity 为主，而Unity 中观察空间的坐标轴选择是: +x轴指向右方，+y轴指向上方，而 +z轴指向的是摄像机的后方，即右手坐标系。
- **观察空间**和**屏幕空间**是不同的。**观察空间是一个三维空间，而屏幕空间是一个二维空间**。从观察空间到屏幕空间的转换需要经过一个操作，那就是**投影（projection)**。
- 顶点变换的第二步，就是将顶点坐标从世界空间变换到观察空间中。这个变换通常叫做**观察变换（view transform)。**

观察变换例：

![image-20221205211545208](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221205211545208.png)

## 第五章 开始Unity Shader的学习之旅

### 5.1 一个最简单的顶点/片元着色器

```c#
//shader的路径命名
Shader "Book/Chapter5/SimpleShader"{
    
    //在材质面板上显示的属性
	Properties {
        //属性名为“_Color”，在面板上的显示名称为“Color Tint”
        //类型为Color，默认值为(1.0, 1.0, 1.0, 1.0)
		_Color ("Color Tint", Color) = (1.0, 1.0, 1.0, 1.0)
	}
    
    //针对显卡A的SubShader
	SubShader{
		Pass{
            //设置渲染状态和标签
            
            //开始CG代码片段
			CGPROGRAM
                
            //顶点着色器的名字“vert”
			#pragma vertex vert
            //片元着色器的名字“frag”
			#pragma fragment frag 
                
            //在CG代码中，我们需要定义一个与属性名称和类型都匹配的变量
			fixed4 _Color;

            //顶点着色器的输入结构
			struct VertexInput {  
                //POSITION语义告诉Unity，用模型空间的顶点坐标填充vertex变量
                float4 vertex : POSITION;
                //NORMAL语义告诉Unity，用模型空间的法线方向填充normal变量
				float3 normal : NORMAL;
                //TEXCOORD0语义告诉Unity，用模型的第一套纹理坐标填充texcoord变量
				float4 texcoord : TEXCOORD0;
            };
            
            //顶点着色器的输出结构
			struct VertexOutput {
                
                //顶点在裁剪空间的位置信息
                float4 pos : SV_POSITION;
                //COLOR0语义用于存放颜色信息
				fixed3 color : COLOR0;
            };

            //顶点着色器，名字为vert，返回值是一个VertexOutput结构
			VertexOutput vert(VertexInput v){
                //声明一个输出结构变量o
				VertexOutput o;
                //o的pos存放输入结构中转换到裁剪空间的顶点坐标
				o.pos = UnityObjectToClipPos(v.vertex);
                //o的color存放一个与发现有关的常数
				o.color = v.normal * 0.5 + fixed3(0.5, 0.5, 0.5);
                //return刚刚声明的输出结构
				return o;
			}

            //片元着色器，名字为frag，返回值是一个fixed4变量
			fixed4 frag(VertexOutput i) : SV_Target {
				fixed3 c = i.color;
				c *= _Color.rgb;
				return fixed4(c, 1.0);
			}

			ENDCG
		}
	}
}
```

### 5.2 Unity 提供的CG/HLSL语义

- 在前面出现的`SV_POSITION`、`POSITION`、`COLOR0`等这些大写的名字就是CG/HLSL语义。

- 语义实际上就是一个赋给Shader输入和输出的字符串，这个字符串表达了这个参数的含义。

- 通俗地讲，这些语义可以让Shader 知道从哪里读取数据，并把数据输出到哪里,它们在CG/HLSL的Shader流水线中是不可或缺的。需要注意的是，Unity并没有支持所有的语义。

  ![image-20221206212958448](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221206212958448.png)

![image-20221206213032591](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221206213032591.png)

![image-20221206213049119](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221206213049119.png)

## 第六章 Unity中的基础光照

### 6.1 标准光照模型

**标准光照模型**只关心**直接光照（direct light)**，也就是那些直接从光源发射出来照射到物体表面后，经过物体表面的**一次反射**直接进入摄像机的光线。

它的基本方法是，把进入到摄像机内的光线分为4个部分，每个部分使用一种方法来计算它的贡献度。这4个部分是

- 自发光（emissive）
- 高光反射（specular）
- 漫反射（diffuse）
- 环境光（ambient）

#### 6.1.1 环境光

虽然标准光照模型的重点在于描述直接光照，但在真实的世界中，物体也可以被**间接光照(indirect light）**所照亮。

在标准光照模型中，我们使用了一种被称为**环境光**的部分来近似模拟间接光照。环境光的计算非常简单，它通常是一个全局变量，即场景中的所有物体都使用这个环境光。

![image-20221208085957135](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221208085957135.png)

#### 6.1.2 自发光

光线也可以直接由光源发射进入摄像机，而不需要经过任何物体的反射。

标准光照模型使用自发光来计算这个部分的贡献度。它的计算也很简单，就是直接使用了该材质的自发光颜色:

![image-20221208090123405](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221208090123405.png)

#### 6.1.3 漫反射

漫反射光照是用于对那些被物体表面随机散射到各个方向的辐射度进行建模的。

在漫反射中，视角的位置是不重要的，因为反射是完全随机的，因此可以认为在任何反射方向上的分布都是一样的。但是，入射光线的角度很重要。

漫反射光照符合**兰伯特定律(Lambert's law)**:反射光线的强度与表面法线和光源方向之间夹角的余弦值成正比。

因此,漫反射部分的计算如下:

![image-20221208090416963](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221208090416963.png)

c是光源颜色，m材质的漫反射颜色，n是表面法线，I是指向光源的单位矢量

#### 6.1.4 高光反射

这里的高光反射是一种经验模型，也就是说，它 并不完全符合真实世界中的高光反射现象。它可用于计算那些沿着完全镜面反射方向被反射的光线，这可以让物体看起来是有光泽的，例如金属材质。

- **表面法线、视角方向、光源方向、反射方向**

![image-20221208091241089](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221208091241089.png)

- **反射方向**

![image-20221208091430724](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221208091430724.png)

- Phong模型计算高光

![image-20221208091501055](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221208091501055.png)

其中，**m_gloss**是材质的**光泽度（gloss)**，也被称为**反光度(shininess)**。它用于控制高光区域的“亮点”有多宽，**越大，亮点就越小。m_specular是材质的**高光反射颜色**，它用于控制该材质对于高光反射的强度和颜色。C_light则是光源的颜色和强度。





![image-20221208091856103](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221208091856103.png)

![image-20221208091938071](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221208091938071.png)

- Blinn模型计算高光

![image-20221208091953824](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221208091953824.png)

- 在硬件实现时，如果摄像机和光源距离模型**足够远**的话，**Blinn模型会快于Phong模型**，这是因为,此时可以认为**V**和**I**都是定值，因此**h**将是一个常量。但是，当**V**或者**I**不是定值时，**Phong模型可能反而更快一些**。
- 需要注意的是，这两种光照模型都是经验模型

#### 6.1.5 逐顶点还是逐像素

- 通常来讲，我们有两种选择:在**片元着色器**中计算，也被称为**逐像素光照（per-pixel lighting)**;在**顶点着色器**中计算，也被称为**逐顶点光照(per-vertex lighting)**。
- 在**逐像素光照**中，我们会以每个像素为基础，得到它的法线(可以是对顶点法线插值得到的，也可以是从法线纹理中采样得到的)，然后进行光照模型的计算。这种在面片之间对顶点法线进行插值的技术被称为**Phong 着色(Phong shading)**，也被称为**Phong插值或法线插值着色技术**。这不同于我们之前讲到的Phong 光照模型。
- 与之相对的是**逐顶点光照**，也被称为**高洛德着色（Gouraud shading)**。在逐顶点光照中，我们在每个顶点上计算光照，然后会在渲染图元内部进行**线性插值**，最后输出成像素颜色。由于顶点数目往往远小于像素数目，因此逐顶点光照的计算量往往要小于逐像素光照。但是，由于**逐顶点光照依赖于线性插值来得到像素光照**，因此，当光照模型中有非线性的计算（例如计算高光反射时）时，逐顶点光照就会出问题。

### 6.2 Unity中的环境光和自发光

- Unity中，场景的环境光可以在`Window->Rendering->Lighting->Environment Lighting`中控制。

![image-20230104112150213](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230104112150213.png)

- 在Shader中只需要通过内置变量`UNITY_LIGHTMODEL_AMBIENT`就可以得到环境光的颜色和强度信息。
- 而大多数物体是没有自发光特性的，因此在本书绝大部分的 Shader中都没有计算自发光部分。如果要计算自发光也非常简单,我们只需要在**片元着色器**输出最后的颜色之前，**把材质的自发光颜色添加到输出颜色上即可**。

### 6.3 在Unity Shader中实现漫反射光照模型

- 基本光照模型中漫反射部分的计算公式：

![image-20221208090416963](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221208090416963.png)

#### 6.3.1 实践：逐顶点光照

![](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/Diffuse.png)

#### 6.3.2 实践：逐像素光照

![1](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/1.png)

#### 6.3.3 半兰伯特模型

![Lanbert](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/Lanbert.png)

### 6.4 在UnityShader中实现高光反射光照模型

![image-20230112103240431](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230112103240431.png)

![image-20230112103249686](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230112103249686.png)

![image-20230112103255914](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230112103255914.png)

#### 6.4.1 逐顶点

![2](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/2.png)

#### 6.4.2 逐像素（Phong模型）

![3](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/3.png)

#### 小结

- 完整的Phong光照模型为：环境光 + 漫反射 + 高光（ambient + diffuse + specular）

#### 6.4.3 Blinn-Phong光照模型

![image-20230112103208252](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230112103208252.png)

![image-20230112103218208](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230112103218208.png)

![BlinnPhong](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/BlinnPhong.png)

### 6.5 使用Unity内置的函数

![image-20230112104528458](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230112104528458.png)

## 第七章 基础纹理

### 7.1 单张纹理

#### 7.1.1 实践

![image-20230113113411254](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230113113411254.png)![image-20230113113434542](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230113113434542.png)

![SingleTexture](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/SingleTexture.png)

#### 7.1.2 纹理的属性

![image-20230113121152253](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230113121152253.png)

- Wrap Mode：

  - Repeat模式下，纹理坐标超过1，它的整数部分会被舍弃，直接用小数部分进行采样，这样纹理将会不断重复

  - Clamp模式下，超过1的部分会截取至1，小于0相同。

    ![image-20230113121554904](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230113121554904.png)![image-20230113121616655](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230113121616655.png)

- Filter Mode：
  - 决定了当纹理由于变换产生拉伸时，将会采用哪种滤波方式
  - Point，Bilinear和Trilinear，滤波效果一次提升，消耗性能也依次增大。

### 7.2 凹凸映射

- 纹理的另一个常见应用就是**凹凸映射(bump mapping)**，让模型看起来好像是凹凸不平的。通常有两种方法来实现：

  - 使用**高度纹理(height map)**来模拟**表面位移(displacement)**然后得到一个修改后的法线值，这种方法也被成为**高度映射(height maping)**
  - 使用**法线纹理(normal map)**来直接存储表面法线，这种方法也叫做**法线映射(normal mapping)**

  
  
  

#### 7.2.1 实践

![NormalMapTangentSpace](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/NormalMapTangentSpace.png)

![NormalMapWorldSpace](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/NormalMapWorldSpace.png)

### 7.3 渐变纹理

#### 7.3.1 实践

![RampTexture](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/RampTexture.png)

## 第八章 透明效果

在Unity中，我们通常用两种方法来实现透明效果：

- 第一种是使用**透明度测试(Alpha Test)**，这种方法无法得到真正的半透明效果
- 第二种是**透明度混合(Alpha Blend)**

对于**不透明(opaque)物体**，不考虑它们的渲染顺序也能得到正确的排序效果，这是由于强大的**深度缓冲(depth buffer,也被称为z-buffer)**的存在。它的基本思想是:

- 根据深度缓存中的值来判断该片元距离摄像机的距离，当渲染一个片元时需要把它的深度值和已经存在于深度缓冲中的值进行比较(如果开启了深度测试)，如果它的值距离摄像机更远，那么说明这个片元不应该被渲染到屏幕上(有物体挡住了它);否则，这个片元应该覆盖掉此时颜色缓冲中的像素值，并把它的深度值更新到深度缓冲中（如果开启了深度写入)。

透明度测试和透明度混合的基本原理：

- **透明度测试:**
  - 只要一个片元的**透明度不满足条件.(通常是小于某个阈值)**，那么它对应的片元就会**被舍弃**。
  - 被舍弃的片元将不会再进行任何处理,也不会对颜色缓冲产生任何影响;否则,就会按照普通的不透明物体的处理方式来处理它,即进行深度测试、深度写入等。
  - 也就是说，**透明度测试是不需要关闭深度写入的**，它和其他不透明物体最大的不同就是它会根据透明度来舍弃一些片元。
  - 虽然简单，但是它产生的效果也很极端，**要么完全透明，即看不到，要么完全不透明，就像不透明物体那样。**
- **透明度混合:**
  - 这种方法可以得到真正的半透明效果。
  - 它会使用当前片元的透明度作为混合因子，与已经存储在颜色缓冲中的颜色值进行混合，得到新的颜色。
  - 但是，透明度混合**需要关闭深度写入**（这使得我们要**非常小心物体的渲染顺序**。
  - 需要注意的是，透明度混合**只关闭了深度写入，但没有关闭深度测试**。
  - 这意味着当使用透明度混合渲染一个片元时，还是**会比较它的深度值与当前深度缓冲中的深度值**，如果它的深度值距离摄像机更远，那么就不会再进行混合操作。
  - 这一点决定了，当一个不透明物体出现在一个透明物体的前面，而我们先渲染了不透明物体，它仍然可以正常地遮挡住透明物体。也就是说，对于**透明度混合**来说，**深度缓冲是只读的**。

### 8.1 Unity Shader的渲染顺序

Unity为了解决这个问题，提供了**渲染队列(render queue)**这一解决方案

![image-20230119113607728](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230119113607728.png)

- 如果我们想要通过透明度测试实现透明效果，代码中应该包含类似下面的代码：

  ```c#
  SubShader
  {
      Tags ("Queue"="AlphaTest")
      Pass 
      {
          ...
      }
  }
  ```

- 如果我们想要通过透明度混合实现透明效果：

  ```c#
  SubShader
  {
      Tags ("Queue"="Transparent")
      Pass
      {
          ZWrite Off
          ...
      }
  }
  ```

### 8.2 透明度测试

![alpha test](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/alpha%20test.png)

### 8.3 透明度混合

![透明度混合](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/%E9%80%8F%E6%98%8E%E5%BA%A6%E6%B7%B7%E5%90%88.png)

### 8.4 开启深度写入的半透明效果

- 代码与`AlphaBlend`一样，不过在原有的`Pass`前新加一个`Pass`内容仅为开启深度写入与`ColorMask 0`(不写入任何颜色信息)![透明度混合开启深度写入](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/%E9%80%8F%E6%98%8E%E5%BA%A6%E6%B7%B7%E5%90%88%E5%BC%80%E5%90%AF%E6%B7%B1%E5%BA%A6%E5%86%99%E5%85%A5.png)

### 8.5 ShaderLab 的混合命令

#### 8.5.1 混合等式和参数

- 现在已知两个操作数：源颜色S (Source Color,片元着色器产生的颜色值)，目标颜色D(Destination Color,颜色缓冲里读到的颜色值)。
- 要得到混合后的颜色O，需要使用一个等式来计算，这个等式成为**混合等式(blend equation)**

![image-20230313131630084](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230313131630084.png)

![image-20230313131642029](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230313131642029.png)

![image-20230313131701923](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230313131701923.png)

几种混合：

- Blend SrcAlpha OneMinusSrcAlpha

![image-20230313132120062](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230313132120062.png)

- Blend One Zero

![image-20230313132204325](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230313132204325.png)

- Blend Zero One

![image-20230313132229104](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230313132229104.png)

- Blend Zero Zero

![image-20230313132307527](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230313132307527.png)

#### 8.5.2 混合操作

- 上述的混合等式除了加，还有其他形式

![image-20230313132527944](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230313132527944.png)

![image-20230313132537199](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230313132537199.png)

![image-20230313132738949](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230313132738949.png)

### 8.6 双面渲染的透明效果

- 正常来说，我们会剔除物体背面的渲染片元，但是在透明效果里我们需要看到物体背面的效果，因此需要使用`Cull`指令选择剔除
  - `Cull Off`——关闭剔除
  - `Cull Front(Back)`——剔除摄影机的正面（背面）
- 在透明混合中我们需要注意背面与正面的渲染顺序，因此将渲染分为两个`Pass`，先渲染背面剔除正面，在渲染正面剔除背面

![双面渲染的透明度混合](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/%E5%8F%8C%E9%9D%A2%E6%B8%B2%E6%9F%93%E7%9A%84%E9%80%8F%E6%98%8E%E5%BA%A6%E6%B7%B7%E5%90%88.png)

## 第九章 更复杂的光照

### 9.1 Unity 的渲染路径

- 在Unity中，**渲染路径(Rendering Path)**决定了光照是如何被应用在Shader里的。我么需要为每个`Pass`指定它使用的渲染路径。只有正确的选择和设置了渲染路径，该Shader的光照计算才能正常执行。
- 主要有三种渲染路径：**前向渲染路径(Forward Rendering Path)、延迟渲染路径(Deferred Rendering Path)和顶点照明渲染路径(Vertex Lit Rendering Path).**//顶点照明渲染路径已经Unity抛弃，但是仍可使用。
- 大多数情况，一个项目只使用一个渲染路径，可以在`Edit->Project Setting->Graphics`里设置，但是每个摄像机也能单独设置渲染路径
- 在`Pass`里用`LightMode`标签选择渲染路径

![image-20230314112118399](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230314112118399.png)

#### 9.1.1 前向渲染路径

##### 前向渲染的原理

- 每进行一次完整的前向渲染，我们需要渲染该对象的渲染图元，并计算两个缓冲区的信息:一个是**颜色缓冲区**，一个是**深度缓冲区**。我们利用深度缓冲来决定一个片元是否可见，如果可见就更新颜色缓冲区中的颜色值。
- 对于每个逐像素光源，我们都需要进行上面一次完整的渲染流程。如果——个物体在多个逐像素光源的影响区域内，那么该物体就需要执行多个 Pass，每个Pass计算一个逐像素光源的光照结果，然后在帧缓冲中把这些光照结果混合起来得到最终的颜色值。
- 如果有大量的逐像素光照，需要执行的`Pass`数目也会很大，所以通常渲染引擎会限制每个物体的逐像素光照的数目

##### Unity中的前向渲染

- Unity中有三种处理光照的方式：逐顶点处理、逐像素处理、球谐函数(Spherical Harmonics,SH)处理。
- 在前向渲染中，我们渲染一个物体，会根据光源的设置以及影响程度，对光源进行一个排序，其中一定数量的光源会按逐像素处理，最多有四个按逐顶点处理，剩下的按SH处理。
- 规则如下：
  - 场景中最亮的平行光总是按逐像素处理的。
  - 渲染模式被设置成`Not Important`的光源,会按逐顶点或者SH 处理。
  - 渲染模式被设置成`Important`的光源,会按逐像素处理。
  - 如果根据以上规则得到的逐像素光源数量小于`Quality Setting`中的逐像素光源数量(PixelLight Count)，会有更多的光源以逐像素的方式进行渲染。
- 前向渲染有两种`Pass`，`Base Pass`和`Additional Pass`

![image-20230314113257259](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230314113257259.png)

##### 内置的光照变量和函数

- 当我们选择前向渲染后，我们可以使用的变量与函数

![image-20230315140042415](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230315140042415.png)

![image-20230314113717604](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230314113717604.png)

#### 9.1.2 顶点照明渲染路径

- 顶点照明渲染路径通常在一个 Pass 中就可以完成对物体的渲染。在这个Pass 中，我们会计算我们关心的所有光源对该物体的照明，并且这个计算是按逐顶点处理的。这是Unity 中最快速的渲染路径，并且具有最广泛的硬件支持（但是游戏机上并不支持这种路径)。
- 由于顶点照明渲染路径仅仅是前向渲染路径的一个子集，在未来的版本中，顶点照明渲染路径的相关设定可能会被移除。

#### 9.1.3 延迟渲染路径

- 前向渲染的问题是：当场景中包含大量实时光源时，前向渲染的性能会急速下降。
- 延迟渲染本来是一个更老的渲染方式，但由于上述瓶颈问题又流行起来。
- 除了前向渲染中使用的**颜色缓冲和深度缓冲**外，延迟渲染还会利用额外的缓冲区，这些缓冲区也被统称为**G缓冲（G-buffer)**，其中G是英文Geometry 的缩写。
- G缓冲区存储了我们所关心的表面(通常指的是离摄像机最近的表面）的**其他信息**，例如该表面的**法线、位置、用于光照计算的材质属性**等。

##### 延迟渲染的原理

- 延迟渲染主要包含了两个 Pass。
  - 在**第一个Pass**中，我们**不进行任何光照计算**，而是仅仅**计算哪些片元是可见的**，这主要是**通过深度缓冲技术**来实现，当发现一个片元是可见的，我们就**把它的相关信息存储到G缓冲区中**。
  - 在**第二个 Pass**中，我们**利用G缓冲区**的各个片元信息，例如表面法线、视角方向、漫反射系数等，**进行真正的光照计算。**

- 延迟渲染使用的Pass 数目通常就是**两个**，这跟场景中包含的**光源数目是没有关系的。**
- 延迟渲染的**效率**不依赖于场景的复杂度，而是和我们使用的**屏幕空间的大小有关**。这是因为，我们需要的信息都存储在缓冲区中，而这些缓冲区可以理解成是一张张2D图像，我们的计算实际上就是在这些图像空间中进行的。

##### Unity中的延迟渲染

- 对于延迟渲染路径来说，它最适合在场景中光源数目很多、使用前向渲染会造成性能瓶颈的情况使用
- 它的缺点是：
  - 不支持真正的抗锯齿（anti-aliasing）功能
  - 不能处理半透明物体
  - 对显卡有一定要求。

### 9.2 Unity 的光源类型

- Unity一共支持四种光源类型：平行光、点光源、聚光灯和面光源（area light）
  - 面光源仅在烘焙时发挥作用，不计入讨论

#### 9.2.1 光源类型有什么影响

##### 平行光

- 几何属性只有方向，没有衰减

##### 点光源

- 由空间的一个球体定义，几何属性由位置（Position）、半径（Range）
- 存在光照衰减，中心最强，边缘最弱

##### 聚光灯

- 由空间中的一块锥形区域定义
- 锥形区域半径由Range属性决定，张开角度由Spot Angle属性决定，位置由Position属性决定，方向要用聚光灯的位置减去某点的位置得到，衰减也是与到光源的距离有关。
- 计算最复杂，需要判断一个点是否在锥体范围内

#### 9.2.2 在前向渲染中处理不同的光源类型

![前向渲染](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/%E5%89%8D%E5%90%91%E6%B8%B2%E6%9F%93.png)

- 设置四个点光源与一个平行光，使用`Frame Debugger`观察渲染过程

  - 平行光

    ![image-20230315144859433](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230315144859433.png)![image-20230315144913765](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230315144913765.png)

  - 第一个点光源

    ![image-20230315145043310](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230315145043310.png)![image-20230315145050286](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230315145050286.png)

  - 第二个点光源

    ![image-20230315145102415](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230315145102415.png)![image-20230315145108882](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230315145108882.png)

  - 第三个点光源

    ![image-20230315145119045](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230315145119045.png)![image-20230315145124812](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230315145124812.png)

  - 第四个点光源

    ![image-20230315145137005](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230315145137005.png)![image-20230315145142310](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230315145142310.png)

- 当我们创建一个光源时，默认的`Render Mode`为`Auto`，Unity会为我们判断哪些光源按逐像素处理，哪些按逐顶点和SH方式处理。

- 由于我们没有更改`Edit -> Project Setting -> Quality -> Pixel Light Count`的数值，默认一个物体可以接收除最亮的平行光外的四个逐像素光照

- 上述过程，第一次调用的是`Base Pass`，渲染平行光，后面的是`Additional Pass`依次渲染四个点光源。

- 点光源渲染顺序是按照他们的重要度排序，重要度取决于 颜色、强度、远近

- 若我们将四个点光源设置为`Not Important`，因为我们没有计算逐顶点和SH光，则这四个光源将不会被渲染

### 9.3 Unity 的光照衰减

#### 9.3.1 用于光照衰减的纹理

- Unity内部使用一张名为`_LightTexture0`的纹理来计算光源衰减，(0，0)代表光源位置的衰减值，(1，1)代表光源空间中最远的点的衰减。
- 我们需要先通过`_LightMatrix0`变换矩阵得到光源空间的点的位置

```c#
float3 lightCoord = mul(_LightMatrix0,float4(i.worldPosition,1)).xyz;
```

- 使用这个坐标的模的平方对衰减纹理进行采样。

```c#
fixed atten = tex2D(_LightTexture0,dot(lightCoord,lightCoord).rr).UNITY_ATTEN_CHANNEL
//这里面的.rr相当于创建了一个二维向量（x,x）
```

- 最后通过`UNITY_ATTEN_CHANNEL`分量来得到衰减值

#### 9.3.2 使用数学公式计算衰减

-  有时候我们希望通过数学公式来计算光源的衰减。例如下面的代码可以计算线性衰减。

```c#
float distance = length(_WorldSpaceLightPos0.xyz-i.worldPosition.xyz);
atten = 1.0/distance;
```

- 但是Unity没有在文档中给出内置衰减计算的说明，虽然我们仍然可以在片元着色器中利用数学公式计算衰减，但是我们无法通过内置变量得到光源的范围、聚光灯的朝向、张开角度等信息，最后得到的效果不尽人意。

### 9.4 Unity 的阴影

#### 9.4.1 阴影是如何实现的

- 在实时渲染中，我们常用的阴影实现方式是**Shadow Map**技术
  - 首先把摄像机的位置放在与光源重合的位置，此时该光源的阴影区域就是那些摄像机看不到的地方。
  - Unity 就是使用了这种技术
- 在前向渲染路径中，如果场景最重要的平行光开启的阴影，Unity 就会为该光源计算它的**阴影映射纹理（shadermap）**——本质上是一张深度图。
  - 计算深度信息时，先把摄像机放在光源位置，然后如果采用正常的渲染流程，调用`Base Pass`和`Additional Pass`来更新深度信息，这样会对性能造成一定的浪费，因为实际上我们仅仅需要深度信息而已。
  - 因此因此Unity会选择一个额外的`Pass`专门来更新深度信息，`LightMode`标签是`ShaderCaster`
- 一个物体接受来自其他物体的阴影，以及它向其他物体投射阴影是两个过程
- 如果我们想要一个物体接收来自其他物体的阴影，就必须在Shader 中对阴影映射纹理(包括屏幕空间的阴影图）进行采样，把采样结果和最后的光照结果**相乘**来产生阴影效果。
- 如果我们想要一个物体向其他物体投射阴影,就必须把该物体加入到光源的阴影映射纹理的计算中，从而让其他物体在对阴影映射纹理采样时可以得到该物体的相关信息。在Unity中，这个过程是通过为该物体执行LightMode为 ShadowCaster的 Pass来实现的。如果使用了屏幕空间的投影映射技术，Unity还会使用这个Pass产生一张摄像机的深度纹理。

#### 9.4.2 不透明物体的阴影

##### 让物体投射阴影

- 在Untiy中通过设置`Mesh Renderer`中的`Cast Shadows`和`Receive Shadows`属性来实现的

![image-20230320193707062](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230320193707062.png)

- 打开之后会发现没有设置`ShadowCaster`的`Pass`也能产生阴影，这是因为两个平面默认为Standard材质，而cube虽然使用了前面自己写的`ForwardRendering`Shader,但是因为最后设置了`Fallback "Specular"`，为它设置了一个用于回调的Unity Shader，即内置的`Specular`，虽然它也没有包含`ShaderCaster`，但是它会继续`Fallback`调用`VertexLit`，这里面包含了阴影

```c#
//Pass to render object as a shadow caster
Pass 
{
    Name "ShadowCaster"
    Tags {"LightMode" = "ShadowCaster"}
    
    CGPROGRAM
    #pragma vertex vert
    #pragma fragment frag
    #pragma multi_compile_shadowcaster
    #include "UnityCG.cginc"
        
    struct v2f
    {
        V2F_SHADOW_CASTER;
    };
    
    v2f vert (appdata_base v)
    {
        v2f o;
        TRANSFER_SHADOW_CASTER_NORMALOFFSET(o)
        return o;
    }
    
    float4 frag (v2f i): SV_Target
    {
        SHADOW_CASTER_FRAGMENT(i)
    }
    ENDCG
}
```

##### 让物体接收阴影

![接收阴影](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/%E6%8E%A5%E6%94%B6%E9%98%B4%E5%BD%B1.png)

- 包含进一个新的内置文件

```c#
#include "AutoLight.cginc"
```

- 在顶点着色器的输出结构体v2f中添加一个新的内置宏，声明了一个阴影纹理采样坐标，参数是下一个可用的插值寄存器的索引值。

```c#
struct v2f
{
	float4 pos : SV_POSITION;
	float3 worldNormal : TEXCOORD0;
	float3 worldPos : TEXCOORD1;
	SHADOW_COORDS(2)
}
```

- 在顶点着色器返回之前添加另一个内置宏

```c#
TRANSFER_SHADOW(o);
```

- 在片元着色器中计算阴影值，使用了一个内置宏

```c#
fixed shadow = SHADOW_ATTENUATION(i)
```

- 最后将阴影值与漫反射高光相乘

```c#
return fixed4(ambient + (diffuse + specular)*atten*shadow,1.0);
```

#### 9.4.3 使用帧调试器查看阴影绘制过程

#### 9.4.4 统一管理光照衰减和阴影

![fwdadd](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/fwdadd.png)

- 用内置宏替代原本的atten计算，同时计算阴影，`UNITY_LIGHT_ATTENUATION`会分辨不同光源类型
- 在`#pragma multi_compile_fwdadd`后增加`_fullshadows`

#### 9.4.5 透明度物体的阴影

![alphatest](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/alphatest.png)

- 将`AlphaTest.shader`增加阴影相关，但是最后的`Fallback`设置为`VertexLit`，可以看到透明的地方仍有阴影

![image-20230320220108471](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230320220108471.png)

- 此时需要选择有透明度测试的`ShadowCaster Pass`，虽然我们也可以自行设置，但是为减少代码量使用内置的`Transparent/Cutout/VertexLit`。注意要使用同名的`_Cutoff`。
- 但是仍有一些问题，如：出现一些不应该透过光的部分。原因是默认情况下把物体渲染到深度图和阴影映射纹理中进考虑了物体的正面，由于一些面完全背光，因此这些面的深度信息没有加入到阴影映射纹理的计算中，此时因将正方体的`Cast Shadows`设置为`Two Sided`。

![image-20230320221115030](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230320221115030.png)



## 第十章 高级纹理
