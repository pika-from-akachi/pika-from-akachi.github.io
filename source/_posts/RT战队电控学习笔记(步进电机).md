---
title: RT战队电控学习笔记 (步进电机)
date: 2025-04-13
categories: Hardware
tags: [Hardware,Blog,Tech]
mathjax: true
---



# RT战队电控学习笔记(步进电机)

> **作者：重庆大学国家卓越工程师学院 Kaiden Poon**
>
> **时间：2025年4月13日**

**🛸本套笔记为适应Robotac战队电控组代码需求编写，主要涉及STM32单片机的编程实现🚀**

------

## 一、步进电机基础理论

> **步进电机简介**

步进电机是将**电脉冲信号**转变为**角位移或线位移**的**开环控制元件。**在非超载的情况下，电机的转速、停止的位置只取决于**脉冲信号的频率和脉冲数**，而不受负载变化的影响，即给电机一个脉冲信号，电机转过一个**步距角。**

> **步进电机的几个基础概念**

- **相：**指步进电机有几个线圈（绕组）。常见2相、4相电机。
- **线：**指步进电机有几个接线口（多少根线）。
- **极性：**分为单极性和双极性。若步进电机的线圈可以双向导电，则步进电机就是双极性的；相反，若步进电机的线圈只允许单向导电，那么这个步进电机就是单极性的。

这三个概念只要知道其中两个，就可以推断出第三个。

例如**五线四相**步进电机，可以推断出其为**单极性**的步进电机。

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250413202010479.png" alt="image-20250413202010479" style="zoom:67%;" />

- **步进方式：**单拍、双拍、单双拍。

**单拍**（四拍）工作方式即**每次只给一个线圈通电**，通过改变每次通电的线圈从而使步进电机转动。

对于五线四相的步进电机，在单拍工作方式下，线圈的通电方式依次是：**A-COM、B-COM、C-COM、D-COM**。

对于四线双极性步进电机，在单拍工作方式下，线圈的通电方式依次是：**A-A'、B-B'、A‘-A、B’-B**。

需要四拍才能完成一个周期。

**双拍**（四拍）工作方式即每次给两个线圈通电，通过改变通电的线圈从而使步进电机转动。

对于五线四相步进电机，在双拍工作方式下，线圈的通电方式依次是：**A-COM与B-COM、B-COM与C-COM、C-COM与D-COM、D-COM与A-COM**。

对于四线双极性步进电机，在双拍工作方式下，线圈的通电方式依次是：**A-A'与B-B'、B-B'与A'-A、A'-A与B'-B、B'-B与A-A'**。

需要四拍才能完成一个周期。

**单双拍**（八拍）工作方式即单拍工作方式和双拍工作方式交替进行。

对于五线四相步进电机：**A-COM、A-COM与B-COM、B-COM、B-COM与C-COM、C-COM、C-COM与D-COM、D-COM、D-COM与A-COM**。

对于四线双相步进电机：**A-A'、A-A'与B-B'、B-B'、B-B'与A'-A、A'-A、A'-A与B'-B、B'-B、B'-B与A-A'**。

需要八拍才能完成一个周期，电机一拍只转动45°，**运行较为稳定**。

- **步距角：**步进电机切换一次定子绕组的激磁电流时，转子就旋转一个固定角度，即步距角。步距角由**切换的相电流产生的旋转力矩**得到。

步距角的大小与**控制绕组的相数、转子齿数和通电方式**有关。**步距角越小，运转的平稳性越好**。

1. 转子齿数越多，步距角θs越小；
2. 定子相数越多，步距角θs越小；
3. 通电方式的节拍越多，步距角θs越小；

$$
θ_s=\frac{360°}{m*Zr}
$$

m为运行的拍数。常见的两相电机有4拍和8拍运行方式。

Zr为转子的齿数。常见的两相步进电机齿数为50。

- **保持转矩：**指步进电机通电但是没有转动时，定子锁住转子的力矩。它是步进电机最重要的参数之一，**通常步进电机在低速时的力矩接近于保持转矩**。

由于步进电机的输出力矩随速度的增大不断衰减，输出功率也随速度的增大而变化，所以保持转矩就成为衡量步进电机最重要的参数之一。

- **失步（丢步）：**电机运转时**运转的步数不等于理论上的步数**，称为失步。一般发生在**启动和停机阶段**，特别如果**给的脉冲频率过高**也会产生丢步情况。

- **最大空载起动频率：**电机在某种驱动形式、电压及额定电流下，在不加负载的情况下，能够直接起动的最大频率。

- **最大空载运行频率：**电机在某种驱动形式、电压及额定电流下，电机不带负载的最高转速频率。

- **运行矩频特性：**电机在某种测试条件下测得运行中**输出力矩与频率关系的曲线**称为运行矩频特性，这是电机诸多动态曲线中最重要的，也是电机选择的根本依据。

    <img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250413232432828.png" alt="image-20250413232432828" style="zoom:50%;" />

> **步进电机的工作原理**

当电流流过定子绕组时，定子绕组产生一矢量磁场，该磁场会带动转子旋转一角度，使得转子的一对磁场方向与定子的磁场方向一致。当定子的矢量磁场旋转一个角度，转子也随着磁场转一个角度。**每输入一个电脉冲，改变一次定子磁场，电动机转动一个角度前进一步。**它输出的**角位移与输入的脉冲数成正比**、**转速与脉冲频率成正比**。改变绕组通电的顺序，电机就会反转。所以可用控制**脉冲数量、频率及电动机各相绕组的通电顺序**来控制步进电机的转动。

- **单极性驱动的原理**

单极性驱动分为**整步驱动**（四拍）和**半步驱动**（八拍）两大类。

**整步驱动工作原理：**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250413212751491.png" alt="image-20250413212751491" style="zoom: 50%;" />

**半步驱动工作原理：**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250413213130413.png" alt="image-20250413213130413" style="zoom:50%;" />

需要注意的是，在驱动过程中，为了让转子的机械速度能够跟上定子的通电速度，**每驱动一步，都要延时一段时间才能驱动下一步。**改变延时的时间，即可改变速度。但是如果延时时间太短，转子还没转到位，就开始驱动下一步，那么转子就会出现**失步、震荡**的情况。

> **双极性驱动的原理**

双极性驱动分为**整步驱动、半步驱动**和**细分驱动**三大类。

**整步驱动工作原理：**（双极性电机A2端和C2端、B2端和D2端已经在内部联通，所以外部只会有四根引出线。）

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250413222039634.png" alt="image-20250413222039634" style="zoom:50%;" />

与单极性整步驱动相比，有两个线圈被通电，所产生的**转矩会更大**。



**半步驱动工作原理：**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250413222541746.png" alt="image-20250413222541746" style="zoom:50%;" />

同样的，与单极性半步驱动相比，有两个线圈被通电，所产生的**转矩也会更大**。



**细分驱动工作原理：**

细分驱动简介：如图所示，设流过A、C线圈的电流大小为Ia，流过B、D线圈的电流大小为Ib，因为**磁场强度和电流大小成正比**，如果Ia等于Ib，转子将停在相邻的两个线圈的中央，如果Ia不等于Ib，那么转子将停在电流较大的一侧，转子停住时，和水平方向的夹角满足以下关系：
$$
θ=tan^{-1}(\frac{I_a}{I_b})
$$
<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250413223345516.png" alt="image-20250413223345516" style="zoom:50%;" />

细分驱动具有**转动顺畅、精度高、转矩大**的特点，但是控制复杂，一般需要专用的驱动芯片来实现。

> **步进电机驱动芯片**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/968bc4a553dafea84ee59d62054f5a3.jpg" alt="968bc4a553dafea84ee59d62054f5a3" style="zoom:45%;" />

战队使用的步进电机为**四线双极性两相电机**，可以使用驱动芯片进行**细分驱动**以使电机运转得更加平稳。使用TB6600驱动器，内部集成了**TB67S109AFTG芯片**。

==**TB67S109AFTG芯片概览：**==

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250414104402160.png" alt="image-20250414104402160" style="zoom: 67%;" />

- **DMODE：**细分模式，三个引脚，置高/低后有8种组合，可以实现8种细分模式。
- **CW/CCW：**顺时针/逆时针，选择步进电机运行方向。
- **CLK：**时钟。
- **RESET：**复位引脚。
- **ENABLE：**使能引脚。
- **MO：**监视角度后输出，例如整步驱动在完成四拍后输出一个脉冲，半步驱动在完成八拍后输出一个脉冲。
- **LO：**错误输出引脚，监测过温，电流过大等电机错误。
- **RSA/RSB：**电流补偿。
- **OUT：**输出引脚，接电机的四个引脚。

==**TB67S109A芯片功能：**==

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250414105720306.png" alt="image-20250414105720306" style="zoom:50%;" />

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250414111525962.png" alt="image-20250414111525962" style="zoom:50%;" />

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250414135429039.png" alt="image-20250414135429039" style="zoom: 60%;" />

==**步进电机驱动器：**==

战队选用的TB6600步进电机驱动器可实现正反转控制，通过3位拨码开关选择**7档细分控制**(1,2/A,2/B,4,8,16,32),通过3位拨码开关选择**8档电流控制**（0.5A,1A,1.5A,2A,2.5A,2.8A,3.0A,3.5A)。适合驱动**57、42型**两相混合式步进电机。

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250414113752983.png" alt="image-20250414113752983" style="zoom:67%;" />

- **电机绕组连接：**

**A+:**连接电机绕组A+相。	**A-:**连接电机绕组A-相。

**B+:**连接电机绕组B+相。	**B-:**连接电机绕组B-相。

- **电源电压连接：**

**VCC：**电源正端"+"		**GND：**电源负端"-"

注意：DC9-42V。不可以超过此范围，否则会无法正常工作甚至损坏驱动器。

- **信号输入端：**

**PUL+：**脉冲信号输入正。		**PUL-：**脉冲信号输入负。

**DIR+：**电机正、反转控制正。		**DIR-：**电机正、反转控制负。

**EN+：**电机脱机控制正。		**EN-：**电机脱机控制负。



输入信号一共有三路，分别是：1、步进脉冲信号PUL+，PUL-；2、方向电平信号DIR+，DIR-；3、脱机（关闭）信号EN+，EN-。输入信号接口有两种接法，用户可根据需要采用**共阳极接法**或**共阴极接法**。



**共阳极接法：**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250414133122275.png" alt="image-20250414133122275" style="zoom: 67%;" />

**共阴极接法：**

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/image-20250414133413011.png" alt="image-20250414133413011" style="zoom:67%;" />

------

## 二、步进电机旋转控制的代码实现

  **共编写了8个步进电机驱动函数**

- **void Stepping_MOTOR_Init(void);**(步进电机初始化)
- **void Stepping_MOTOR_SetFrequency(uint32_t frequency);**（设置电机运行频率）
- **void Stepping_MOTOR_Rotate(uint32_t revolutions, uint32_t frequency);**（重要函数，设置电机运行圈数及运行频率，传入参数**revolutions:圈数，frequency:频率**）

- **void Stepping_MOTOR_SetDirection(uint8_t direction);**（设置电机转动方向，参数为0或1，表示顺时针或逆时针）
- **void Stepping_MOTOR_Stop(void);**（停止电机）
- **void Stepping_MOTOR_Enable(void);**（使能电机）
- **void Stepping_MOTOR_Disable(void);**（失能电机）
- **void Stepping_MOTOR_ConfigTimer(uint32_t frequency);**（设置电机运行频率，并且保证占空比为50%，上升沿明显）



- **Stepping_MOTOR.c：**

    ```c++
    #include "stm32f10x.h"
    #define STEPS_PER_REVOLUTION 6400 // 每圈步数（1.8°步距角，32细分）
    #define MOTOR_ENABLE_PIN GPIO_Pin_5 // 电机使能引脚
    
    void Stepping_MOTOR_Init(void);
    void Stepping_MOTOR_SetFrequency(uint32_t frequency);
    void Stepping_MOTOR_Rotate(uint32_t revolutions, uint32_t frequency);
    void Stepping_MOTOR_SetDirection(uint8_t direction);
    void Stepping_MOTOR_Stop(void);
    void Stepping_MOTOR_Enable(void);
    void Stepping_MOTOR_Disable(void);
    void Stepping_MOTOR_ConfigTimer(uint32_t frequency);
    
    
    // 初始化步进电机相关的 GPIO 和定时器
    void Stepping_MOTOR_Init(void) {
        // 开启 GPIOA 和 TIM2 的时钟
        RCC_APB1PeriphClockCmd(RCC_APB1Periph_TIM2, ENABLE);
        RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
    
        // 配置 A2 为复用推挽输出，用于 PWM 信号输出
        GPIO_InitTypeDef GPIO_InitStructure;
        GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP; // 复用推挽输出
        GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz; // 输出速度 50MHz
        GPIO_InitStructure.GPIO_Pin = GPIO_Pin_2; // A2 引脚
        GPIO_Init(GPIOA, &GPIO_InitStructure);
    
        // 配置 A3 和 A4 为普通推挽输出，用于方向控制
        GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP; // 普通推挽输出
        GPIO_InitStructure.GPIO_Pin = GPIO_Pin_3 | GPIO_Pin_4; // A3 和 A4 引脚
        GPIO_Init(GPIOA, &GPIO_InitStructure);
    
        // 配置 A5 为普通推挽输出，用于电机使能
        GPIO_InitStructure.GPIO_Pin = MOTOR_ENABLE_PIN; // A5 引脚
        GPIO_Init(GPIOA, &GPIO_InitStructure);
    
        // 默认设置方向为顺时针
        GPIO_SetBits(GPIOA, GPIO_Pin_3); // A3 高电平
        GPIO_ResetBits(GPIOA, GPIO_Pin_4); // A4 低电平
    
        // 默认禁用电机
        GPIO_SetBits(GPIOA, MOTOR_ENABLE_PIN); // A5 高电平，禁用电机
    
        // 配置 TIM2 定时器
        TIM_InternalClockConfig(TIM2);
        TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStructure;
        TIM_TimeBaseInitStructure.TIM_ClockDivision = TIM_CKD_DIV1; // 不分频
        TIM_TimeBaseInitStructure.TIM_CounterMode = TIM_CounterMode_Up; // 向上计数模式
        TIM_TimeBaseInitStructure.TIM_Period = 1000 - 1; // 默认周期
        TIM_TimeBaseInitStructure.TIM_Prescaler = 72 - 1; // 默认预分频器
        TIM_TimeBaseInitStructure.TIM_RepetitionCounter = 0; // 无重复计数
        TIM_TimeBaseInit(TIM2, &TIM_TimeBaseInitStructure);
    
        // 配置 PWM 输出
        TIM_OCInitTypeDef TIM_OCInitStructure;
        TIM_OCStructInit(&TIM_OCInitStructure);
        TIM_OCInitStructure.TIM_OCMode = TIM_OCMode_PWM1; // PWM 模式 1
        TIM_OCInitStructure.TIM_OCPolarity = TIM_OCPolarity_High; // 高电平有效
        TIM_OCInitStructure.TIM_OutputState = TIM_OutputState_Enable; // 启用输出
        TIM_OCInitStructure.TIM_Pulse = 500; // 默认占空比为 50%
        TIM_OC3Init(TIM2, &TIM_OCInitStructure);
    
        // 禁用定时器，保持静止
        TIM_Cmd(TIM2, DISABLE);
    }
    
    // 配置定时器的频率
    void Stepping_MOTOR_ConfigTimer(uint32_t frequency) {
        uint32_t timer_clock = 72000000; // 定时器时钟频率 72MHz
        uint16_t prescaler = 71; // 预分频器值
    
        if (frequency == 0) return;
    
        // 计算定时器周期
        uint32_t period = (timer_clock / (prescaler + 1)) / frequency;
        if (period > 0xFFFF) period = 0xFFFF;
    
        // 配置定时器
        TIM_PrescalerConfig(TIM2, prescaler, TIM_PSCReloadMode_Immediate);
        TIM_SetAutoreload(TIM2, period - 1);
    
        // 设置占空比为 50%
        TIM_SetCompare3(TIM2, period / 2);
    }
    
    // 设置步进电机的频率
    void Stepping_MOTOR_SetFrequency(uint32_t frequency) {
        Stepping_MOTOR_ConfigTimer(frequency); // 配置定时器
        TIM_CCxCmd(TIM2, TIM_Channel_3, TIM_CCx_Enable); // 启用通道
        TIM_Cmd(TIM2, ENABLE); // 启动定时器
    }
    
    // 控制步进电机旋转指定圈数
    void Stepping_MOTOR_Rotate(uint32_t revolutions, uint32_t frequency) {
        uint32_t total_steps = revolutions * STEPS_PER_REVOLUTION;
    
        Stepping_MOTOR_Enable(); // 启用电机
        Stepping_MOTOR_ConfigTimer(frequency); // 配置定时器
        TIM_CCxCmd(TIM2, TIM_Channel_3, TIM_CCx_Enable); // 启用通道
        TIM_Cmd(TIM2, ENABLE); // 启动定时器
    
        for (uint32_t step = 0; step < total_steps; step++) {
            while (TIM_GetFlagStatus(TIM2, TIM_FLAG_Update) == RESET);
            TIM_ClearFlag(TIM2, TIM_FLAG_Update);
        }
    
        Stepping_MOTOR_Stop(); // 停止电机
    }
    
    // 设置步进电机的旋转方向
    void Stepping_MOTOR_SetDirection(uint8_t direction) {
        if (direction == 0) { // 顺时针
            GPIO_SetBits(GPIOA, GPIO_Pin_3);
            GPIO_ResetBits(GPIOA, GPIO_Pin_4);
        } else { // 逆时针
            GPIO_ResetBits(GPIOA, GPIO_Pin_3);
            GPIO_SetBits(GPIOA, GPIO_Pin_4);
        }
    }
    
    // 停止步进电机
    void Stepping_MOTOR_Stop(void) {
        TIM_Cmd(TIM2, DISABLE); // 停止定时器
        TIM_CCxCmd(TIM2, TIM_Channel_3, TIM_CCx_Disable); // 禁用输出通道
        Stepping_MOTOR_Disable(); // 禁用电机
    }
    
    // 启用步进电机
    void Stepping_MOTOR_Enable(void) {
        GPIO_ResetBits(GPIOA, MOTOR_ENABLE_PIN); // A5 低电平，启用电机
    }
    
    // 禁用步进电机
    void Stepping_MOTOR_Disable(void) {
        GPIO_SetBits(GPIOA, MOTOR_ENABLE_PIN); // A5 低电平，禁用电机
    }
    
    
    ```

    