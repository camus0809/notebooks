# 0. 预备工作

STM32是ST公司基于ARM Cortex-M内核开发的32位微控制器

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
> - `GPIO_MODE_IPD`				下拉输出
> - `GPIO_MODE_IPU`				上拉输出
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
   - `GPIO_Write(GPIO_TypeDef* GPIOx, uint16_t PortVal)`，设置整个GPIOx口
4. 设置输入状态（可选）
   - `GPIO_ReadInputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)`，读取GPIOx中的GPIO_Pin的输入电平
   - `GPIO_ReadInputData(GPIO_TypeDef* GPIOx)`，读取GPIOx中16位的输入电平
   - `GPIO_ReadOutputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin)`，读取GPIOx中的GPIO_Pin的输出电平
   - `GPIO_ReadOutputData(GPIO_TypeDef* GPIOx)`，读取GPIOx中16位的输出电平







 

[^1]: `RCC->APB2ENR`定义在`stm32f10x.h`中，为一个结构体
