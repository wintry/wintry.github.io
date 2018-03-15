---
layout: original
title: "在lunux下运行ASF(ArchiSteamFarm)V3"
date: 2018-03-15 9:16:00 +0800 
categories: 分类
tag: [linux,steam]
---
* content
{:toc}

ASF是目前最为广泛使用的steam挂卡软件，由[JustArchi](https://github.com/JustArchi "JustArchi")编写，ASF V3不再基于.NET Framework 4.6.1（ASF V2系列）编写，现在使用.NET Core 2.0（ASF V3系例）。  
由于使用了.NET Core，ASF现在可以跨平台使用，而本文就是介绍如何在linux上运行ASF，方便又服务器的玩家24小时云挂卡。  
有任何疑问的小伙伴，可以在博客首页获取我的联系方式向我提问。
<!-- more -->
<!-- TOC -->
## 准备
+ linux服务器（阿里云，腾讯云，搬瓦工等均可），也可以是树莓派这样的设备  
+ ASF软件挂卡软件 [ArchiSteamFarm](https://github.com/JustArchi/ArchiSteamFarm/releases/)  也可以先不下载，在服务器上使用wget下载使用  
注意请下载Latest release版本，Pre-release为预发布版本，可能存在bug
  - 64位请下载 ASF-linux-x64.zip  
  - 32位请下载 ASF-linux-arm.zip  
+ SSH工具 Xshell，putty等  
+ FTP工具 XFTP等（非必需，asf程序也可以直接在服务器上下载）

## 安装  
### 安装.NET Core

本文以CentOS7为例 其他版本可参考[https://www.microsoft.com/net/download/linux/run](https://www.microsoft.com/net/download/linux/run)  
+ 注册Microsoft密钥
```linux
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
```
+ 添加 Microsoft源
```linux
sudo sh -c 'echo -e "[packages-microsoft-com-prod]\nname=packages-microsoft-com-prod \nbaseurl= https://packages.microsoft.com/yumrepos/microsoft-rhel7.3-prod\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/dotnetdev.repo'
```
+ 安装依赖和.NET SDK 
```linux
sudo yum update
sudo yum install libunwind libicu
sudo yum install dotnet-runtime-2.0.6
```
+ 将 dotnet 添加到 PATH
```linux
export PATH=$PATH:$HOME/dotnet
```

+ 验证
```linux
dotnet --version
```
若显示版本，则表示安装成功

### 下载安装ASF

+ 下载ASF  
- 方法一：ftp上传  
将下载的ASF-linux-x64.zip通过FTP将软件上传到服务器/home/ 目录（可自行放入合适的目录）下  
- 方法二：使用wget下载
```linux
sudo yum install wget 
wget -P /home https://github.com/JustArchi/ArchiSteamFarm/releases/download/3.1.1.2/ASF-linux-x64.zip
```
下载地址请使用最新的

+ SSH连接服务器，在home中创建ASF文件夹并进入：
```linux
mkdir /home/ASF
cd /home/ASF
```
+ 解压ASF
```linux
unzip /home/ASF-linux-x64.zip
```
+ 修改权限: 
```linux
chmod +x ArchiSteamFarm
```

### 配置ASF
+ 使用官方的[ASF Config Generator](https://justarchi.github.io/ArchiSteamFarm/#/bot)

- Name 配置文件名称  
- SteamLogin steam账号  
- SteamPassword steam密码  
- Enabled  √  
- IsBotAccount ×  
更多配置请参考[官方wiki](https://github.com/JustArchi/ArchiSteamFarm/wiki)
或SteamCN网友@[Singx9](https://steamcn.com/suid-280275)的[配置教程](https://steamcn.com/t301016-1-1)
+ 点击Download下载json文件  
+ 将文件通过FTP上传至/home/ASF/config/  
现在搭建完成  
+ 进入目录启动ASF
```linux
cd /home/ASF/
./ArchiSteamFarm
```
大功告成
