# 1.Linux文件系统结构

- 始于一个点`.`,根目录使用`/`表示,是层次结构的最顶端

- 顶层目录

  - `System Binaries`: 有核心命令的系统二进制文件
  - `Boot & Kernel-related`:帮助系统启动
  - `Configuration`:定义Linux操作方式的配置文件
  - `User-related`:用户相关目录
  - `Shared Libraries`: 支持核心程序的共享库
  - `Mount Points & Media`:挂载点和外部设备使用的媒体
  - `System Info`:系统和运行时信息反应实时发生的情况
  - `Multi-user Resources`:多用户资源用于软件和日志
  - `Temporary File`:系统的临时使用文件
  - `Optional Software`:为单独安装的可选软件准备的文件夹

  **Linux的所有东西都是从==根目录==开始**,用一个单独的`/`表示,是所有文件和目录的顶级文件夹

  

  - `/` != `/root`,`/root`只是一个子目录
  
  - `/bin`有一些重要的命令行工具(==ls, cp, mv, cat),是系统正常运作所必需的
  
  - `/sbin`有专用的系统工具,通常由**管理员或root**使用

  - `/bin`提供常用功能
  
  - `/usr`是Unix系统资源的缩写,包含了用户空间的应用程序, 库和文档
  
  - `/boot`包含所有启动系统所需要的文件
  
  - `/dev`存放设备文件,作为硬件的接口,通常包含两种
  
    - 块设备:磁盘
    - 字符设备:键盘
  
  - `/etc`存放系统配置文件的中心位置
  
  - `/home`是普通用户的个人空间,以`.`开头的文件通常为隐藏文件
  
  - `/media`是Linux自动挂载可移动设备的地方
  
    > [!important]
    >
    > 与上述`/dev`的区别在于,`/dev`的文件代表硬件本身,`/media`是这些设备挂载的位置 
  
  - `/mnt`用于挂载文件系统的目录,专用于**手动临时挂载**
  
  - `/proc`是一个虚拟文件系统,==不包含实际存储在此盘上的文件,而是提动系统的动态内存展示,是**监控, 调试和交互系统的重要工具**
  

```
image_{y}/{m}{d}/{origin}
```

`archlinux `中，生成密钥需要下载openssh
```bash
sudo pacman -S openssh
```
