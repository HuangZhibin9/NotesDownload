# Unity XR interaction toolkit 学习笔记

> Started 2022 10 08
>
> Author 黄智斌 HuangZhibin9
>
> Unity3d 2021.3.10f1c2
>
> steamVR
>
> 笔记下载：[HuangZhibin9/NotesDownload (github.com)](https://github.com/HuangZhibin9/NotesDownload)



## VR项目设置

[Unity XR 用户手册](https://docs.unity3d.com/cn/2020.2/Manual/XR.html)





**使用前，需要先下载steam安装好steamVR，并连接好设备**





### Package导入及相关设置

- 进入你的Unity项目，或者新建一个3d项目

- 进入package manger

`Window -> Package Manager`

- 点击加号右边的package选项，选择`Unity Registry`

![](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/Unity XR 1.png)

- 找到`XR interaction Toolkit`和`XR Plugin Management`，将其安装导入
  - 若未找到这两个选项，则前往`Edit -> project setting -> Package Manager`将Advanced Settings 的 Enable Pre-release Packages勾上

![image-20221008112951684](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221008112951684.png)

- 随后Unity会重启，重启后在选择`XR interaction Toolkit`，打开Sample，将Starter Assets导入

![image-20221008113300341](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/UnityXR3.png)

- 关闭该界面，前往`Edit -> project setting -> XR Plug-in Management`将Open XR勾上，此时右边会出现一个警告符号，双击，点击edit，点击加号，选择你使用的VR设备，我自己使用的是HTC vive，然后运行一下场景，报错消失。

![image-20221008113611374](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221008113611374.png)

![image-20221008113655044](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221008113655044.png)

- 前往你的Project面板，找到刚刚导入的Starter Assets`Assets -> Samples -> XR interaction Toolkit -> 2.0.3(该处也许会改变) -> Starter Assets`，依次点击五个预设，在inspector面板上点击`Add to ActionBasedContinuousMoveProvider default`。

![image-20221008114308932](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221008114308932.png)

- 然后前往project setting点击Preset Manager`Edit -> Project Setting -> Preset Manager`，找到我们的`ActionBasedController`，将其对应输入Right和Left。

![image-20221008114813173](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221008114813173.png)

- 在Hierarchy面板找到`XR Interaction Manager`为其添加组件`Input Action Manager`

  ![image-20221125125550858](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221125125550858.png)

- 找到预设里的`XRI Default Input Action`，将其拖入刚刚组件里的`Action Assets `中

  ![image-20221125125839675](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221125125839675.png)

  ![image-20221125125852385](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221125125852385.png)

- 完成相关设置



### 简单项目创建

- 回到Hierarchy面板，右击创建`XR -> XR Origin (Action-based)(此处名字可能随Toolkit版本会变动)`，得到的物体有如下组成，且左右手柄自动匹配好我们先前设置好的预设

![image-20221008115347691](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221008115347691.png)![image-20221008115458972](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221008115458972.png)

- 创建一个Plane，调整好大小

![image-20221008115656413](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221008115656413.png)

- 点击运行，你能看到VR头盔和手柄已经连接完成，同时可以看到手柄的两条射线





## 移动与传送

- 在连接上头盔后，我们的移动以及转动视角实际上都可以依靠头盔完成，但是由于我们的空间有限，大部分时候我们是依靠手柄来进行移动以及转动视角的



### 转动视角

Unity interaction Toolkit 已经封装好了相关脚本，使用方法如下：

- 创建`Locomotion System`,在Hierarchy面板上右击然后选择`XR -> Locomotion System(Action-based)`
  - 此处我们单击`Locomotion System`物体，可以看到上面挂载了三个脚本，分别用于确定头盔位置、提供传送功能、提供转视角功能，同理，若是我们不想创建一个`Locomotion System`物体，也可直接在XR Origin物体上Add Component添加上相关脚本完成相应功能。

![image-20221008150349172](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221008150349172.png)

- 然后在该脚本的相关设置中，我们可以选择使用哪个手柄，如果不使用哪个手柄便将其的Use Reference的勾取消即可，比如此处我门选择用左手手柄操控转向，便将右手的勾取消。

![image-20221008151319192](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221008151319192.png)

- 运行之后，我们触碰左手手柄的轮盘便可实现转动视角。

![3](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/3.jpg)

### 移动

- 在先前我们创建好的`Locomotion System`物体的inspector面板上点击Add Component添加`Continuous Move Provider (Action-based)`脚本，如下：

![image-20221008150945355](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221008150945355.png)

- 同理，我们用右手移动，就将左手的勾取消掉。
- 运行之后，我们便可触碰右手手柄的轮盘进行移动，但是这种移动会有点僵硬，所以我们可以考虑使用传送，这也是很多VR游戏使用的方式

![3](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/3.jpg)

### 传送

​	传送所需要的两个组件分别是`Teleportation Provider`(已经挂载在`Locomotion System`物体上了)和`Teleportation Area`(挂载在可传送区域的物体上)

- 添加`Teleportation Provider`，已经存在所以不用设置

- 添加`Teleportation Area`，选择我们前面创建的平面`Plane`,在其inspector面板上添加`Teleportation Area`脚本，需要注意的是该脚本需要一个`Collider`,而我们的平面创建时自带一个`Mesh Collider`所以我们不用设置，如果没有则需要手动添加`Collider`组件。

  ![image-20221008153213142](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221008153213142.png)

- 运行，我们可以看到当射线指向地面时，会变成白色，然后按手柄侧键（此处使用的HTC vive，其他设备可能不一样），完成传送。

![image-20221008153634924](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20221008153634924.png)![2s](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/2s.jpg)

