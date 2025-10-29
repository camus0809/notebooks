# git分布式控制

- git的基本概念
- git的工作流程
- git的常用命令
- git代码托管服务



## 1.使用git的好处

- 备份
- 代码还原
- 协同开发
- 追溯问题代码的编写人和编写时间

## 2. 版本控制器的方式

- 集中式版本控制工具	E.g. SVN和CVS 集中存放在中央服务器，
    - 团队中每个人需要时从中央下载代码，必须==联网==（可以是局域网也可以是互联网）
    - 个人修改后提交到中央版本库
- 分布式版本控制工具        E.g. Git
    - 没有“中央服务器”，个人电脑即为一个完整的版本库，故==无须联网==
    - 多人协作只需要各自的修改推送给对方即可

## 3. git工作流程图

## 4. git安装与基本配置

### 4.1 安装

linux下安装使用

```bash
sudo apt install git
```

Windows到官网安装即可

### 4.2 基本配置

1. 设置用户信息

```bash
git config --global user.name "camus"
git config user.email "exmaple@outlook.com"
```

2. 为常用指令配置别名（optional）

- 打开用户目录，创建.bashrc文件

```bash
# 配置了日志命令
alias git-log='git log --pretty=oneline --all --graph --abbrev-commit'
```

> [!IMPORTANT]
>
> 修改保存好`.bashrc`文件后，需要重启相关服务
>
> ```bash
> source ~/.bashrc
> ```
>

3. 将git的默认分支`master`修改成`main`

```bash
git config --global init.defaultBranch main
```

### 4.3 基本操作指令

git工作目录下对于文件的**修改**[^1]会存在几个状态，这些**修改**会随着我们执行git命令而发生变化



- git add （工作区--> 暂存区）
- git commit （暂存区--> 本地仓库）



1. 使用`git status`查看当前文件的状态



2. 使用`git add .`将==未跟踪或者未暂存==的文件从工作区-->暂存区



3. 使用`git commit -m （label）` 注释完成提交到仓库



4. 使用`git log`查看日志生成的

![image-20251008231721143]assets/image-20251008231721143.png "log日志")

`HEAD`是一个指向最新提交的指针，`main`也是一个指向特定提交的指针

==当HEAD指向main时，代表当前位于main分支上==

5. 使用`git reset --hard commitID`即可完成版本回退

6. 使用`git reflog`可以查看已删除的记录

### 4.4 使用`.gitignore`文件

忽略`git add .`时不需要上传的文件

```
# 使得git add .时忽略拓展名为.a的文件  Windows
*.a

# 使得git add .时忽略扩展名为.out的文件 Linux
*.out

# 忽略所有在build下的文件
build/
```

[^1]: 增加、删除、更新



## 5. git分支

几乎所有的版本控制系统都以某种形式支持分支

### 5.1 查看本地分支

```bash
git branch
```



### 5.2 创建本地分支

```bash
git branch (分支名)
```



使用`git-log`查看，观察可得新建的分支`admin`是直接复制原有分支`main`的内容的



而正好新建的`.gitignore`并未提交，因此提交后，再使用`git-log`命令后可以观察得，`admin`停留在了其刚创建的分支，而当前的主分支`main`则指向了新提交的分支



### 5.3 切换分支/checkout

```bash
git checkout (分支名)
```

现在切换到了`admin`分支，在这个分支下新建`Git_branch.md`文件再进行提交，使用`git-log`后，可以看到现在的文件树出现了分支



再次切回`main`分支，修改内容后提交使用`git-log`查看

其中，切换为不存在的分支命令为：

```bash
git checkout -b (分支名)
```

### 5.4 合并分支/merge

一个分支的提交可以合并到另一个分支

```bash
git merge (分支名)
```

切换回`main`后，看不见再`admin`中的提交，进行再次修改后使用`git-log`查看结果为



将`admin`中所做的提交合并到`main`，**应保证当前分区为合并后的`main`**,使用`git-log`查看成功合并



### 5.5 删除分支

==*不能删除当前分支*==

```bash
git branch -d (分支名)
```

现在位于`main`分区，故无法删除`main`，将`admin`进行删除



*强制删除分支*

```bash
git branch -D (分支名)
```



### 5.6 合并冲突

当两个分支对文件的修改可能会存在冲突，如*修改同一个文件的同一行*，这时候就需要手动解决冲突

​	step 1. 处理文件中冲突的地方

​	step 2. 将解决冲突的文件加入暂存区`git add .`

​	step 3. 提交到仓库`git commit -m "注释文本"`

**快进合并**

当我在一个特性分支（比如 `feature`）上进行了更改并提交，并且自从我创建这个 `feature` 分支以来，**我所要合并的目标分支（比如 `main`）上没有任何新的提交**时，使用` git merge` 命令将 `feature` 分支合并到 `main` 分支时，就会自动进行快进合并。

### 5.7 开发中分支使用的原则与流程

一般的有：

- `main`（生产）分支：线上分支，主分支，中小规模项目作为线上运行的应用对应的分支
- `develop`（开发）分支：从`main`创建的分支，一般为开发部门的主要开发分支，==如没有其他并行开发不同期上线要求==，都可以在这个版本进行开发，阶段开发完成后，需要==合并到`main`分支，准备上线==
- `feature/xxx`分支：从`develop`分支创建，一般是==同期并行开发==，但不同期上线时创建的分支，分支的研发任务完成后合并到`develop`分支
- `hotfix/xxx`分支：从`main`分支派生，一般作为==线上bug修复使用==，修复完成后需要合并到`main`、`test`、`develop`分支



## 6. git远程仓库

在托管网站上创建一个repository

### 6.1 配置SSH公钥

1. 生成公钥

```bash
ssh-keygen -t rsa -b 4096 -C "email@example.com"
```

2. 查看公钥

```bash
cat ~/.ssh/is_rsa.pub
```

3. 配置公钥

​	直接粘贴即可

### 6.2 远程仓库的添加、查看与推送

1. 将远程仓库的ssh链接复制后执行`git remote add`命令

    其中`origin`为别名，默认设置，代表远程仓库

```bash
git remote add origin git@github.com:xxx/xxx.git # 添加远程仓库
git remote # 查看是否添加了远程仓库
```



2. 将文件推送到远程仓库

```bash
git push [-f] [--set-upstream] (远程名称(本地分支名)(:远程分支名))
```

- `-f`：强制覆盖

- `--set-upstream`：推送到远端的同时并建立起和远端分支的关联关系

- 如果**当前分支与远端分支关联**，则可以省略分支名和远端名



通过下述指令来查看本地分支和远程分支的对应关系

```bash
git branch -vv
```



当前是处于没有关联的状态，故使用下面的指令将本地和远程的分支联系起来，现在即可直接使用`git push`进行推送

```bash
git checkout main # 切换回main分支 暂不知是什么操作导致头指针与main分离
git push --set-upstream origin main:main
```

### 6.3 从远程仓库克隆

```bash
git clone <仓库路径> [本地目录]
```



### 6.4 从远程仓库进行抓取和拉取

远程分支与本地的分支一样，也可以进行`merge`操作，只是需要先将远端仓库的更新都下载到本地，再进行操作

1. 抓取命令`fetch`

```bash
git fetch [远端名称][分支名]
```

- 抓取指令是**将仓库中的更新全都抓取到本地，不会进行合并**
- 如何不指定远端名称和分支名，则会抓取所有的分支

2. 拉取命令`pull`

```bash
git pull [远端名称][分支名]
```

- 拉取指令是**将远端仓库的修改拉到本地并自动合并**，等于==fetch+merge==
- 如何不指定远端名称和分支名，则会抓取所有的分支

## 7. idea相关配置[暂无]

[相关视频 P23]( https://www.bilibili.com/video/BV1MU4y1Y7h5/?p=23&share_source=copy_web&vd_source=f786f9b9afd83fa22eb1cf2099582d39)

