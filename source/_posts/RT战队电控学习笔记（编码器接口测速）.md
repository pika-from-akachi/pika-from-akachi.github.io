---
title: RT战队电控学习笔记 (编码器接口测速)
date: 2025-04-26
categories: Hardware
tags: [Hardware,Blog,Tech]
mathjax: true
---



# RT战队电控学习笔记（编码器接口测速）

> **作者：重庆大学国家卓越工程师学院 Kaiden Poon**
>
> **时间：2025年4月26日**

**🛸本套笔记为适应Robotac战队电控组代码需求编写，主要涉及STM32单片机的编程实现🚀**

------

## 一、编码器接口理论基础

> **编码器接口简介：**
>
> - **Encoder Interface** 编码器接口
> - 编码器接口可**接收增量（正交）编码器的信号**，根据编码器旋转产生的正交信号脉冲，**自动控制CNT自增或自减**，从而指示编码器的**位置、旋转方向和旋转速度**
> - 每个**高级定时器和通用定时器**都**拥有一个编码器接口**
> - 两个输入引脚借用了**输入捕获**的通道1和通道2

**正交编码器：**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250513133043788.png" alt="image-20250513133043788" style="zoom: 67%;" />

**编码器接口基本结构：**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250513133844205.png" alt="image-20250513133844205" style="zoom: 67%;" />

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250513134438915.png" alt="image-20250513134438915" style="zoom: 80%;" />

简单来说，编码器的计数逻辑是**正转向上计数，反转向下计数。**

当两相边沿都计数时，工作模式图大致如下：

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250513134707523.png" alt="image-20250513134707523" style="zoom:80%;" />

双相边沿计数时，可以抗噪声，毛刺信号会被滤去。

------

## 二、编码器接口测速的代码实现

**需要新掌握的库函数:**

- **void TIM_EncoderInterfaceConfig(TIM_TypeDef* TIMx, uint16_t TIM_EncoderMode, uint16_t TIM_IC1Polarity, uint16_t TIM_IC2Polarity);**

    **(编码器接口配置)**

> **Encoder.c实现代码**

```C
#include "stm32f10x.h"

void Encoder_Init(void)
{
	//开启总线时钟
	RCC_APB1PeriphClockCmd(RCC_APB1Periph_TIM3,ENABLE);
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);
	//配置GPIO为上拉输入模式
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode=GPIO_Mode_IPU;
	GPIO_InitStructure.GPIO_Pin=GPIO_Pin_6|GPIO_Pin_7;
	GPIO_InitStructure.GPIO_Speed=GPIO_Speed_50MHz;
	GPIO_Init(GPIOA,&GPIO_InitStructure);
	//配置时基单元
	TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStructure;
	TIM_TimeBaseInitStructure.TIM_ClockDivision=TIM_CKD_DIV1;
	TIM_TimeBaseInitStructure.TIM_CounterMode = TIM_CounterMode_Up;
	TIM_TimeBaseInitStructure.TIM_Period=65536-1;		//预装重载值设置为最大
	TIM_TimeBaseInitStructure.TIM_Prescaler=1-1;		//无需分频，外部时钟直接输入	
	TIM_TimeBaseInitStructure.TIM_RepetitionCounter=0;
	TIM_TimeBaseInit(TIM3,&TIM_TimeBaseInitStructure);
	//配置输入捕获单元（只需要使用到定时器输入捕获单元的滤波和极性配置功能）
	TIM_ICInitTypeDef TIM_ICInitStructure;
	TIM_ICStructInit(&TIM_ICInitStructure);
	TIM_ICInitStructure.TIM_Channel=TIM_Channel_1;
	TIM_ICInitStructure.TIM_ICFilter=0xF;		//配置通道1的滤波器
	TIM_ICInitStructure.TIM_ICPolarity=TIM_ICPolarity_Rising; 		//配置通道1的极性
	TIM_ICInit(TIM3,&TIM_ICInitStructure);
	TIM_ICInitStructure.TIM_Channel=TIM_Channel_2;
	TIM_ICInitStructure.TIM_ICFilter=0xF;
	TIM_ICInitStructure.TIM_ICPolarity=TIM_ICPolarity_Rising;
	TIM_ICInit(TIM3,&TIM_ICInitStructure);
	//配置编码器接口（本节内容需要掌握的新库函数）
	TIM_EncoderInterfaceConfig(TIM3,TIM_EncoderMode_TI12,TIM_ICPolarity_Rising,TIM_ICPolarity_Rising);//配置通道极性，与前面配置IC模块时配置的是同一个寄存器
	
	TIM_Cmd(TIM3,ENABLE);
}

int16_t Encoder_Get(void)
{
	int16_t Temp;
	Temp=TIM_GetCounter(TIM3);
	TIM_SetCounter(TIM3,0);
	return Temp;
}
```

