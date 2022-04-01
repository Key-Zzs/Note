# 工程文件创建

## 工程结构解读

1. startup_stm32f10x_hd.s

* 初始化
* 设置堆、栈大小
* 配置SRAM作为数据储存器
* 调用SystemInit()
* 设置C库的分支入口"__main"

## 寄存器版本

### 参见《STM32库开发实战指南》第6章

### 遇到的bug

1. test.axf: Error: L6218E: Undefined symbol SystemInit (referred from startup_stm32f10x_hd.o).`

方法一：在main.c文件中添加SystemInit空函数
```
void SystemInit(void){
}
```

方法二：在startup_stm32f10x_hd.s文件中搜索一下SystemInit，找到一下代码，并将其中三句省略
```
Reset_Handler PROC
EXPORT Reset_Handler [WEAK]
IMPORT __main
;IMPORT SystemInit
;LDR R0, =SystemInit
;BLX R0
LDR R0, =__main
BX R0
ENDP
```
前提是比较简单的小工程不需要用到SystemInit,如果要用到SystemInit的话还是要在合适的位置加上SystemInit的函数定义。

2. Error: L6218E: Undefined symbol main (referred from entry9a.o)

第一种情况：如果main函数书写时出错，把main写mian；

第二种情况：如果在建立工程时未把main.c或是写main函数的文件添加到工程文件；

第三种情况：未编写main函数时也会出现。
