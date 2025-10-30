# 1. 简单的C程序示例

```c
// C Primer Plus 例2.1
#include <stdio.h>

int main()
{
	int num; // 创建一个整型变量num
	num = 1; //为num赋值
	
	printf("I am a simple "); //打印输出内容
	printf("computer.\n");
	printf("My favorite number is %d because it is first.\n", num);

	return 0;
}
```

> [!note]
>
> 如果程序的输出出现一闪而过的情况即某些窗口环境会在单独的 窗口下运行程序，然后在程序结束后自动关闭窗口。
>
> ==解决方案==： 可以在程序中的`return 0`前添加一行`getchar()`，即让程序等待击键，窗口会在用户按下下一个按键后结束

# 2. 示例解释

![Screenshot_2025_1030_093350](https://raw.githubusercontent.com/camus0809/Typora_Image/devl/image_2025/1030/Screenshot_2025_1030_093350.png)

## 2.1 快速概要

```c
#include <stido.h>  // 包含另一个文件
```

该行告诉编译器把`stdio.h`[^1]中的内容包含到当前程序中

```c
int main()	// 函数名
```

C语言中包括一个或多个函数，他们是C语言的基础模块。

```c
// 这是一行注释

/*
	这是一个多行注释
	我是第二行
*/
```

这些注释能提高程序的可读性，编译器会忽略它们

```c
{	// 函数体的开始
```

左花括号表示函数定义的开始

```c
int num；
```

声明一个整型的变量`num`

```c
num = 1；
```

赋值语句，用于将右边的值赋给`num`变量

```c
printf("I am a simple "); 
```

调用`stdio.h`中的`printf()`函数，在屏幕上输出`I am a simple `，光标停在同一行

```c
printf("computer.\n");
```

同样地调用`stdio.h`中的`printf()`函数，在屏幕上输出`computer.`，但`\n`则表示将光标移至下一行

```c
printf("My favorite number is %d because it is first.\n", num);
```

相同地调用`printf()`函数，且将`num`的值内嵌在双引号括起来的内容中，`%d`则告诉计算机以什么形式输出`num`的值，又打印在何处

```c
return 0;
```

向调用方返回一个值，暂时可以看作结束`main()`的要求

```c
}
```

右花括号表示函数定义的结束

## 2.2 程序细节

### 2.2.1 `#include`指令和头文件

`#include <stdio.h>`的作用相当于将`stdio.h`中的所有内容都输入该行所在的位置。这是一行*预处理指令(preprocessor directive)*，通常C编译器在编译前会对源代码做一些准备工作，即*预处理(preprocessing)*

所有的C编译器软件包都提供`stdio.h`文件，其中包含供编译器使用的输入输出函数，其文件名的含义是*标准输入输出头文件*，在C程序顶部的信息集合称为**头文件**

### 2.2.2  `main()`函数

C程序一定要从`main()`函数开始执行，除了`main()`函数外，还可以自定义函数，但`main()`函数必须是开始的函数

### 2.2.3 注释

在程序中，被`/**/`两个符号括起来的部分是程序的注释。C语言注释的好处之一为，**可以将注释放在任意的地方。甚至是与要解释的内容放在同一行**。较长的注释可单独放在一行或者多行

`C99`新增了另一种风格的注释，即以`//`开头，仅限于单行

### 2.2.4 花括号、函数体和块

一般而言，所有的C函数都使用花括号标记函数体的开始和结束，这是规定，不能省略

花括号还可以吧函数中的多条语句合并为一个单元或块

### 2.2.5 声明

```c
int num;
```

这段代码叫作声明，是C语言最重要的特性之一。上述声明完成了两件事

1. 在函数中有一个有一个名为`num`的变量
2. `int`表明`num`是一个整数

`int`是C语言中的一个**关键字**(*keyword*),表是一种基本的C语言数据类型。关键字是语言定义的单词，不能用作其他用途

`num`是一个**标识符**(*identifier*)，即一个变量、函数或其他实体的名称。因此声明把特定标识符与计算机内存中的特定位置联系起来，同时也确定了储存在某位置的信息类型或数据类型

在C语言中，所有的变量都必须先声明才能使用，即必须列出程序中用到的所有变量名及其类型

**变量命名规范**：要使用有意义的变量名或者标识符，如不能，应在注释中进一步说明。`C99`和`C11`允许使用更长的标识符名，但编译器只识别前**==63==**个字符，对于外部标识符，只允许**==31==**个字符

- 操作系统和库中经常视同以一个或两个下划线开始的标识符，因此个人编写程序时应尽量避免
- C语言的名称区分大小写，即`Start`、`start`、`START`是三个不同的标识符

**声明变量的四个理由** ==尽量放在块的顶部==

1. 把所有的变量放在一起方便读者查找和理解程序的用途
2. 声明变量会促使用户在编写程序之前做一些计划
3. 声明变量有助于发现隐藏在程序中的Bug
4. 如果实现未声明变量，C程序将无法通过编译

### 2.2.6 赋值

```c
num = 1;
```

这行代码是**赋值表达式语句**。赋值是C语言的基本操作之一



# 3. 提高程序可读性的技巧

如前面所言，前两个提高程序可读性的技巧为

1. 选择有意义的函数名和变量名
2. 书写恰当的注释

而剩下的技巧为

3. 在函数从用空行分隔概念上的多个部分
4. 每条语句各占一行



# 4. 多个函数

```c
// 一个文件包含两个函数
#include <stdio.h>
// 自定义函数的声明
void butler(void);
int main(void)
{
	printf("I will summon the butler function.\n");
	butler();
	printf("Yes. Bring me some tea and writeable DVDs.\n");

	return 0;
}

void butler(void)
{
	printf("You rang, sir?\n");
}
```

其中`butler()`函数出现了3次

- 第一次为**函数原型**(*prototype*)，是一种声明形式，用于告知编译器正在使用某个函数，因此也被称为**函数声明**(*function declaration*)，且还指明了函数的属性。第一个void表明`butler()`函数没有返回值，第二个void表明该函数不带参数
- 第二次以**函数调用**(*function call*)的形式出现，函数的调用很简单，写出函数名和圆括号即可
- 第三次出现在**函数定义**(*function definition*)，其形式与`main()`函数相同，都包含函数头和用花括号括起来的函数体

自定义函数的执行时间取决于它在`main()`函数中何时被调用，而不是`butler()`函数的定义在文件中的位置。C程序习惯将`main()`放在开头，因为它提供了程序的基本框架

# 5. 调试程序

```c
// 用与debug的程序
#include <stdio.h>

int main(void)
// ( // 1. main()函数的函数体应有{} 而非 ()包含
{
	// int n, int n2, int n3;	// 定义多个变量要么只写一次类型，要么分开多行写并使用;分隔
	// method 1 定义为一行
	// int n, n2, n3;
	// method 2 定义为多行
	int n;
	int n2;
	int n3;
// ------------------- 上述为语法错误，下面为逻辑/语义错误 ------------------
	n = 5;
	n2 = n * n;
	// n3 = n2 * n2; // 这里定义应为n的立方，但计算的值为n^4
	n3 = n2 * n;
	printf("n = %d, n squared = %d, n cubed = %d\n", n, n2, n3);

	return 0;
// )
}

```

# 6. 关键字和保留符

关键字是C语言的词汇。不能使用它们来做为标识符。

- 用于指定不同类型，如`int`。
- 用于控制程序中语句的执行顺序

<p style="color:gray; blod"> 其中粗体表示的是C90的关键字，斜体表示C99新增的关键字，粗斜体表示的是C11新增的关键字</p>

| auto      | extern   | short        | while                |
| --------- | -------- | ------------ | -------------------- |
| break     | float    | **signed**   | ***_Alignas***       |
| case      | for      | sizeof       | ***_Alignof***       |
| char      | goto     | static       | ***_Atomic***        |
| **const** | if       | struct       | ***_Bool***          |
| continue  | inline   | switch       | ***_Complex***       |
| default   | int      | typedef      | ***_Generic***       |
| do        | long     | union        | ***_Imaginary***     |
| double    | register | unsigned     | ***_Noreturn***      |
| else      | restrict | void         | ***_Static_assert*** |
| **enum**  | return   | **volatile** | ***_Thread_local***  |



[^1]:C编译器软件包的标准部分，提供键盘输入和屏幕输出的支持





