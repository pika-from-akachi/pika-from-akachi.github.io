---
title: 基于立创EDA的PCB设计基础Day1(PCB结构与组成)
date: 2025-01-11
categories: Hardware
tags: [Hardware,Blog,Tech,PCB Layout]
mathjax: true
---



# 基于立创EDA的PCB设计基础Day1(PCB结构与组成)

> **作者：重庆大学国家卓越工程师学院 Kaiden Poon**
>
> **时间：2025年1月11日**

**🛻本套笔记为嵌入式设计所需的PCB绘制所涉及的知识✈️**

<img src="https://dl4.weshineapp.com/gif/20160824/7b1a32d314324cd5183b7e24edbdfe64.gif?f=micro_" alt="img" style="zoom: 33%;" />

## 一、学习过程

------

### 学习链接：@Expert电子实验室[【入门篇】8-PCB设计基础-PCB结构与组成_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1At421h7Ui?spm_id_from=333.788.player.switch&vd_source=e642a500cdc228005dcc2cb1ebf09e3f&p=9)

### 学习笔记：

#### ヾ(≧▽≦*)o PCB板上的一些重要的元素：

> **导线（Track）:**导线具有和原理图对应的网络连接关系。
>
> 导线带有**网标（NetLable）**,对应电路图结点。
>
> 在布线时，导线可以被自动**推挤**、**环绕**等，通常用于信号。

![image-20250111192305671](https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250111192305671.png)

> **铺铜：**通过一整块铜皮对网络进行连接，通常用于**地（GND）**和**电源（POWER）**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250111192625092.png" alt="image-20250111192625092" style="zoom:95%;" />

> **过孔：**
>
> 1. **电气连接**：过孔用于将**不同层面的**电路连接起来，使得电路板能够在不同的层次上进行有效的**信号和电源传输**。
> 2. **器件固定或定位**：过孔还可以用作**固定电子部件的位置**，如电阻、电容等，确保其在电路板上的正确布局。

![image-20250111193053090](https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250111193053090.png)



**过孔的类型：**

- 通孔：最常见和最简单的PCB过孔。是从PCB**上层钻到底层**的**机械钻孔**。

- 埋孔：**可以是激光钻孔也可以是机械钻孔**，指**位于印刷线路板内层的**连接孔，它不会延伸到线路板的表面。

- 盲孔：**激光钻孔**的一种，是从PCB的**上层**或**底层**到**内层钻孔**和电镀的孔。

    > **焊盘：**元件通过PCB上的引线孔，用焊锡焊接固定在PCB上，印制导线把焊盘连接起来，实现元件在电路中的电气连接。
    >
    > **引线孔及周围的铜箔称为焊盘**。
    >
    > 焊盘的类型有**通孔式焊盘**、**表贴式焊盘**等

![image-20250111194153362](https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250111194153362.png)



> **丝印：**指在电子线路板上印刷的信息，如**文字、标志、图形**等。这些丝印具有重要的功能，它们可以帮助**标识电子元件的位置、数值、型号等信息**，以及**元件的方向**和**正确的安装方式**。

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250111194517297.png" alt="image-20250111194517297" style="zoom:140%;" />

> **阻焊：**顾名思义，就是“阻止焊接”，在铜层上面覆盖**油墨层**，油墨层覆盖住铜层上面不需要焊接的线路，防止PCB上的线路和其他的金属、焊锡或者其他的导电物体接触**导致短路**，起到**绝缘及保护铜层的作用**，选择性露出焊接需要的**铜PAD**、**IC**等。

![image-20250111195028113](https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250111195028113.png)

#### ( •̀ ω •́ )✧ PCB的叠层结构

> PCB材料主要由**PP半固态片**和**Core芯板**两部分组成，这构成了所看到的绿色、红色或者黑色等的板子，再加上敷铜线路层，器件就构成了电路板。

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250111195823750.png" alt="image-20250111195823750" style="zoom:67%;" />



> **信号层（Signal）：**包括**顶层、底层、中间层**，各层之间可以通过**通孔、埋孔和盲孔**实现互相连接。
>
> 对于双层板而言，信号层就是顶层和底层。

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250111201100136.png" alt="image-20250111201100136" style="zoom:90%;" />

**上图中，红线位于顶层，而蓝线位于底层。**

> **丝印层：**
>
> - 采用**丝网印刷工艺**涂印
> - 作为**装配图、注释标记、Logo**
> - 作为**切割、装配**标记
> - 局部覆盖，**增加绝缘性**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250111201412704.png" alt="image-20250111201412704" style="zoom:130%;" />

> **阻焊层：**
>
> - Solder层**划线部分为裸露金属**，**空白部分**为阻焊层**（俗称绿油）**
> - **大电流导线**可以用Solder层裸露出来**搪焊锡**加厚

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250111201912459.png" alt="image-20250111201912459" style="zoom: 250%;" />

> **锡膏层：**用于**涂抹锡膏**、**制作钢网**用于**回流焊**
>
> - Paste层划线部分为**钢网刻孔部分**
> - 用于SMT工艺刷锡浆
> - 大电流导线可以用Solder层裸露并加Paste锡浆加厚

![image-20250111202533761](https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250111202533761.png)

> **多层：**
>
> - 多层上画的实体在**每个Layer都有**（Plane除外）
> - 常用于**直插焊盘、过孔**等需要**穿透每个层的**操作
> - 用于焊盘时，可定义**电镀孔（PTH）**和**非电镀孔（NPTH）**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250111202930771.png" alt="image-20250111202930771" style="zoom: 80%;" />

> **机械层：**定义整个PCB版的外观，一般用于设置电路板的**外形尺寸，数据标记，对齐标记，装配说明**以及**其它的机械信息**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250111203200465.png" alt="image-20250111203200465" style="zoom:80%;" />

> **3D外壳层：**可用于对PCB外壳的建模

## 二、学习收获概述

------

本次笔记主要是关于**PCB中基本元素**的记录，如**导线、铺铜、过孔**等，为日后PCB设计需要考虑到的参数打好基础。同时**PCB的叠层结构**赋予了我们创造更为复杂的电路板的机会，但是与之相伴的是要求**更为严格的布局，布线。**至此，有关于PCB设计所需要的理论知识已经准备齐全，在理论储备充实的前提下建立第一个PCB工程，开始画板路上的第一步，日后在遇到问题时也能回想学过的理论尝试解决。设计PCB的道路一定是艰苦的，但也一定是快乐的，这种快乐相信将所学变现过的诸君都明白。我的下一篇笔记将会是真正的EDA画板实战，让我们一起加油，早日成为真正的PCB侠✌️😊👍🚀