---
title: ArchLinux安装最新百度网盘客户端
date: 2019-06-24 11:08:49
categories: [Linux]
tags: [Arch Linux,pacman,debtap,Electron]
---
之前，由于百度公司之前不做Linux的客户端软件，我们使用百度网盘都是借助第三方的 [BaiduPCS-Go](https://github.com/iikira/BaiduPCS-Go/releases) 来下载网盘内容，最近，国内知名互联网公司都开始向 Linux 进军了，百度网盘首先中标麒麟 Linux 系统，后续微信也即将出 Linux 版，由于百度网盘也是刚出 Linux，ArchLinux 社区包还没跟进，所以我们自己通过[官网](https://pan.baidu.com/download)提供的 `deb` 包进行转换成 ArchLinux 可以安装的 `.pkg.tar.xz` 包  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g4c1xt9banj31hc0lvgs6.jpg)  
  
下载官网的 `baidunetdisk_linux_2.0.1.deb` 安装包到本地  
`deb` 包转换成 `ArchLinux` 包需要借助 `Debtap`,所以需要先安装一下  
```bash
yaourt -S debtap    #依赖bash、binutils、pkgfile、fakeroot包
```
顺便检查一下电脑是否安装上面几个依赖包，确认无误后，同步一下 `pkgfile` 和 `debtap` 数据源  
```bash
sudo debtap -u  
```
接下来就可以转换包了，以 `baidunetdisk_linux_2.0.1.deb` 为例  
```bash
debtap baidunetdisk_linux_2.0.1.deb     #转换包
#--------------------------输出日志------------------------------------
==> Extracting package data...
==> Fixing possible directories structure differencies...
==> Generating .PKGINFO file...

:: Enter Packager name:
baidunetdisk_linux_2.0.1    #输入包名字

:: Enter package license (you can enter multiple licenses comma separated):
SEVGHUNHSFA4SKKSN7B8BG  #输入许可证，随便写（大写字母+数字组合），反正是自己用

*** Creation of .PKGINFO file in progress. It may take a few minutes, please wait...

==> Checking and generating .INSTALL file (if necessary)...

:: If you want to edit .PKGINFO and .INSTALL files (in this order), press (1) For vi (2) For nano (3) For default editor (4) For a custom editor or any other key to continue: 


==> Generating .MTREE file...

==> Creating final package...
==> Package successfully created!
==> Removing leftover files...
```
转换成功之后，就可以看到本地多了 `baidunetdisk-2.0.1-1-x86_64.pkg.tar.xz` 包，使用 `pacman` 将其安装  
```bash
sudo pacman -U baidunetdisk-2.0.1-1-x86_64.pkg.tar.xz   #pacman 安装本地包
```
![](http://ww1.sinaimg.cn/large/006kWbIoly1g4c2cm7jdwj31hc0u0gof.jpg)  
 
