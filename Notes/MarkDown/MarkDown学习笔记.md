

# 面向开发的MarkDown学习

> Strated on 2022 09 22
>
> software: typora
>
> Author:黄智斌 HuangZhibin9
>
> Learned from 杨希杰 Yang Xijie



## 	Markdown语法



### 		具体语法



#### 			Headers 标题

1~6个#号加上一个空格再加上标题的名称（快捷键***ctrl+对应数字键***）

```
# 一级标题
## 二级标题
……
###### 六级标题
```



#### 			Emphasis 加粗和斜体

```
*This text will be italic*
_This text will be italic_
——斜体（快捷键ctrl+I）

**This text will be italic**
__This text will be italic__
——加粗（快捷键ctrl+B）

_You **can** combine them_
```

效果：

*This text will be italic*

*This text will be italic*

**This text will be italic**

**This text will be italic**

*You **can** combine them*





#### 			Lists 列表



##### Unordered 无序列表

​	这里可以缩进

```
* Item 1
* Item 2
	*Item 2a
	*Item 2b
( 快捷键 ctrl+shift+] )
```

效果：

* Item 1

* Item 2
	* Item 2a
	
	* Item 2b

（按两次***Enter***结束该层Lists）





##### Ordered 有序列表

​	这里也可以缩进，但是缩进会重新编号，或者变成小标题（如1.1 	2.1.3等）

```
1. Item 1
1. Item 2
1. Item 3
	1. Item 3a
	1. Item 3b
（快捷键 ctrl+shift+[）
```

效果：

1. Item 1
2. Item 2
1. Item 3
	1. Item 3a
	2. Item 3b





##### Task Lists 任务列表 

```
- [x] this is a complete item
- [ ] this is an incomplete item
（快捷键 ctrl+shift+x）
```

效果：

- [x] this is a complete item
- [ ] this is an incomplete item







#### Links 链接



##### 链接基本操作

```
[Text](url):中括号，后面接小括号。中括号里放说明，小括号里放链接；不需要说明的可以直接放链接，也可以两侧加上< >

[GitHub Official Site](https://github.com)
https://github.com
<https://github.com>
(快捷键 ctrl+k)
```

效果：

[GitHub Official Site](https://github.com)

https://github.com

<https://github.com>





##### 链接进阶操作

```
[Baidu](https://www.baidu.com) is your best friend!
```

效果：

[Baidu](https://www.baidu.com) is your best friend!

```
链接也可以放文件路径

点击查看[帮助文档](./doc/help.md)
该处使用了相对路径，表示和当前文档同一路径下的doc文件夹中help.md文件，同理也可以填入绝对路径，但是一但文件移动则会路径失效
```

效果：

点击查看[帮助文档](./doc/help.md)



```
该链接也可以换成当前文档的某个大标题

点击跳转到这篇markdown的[Markdown语法部分](#Markdown语法)
```

效果：

点击跳转到这篇markdown的[Markdown语法部分](#Markdown语法)





#### Images 图片

```
！[Text](url):感叹号，中括号，小括号。中括号里放图片说明（不必须），小括号里放链接。直接复制入图片地址

![风灵玉秀]（https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fi0.hdslb.com%2Fbfs%2Farticle%2F66c317ae0aa1df34adff0a54006c6fa8b1c0eeab.jpg&refer=http%3A%2F%2Fi0.hdslb.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1666439056&t=f226e6fa59f4fb5363193fe4b409d71b）

```

效果：

![风灵玉秀](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fi0.hdslb.com%2Fbfs%2Farticle%2F66c317ae0aa1df34adff0a54006c6fa8b1c0eeab.jpg&refer=http%3A%2F%2Fi0.hdslb.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1666439056&t=f226e6fa59f4fb5363193fe4b409d71b)



------



#### Inline Code 行内代码

```
行内代码中用英文`，快捷键为 ctrl+shift+`

`Swift`中,`var`定义变量，`let`定义常量
```

效果：

`Swift`中,`var`定义变量，`let`定义常量

`Swift`中,`var`定义变量，`let`定义常量



------



#### Code Block 代码块

用一对```包裹，在最开始三个反撇后加上代码语言名称或者简写，可以获得高亮支持，在typora中右下角可以直接选择语言

（快捷键  ***ctrl+shift+k***）

````
```c#
public class Test
{
	public int num1;
	private int num2;
}
```
````

效果：

```c#
public class Test
{
	public int num1;
	private int num2;
}
```



------



#### Blockquotes 引用

```
引用在段落前加上>,快捷键为 ctrl+shift+Q

黄智斌曾经说过：
>我随便说说
>——黄智斌在md学习笔记中说。2022，9，22
```

效果：

黄智斌曾经说过：
>我随便说说
>——黄智斌在md学习笔记中说。2022，9，22





#### Tables 表格

```
First Header|Second Header
------------|-------------
content1|content2
content3|content4

也可以直接用快捷键（ctrl+T）插入表格
```

效果：

| First Header | Second Header |
| ------------ | ------------- |
| content1     | content2      |
| content3     | content4      |



------



#### 最后

本篇文章作于Typora软件，且该软件是我首推的Markdown编辑软件。

