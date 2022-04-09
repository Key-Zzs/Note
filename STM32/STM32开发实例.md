# STM32开发实例

## 点灯|blink|呼吸灯

### 点灯

#### 寄存器方式驱动

由于寄存器操作实在过于复杂，我们只需要清楚他的原理和操作过程即可，不必将其实际运用于实际开发中。

```
/*片上外设基地址*/
#define PERIPH_BASE         ((unsigned int)0x40000000) 

/*总线基地址*/
#define APB2PERIPH_BASE     (PERIPH_BASE + 0x10000)
#define AHBPERIPH_BASE	    (PERIPH_BASE + 0x20000)

/*GPIO外设基地址*/
#define GPIOC_BASE          (APB2PERIPH_BASE + 0x1000)

/*GPIO寄存器地址:BASE+偏移地址(强转指针)*/
#define GPIOC_CRL           *(unsigned int*)(GPIOC_BASE + 0x00)
#define GPIOC_CRH           *(unsigned int*)(GPIOC_BASE + 0x04)
#define GPIOC_ODR           *(unsigned int*)(GPIOC_BASE + 0x0C)

/*RCC外设基地址*/
#define RCC_BASE            (AHBPERIPH_BASE + 0x1000)

/*RCC时钟是能寄存器*/
#define RCC_AHBENR          *(unsigned int*)(RCC_BASE + 0x14)
#define RCC_APB2ENR         *(unsigned int*)(RCC_BASE + 0x18)
```


```
#include "stm32f10x.h"
void SystemInit(void){
}

int main(){
	
	//开启GPIO端口时钟
	RCC_APB2ENR |= (1<<4);
	
	//清空端口控制位
	GPIOC_CRH &= ~(0x0F<<(4*0));
	
	//配置为通用推挽输出，速度50M
	GPIOC_CRH |= 3 << (4*5);
	
	//输出高电平
	GPIOC_ODR |= (2<<4*3); 

	while (1);
}
```

#### 标准库方式驱动

```
```

### blink

### 呼吸灯


## 屏幕驱动


## 摄像头驱动


## MPU6050数据获取


## 电机驱动

### 有刷电机驱动

### 舵机驱动

### 步进电机驱动

### 无刷电机驱动
