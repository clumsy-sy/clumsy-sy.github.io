---
title: "Terraria"
description: Terraria Linux 服务器搭建
date: 2022-06-28
slug: terraria
image: "../../post/Terraria/Photo.png"
tags: 
    - 服务器
    - 游戏
---

# Terraria Linux 服务器搭建（支持 mod ）

## 简介
泰拉瑞亚 1.4 Linux 服务器搭建， TmodLoader（后简称 TML） 1.4 服务器搭建（支持mod），面向只有一点点 Linux 基础的小伙伴。

如果只是 TML 运行后一直在 **下载** 或者 **5分钟没反应** 这个问题（win 和 Linux 通用），可以直接看 TML 安装的最后部分。

网上泰拉服务器教程已经很多了，但是都基本都是 1.3 版本， TML 基本在 0.11.8.9 版本之前，这次本体更新到 1.4 版本，TML更新到 v2022.05.103.34 版本（本文写于2022.6.28）后有了较大的变化，搭建过程中我也走了很多弯路，所以写一篇文章，希望对大家有帮助。

服务器优势还是很大的，可以最大支持16个人，可以长期挂载，可以同时开多个资源世界等。

## 环境说明

服务器 ：腾讯云 8 核 4 G（如果要安装 TML 建议服务器 在 2核2G 以上）、操作系统 debain 11

游戏本体：steam上的 Terraria 1.4.3.6 ，TML v2022.05.103.34

泰拉的服务器端是免费的，在 [官网](https://terraria.org/) 或者 [Terraria wiki](https://terraria.fandom.com/zh/wiki/%E6%9C%8D%E5%8A%A1%E5%99%A8#.E5.BC.80.E6.9C.8D.E6.96.B9.E6.B3.95_.28Linux.29)， TML 在 [GitHub](https://github.com/tModLoader/tModLoader/releases) 获得最新版，也有封装完整的 [docker](https://hub.docker.com/search?q=terraria) 镜像供大家使用（这个方法我没有试过）

我这里推荐和使用的是利用 steamcmd 的方法，先说一下优势：
1. 服务器与桌面端版本统一方便。
2. 版本更新方便，steamcmd 有专门的指令更新游戏，而别的方法需要重新下载安装。
3. 1.4 版本后的 TML 支持创意工坊直接导入 mod ，这个在服务器端也生效！！！

## 服务器搭建

### 安装 [steamcmd](https://developer.valvesoftware.com/wiki/SteamCMD:zh-cn#Linux)

习惯看文档的请看文档，里面比较详细（支持中文）, 内含 docker 安装方法。

创建一个名为 steam 的用户帐户以安全地运行 SteamCMD，并将其与操作系统的其余部分隔离。**以 root 用户身份登录时请勿运行 steamcmd——这样做会带来安全风险**。

1. 以 root 用户身份创建 steam 用户：
   
```shell
useradd -m steam

# 添加密码
passwd steam
```

2. 进入其文件夹

```shell
cd /home/steam

# 或者 
su steam
bash # 切换到 bash 以保证命令行体验
cd ~

# 64 位机需要
sudo add-apt-repository multiverse
sudo dpkg --add-architecture i386
sudo apt update
sudo apt install lib32gcc1 steamcmd 
```

3. 安装

Ubuntu/Debian

```shell
sudo apt install steamcmd
```

4. 链接 steamcmd 可执行文件：

```shell
ln -s /usr/games/steamcmd steamcmd
```

5. 运行 Steamcmd 

```shell
cd ~
steamcmd
```

正常情况下你会看到

![Alt](../../post/Terraria/steam0)

输入`login + 你的用户名` 然后按提示登录即可（第一次登录会需要令牌）

安装 terraria

``` shell
app_update 105600 # 安装 Terraria(它在steam中的代码是 105600，不会改变)
```

下载完成后你应该可以在 `~/Steam/steamapps/common` 下面看到 Terraria 文件夹

![Alt](../../post/Terraria/steam1)

里面的 `TerrariaServer.bin.x86_64` 就是服务器运行文件了

```shell
# 新建会话
screen -S Tr
./TerrariaServer.bin.x86_64
```

然后就可以开始的愉快游戏了，[常用指令](https://www.bilibili.com/read/cv16191402)

## TML 搭建

这个需要在本体完成的基础上进行

如何查找游戏在steam中的代码

![Alt](../../post/Terraria/steam2)

我们可以看到 TML 的代码是 1281930 版本是 8980144， 所以

``` shell
# app_update <应用ID> [-beta <测试名称>] [-betapassword <密码>] [validate]
app_update 1281930 8980144 validate # 如果发现安装有问题请加 validate
```

下载完成后，同样进入 TML 文件夹

![Alt](../../post/Terraria/steam3)

```shell
# 新建会话
screen -S Tr
./start-tModLoaderServer.sh
```

### ！！！最大的问题来了
这里演示的是 windows 下的情况，Linux服务端一样，只不过服务器上可以多看到下载信息，在下载 .net 但是国内服务器应该是下载不下来的，所以会等到死。

![Alt](../../post/Terraria/steam4)

windows 和 Linux 解决方法一样，[下载 .net](https://dotnet.microsoft.com/zh-cn/download)

我们可以在 TML 内的 `dotnet/6.0.0` 内看到里面是空的，所以我们要手动下载二进制文件包，丢进去

```shell
cd ~/Steam/steamapps/common/tModLoader/dotnet/6.0.0

# 下载二进制文件
wget https://dotnet.microsoft.com/zh-cn/download/dotnet/thank-you/sdk-6.0.301-linux-x64-binaries

# 解压文件
tar -zxvf dotnet-sdk-6.0.301-linux-x64.tar.gz
```
windows 下操作同理

然后运行即可
steam 的最大优势是 TML 内的mod就是 改登录的steam账号内 TML 订阅的mod，如果需要更新

``` shell
app_update 1281930 validate
```

已经成功和小伙伴玩上 1.4 版本的 **灾厄mod** ，请注意服务器mod与主机端应当相同。


本文主要是为了介绍 TML 1.4 运行不起来的解决方法，下面的参考链接介绍会更为详细

参考连接：
[泰拉瑞亚服务器搭建教程（带 MOD）](https://zerol.me/2020/02/08/Terraria-Server-With-Mods/)

[使用Linux搭建Terraria服务器，详细步骤](https://zhuanlan.zhihu.com/p/94570876)