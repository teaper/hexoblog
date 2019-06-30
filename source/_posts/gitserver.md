---
title: 搭建Git服务器
categories: [Git]
tags: [git]
date: 2019-06-30 13:16:21
---
[<span style="color:#B900ff;">修改系统源</span>](#修改系统源)  
[<span style="color:#B900ff;">安装git</span>](#安装git)  
[<span style="color:#B900ff;">创建git用户</span>](#创建git用户)  
[<span style="color:#B900ff;">创建证书登录</span>](#创建证书登录)  
[<span style="color:#B900ff;">初始化Git仓库</span>](#初始化git仓库)  
[<span style="color:#B900ff;">禁用shell登录</span>](#禁用shell登录)  
[<span style="color:#B900ff;">测试远程仓库</span>](#测试远程仓库)  
[<span style="color:#B900ff;">后续功能</span>](#后续功能)  
  
#### 修改系统源  
```bash
sudo vim /etc/apt/sources.list   #输入i进入install模式
-------------------------------粘贴源---------------------------------
deb http://mirrors.aliyun.com/ubuntu/ xenial main
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main
 
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main
 
deb http://mirrors.aliyun.com/ubuntu/ xenial universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
 
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main
deb http://mirrors.aliyun.com/ubuntu/ xenial-security universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security universe
-----------------------------------------------------------------------
#输入:wq(保存退出) :q(不保存退出)
 
sudo apt-get update
sudo apt-get upgrade
```
  
#### 安装git  
```bash
sudo apt-get install git   #输入y回车
```
  
#### 创建git用户  
```bash
sudo adduser git
```
  
#### 创建证书登录  
收集所有需要登录的用户的公钥，就是他们自己本机的id_rsa.pub文件，把所有公钥粘贴到购买的主机/home/git/.ssh/authorized_keys文件里，一行一个  
```bash
cd .ssh
sudo vim authorized_keys  #把key粘贴进去,Esc+:wq+回车
```
  
#### 初始化Git仓库  
先选定一个目录作为Git仓库，假定是/srv/sample.git，在/srv目录下输入命令  
```bash
cd /srv
sudo git init --bare twtity.git    #git就会创建一个裸仓库twtity.git,仓库名twtity可自定义
sudo chown -R git:git twtity.git   #将仓库的拥有者改为git
```
  
#### 禁用shell登录  
```bash
sudo vim /etc/passwd
-----------------------修改-------------------------
git:x:1001:1001:,,,:/home/git:/bin/bash
                    改为
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
---------------------------------------------------
sudo vim /etc/ssh/sshd_config    #把PasswordAuthentication no改为yes
```
  
#### 测试远程仓库  
在本机上执行  
```bash
git clone git@主机名或主机IP:/srv/twtity.git
```
  
#### 后续功能  
**管理公钥**  
如果团队很小，把每个人的公钥收集起来放到服务器的.ssh/authorized_keys文件里就是可行的。如果团队有几百人，就没法这么玩了，这时，可以用[Gitosis](https://github.com/res0nat0r/gitosis)来管理公钥  
  
**管理权限**  
因为Git是为Linux源代码托管而开发的，所以Git也继承了开源社区的精神，不支持权限控制,不过，因为Git支持hook,所以，可以在服务器端编写脚本来控制提交等操作,从而达到权限控制的目的! [Gitolite](https://github.com/sitaramc/gitolite)允许您在中央服务器上设置git托管，具有非常细粒度的访问控制和许多更强大的功能  

