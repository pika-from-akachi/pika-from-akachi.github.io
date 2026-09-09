---
title: RT战队电控学习笔记 (HAL库开发入门)
date: 2025-10-02
categories: Hardware
tags: [Hardware,Blog,Tech]
mathjax: true
---

# RT战队电控学习笔记 (HAL库开发入门)

------

> **作者：重庆大学国家卓越工程师学院 Kaiden Poon**
>
> **时间：2025年10月2日**

**🛸本套笔记为适应Robotac战队电控组代码需求编写，主要涉及STM32单片机的HAL库编程实现🚀**

------

## 一、初识HAL库

> **1.1 CMSIS简介**

CMSIS（微控制器软件接口标准）：Cortex Microcontroller Software Interface Standard，是由ARM与其合作的芯片厂商、软件工具厂商等共同制定的标准。

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20251002193838983.png" alt="image-20251002193838983" style="zoom:50%;" />

> **1.2 HAL库简介**

ST为了方便用户开发STM32芯片开发提供了三种库：

- **标准外设库（Standard Peripheral Libraries）**
- **HAL库（硬件抽象层）：Hardware Abstraction Layer**
- **LL库：Low Layer**

HAL库全系列兼容、是ST公司目前主推的库，兼容性、可移植性强。

> **2.1 STM32Cube固件包文件夹简介**

固件包内文件：

- Documentation：说明文档
- Drivers：驱动源码（重要）
- Middleware：中间文件
- Projects：ST公司官方开发板例程
- Utilities：公众组件
- Licence.md：软件版权信息
- package.xml：固件包版本信息
- Readme.md：自述文件