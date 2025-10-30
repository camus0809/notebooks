# 1. shell环境

- Bourne Shell  			--> /bin/sh
- Bourne Again Shell                --> /bin/bash
- C Shell                                   --> /usr/bin/csh
- K Shell                                   --> /usr/bin/ksh
- Shell for Root                         --> /sbin/sh

## 1.1 第一个shell脚本

```shell
#!/bin/zsh
echo "hello world!"
```

`#!`是一个约定的标记，告诉系统这个脚本需要什么解释器来执行

`echo`为向窗口输出文本

**运行shell脚本的方法**

1. 作为可执行程序

   保存上述代码为`test.sh`，为这个代码添加权限后即可直接执行

   ```shell
   chmod +x ./test.sh
   ./test.sh
   ```

2. 作为解释器参数

   直接运行解释器，参数为shell脚本的内容

   ```shell
   /bin/zsh test.sh
   ```




# 2. shell变量

用于存储数据值的名称，定义变量时，变量名不加`$符号，其命名规则遵循：

- 只包含字母、数字和下划线
- 不能移数字开头
- 避免使用shell关键字 `if` `then` `else` `fi` `for` `while`等
- 使用大写字母代表常量 ==大小写敏感==
- 避免使用特殊符号
- 避免使用空格 

> [!important]
>
> 等号旁边避免使用空格！！！

除了显式地赋值。还可以用语句给变量赋值

```shell
for file in `ls/etc`
or
for file in $(ls/etc)
```

## 2.1 使用变量

使用一个定义过的变量，只需要在变量前加`$`即可

```shell
skill="Java"
echo "I am good at ${skill}Script."
```

### 2.1.1 只读变量

使用`readonly`命令即可将变量定义为只读变量

```shell
readonly skill
```

### 2.1.2 删除变量

使用`unset`命令可以删除变量

```shell
unset variable_name
```

### 2.1.3 变量类型

1. 字符串：在shell中，变量通常为字符串类型。可以使用`'`和`"`来定义字符串

```shell
my_string="hello"
or
my_string='hello'
```

   - 是shell编程中最常见最有用的数据类型
   - 单引号字符串的限制
     - 单引号里的任何字符都会原样输出，单引号中的变量是无效的
     - 单引号字符串中不能出现单独一个单引号（**==转义后也不行==**），但可以成对出现，作为字符串拼接使用
   - 双引号的优点
     - 双引号中可以有变量
     - 双引号中可以出现转义字符
   - 拼接字符串

```shell
name="camus"
greeting="hello, "${name}" !"
greeting_1="hello, ${name} !"
echo ${greeting} ${greeting_1}

greeting_2='hello, '${name}' !'
greeting_3='hello, ${name} !'
echo ${greeting_2} ${greeting_3}


[output]
hello, camus ! hello, camus !
hello, camus ! hello, ${name} !
```

   - 获取字符串长度

```shell
string="this is a test text"
echo "sizeof \"string\" is "${#string}

[output]
sizeof "string" is 19
```

   - 提取子字符串

```shell
string="this is a test for shell"
echo ${string:3:10}
# 从第3个字符开始截取10个字符 ！从零开始
[output]
s is a tes
```

   - 查找子字符串 查找字符`x`的位置

```shell
string="this is a test text"
echo `expr index "${string}" x`

[output]
18
```

2. 整数变量：在shell中，可以使用`declare`和`typeset`命令来声明整数变量，这样的变量只包含整数值

```shell
declare -i a_integer=12
```

3. 数组变量：运行在一个变量中存储多个值，数组可以是整数索引数组或关联数组

```shell
# 整数索引数组
my_array=(1 2 3 4 5)
# 关联数组
declare -A associative_array
associative_array["name"]="John"
associative_array["age"]=27
```

在shell中用括号表示数组，数组元素用<kbd>space</kbd>分隔，可以不适用连续的下标，而且下标的范围没有限制

```shell
# 定义数组的一般形式
array_name=(value1 value2 value3 ... valuen)
or
array_name=(
value1
value2
value3
...
valuen
)

# 可以单独定义数组的各个分量
array_name[0]=value0
```

- 读取数组

```shell
# 一般格式为
valuen=${array_name[n]}
# 使用@可以读取数组中的所有元素
echo ${array_name[@]}
```

- 获取数组的长度

方法与获取字符串的长度相同，都使用`#`

```shell
length=${#array_name[@]}
length=${#array_name[*]}

# 取得数组单个元素的长度
length=${#array_name[n]}
```



4. 环境变量：是由操作系统或用户设置的特殊变量，用于配置shell的行为和影响其执行环境

```shell
echo $PATH
```

5. 特殊变量：有些特殊变量在shell中有特殊的含义，如`$0`表示脚本的名称，`$1`，`$2`表示脚本的参数 `$#`表示传递给脚本的参数数量，`$?`表示上一个命令的退出状态

## 2.2 注释

1. 单行注释

```shell
# 这是一行注释
```

1. 多行注释

   1. 使用Here文档 `EOF`可以换成其他的符号，例如`COMMENT`

   ```shell
   ：<<EOF
   注释内容
   注释内容
   注释内容
   EOF
   
   ：<<COMMENT
   注释内容
   注释内容
   注释内容
   COMMENT
   
   ：<<'
   注释内容
   注释内容
   注释内容
   '
   
   ：<<!
   注释内容
   注释内容
   注释内容
   !
   ```

   2. 直接使用`:`

   ```shell
   :'
   这是注释的内容
   可以有多行内容
   '
   ```

   