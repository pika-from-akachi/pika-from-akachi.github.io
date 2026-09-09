---
title: RT战队电控学习笔记 (STM32开发板设计)
date: 2025-05-14
categories: Hardware
tags: [Hardware,Blog,Tech]
mathjax: true
---

# RT战队电控学习笔记(STM32开发板设计)

> **作者：重庆大学国家卓越工程师学院 Kaiden Poon**
>
> **时间：2025年5月14日**

**🛸本套笔记为适应Robotac战队电控组代码需求编写，主要涉及STM32单片机的编程实现🚀**

------

## 一、电源模块设计

> **电源树**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250514164726943.png" alt="image-20250514164726943" style="zoom: 50%;" />

> [!IMPORTANT]
>
> **电源稳压器(Power Regulator)是一种在电源电压或者负载电流发生变化的时候，依然能够提供稳定输出电压的元件。**

> **LDO(Low Dropout Regulator):低压差线性稳压器***

**LDO芯片结构框图：**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250514170455094.png" alt="image-20250514170455094" style="zoom:50%;" />

**LDO的基本结构由四部分组成：**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250514170154603.png" alt="image-20250514170154603" style="zoom:33%;" />

**LDO的电气特性：**

- **LDO外围器件少，电路简单，成本低，通常只需要一两个旁路电容**
- **LDO负载响应快，输出纹波小，噪声小**
- LDO效率低，输入输出的压差不能太大
- LDO只能用于降压
- LDO输出电流有限，最高可能只有几安

**应用场合：低压差、低纹波、低噪声**

> [!Important]
>
> **DC/DC转换器是一种开关电源稳压器，指利用电容、电感的储能特性，通过可控开关（MOSFET等）进行高频开关的动作，将输入的电能储存在电容(感)里，当开关断开时，电能再释放给负载，提供能量。**

- 以**降压**为目的的DCDC电源电路称为**BUCK电路**
- 以**升压**为目的的DCDC电源电路称为**BOOST电路**



**BUCK电路：**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250514173038750.png" alt="image-20250514173038750" style="zoom:67%;" />

- MOS管功能：**将直流电变成PWM波**，MOS管导通，则输出一个电压，MOS管关断则没有电压。

但是**PWM波不稳定**，不能作为稳定供给的直流电给负载使用，则需要通过右半部分电路将其**变为稳定的直流电。**

- 电容功能：**储能（电容的内部可以积累电量）、滤波（把不稳定的电压滤成稳定的直流电压）。**
- 电感功能：**抑制电容两端的电压突变**，使储能电路部分维持在相对稳定的状态。
- 二极管功能：**续流，在MOS管关断的时候为电流提供一条续流的路径**，使其能够持续流过负载。



**BOOST电路：**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250514174745419.png" alt="image-20250514174745419" style="zoom: 67%;" />

- MOS导通时：左半部分电路导通，右半部分电路被隔离，**电源对电感充电**。
- MOS关断时：左半部分电路断开，电感向后端的电容和负载放电，同时根据电感的特性，为了阻止电流的变小，**电感感应出的电压应为右正左负**，则输入负载的电压会高于电源电压，实现**升压**。
- MOS管再次导通时：电源重新向电感进行充电，由于续流二极管的存在，二极管左边电压小于右边电压，**二极管关断**，左右两部分电路再次隔离，此时**负载供电由电容提供**。



**DCDC开关电源的电气特性：**

- DC-DC外围器件多，电路复杂，成本高
- DC-DC负载响应比LDO慢，输出波纹大，噪声大
- **DC-DC效率高，输入电压范围宽泛**
- **DC-DC支持降压和升压**
- **DC-DC输出电流高，功率大**

**应用场合：大压差、功率大**



> **LDO与DCDC的优劣势对比**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250514180509249.png" alt="image-20250514180509249" style="zoom: 67%;" />

