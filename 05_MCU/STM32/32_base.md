# 0. 预备工作

STM32是ST公司基于ARM Cortex-M内核开发的32位微控制器

## 0.1 片上资源/外设 peripheral

> 深色位Cortex-M3内核中的外设，浅色位外部外设

![image-20251027170809721](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1027/image-20251027170809721.png)

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

![image-20251027180934634](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1027/image-20251027180934634.png)

- 系统结构

![image-20251027181304066](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1027/image-20251027181304066.png)

1. ICode：加载指令
2. DCode：加载数据，例如**常量和调试数据**
3. System

## 0.4 最小系统电路

![image-20251027183215300](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1027/image-20251027183215300.png)

- 0.1uF 滤波电容

![image-20251027192456289](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1027/image-20251027192456289.png)

- 如需使用`RTC`功能，则需在3、4引脚按照同样的方式接上==32.768KHz==的晶振
  - `OSC32`即32.768KHz
  - 可以生成1s的时间信号

![image-20251027192721822](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1027/image-20251027192721822.png)

![image-20251027192922193](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1027/image-20251027192922193.png)

![image-20251027192932601](https://raw.githubusercontent.com/camus0809/Typora_Image/devw/Image_2025/1027/image-20251027192932601.png)

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