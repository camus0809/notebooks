#  0. 预备工作

STM32是ST公司基于ARM Cortex-M内核开发的32位微控制器

![image-20251109235122289](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1109/image-20251109235122289.png "stm32f103c8t6引脚分布图")

- 特殊功能引脚
  - VDD 供电+
  - VSS 供电-
  - NRST 复位
  - VBAT 备用电源
  - BOOT 启动模式选择
- 

## 0.1 片上资源/外设 peripheral

> 深色位Cortex-M3内核中的外设，浅色位外部外设

![image-20251103164856606](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1103/image-20251103164856606.png)

## 0.2 STM32系列芯片命名规则

==E.G. STM32C8T6==

- STM： 产品系列 基于ARM核心的32位微控制器
- F： 产品类型 通用类型
- 103：产品子系列
  - 101 基本型
  - 102 USB基本型 USB 2.0全速设备
  - 103 增强型
  - 105或107 互联网型
- C：引脚数目
  - T 36 pin
  - C 48 pin
  - R 64 pin
  - v 100 pin
  - z 144 pin
- 8：闪存存储器容量
  - 4 16K字节
  - 6 32K字节
  - 8 64K字节
  - B 128K字节
  - C 256K字节
  - D 384K字节
  - E 512K字节
- T：封装
  - H BGA
  - I UFBGA
  - T LQFP
  - U VFQFPN
  - Y WLCSP64
- 6：温度范围
  - 6 工业级温度范围 -40~85°C
  - 7 工业级温度范围 -40~105°C

## 0.3 器件一览

- 系统结构

![image-20251103165638083](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1103/image-20251103165638083.png)

1. ICode：加载指令 
2. DCode：加载数据，例如**常量和调试数据**
3. System：协调内核和DMA的访问
4. DMA：此总线将DMA的AHB主控接口与总线矩阵相联，总线矩阵协调着CPU的DCode和DMA到
   SRAM、闪存和外设的访问。
5. 总线矩阵：协调内核系统总线和DMA主控总线之间的访问仲裁，仲裁利用轮换算法
6. AHB/APB桥(APB)：两个AHB/APB桥在AHB和2个APB总线间提供同步连接。APB1操作速度限于36MHz，APB2操作于全速(最高72MHz)。

## 0.4 最小系统电路

![image-20251103170417355](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1103/image-20251103170417355.png)

- 0.1uF 滤波电容

- 如需使用`RTC`功能，则需在3、4引脚按照同样的方式接上==32.768KHz==的晶振
  - `OSC32`即32.768KHz
  - 可以生成1s的时间信号

![image-20251103170503066](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1103/image-20251103170503066.png)

## 0.5 型号分类即缩写

| 缩写   | 释义               | Flash容量   | 型号                  |
| ------ | ------------------ | ----------- | --------------------- |
| LD_VL  | 小容量产品超值系列 | 16~32K      | STM32F100             |
| MD_VL  | 中容量产品超值系列 | 64~128K     | STM32F100             |
| HD_VL  | 大容量产品超值系列 | 256~512K    | STM32F100             |
| LD     | 小容量产品         | 16~32K      | STM32F101/102/103     |
| **MD** | **中容量产品**     | **64~128K** | **STM32F101/102/103** |
| HD     | 大容量产品         | 256~512K    | STM32F101/102/103     |
| XL     | 加大容量产品       | 大于512K    | STM32F101/102/103     |
| CL     | 互联型产品         | -           | STM32F105/107         |



## 0.6 新建工程步骤

1. 创建工程文件夹，keil中新建工程，选择型号
2. 复制固件库中对应的文件到工程文件夹中新建的`Library、User、Start`文件夹下
3. 在keil中新建`Library、User、Start`分组，将对应文件夹下的内容添加到工程分组中
4. 工程选项中的`C/C++`，在`Include Paths`内声明所有包含有头文件的文件夹
5. 工程选项中的`C/C++`，在`Define`内定义`USER_STDPERIPH_DRIVER`
6. 工程选项中的`Debug`下拉列表选择`STLink Debugger`，`Settings`中的`Flash Download`里勾选`Reset and Run`



## 0.7 第一个测试程序

```c
#include "stm32f10x.h"                  // Device header

int main()
{
	
//	直接使用寄存器点亮PC13
//	RCC->APB2ENR = 0x00000010;
//	GPIOC->CRH = 0x00300000;
//	GPIOC->ODR = 0x00000000;
	
	// 板载LED为PC13
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_13;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOC, &GPIO_InitStructure);
//	GPIO_SetBits(GPIOC, GPIO_Pin_13);
	GPIO_ResetBits(GPIOC, GPIO_Pin_13); 
	while(1)
	{
	}
}

```


1. 由于GPIO都是APB2外设，需要先配置RCC中的APB2外设时钟使能寄存器`RCC_APB2ENR`

![image-20251103182754841](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1103/image-20251103182754841.png "参考手册P27 2.1系统架构下的AHB/APB桥")

![image-20251103183240953](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1103/image-20251103183240953.png "参考手册P96 7.3.7")

由参考手册说明，位4置1即为打开IO端口C时钟，故配置`RCC->APB2ENR=0x00000010`[^1]

![image-20251103184632960](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1103/image-20251103184632960.png)

由于板载的LED引脚为PC13，因此这里配置`GPIOC_CRH`（CNF需配置为通用推挽输出模式，MODE需配置为输出模式，最大速度为50MHz即0 x00300000）

![image-20251103185921542](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025%2F1103%2Fimage-20251103185921542.png "手册P115 8.2.4 端口输出数据寄存器")

由图示即手册可知

```c
GPIOC->ODR=0x00000000;	// 配置PC13=0
GPIOC->ODR=0x00002000;	// 配置PC13=1
```



# 1. GPIO

General purpose input output 通用输入输出端口

STM32F103C8T6的GPIO包括

- GPIOA： PA0-PA15
- GPIOB： PB0-PB15
- GPIOC：PC13-PC15
- GPIOD：PD0 PD1

输出速度：

1. IO的最大输出速度：向IO交替写0和1且输出不失真的最快速度
2. 上升时间、保持时间和下降时间

![image-20251110002534445](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1110/image-20251110002534445.png)

3. 限制最大输出速度的因素

![image-20251110002732346](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1110/image-20251110002732346.png)

即上升时间和下降时间的长度

4. ​	选择最大输出速度的原因

![image-20251110002905820](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1110/image-20251110002905820.png)

应当选择满足要求的最小值

过于陡峭的边沿会增加功耗，并引入EMI问题 *电磁干扰*

作用：负责采集外部器件的信息或者控制外部器件工作

![image-20251107161509128](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1107/image-20251107161509128.png)

松手为高电平，按下为低电平 --> ==因为按下之后回路闭合形成一个新的、电阻更低的电流通路到地==

## 1.1 GPIO特点

1. 不同型号的IO数量可能不同，可以通过选型手册快速查询
2. 快速翻转，每次翻转最快只需2个时钟周期
3. 每个IO都可以用作中断
4. 支持8种工作模式

## 1.2 GPIO电气特性

1. GPIO工作电压范围：2v到3.6v
2. GPIO识别电压范围：
   - CMOS端口：-0.3到1.164v为低电平，1.833到3.6v为高电平
   - TTL端口：0到0.8v为低电平，2.4到5v为高电平
3. 输出电流：单个IO，最大25mA

## 1.3 GPIO引脚分布

电源引脚、晶振引脚、复位引脚、下载引脚、BOOT引脚、GPIO引脚

## 1.4 GPIO基本结构

![image-20251103192723852](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1103/image-20251103192723852.png)

## 1.5 GPIO位结构

![image-20251103193855485](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1103/image-20251103193855485.png)

> [!note]
>
> 最右边的保护二极管

1. 输入浮空：输入用，完全浮空，状态不定

   - 上拉电阻关闭
   - 下拉电阻关闭
   - 施密特触发器打开
   - 双MOS管不导通

   ==空闲时（高阻态），IO状态不确定，由外部环境决定==

   ![image-20251105000210998](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1105/image-20251105000210998.png)

2. 输入上拉：输入用，用内部上拉，默认为高电平

   - 上拉电阻开启
   - 下拉电阻关闭
   - 斯密特触发器打开
   - 双MOS管不导通

   ![image-20251105000333664](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1105/image-20251105000333664.png)

3. 输入下拉：输入用，用内部下拉，默认为低电平

   - 上拉电阻关闭
   - 下拉电阻开启
   - 施密特触发器开启
   - 双MOS管不导通

   ![image-20251105000417302](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1105/image-20251105000417302.png)

4. 模拟输入：ADC、DAC

   - 上拉电阻关闭
   - 下拉电阻关闭
   - 施密特触发器关闭
   - 双MOS管不导通

   ![image-20251105000500028](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1105/image-20251105000500028.png)

> [!important]
>
> 输出模式可以读取引脚电压
>
> ==输出控制为反相器==，开漏输出模式由于P-MOS管不导通，所以因始终接上高电平

5. 开漏输出：软件IIC的SDL、SCL等

   ==不能输出高电平，必须有外部（或内部）上拉才能输出高电平==

   - 上拉电阻关闭
   - 下拉电阻关闭
   - 施密特触发器打开
   - P-MOS始终不导通
   - 往ODR写0,N-MOS导通；写1不导通

![image-20251107232738467](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1107/image-20251107232738467.png)

5. 推挽输出：驱动能力强，25mA（max），通用输出
   - 上拉电阻关闭
   - 下拉电阻关闭
   - 施密特触发器打开
   - 往ODR写0，N-MOS导通
   - 往ODR写1,P-MOS导通

![image-20251107234534160](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1107/image-20251107234534160.png)

5. 开漏复用：片上外设功能（硬件IIC的SDL和SCL引脚等）

   - 上拉电阻关闭
   - 下拉电阻关闭
   - 施密特触发器打开
   - P-MOS管始终不导通

   同上述的开漏输出模式，且由==其他外设控制==

![image-20251107232829066](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1107/image-20251107232829066.png)

5. 推挽复用：片上外设功能（SPI的SCK、MISO、MOSI引脚等）
   - 上拉电阻关闭
   - 下拉电阻关闭
   - 施密特触发器开启

![image-20251107235503748](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1107/image-20251107235503748.png)

> [!note]
>
> <a id="GPIO_Mode">GPIO的八种模式：</a>
>
> - `GPIO_MODE_AIN`				 模拟输入
> - `GPIO_MODE_IN_FLOATING`		输入浮空
> - `GPIO_MODE_IPD`				下拉输入
> - `GPIO_MODE_IPU`				上拉输入
> - `GPIO_MODE_OUT_OD`			  开漏输出
> - `GPIO_MODE_OUT_PP`			  推挽输出
> - `GPIO_MODE_AF_OD`			    开漏复用
> - `GPIO_MODE_AF_PP`			    推挽复用

## 1.6 GPIO寄存器 （GPIOx_yyy）

- CRL与CRH：配置工作模式，输出速度
  - 4bit控制一个IO口
- IDR：输入数据
- ODR：输出数据
- BSRR：设置ODR寄存器的值
- LCKR：配置锁定

> [!note]
>
> BSRR和ODR的区别在于：ODR可读可写，故在读与修改访问之间产生中断时，可能会有风险；而BSRR则没有这个风险
>
> ODR修改：读 --> 改 --> 写
>
> BSRR修改：	      --> 写

## 1.7 通用外设驱动模型（四步法）

1. 初始化 时钟设置、参数设置、*IO设置、中断设置（开中断、设置NVIC）*
1. 读函数（可选） 从外设读取数据
1. 写函数（可选）往外设写入数据
1. 中断服务函数（可选） 根据中断标志，处理外设各种中断事务

==配置步骤==

1. 使能时钟 `RCC_APB2PeriphClockCmd(uint32_t RCC_APB2Periph, FunctionalState NewState)`

2. 设置工作模式  配置`GPIO_InitTypeDef`结构体后，使用`GPIO_Init(GPIO_TypeDef* GPIOx, GPIO_InitTypeDef* GPIO_InitStruct)`初始化工作模式
   - `GPIO_InitTypeDef`
     - GPIO_Pin：`GPIO_Pin_y` (y=0...15)
     - GPIO_Mode：<a href="#GPIO_Mode">点击查看</a>
     - GPIO_Speed：`GPIO_Speed_rMHz` (r=2, 10, 50)
   
3. 设置输出状态（可选）
   - `GPIO_SetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)`：设置为1
   
   - `GPIO_ResetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)`：设置为0
   
   - `GPIO_WriteBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin, BitAction BitVal)`，设置为BitVal
   
     - BitVal	--> Bit_RESET
   
       ​		  -->  Bit_SET
   
   - `GPIO_Write(GPIO_TypeDef* GPIOx, uint16_t PortVal)`，设置整个GPIOx口
   
4. 设置输入状态（可选）
   - `GPIO_ReadInputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)`，读取GPIOx中的GPIO_Pin的输入电平
   - `GPIO_ReadInputData(GPIO_TypeDef* GPIOx)`，读取GPIOx中16位的输入电平
   - `GPIO_ReadOutputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)`，读取GPIOx中的GPIO_Pin的输出电平
   - `GPIO_ReadOutputData(GPIO_TypeDef* GPIOx)`，读取GPIOx中16位的输出电平



# 2. EXTI外部中断

External Interrupt and Event Controller 外部中断和时间控制器

## 2.1 中断系统

中断是指**在主程序运行过程中**，出现在**特定的中断触发条件（中断源）**，使得CPU暂停当前正在运行的程序，转去处理中断程序，处理好后又返回到被暂停的位置继续运行

中断优先级：当有多个中断源**同时申请中断**，CPU会根据中断源的轻重缓急进行裁决，优先响应更加紧急的中断源 (==自行设定==)

中断嵌套：当一个中断程序**正在运行**时，又有新的更高优先级的中断程序申请中断，CPU再次暂停当前中断程序，转而处理优先级更高的中断程序，处理好后依次进行返回

![image-20251108181014684](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1108/image-20251108181014684.png "左为中断 右为中断嵌套")

## 2.2 STM32中断

- 68个可屏蔽中断通道[^2]，包含EXTI、TIM、ADC、USART、SPI、IIC、DMA、CAN、RTC等多个外设

- 使用NVIC[^3]统一管理中断，每个中断通道都拥有16个可编程的优先等级，可对优先级进行分组，进一步设置抢占优先级和响应优先级

  - NVIC优先级分组 **==值越小，优先级越高==**

    - 由优先级寄存器的4位决定，着4位可以进行切分，分为高n位的抢占优先级和低4-n位的响应优先级

    - 抢占优先级高的可以中断嵌套，响应优先级高的可以优先排队，抢占优先级和响应优先级均相同的按中断号排队

| 分组方式 | 抢占优先级      | 响应优先级      |
| -------- | --------------- | --------------- |
| 分组0    | 0位，取值为0    | 4位，取值为0~15 |
| 分组1    | 1位，取值为0~1  | 3位，取值为0~7  |
| 分组2    | 2位，取值为0~3  | 2位， 取值为0~3 |
| 分组3    | 3位，取值为0~7  | 1位，取值为0~1  |
| 分组4    | 4位，取值为0~15 | 0位，取值为0    |



## 2.3 EXTI(Extern Interrupt) 

- 可以检测指定GPIO的电平信号，当指定的GPIO口产生电平变化时，EXTI将立即向NVIC发出中断申请，经过NVIC裁决后即可中断CPU主程序，使CPU执行EXTI对应的中断程序

- 支持的触发方式：上升沿/下降沿/双边沿/软件触发
- 支持的GPIO口：所有的，==但相同的Pin不能同时触发中断==
- 通道数：**16**个GPIO_Pin，外加PVD输出、RTC闹钟、USB唤醒、以太网唤醒
- 触发响应方式：中断响应（正常流程，引脚电平变化触发中断）/事件响应（==不会触发中断==，而是触发别的外设操作）

![image-20251108184737989](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1108/image-20251108184737989.png)

![image-20251108185419752](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1108/image-20251108185419752.png)

## 2.4 AFIO 复用IO口

AFIO Alternate Function Input/Output

<!-- 相同的Pin不能同时触发中断的原因 -->

- 主要用于引脚复用功能的选择和重定义
- 在STM32中，AFIO主要完成两个任务：复用功能引脚重映射、中断引脚选择

![image-20251108184956105](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1108/image-20251108184956105.png)

标准库相关函数 --> 没有专门的库文件，存在GPIO的库文件中

- `GPIO_AFIODeInit()`： 复位APIO
- `GPIO_PinLockConfig(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)`: 锁定GPIO配置
- `GPIO_EventOutputConfig(uint8_t GPIO_PortSource, uint8_t GPIO_PinSource)`： AFIO的事件输出
- `GPIO_EventOutputCmd(FunctionalState NewState)`： AFIO的事件输出
- `GPIO_PinRemapConfig(uint32_t GPIO_Remap, FunctionalState NewState)`： 引脚重映射
- `GPIO_EXTILineConfig(uint8_t GPIO_PortSource, uint8_t GPIO_PinSource)`： 配置AFIO的数据选择器，选择需要的中断引脚
- `GPIO_ETH_MediaInterfaceConfig(uint32_t GPIO_ETH_MediaInterface)`： 以太网配置

## 2.5 旋转编码器

用于==测量位置、速度或旋转方向==的装置，当其旋转轴旋转时，其输出端可以输出与旋转速度和方向对应的方波信号，读取方波信号的频率和相位信息即可得知旋转轴的速度和方向

类型：机械触点式、霍尔传感器式、光栅式





# 3. TIM定时器

## 3.1 TIM简介

TIM（Timer）**专门用于定时功能的片上外设**

- 定时器可以对输入的时钟进行计数，并在计数值达到设定值时触发中断
- 16位计数器、预分频器、自动重装寄存器的时基单元，在72MHz计数时钟下可以实现最大59.65s的定时
- 不仅具备基本的定时中断功能，而且还包含内外时钟源选择、输入捕获、输出比较、编码器接口、主从触发模式等功能
- 根据复杂度和应用场景分为高级定时器、通用定时器、基本定时器三种

![image-20251109231929382](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1109/image-20251109231929382.png)

## 3.2 时基单元

![image-20251109233033036](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1109/image-20251109233033036.png)

## 3.3 定时器类型

| 类型       | 编号       | 总线 | 功能                                                         |
| ---------- | ---------- | ---- | ------------------------------------------------------------ |
| 高级定时器 | TIM1、TIM8 | APB2 | 拥有通用定时器全部功能，并额外具有重复计数器、死区生成、互补输出、刹车输入等功能 |
| 通用定时器 | TIM2-TIM5  | APB1 | 拥有基本定时器全部功能，并额外具有内外时钟选择、输入捕获、输出比较、编码器接口、主从触发模式等功能 |
| 基本计时器 | TIM6、TIM7 | APB1 | 拥有定时中断、主模式触发DAC的功能                            |

### 3.3.1 高级控制定时器

1. 由16位的自动装载计数器组成，由一个可编程的预分频器驱动

2. 适用场景：

   - 测量输入信号的脉冲宽度（输入捕获），产生输出波形（输出比较、PWM、嵌入死区时间的互补PWM等）


3. 使用定时器预分频器和RCC时钟控制预分频器可以实现==脉冲宽度和波形周期从几个微秒到几个毫秒的调节==

4. 与通用定时器**完全独立**，不共享任何资源，可以**同步操作**



# 4. 串口通信

<p style="color:red"> 低位先行</p>

Tx/Txd: Transmit 发送引脚

Rx/Rxd: Receive 接收引脚

![image-20251116174944572](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1116/image-20251116174944572.png)

## 4.1 通信协议/数据格式

串口数据帧格式

![image-20251116175355590](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1116/image-20251116175355590.png)

可选设置：

- 数据位的长度：8~9位
- 校验位是否启用：若启用，则数据位的最后一位为校验位
  - 奇校验：要求数据位中有奇数个1
  - 偶校验：要求数据位中有偶数个1

> [!note]
>
> 包含**校验位**

- 停止位的长度：0.5，1，1.5，2

## 4.2 USART的使用方法

<!-- universal synchronous asynchronous receiver transmitter -->

- 通信接口
  - 通信的目的：将一个设备的数据传送到另一个设备，扩展硬件系统
  - 通信协议：制定通信的规则，通信双方按照协议规则进行数据收发

| 名称  | 引脚                 | 双工   | 时钟 | 电平 | 设备   |
| ----- | -------------------- | ------ | ---- | ---- | ------ |
| USART | Tx, Rx               | 全双工 | 异步 | 单端 | 点对点 |
| I2C   | SCL、SDA             | 半双工 | 同步 | 单端 | 多设备 |
| SPI   | SCLK、MOSI、MISO、CS | 全双工 | 同步 | 单端 | 多设备 |
| CAN   | CAN_H、CAN_L         | 半双工 | 异步 | 差分 | 多设备 |
| USB   | DP、DM               | 半双工 | 异步 | 差分 | 点对点 |

- 单端电平需共地
- 常见的电平标准
  - TTL		 +3.3v~+5v --> 1， 	   0v --> 0
  - RS232	 -3v~-15v    -->1, 		+3~+15v --> 0
  - RS485	两线压差+2~+6v表示1， -2~-6v表示0

1. STM32F103C8T6的USART接口

   - APB1: USART2、USART3
   - APB2: USART1

2. USART基本用法

   ![image-20251117213835900](C:/Users/aurora/AppData/Roaming/Typora/typora-user-images/image-20251117213835900.png)

3. 移位寄存器和串并转换

   - 发送时为**并转串**
   - 接受时为**串转并**

4. 数据帧格式的设置方法

   上图所示的控制电路

5. 波特率的设置方法

   ==二进制调制下，波特率与比特率数值相等==

   - 波特率：每秒最多传输多少位
   - 常见的波特率：9600、115200、921600
   - ==分频器==：

6. USART***初始化***编程接口

   - void USART_Init(USART_TypeDef* USARTx, USART_InitTypeDef* USART_InitStruct)

   - USART_InitTypeDef

     - USART_BaudRate
     - USART_WordLength
       - USART_WordLength_8b
       - USART_WordLength_9b
     - USART_StopBits
       - USART_StopBits_0_5
       - USART_StopBits_1
       - USART_StopBits_1_5
       - USART_StopBits_2
     - USART_Parity
       - USART_Parity_No
       - USART_Parity_Even
       - USART_Parity_Odd
     - USART_Mode
       - USART_Mode_Rx
       - USART_Mode_Tx
     - USART_HardwareFlowControl
       - USART_HardwareFlowControl_None
       - USART_HardwareFlowControl_RTS
       - USART_HardwareFlowControl_CTS
       - USART_HardwareFlowControl_RTS_CTS

     ![image-20251118154423980](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1118/image-20251118154423980.png)

7. 引脚分布表 PA9 --> USART1_TX PA10 --> USART1_RX

   ![image-20251118153438917](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1118/image-20251118153438917.png)

8. 重映射表 PB6 --> USART1_TX PB7 --> USART1_RX

   ![image-20251118153540816](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1118/image-20251118153540816.png)

9. 编程接口

   - void USART_Cmd(USARTTypedef *USARTx, FunctionalState NetState)
     - 控制USART模块的使能和禁止
     
   - FlagStatus USART_GetFlagStatus(USART_TypeDef* USARTx, uint16_t USART_FLAG)
     - 查询USART标志位的值
     
     - 返回值：RESET：0 SET：1
     
     - USART_FLAG
       - USART_FLAG_CTS
       
       - USART_FLAG_LBD
       
       - **USART_FLAG_TXE**  Tx
       
         - Transmit Data RegisterEmpty 发送数据寄存器空
       
           当TDR（发送数据寄存器）空时，TxE=1，否则则为0
       
       - **USART_FLAG_TC** Tx
       
         - Transmit Complete 发送完成
       
           当TDR空且移位寄存器空时，TC=1，否则TC=0
       
       - **USART_FLAG_RXNE** Rx
       
         - Receive Data Register Not Empty 接收寄存器非空
       
           当RDR非空时，RxNE=1，否则为0
       
       - USART_FLAG_IDLE
       
       - USART_FLAG_ORE：过载错误
       
         - 读取太慢
       
       - USART_FLAG_NE：噪声错误
       
       - USART_FLAG_FE：帧格式出错
       
         - 最常见的为*检测不到停止位*
       
       - **USART_FLAG_PE**： 奇偶校验出错
     
   - void USART_SendData(USART_TypeDef* USARTx, uint16_t Data)
     - 把要发送的数据写入发送数据寄存器里
     
   - uint16_t USART_ReceiveData(USART_TypeDef* USARTx)

## 4.3 数据包

数据包的作用：把单独的数据打包起来，方便进行多字节的通信

1. HEX数据包

   - 固定包长，含包头包尾 

   ![image-20251119222215646](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1119/image-20251119222215646.png)

   - 可变包长，含包头包尾

   ![image-20251119222223945](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1119/image-20251119222223945.png)

2. 文本数据包

   - 固定包长，含包头包尾

   ![image-20251119222441739](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1119/image-20251119222441739.png)

   - 可变包长，含包头包尾

   ![image-20251119222502418](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1119/image-20251119222502418.png)



# 5. 中断

单片机应对突发事件的一种方式

![image-20251120194322818](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1120/image-20251120194322818.png)

![image-20251120194932560](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1120/image-20251120194932560.png)

## 5.1 中断优先级概念

为使系统能及时响应并处理发生的所有中断，系统根据引起中断事件的重要性和紧迫程度，硬件将[中断源](https://baike.baidu.com/item/中断源/10509482?fromModule=lemma_inlink)分为若干个级别

## 5.2 中断优先级的表示方法

![image-20251120212459079](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1120/image-20251120212459079.png)

## 5.3 抢占优先级与中断嵌套

- 中断嵌套：更高优先级的中断打断正在执行的中断

  ![image-20251120212557677](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1120/image-20251120212557677.png)

## 5.4 子占优先级与中断排队

- 中断排队： 优先级相仿，等待前一个中断执行完再处理新中断

![image-20251120212932746](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1120/image-20251120212932746.png)

# 6. IIC

<p style="color:red" > 高位先行 </p>

- 为什么使用IIC（即串口通信的缺点）
  - 只能实现点对点通信

## 6.1 IIC的电路结构

![image-20251120215808619](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1120/image-20251120215808619.png)

- 从机地址：0000000~1111111 0~127

1. 数据线和时钟线
   - **时钟信号**总是从主机发送给从机
   - **数据信号**可以双向

2. 逻辑线与

![image-20251120222840555](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1120/image-20251120222840555.png)

即有0则0

3. 主机与从机通信

   1. 主机发送时钟信号
      - 让所有的从机的SCL置1
      - 通过主机拉低和释放SCL实现时钟信号的发送

   2. 主机发送数据

      - 让所有从机的SDA置1

      - 通过主机拉低和释放SDA实现数据的发送

   3. 从机发送数据

      - 让主机和不发送数据的所有从机的SDA置1

      - 从机通过拉低和释放SDA实现数据的发送

4. IIC通信协议

![image-20251120223613918](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1120/image-20251120223613918.png)

- 主机通过SDA发送一个字节的数据（包括从机地址和读写）
- 进行数据的通信
- 发送停止位

5. IIC的数据帧格式

![image-20251120223756431](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1120/image-20251120223756431.png)

- 起始位：SCL高电平，拉低SDA
- 停止位：SCL高电平，释放SDA

![image-20251120223938852](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1120/image-20251120223938852.png)

- 7位地址0x00~0x7F 所以传输时
  - 例如寻址0x78（读）	**因为数据手册给出的地址一般是已经左移后包含标志位的8位数据**
    - 0111 100~~0~~
    - 补上读写位 0111 1001
- ACK
  - 即 主机释放SDA，若有从机将SDA拉低，即为应答

![image-20251120225321416](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1120/image-20251120225321416.png)



## 6.2 IIC模块

1. 属于APB1：I2C1、I2C2

   1. 默认：
      - I2C1	SCL -->PB6    SDA --> PB7
      - I2C2        SCL -->PB10  SDA --> PB11

   2. 重映射：

      - I2C1	SCL -->PB8    SDA --> PB9

      - I2C2        无

![image-20251121152828248](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1121/image-20251121152828248.png "中文版 P111")

![image-20251121152913878](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1121/image-20251121152913878.png "RM0008 P168")

2. I2C的速度模式

| 标准模式 | Sm   | Standard Mode | <= 100kbps |
| -------- | ---- | ------------- | ---------- |
| 快速模式 | Fm   | Fast Mode     | <=400kbps  |

- 时钟信号的占空比

  - 快速模式下可以设置**时钟信号**的占空比 （*一般选择2/1*）

  ![image-20251121165354568](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1121/image-20251121165354568.png)
  
  2. 初始化I2C
  
     ```c
     //	#2. 对I2C1进行初始化
     	
     	//	开启I2C1的时钟使能
     	RCC_APB1PeriphClockCmd(RCC_APB1Periph_I2C1, ENABLE);
     	//	施加复位信号
     	RCC_APB1PeriphResetCmd(RCC_APB1Periph_I2C1, ENABLE);
     	//	释放复位信号
     	RCC_APB1PeriphResetCmd(RCC_APB1Periph_I2C1, DISABLE);
     ```
  
     - 开启I2C1使能后先施加复位信号后释放复位信号的原因
  
       - 确保寄存器处于“干净”的默认状态 (Clean Slate)确保寄存器处于“干净”的默认状态 (Clean Slate)
  
       - 重置内部硬件状态机 (如果系统在 I2C 通信过程中突然发生异常
  
         - 例如：正在传输数据时程序被断点暂停了、线路受到干扰导致总线死锁、或者上一次传输未正常结束），I2C 的内部状态机可能会卡在某个中间状态（比如“忙”状态或等待 ACK 状态）。
  
           仅仅修改寄存器配置有时无法让状态机复位。**施加 RCC 复位信号** 就像是给这个外设模块断电重启一样，能强制其内部逻辑逻辑回到初始的 IDLE（空闲）状态，防止初始化后 I2C 依然显示“Bus Busy”
  
       - 防御性编程
  
         - STM32 的 I2C 外设（尤其是 F1 系列）以“容易卡死”和“硬件 bug 多”而著称。
           在初始化之前进行一次完整的复位操作，是一种**防御性编程**的习惯。它能最大程度地消除硬件层面的不确定性，提高系统的**鲁棒性（Robustness）**。
  

3. I2C结构框图

![image-20251121213526435](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1121/image-20251121213526435.png)

- SR： status register

  - SR1

    - SB

      1. 0 --> 起始位未发送
      2. 1 --> 起始位发送完成

    - AF Acknowledge Failure 应答失败  

      <p style="color:red">需要提前清零</p>

      1. 1 --> 未收到ACK

    - ADDR 寻址成功

      1. 1 --> 寻址成功

    - TxE 发送数据寄存器为空

      1. 1 --> 寄存器为空 

    - BTF 表示一个完整的字节已成功发送或接收

      1. 1 --> 成功发送或接收
      
    - RxNE 接收寄存器不为空

      1. 1 -->  

  - SR2

    - BUSY	总线忙标志位
      1. 0 --> 总线空闲 
      2. 1 --> 总线忙

- SDA控制
  - START：发送起始位 写1
  - ACK 应答位   **只作用于正在被接收的字节**
    1. 0 --> 发送NAK
    2. 1 --> 发送ACK
  - STOP 停止位   **当前字节接收完成之后**
    1. 1 --> 发送停止位

## 6.3 软件I2C

- 硬件I2C的缺点
  - 操作繁琐
  - 引脚位置收到限制

![image-20251123225825574](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1123/image-20251123225825574.png)

由于此时是引脚直接控制输出,故因使用通用开漏模式





[^1]: `RCC->APB2ENR`定义在`stm32f10x.h`中，为一个结构体
[^2]: 即中断源
[^3]:  Nested Vectored Interrupt Controller，嵌套向量中断控制器
