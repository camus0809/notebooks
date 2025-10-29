# 一、`set`指令

## 1. Cmake变量设置

[示例文件 #2. 编写一个CMakeLists.txt文件](CMake_Basic.md)

上述例子共存在五个源文件，每次都在`add_executable`中列出很麻烦，故尝试定义一个变量将这些文件名存储起来

- `VAR`：变量名
- `VALUE`：变量值

```txt
SET (VAR [VALUE] [CACHE TYPE DOCSTRING [FORCE]])
```

E.g.

```txt
# 方式1 使用空格间隔
set(SRC_LIST add.cpp div.cpp main.cpp mult.cpp sub.cpp)

# 方式2 使用分号间隔
set(SRC_LIST add.cpp;div.cpp;main.cpp;mult.cpp;sub.cpp)

# 使用
add_executable(app ${SRC_LIST})
```



## 2. 指定使用的C++标准

在编写C++程序的时候，可能会用到C++11、C++14、C++17、C++20等特性，则需要在编译命令中制定出要使用哪一个标准

```bash
g++ *.cpp -std==c++11 -o app
```

上述命令通过参数`-std=c++11`置顶了C++11标准编译程序，C++标准对应有一宏`DCMAKE_CXX_STANDAED`，在CMake中**指定**C++标准有两种方式

### 2.1 在CMakeLists.txt中通过`set`指定

```txt
# 增加-std=c+11
set(CMAKE_CXX_STANDARD 11)
# 增加-std=c+14
set(CMAKE_CXX_STANDARD 14)
# 增加-std=c+17
set(CMAKE_CXX_STANDARD 17)
```

### 2.2 在执行cmake命令时指出这个宏的值

```bash
# 增加-std=c+11
cmake CMakeLists.txt路径 -DCMAKE_CXX_STANDARD=11
# 增加-std=c+14
cmake CMakeLists.txt路径 -DCMAKE_CXX_STANDARD=14
# 增加-std=c+17
cmake CMakeLists.txt路径 -DCMAKE_CXX_STANDARD=17
```

## 3. 指定输出的路径

在CMake中指定可执行程序输出的路径，也对应一个宏，叫做`EXECUTABLE_OUTPUT_PATH`,它的值也是通过`set`命令设置

```txt
set(HOME /home/aurora/xxx)
set(EXECUTABLE_OUTPUT_PATH ${HOME}/bin)
```

- 第一行定义了一个变量表示==绝对路径==
- 第二行将拼接好的路径值设置给`EXECUTABLE_OUTPUT_PATH`
    - 如果目录不存在，这是自动生成

---

# 二、搜索文件

如果一个项目中的源文件很多，在编写`CMakeLists.txt`时将项目目录的各个文件一一罗列出来这样不现实也不方便。故可以使用`aux_source_directory`命令或者`file`命令

## 1. `aux_source_directory`

在CMake中使用`aux_source_directory`可以查找都某个路径下的==所有源文件==，命令格式为

```txt
aux_source_directory(< dir > <variable >)
```

- `dir`：要搜索的目录
- `variable`：将从`dir`中搜索到的源文件列表存储到该变量中

`auto_source_directory(${PROJECT_SOURCE_DIR}, )`

- 其中的`PROJECT_SOURCE_DIR`对应执行`cmake`命令后面的路径，即`CMakeLists.txt`所在的位置

## 2. `file`

`file`除了搜索外还能做其他的事情

```txt
file(GLOB/GLOB_SOURCE 变量名 要搜索的文件路径和文件类型)
```

- `GLOB`：将指定目录下搜索到的满足条件的所有文件名生成一个列表，并存储在变量中
- `GLOB_SOURCE`:递归搜索指定目录，将搜索到满足条件的文件名生成一个列表，并存储在变量中

`ile(GLOB SRC ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)`

- 其中的`CMAE_CURRENT_SOURCE_DIR` 和`PROJECT_SOURCE_DIR`位置相同



---

# 三、指定头文件路径

在编译项目源文件的时候，很多时候都需要将源文件对应的头文件路径指定出来，这样才能保证在编译过程中编译器能够找到这些头文件，并顺利进行编译。这时便需要用到`include_directories`命令

```txt
include_directories(headPath)
```



---

# 四、库

当编写的源代码并不需要生成可执行程序而是给第三方使用时，则需要生成相应的库文件

在**Linux**下，动态库**(有可执行权限)**的后缀为`.so`，**Windows**下为`.dll`

在**Linux**下，静态库**（没有可执行权限）**的后缀为`.a`，**Windows**下为`.lib`

## 1. 制作库

```txt
add_library(库名称 STATIC 源文件1 [源文件2]) # 静态库
add_library(库名称 SHARED 源文件1 [源文件2]) # 动态库
```

在Linux下，静态库名字分为三部分：`lib`+`库名字`+`.a`，只需指定`库名字`即可，*其余的会自动填充*

## 2. 导入库的路径

由于Linux下静态库不具有可执行权限，故不能使用`EXECUTABLE_OUTPUT_PATH`，而应使用`LIBRARY_OUTPUT_PATH`这个宏，==对于静态动态库都适用==

 ### 2.1 链接静态库

在cmake中，链接静态库的命令是：

```txt
link_libraries(<static lib> [<static lib]> ...)
```

- 参数1需要指定要链接的静态库的名字
    - 可以是全名`libxxx.a`
    - 也可以掐头去尾只要`xxx`
- 参数2-N为*其余要链接的静态库名称*

如果该静态库==不是系统==提供的，可能会出现找不到静态库的情况，此时可以将静态库的路径通过`link_directories`指定出来，命令如下

```txt
link_directories(<lib path>)
```

> [!important]
>
> 若生成的库文件为静态库，执行`add_executable`后生成的可执行程序会自动打包该静态库

### 2.2 链接动态库

命令如下：

```txt
target_link_libraries(
	<target>
	<PRIVATE | PUBLIC | INTERFACE> <item> ...
	[<PRIVATE | PUBLIC | INTERFACE> <item> ...] ...)
```

- **target**：指定要加载动态库的文件的名字
    - 可能是源文件
    - 可能是一个动态库文件
    - 可能是一个可执行文件
- **PRIVATE|PUBLIC|INTERFACE**：动态库的访问权限，默认为==PUBLIC==
    - 如果各个动态库直接没有依赖关系，无须做任何设置，三者没有区别，==一般无须指定，使用默认即可==
    - ==动态库的链接具有传递性==，如果动态库a链接了动态库b、c，动态库d链接了a，此时动态库d也相当于链接了b、c，并可以使用动态库b、c中的方法
        - `PUBLIC`: 在public后的库会被link到前面的target中，并且其中的符号也会被导出，提供给第三方使用
        - `PRIVATE`:在private后面的库仅被link到前面的target中，并且==终结掉==，第三方不能感知你调了什么库 -->没有==传递性==
        - `INTERFACE`: 在interface后面引入的库不会被链接到前面的target中，只会导出符号

> [!important]
>
> - 静态库在生成可执行程序的链接阶段被打包到可执行程序中，所以可执行程序启动，静态库即被加载到内存中
>
> - 动态库则相反，在链接阶段**不会**被打包到可执行程序中，当可执行程序启动并调用动态库中的函数时，动态库才会被加载

==因此，在`cmake`链接动态库时，==应将命令卸载生成可执行文件（`add_executable`)之后

# 五、日志

在CMake中可以用用户显示一条消息，该命令的名字是`message`：

```txt
message([STATUS|WARNING|AUTH_WARNING|FATAL_ERROR|SEND_ERROR] "message to dispaly" ...)
```

- =={无}==：重要消息
- =={STATUS}==：非重要消息
- =={WARNING}==：CMake警告，会继续执行
- =={AUTH_WARNING}==：CMake警告（dev），会继续执行
- =={SEND_ERROR}==：CMake错误，继续执行，但会跳过生成步骤
- =={FATAL_ERROR}==：CM错误，终止所有处理过程  