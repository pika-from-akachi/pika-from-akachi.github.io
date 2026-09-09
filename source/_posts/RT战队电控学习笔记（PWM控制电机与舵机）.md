---
title: RT战队电控学习笔记 (PWM控制电机及舵机)
date: 2025-03-13
categories: Hardware
tags: [Hardware,Blog,Tech]
mathjax: true
---

# RT战队电控学习笔记（PWM控制电机及舵机）

> **作者：重庆大学国家卓越工程师学院  Kaiden Poon**
>
> **时间：2025年3月13日**

**🛸本套笔记为适应Robotac战队电控组代码需求编写，主要涉及STM32单片机的编程实现🚀**

## 一、TIM定时中断

------

> **TIM(Timer)定时器**

1. 定时器可以对输入的时钟进行计数，并**在计数值达到设定值时触发中断**。
2. 16位计数器、预分频器、自动重装寄存器的**时基单元**，在72MHz计数时钟下可以实现**最大59.65s**的定时。
3. 除具备基本的定时中断功能外，还包含**内外时钟源选择、输入捕获、输出比较、编码器接口（用于电机测速）、主从触发模式**等多种功能。

**定时器类型**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250313155908143.png" alt="image-20250313155908143" style="zoom:80%;" />



**定时中断基本结构**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250313162153814.png" alt="image-20250313162153814" style="zoom: 70%;" />

- **计数器计数频率：CK_CNT=CK_PSC/(PSC+1),即定时器时钟=定时器时钟源/(预分频值+1)。**
- **计数器溢出频率：CK_CNT_OV=CK_CNT/(ARR+1)=CK_PSC/(PSC+1)/(ARR+1),即溢出频率=计数器计数频率/(自动重装值+1)。**

> **TIM定时中断（Timer.c的代码）**

==需要掌握的库函数：==

- **void TIM_DeInit(TIM_TypeDef* TIMx);（恢复缺省配置）**
- **void TIM_TimeBaseInit(TIM_TypeDef* TIMx, TIM_TimeBaseInitTypeDef* TIM_TimeBaseInitStruct);（时基单元初始化）**
- **void TIM_TimeBaseStructInit(TIM_TimeBaseInitTypeDef* TIM_TimeBaseInitStruct);（给时基单元的结构体变量赋默认值）**
- **void TIM_Cmd(TIM_TypeDef* TIMx, FunctionalState NewState);（使能计数器）**
- **void TIM_ITConfig(TIM_TypeDef* TIMx, uint16_t TIM_IT, FunctionalState NewState);（使能中断输出信号）**

==时基单元的时钟源选择函数：==

- **void TIM_InternalClockConfig(TIM_TypeDef* TIMx);（选择内部时钟）**
- **void TIM_ITRxExternalClockConfig(TIM_TypeDef* TIMx, uint16_t TIM_InputTriggerSource);（选择ITRx其他定时器的时钟）**
- **void TIM_TIxExternalClockConfig(TIM_TypeDef* TIMx, uint16_t TIM_TIxExternalCLKSource,**
                                    **uint16_t TIM_ICPolarity, uint16_t ICFilter);（选择TIx捕获通道的时钟）**
- **void TIM_ETRClockMode1Config(TIM_TypeDef* TIMx, uint16_t TIM_ExtTRGPrescaler, uint16_t TIM_ExtTRGPolarity,**
                                 **uint16_t ExtTRGFilter);（选择ETR通过外部时钟模式1输入的时钟）**
- **void TIM_ETRClockMode2Config(TIM_TypeDef* TIMx, uint16_t TIM_ExtTRGPrescaler,** 
                                 **uint16_t TIM_ExtTRGPolarity, uint16_t ExtTRGFilter);（选择ETR通过外部时钟模式2输入的时钟）**
- **void TIM_ETRConfig(TIM_TypeDef* TIMx, uint16_t TIM_ExtTRGPrescaler, uint16_t TIM_ExtTRGPolarity,**
                       **uint16_t ExtTRGFilter);（用于配置ETR引脚的预分频器、极性、滤波器等参数）**

```c++

#include "stm32f10x.h"

void Timer_Init(void)
{
    RCC_APB1PeriphClockCmd(RCC_APB1Periph_TIM2,ENABLE);	               //开启APB1总线的时钟，使能TIM2（通用定时器）
    TIM_InternalClockConfig(TIM2);						             //配置时基单元的时钟源为内部时钟
     
    //配置时基单元的结构体
    TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStructure;				 
    TIM_TimeBaseInitStructure.TIM_ClockDivision=TIM_CKD_DIV1;					  //配置滤波器的采样频率，与时基单元没有很大的关系
    TIM_TimeBaseInitStructure.TIM_CounterMode=TIM_CounterMode_Up;				  //选择计数模式为向上计数
	TIM_TimeBaseInitStructure.TIM_Period=10000-1;								//设置ARR自动重装器的值
	TIM_TimeBaseInitStructure.TIM_Prescaler=7200-1;								//设置PSC预分频器的值
	TIM_TimeBaseInitStructure.TIM_RepetitionCounter=0;							//设置重复计数器的值(高级定时器才有的配置，使用通用定时器时直接设置为0)
	//用时基单元结构体配置时基单元
    TIM_TimeBaseInit(TIM2,&TIM_TimeBaseInitStructure);
    //使能中断
	TIM_ITConfig(TIM2,TIM_IT_Update/*更新中断*/,ENABLE);
    //配置NVIC
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);						//选择NVIC优先级分组为分组2
	//配置NVIC初始化的结构体
    NVIC_InitTypeDef NVIC_InitStructure;							  			  
	NVIC_InitStructure.NVIC_IRQChannel=TIM2_IRQn;						//设置TIM2在NVIC中的中断通道
	NVIC_InitStructure.NVIC_IRQChannelCmd=ENABLE;						//使能该通道
	NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority=1;				 //设置中断的抢占优先级
	NVIC_InitStructure.NVIC_IRQChannelSubPriority=1;					//设置中断的相应优先级
    //用NVIC初始化结构体初始化NVIC
	NVIC_Init(&NVIC_InitStructure);
    //启动定时器
    TIM_Cmd(TIM2,ENABLE);
}

//配置中断函数
void TIM2_IRQHandler(void){
	if(TIM_GetITStatus(TIM2,TIM_IT_Update)==SET){     					//设置中断标志位
		NUM++;
		TIM_ClearITPendingBit(TIM2,TIM_IT_Update);					   //清除中断标志位
	}
}

```

## 二、TIM输出比较

------

> **输出比较（OC）**

1. **OC（Output Compare）**:输出比较。
2. 输出比较可以通过**比较CNT和CCR寄存器值**的关系，来对输出电平进行**置1、置0或翻转**的操作，用于输出**一定频率和占空比的PWM波形**。
3. 每个高级定时器和通用定时器都拥有**4个输出比较通道**。
4. 高级定时器的前3个通道额外拥有**死区生成和互补输出**的功能。

**输出比较模式：**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250313175702240.png" alt="image-20250313175702240" style="zoom:80%;" />



> **PWM**

- PWM**(Pulse Width Modulation)** 脉冲宽度调制。
- 在具有惯性的系统中，可以通过对一系列脉冲的宽度进行调制，来等效地获得所需要的模拟参量，常用于**电机控速**等领域。
- **PWM参数：**

**频率=1/Ts	占空比=Ton/Ts	分辨率=占空比变化步距**

![image-20250313175222513](https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250313175222513.png)

**OC实现PWM波形输出模式图：**



<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250313175834507.png" alt="image-20250313175834507" style="zoom:67%;" />



**参数计算：**

- **PWM频率：Freq=CK_PSC/(PSC+1)/(ARR+1),即计数器的更新频率**
- **PWM占空比:Duty=CCR/(ARR+1)**
- **PWM分辨率:Reso=1/(ARR+1)**

> **PWM驱动电机（PWM.c和Motor.c的代码）**

需要掌握的库函数：

- **void TIM_OC1Init(TIM_TypeDef* TIMx, TIM_OCInitTypeDef* TIM_OCInitStruct);（配置输出比较模块）**
- **void TIM_OCStructInit(TIM_OCInitTypeDef* TIM_OCInitStruct);（为输出比较模块的结构体赋默认值）**
- **void TIM_SetCompare1(TIM_TypeDef* TIMx, uint16_t Compare1);（更改CCR寄存器值，用于更改PWM的占空比）**

```c++
/*PWM.c代码*/
#include "stm32f10x.h"

void PWM_Init(void)
{	
    //配置定时器
    RCC_APB1PeriphClockCmd(RCC_APB1Periph_TIM2,ENABLE);	               //开启APB1总线的时钟，使能TIM2（通用定时器）
    TIM_InternalClockConfig(TIM2);						             //配置时基单元的时钟源为内部时钟
     
    //配置时基单元的结构体
    TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStructure;				 
    TIM_TimeBaseInitStructure.TIM_ClockDivision=TIM_CKD_DIV1;					  //配置滤波器的采样频率，与时基单元没有很大的关系
    TIM_TimeBaseInitStructure.TIM_CounterMode=TIM_CounterMode_Up;				  //选择计数模式为向上计数
	TIM_TimeBaseInitStructure.TIM_Period=10000-1;								//设置ARR自动重装器的值
	TIM_TimeBaseInitStructure.TIM_Prescaler=7200-1;								//设置PSC预分频器的值
	TIM_TimeBaseInitStructure.TIM_RepetitionCounter=0;							//设置重复计数器的值(高级定时器才有的配置，使用通用定时器时直接设置为0)
	//用时基单元结构体配置时基单元
    TIM_TimeBaseInit(TIM2,&TIM_TimeBaseInitStructure);
    
    //配置输出比较单元
    TIM_OCInitTypeDef TIM_OCInitStructure;
    TIM_OCStructInit(&TIM_OCInitStructure)										/*为结构体变量赋初始值，虽然高级定时器的OC通道一些功能我们不会使用，但是																				不配置完全结构体成员可能会使得运行结果出错*/
	TIM_OCInitStructure.TIM_OCMode=TIM_OCMode_PWM1;								//设置输出比较的模式
	TIM_OCInitStructure.TIM_OCPolarity=TIM_OCPolarity_High;						//设置输出比较的极性
	TIM_OCInitStructure.TIM_OutputState=TIM_OutputState_Enable;					//设置输出使能
	TIM_OCInitStructure.TIM_Pulse=0;		 								 //设置CCR
	TIM_OC1Init(TIM2,&TIM_OCInitStructure);
   
    //初始化GPIO口
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);						//开启APB2总线的时钟（GPIO外设位于APB2总线上）
    GPIO_InitTypeDef GPIO_InitStructure;
 	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;								//将GPIO模式设置为复用推挽输出，将输出控制权转移给片上外设
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;							
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0;
 	GPIO_Init(GPIOA, &GPIO_InitStructure);
    
    //启动定时器
    TIM_Cmd(TIM2,ENABLE);
}

void PWM_SetCompare1(uint16_t Compare)										//设置占空比函数
{
    TIM_SetCompare1(TIM2,Compare);
}
```

```c++
/*Servo.c代码*/
#include "stm32f10x.h"
#include "PWM.h"

void Servo_Init(void)
{
    PWM_Init();
}
void Servo_SetAngle(float Angle)									
{
    PWM_SetCompare1(Angle/180*2000+500);							//按线性对应关系编写设置舵机角度的函数
}
```

```c++
/*Motor.c代码*/
#include "stm32f10x.h"
#include "PWM.h"  

void Motor_Init(void)
{
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);						//开启APB2总线的时钟（GPIO外设位于APB2总线上）
    GPIO_InitTypeDef GPIO_InitStructure;
 	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;								
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;							
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_4|GPIO_Pin_5;
 	GPIO_Init(GPIOA, &GPIO_InitStructure);
    PWM_Init();
}

void Motor_SetSpeed(int8_t Speed)
{
    if(Speed>=0)
    {
        GPIO_SetBits(GPIOA,GPIO_Pin_4);
        GPIO_ResetBits(GPIOA,GPIO_Pin_5);
        PWM_SetCompare3(Speed);
    }
    else
    {
        GPIO_SetBits(GPIOA,GPIO_Pin_5);
        GPIO_ResetBits(GPIOA,GPIO_Pin_4);
        PWM_SetCompare3(-Speed);
    }
}
```

