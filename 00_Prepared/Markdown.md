# 基础语法

## 1.**加粗命令**

```md
**Bold**
__Bold**
```

**this is a Bold** 	or  __this is a Bold__

## 2. _斜体命令_

`````md
_Italic_
*Italic*
`````

*this is Italic* or _this is Italic_

## 3. ***斜体加粗命令***

```md
***Bold and Italic***
___Bold and Italic___
```

***this is Bold and Italic*** or ___this is Bold and Italic___

> notice: 以上两个命令最好都使用`*`，因为下划线在不同的解析器中的表现不同，比较不稳定

## 4. ~~删除线命令~~

```md
~~delete~~
```

~~this is delete~~

***

## 5. 分割线命令

```md
---
***
```

---

***

## 6. 标题命令

```md
#<SPACE> h1
##<SPACE> h2
... 以此类推
```

# 一级标题（演示）

## 二级标题（演示）

### 三级标题（演示）

#### 四级标题（演示）

##### 五级标题（演示）

###### 六级标题（演示）

## 7. 列表命令

### 7.1 无序列表命令

```md
method 1:
*<SPACE> 
```

* list0

* list2

    

```md -<SPACE>
method 2:
-<SPACE>
```

- this is  a test
- 同上

> notice: 不应该混合着使用，会导致一个列表被分成多个

### 7.2 有序列表命令

```md
1.<SPACE>
```

1. 我是第一项任务
2. 我是第二项任务



4. 我是第四项任务
5. 我是第五项任务

> notice: 有序列表一次按照第一个数字的顺序往下走，可以和无序列表进行嵌套，使用tab即可

## 8. 勾选框命令

```md
-<SPACE>[<SPACE>]<SPACE>  复选框
```

- [ ] 这是一个复选框
- [x] 这是第二个复选框



```md
-<SPACE>[x]<SPACE> 已经勾选的复选框
```

- [x] 这是一个已经勾选的复选框

## 9. 代码块

```md
```(所使用的语言) 代码块
```



```c
#include <stdio.h>
int main()
{
    printf("hello world\n");
    getchar();
    return 0;
}
```



```md
`（输入的短小的函数或代码）` 行内的代码块
```

在C语言中我们为变量动态分配存储空间可以使用函数`malloc`,`calloc`,`realloc`,记得需要将声明的指针使用`free`进行释放

而在C++中则只需要使用`new`命令进行分配空间，使用`del`进行释放

## 10. 引用文本

```md
><SPACE>
```

> 这是一段引用的文本
>
> 可以进行换行
>
> 很不错🐮
>
> # 你好
>
> - [x] 并且可以嵌套上述的所有~~命令~~
>
> > 包括它自己



## 11. 链接命令

```md
method 1: (不够美观)
直接输入 www.example.com
```

www.example.com

### 11.1 超链接命令

```md
[(文本内容)]（网址 “描述[可选]”） 不能省略`http://` 
```

这是一个[测试网站](http://www.example.com "这是一个示例")

 ### 11.2 参考链接命令

```md
[Link Text][ref]
or
[ref][]



[ref]: 网址 “描述[可选]”
```

[必应][bing]

[bing][]

[bing]: https://www.bing.com "好用的搜索引擎"



### 11.3 HTML标签

```md
<a href="http://www.example.com" target="__blank">Link Text</a>
```

<a href="http://www.google.com" target="__blank">Link to Google</a>

### 11.4 链接到本地文件

可以使用相对路径或绝对路径作为指向本地文件的链接地址

```md
[Link Text](xxx.xx)
```

[About git](Git.md)

[About git test](Git.md#test) 

### 11.5 内部链接

```md
[Link Text](#(标题名字))
```

这个链接将返回 [3. 斜体加粗命令](#3. ***斜体加粗命令***)

> notice: 如果存在重复的标题，编号即可



## 12. 脚注命令

```md
Text[^(explanation)]

[^(explanation)]: explanation text
```

如何使用必应[^1]

[^1]: 直接打开即可，[bing](https:www.bing.com)

## 13. 插入图片命令

```md
![描述文字](图片链接 "图片标题")
```



## 14. 表格命令

```md
| 属性1 | 属性2 | 属性3 |
在下方插入 <CTRL> + <ENTER>
```

| 姓名  | 年龄 | 性别 |
| :----- | :----: | ----: |
| marry | 12   | f    |
|       |      |      |

## 15. 其他命令

```md
~1~	下标  或者使用<sub>
^2^ 上标	或者使用<sup>
==高亮==
```

H~2~O

2^4^

==hello==

H<sub>2</sub>

H<sup>2</sup>

