# STM32片上外设

## 通用外设
### GPIO
#### GPIO简介
GPIO，全称 通用输入输出（General Purpose Input/Output）。通过GPIO我们得以将STM32与外部设备的连接以实现各种功能。

#### 工作原理及工作模式
![](./STM32_Pic/GPIO框图.png)

*GPIO硬件框图*

1. 上下拉电阻
    
    通过控制上下拉电阻改变该引脚电压的默认电平
    ```
    上拉(Pull-up): 高电平
    下拉(Pull-down): 低电平
    浮空(No pull-up and no pull-down): 不定电平
    ```
    这里需注意,由于上下拉电阻的供电电压由芯片内部提供，因此输出的电流很小，所以上下拉三种模式一般用于处于输入状态的引脚，而需要输出较大电流时还是需要外部上拉。

    ![](./STM32_Pic/GPIOx_PUPDR.png)
    
    *上拉/下拉寄存器GPIOx_PUPDR*


2. MOS管
    
    通过控制MOS管的通断改变该引脚的输出模式
    * 推挽输出(GPIO_Mode_Out_PP)
    ![](./STM32_Pic/推挽输出.png)

    通过配置输出寄存器控制在INT端输入高电平时，上方的P-MOS导通，下方的N-MOS关闭，对外输出高电平；而输入低电平时，经过反向后，N-MOS导通，P-MOS关闭，对外输出低电平，当引脚高低电平切换时，两个管子轮流导通，P管负责灌电流，N管负责拉电流，其负载能力和开关速度都比普通的方式有很大的提升。

    * 开漏输出
    ![](./STM32_Pic/开漏输出.png)

    这一模式下，上方PMOS不工作，控制INT端输出低电平，NMOS导通，输出接地；而输出高电平，NMOS关闭引脚处于高阻状态，此时电路具有“线与”特性，即只有当多个高阻态开漏输出的引脚接在一起，且外部上拉高电平时，引脚才会输出高电平，否则引脚输出低电平。另外，开漏输出还有可以应用在电平不匹配的情况，比如外部上拉电源5V。
    
    ![](STM32_Pic/GPIOx_ODR.png)
    
    *输出数据寄存器GPIOx_ODR*

    ![]()

    *置位/复位寄存器GPIOx_BSRR*

    对于输出数据寄存器，我们又通过修改置位/复位寄存器修改输出数据寄存器的值。


3. 施密特触发器

    施密特触发器也称肖特基触发器，

4. 复用功能输出
    
    STM32的其他片上外设对GPIO引脚进行控制，此时GPIO引脚用作该外设的一部分，作第二用途使用。例如，我们在使用UART串口通讯时，就会将GPIO引脚复用为UART，并通过配置UART的寄存器控制该引脚。

5. 复用功能输入
    
    与复用功能输出类似

6. 模拟输入输出
    
    该引脚用作模拟输入功能时，输入的信号不经过施密特触发器（一种电压比较器），即输入到片上的信号为模拟信号，我们常用于ADC采集信号；而用于模拟输出功能时，输出的信号不经过双MOS管结构，直接将模拟信号输出至引脚。值得注意的是，引脚作为模拟输入输出时，引脚的上下拉电阻失效。

7. 中断输入

#### 实际操作
1. 寄存器操作
    
    ![](./STM32_Pic/STM32f103x8_xB_GPIO寄存器.png)
    
    *stm32存储器映像*

    GPIO外设，修改端口高低控制寄存器GPIOx_CRH和GPIOx_CRL可以配置每个GPIO的工作模式和速度，每四位控制一个IO，CRL控制端口的低8位，CRH控制端口的高8位

    ![]()

    *端口配置低寄存器GPIOx_CRL*

    ![]()

    *端口配置高寄存器GPIOx_CRH*

2. 标准库操作
    
    ```c
    typedef enum
    { 
      GPIO_Speed_10MHz = 1,
      GPIO_Speed_2MHz, 
      GPIO_Speed_50MHz
    }GPIOSpeed_TypeDef;

    typedef enum
    { GPIO_Mode_AIN = 0x0,              //模拟输入
      GPIO_Mode_IN_FLOATING = 0x04,     //浮空输入
      GPIO_Mode_IPD = 0x28,             //下拉输入
      GPIO_Mode_IPU = 0x48,             //上拉输入
      GPIO_Mode_Out_OD = 0x14,          //开漏输出
      GPIO_Mode_Out_PP = 0x10,          //推挽输出
      GPIO_Mode_AF_OD = 0x1C,           //复用开漏输出
      GPIO_Mode_AF_PP = 0x18            //复用推挽输出
    }GPIOMode_TypeDef;

    typedef struct
    {
      uint16_t GPIO_Pin;                //结构体元素1：GPIO_Pin
      GPIOSpeed_TypeDef GPIO_Speed;     //结构体元素3: GPIO_Speed
      GPIOMode_TypeDef GPIO_Mode;       //结构体元素2：GPIO_Mode
      }GPIO_InitTypeDef;
    ```

    ```c
    /* 初始化GPIO引脚 */
    GPIO_InitTypeDef  GPIO_InitStructure;                   //开启GPIO初始化的结构体

    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);   //使能APB2下的PORTA端口时钟
	
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_13;               //PC13端口配置
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;        //配置为推挽输出
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;       //设置IO口速度为50MHz
    GPIO_Init(GPIOC, &GPIO_InitStructure);                  //结构体根据设定参数初始化GPIOC13
    
    GPIO_SetBits(GPIOC,GPIO_Pin_13);	                        //PC13 输出高
    GPIO_ResetBits(GPIOC,GPIO_Pin_13);	                        //PC13 输出高
    ```

3. HAL库操作
   
   * Cube端配置

    ![](./STM32_Pic/GPIO_HAL配置1.png)
    
    *引脚开启*

    ![](./STM32_Pic/GPIO_HAL配置2.png)
    
    *引脚配置*

    ```
    output level //引脚输出电平
    mode //引脚输出模式（开漏/推挽）
    Pull-up/Pull-down //拉高/拉低
    output speed //IO口速度
    ```

### NVIC&EXTI&Systick
#### NVIC简介及EXTI概念引入
NVIC全称是Nested vectoredinterrupt controller，即嵌套向量中断控制器。

* 什么是“中断"？什么是中断服务函数？

    我们可以将中断从字面意思上理解，就是A程序被另外一个事件打断不再进行而去执行B程序。
    
    以饭馆吃饭为例，使用中断就是饭做好了，服务员会为你端上来，然后你开始吃饭（程序B），这种情况下服务员端上来之前你爱干啥就干啥（程序A）；但如果不使用中断，你需要一次一次去问服务员饭做好了没有，这期间你没办法去做其他事情。
    ```c
    伪代码如下：
    /*不使用中断*/
    while(1){
        （询问服务员饭做好了没）
        if (饭做好了){
            （吃饭）
        }
    }
    /*使用中断*/
    [设置中断]（如果服务员把饭端上来了就进入中断服务函数）

    while{
        （干别的事）
    }

    （中断服务函数）
    ```
    那么理解了中断之后，我们再研究NVIC，即将不同的中断进行嵌套，并且与向量一样，有大小和方向，我们可以理解为配置每个中断的优先级。

* 中断的类型
    在微控制器中，我们将中断分为两种：芯片内核产生的中断，称为异常，也称内核中断；而芯片片上外设产生的中断称为外部中断，比如GPIO引脚电平变化、定时器溢出等。

    NVIC与Cortex-M内核有着密不可分的联系，然而基于内核发挥作用的STM32片上外设不需要用上Cortex-M内核高达256种的中断类型。如STM32F1的小、中、大容量产品就仅仅有10个内核中断和60个外部中断，如图为STM32F1的10个内核中断。在这里我们可以理解为优先级数值越小，该中断的优先级越高，也即当多个中断发生时系统需先处理优先级高的中断。

    ![](./STM32_Pic/STM32普通系列内核中断.png)

    *STM32F10xxx产品(小容量、中容量和大容量)的内核中断向量表*

    对于内核中断，从图中我们可以看到，除了前三个异常以外，剩下的异常都可以直接通过NVIC进行控制（NVIC的控制原理参考《Cortex-M3内核编程手册》）

    对于外部中断，我们又分为两类：一类其中断源来自外部引脚GPIO或复用的外部引脚AFIO，我们用外部中断/事件控制器(EXTI)进行控制；另一类为中断源来自不挂载在GPIO下的引脚。

    ![](./STM32_Pic/STM32中断.png)

    *外部中断类型*
    *注：此处的内部中断源包括了可编程的内核中断systick和非GPIO中断，其中的systick不属于外部中断，不要被图片误导*

#### EXTI工作原理
综上述，我们知道了EXTI，全称External interrupt/event controller，即外部中断/事件控制器。下面我们研究这一控制器是如何工作的。

![](./STM32_Pic/EXTI框图.jpg)

*STM32F1的EXTI工作框图*

EXTI具有两大功能：产生中断与产生事件。（什么叫事件？）

输入线通过寄存器设定为某个GPIO/AFIO或某个事件，输入的通常为一存在电平变化的信号。
1. 当此时输入的信号为GPIO/AFIO，
2. 当此时输入的信号为事件，

### DMA

## 总线外设
### UART/USART
#### UART简介
