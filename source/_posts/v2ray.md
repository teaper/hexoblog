---
title: 学习利器V2ray了解一下
date: 2019-06-02 19:15:39
categories: [Linux]
tags: [V2ray]
---
之前我一直在Arch Linux下使用 `shadowsocksr`，随着最近墙的升级，服务器一批一批的封，好多人的 `22` 端口都封了，SSH登录不了服务器，还有各种TCP回程阻断，网络超时问题，恰好发现了[V2ray](https://www.v2ray.com/)这款通信工具，现在就来安利一波～  
  
#### Project V  
要了解 `V2ray` 就必须要知道 [Project V](https://github.com/v2ray)，根据官方说法：`Project V` 是一个工具集合，它可以帮助你打造专属的基础**通信网络**，**Project V 的核心工具称为 V2Ray**，其主要负责网络协议和功能的实现，与其它 `Project V` 通信，`V2Ray` 可以单独运行，也可以和其它工具配合，以提供简便的操作流程  
  
相对于`shadowsocksr`来说，它有以下几个优势：  
> **多入口多出口**: 一个 `V2Ray` 进程可并发支持多个入站和出站协议，每个协议可独立工作  
> **可定制化路由**: 入站流量可按配置由不同的出口发出。轻松实现按区域或按域名分流，以达到最优的网络性能  
> **多协议支持**: V2Ray 可同时支持多个协议，包括 `Socks`、`HTTP`、`Shadowsocks`、`VMess` 等  
> **隐蔽性**: V2Ray 的节点可以伪装成正常的网站（HTTPS），将其流量与正常的网页流量混淆，以避开第三方干扰  
> **反向代理**: 通用的反向代理支持，可实现内网穿透功能  
> **多平台支持**: 原生支持所有常见平台，如 Windows、Mac OS、Linux，并已有第三方支持移动平台  
  
#### 安装V2ray服务器脚本  
废话不多说，这里我直接参考[官方文档](https://www.v2ray.com/)，在我的[Vultr](https://my.vultr.com/billing/)的`Ubuntu16.04 Server`主机上安装网络代理，然后在`Arch Linux`本机上连接，Windows用户请参照官方文档自行安装客户端软件，配置部分是一样的 
  
首先使用SSH登录VPS主机  
```bash
ssh root@139.180.166.23       #使用ssh连接
```
然后使用 bash 命令官方提供的一键安装脚本  
```bash
root@VentUB:~# bash <(curl -L -s https://install.direct/go.sh)  #安装一键脚本
#---------------------------输出日志-----------------------------------
Installing V2Ray v4.18.2 on x86_64
Downloading V2Ray: https://github.com/v2ray/v2ray-core/releases/download/v4.18.2/v2ray-linux-64.zip
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   608    0   608    0     0   1458      0 --:--:-- --:--:-- --:--:--  1458
100 11.2M  100 11.2M    0     0  2988k      0  0:00:03  0:00:03 --:--:-- 4391k
Extracting V2Ray package to /tmp/v2ray.
Archive:  /tmp/v2ray/v2ray.zip
  inflating: /tmp/v2ray/config.json  
   creating: /tmp/v2ray/doc/
  inflating: /tmp/v2ray/doc/readme.md  
  inflating: /tmp/v2ray/geoip.dat    
  inflating: /tmp/v2ray/geosite.dat  
   creating: /tmp/v2ray/systemd/
  inflating: /tmp/v2ray/systemd/v2ray.service  
   creating: /tmp/v2ray/systemv/
  inflating: /tmp/v2ray/systemv/v2ray  
  inflating: /tmp/v2ray/v2ctl        
 extracting: /tmp/v2ray/v2ctl.sig    
  inflating: /tmp/v2ray/v2ray        
 extracting: /tmp/v2ray/v2ray.sig    
  inflating: /tmp/v2ray/vpoint_socks_vmess.json  
  inflating: /tmp/v2ray/vpoint_vmess_freedom.json  
PORT:33857      #记下PORT
UUID:5182c7a0-0279-4221-9294-b16d9d047c6e   #记下UUID
Created symlink from /etc/systemd/system/multi-user.target.wants/v2ray.service to /etc/systemd/system/v2ray.service.
V2Ray v4.18.2 is installed.     #V2Ray版本
```
记录下日志中提示的 **PORT**  和  **UUID** 的值，安装成功之后会在`/etc/v2ray`
下生成一个服务器端模板文件`config.json`文件，使用vim打开它，具体关注我注释的几个参数  
<details>
<summary>服务器端config.json文件</summary>

```bash
{
  "inbounds": [{
    "port": 33857,      #V2Ray 监听的端口号，客户端连接会用到
    "protocol": "vmess",    #入站协议，与客户端保持一致
    "settings": {
      "clients": [
        {
          "id": "5182c7a0-0279-4221-9294-b16d9d047c6e",     #UUID值
          "level": 1,
          "alterId": 64 #用于认证的动态id
        }
      ]
    }
  }],
  "outbounds": [{
    "protocol": "freedom",
    "settings": {}
  },{
    "protocol": "blackhole",
    "settings": {},
    "tag": "blocked"
  }],
  "routing": {
    "rules": [
      {
        "type": "field",
        "ip": ["geoip:private"],
        "outboundTag": "blocked"
      }
    ]
  }
}
```
</details>
  
`config.json`中的数据不需要更改，后面客户端连接的时候就参照上面的值就行  
```bash
service v2ray start #服务器启动v2ray
service v2ray status        #查看运行状态
```
  
#### 安装V2ray客户端  
客户端软件在[发布页](https://github.com/v2ray/v2ray-core/releases)，根据你需要的系统下载匹配型号的安装包，我这里以我系统`Arch Linux`为例子，其他系统请自行参照[官方文档]()配置  
```bash
sudo pacman -S v2ray    #ArchLinux/Manjaro安装v2ray
```
使用gedit编辑`/etc/v2ray/config.json`文件，替换为我这个模板，修改一下参数即可，注意修改我注释的地方，其他地方不变  
<details>
<summary>ArchLinux下的v2ray/config.json</summary>

```bash
{
  "log": {
    "loglevel": "warning"
  },
  "inbound": {
    "port": 1088, #本地端口，没办法，我1080被占用了
    "listen": "127.0.0.1",
    "protocol": "socks", # 入站协议
    "settings": {
      "auth": "noauth",
      "udp": true    #开启udp
    }
  },
  "outbound": {
    "protocol": "vmess", #将出战协议修改为vmess，与服务端保持相同
    "settings": {
      "vnext": [
        {
          "address": "139.180.166.23",# 服务器地址，请修改为你自己的服务器 ip 或域名
          "port": 33857,  # 服务器端口，与服务端保持相同
          "users": [
            {
              "id": "5182c7a0-0279-4221-9294-b16d9d047c6e",  # uuid，与服务端保持相同
              "alterId": 64 # 此处的值也应当与服务器相同
            }]
        }]
      },
    "tag": "direct"
  },
  "policy": {
    "levels": {
      "0": {"uplinkOnly": 0}
    }
  }
}
}
```
</details>
  
使用`v2ray`自带了一个检查工具`v2ray -test`检查`json`文件  
```bash
v2ray -test -config /etc/v2ray/config.json  #检查json
V2Ray 4.18.0 (Po) 20190228
A unified platform for anti-censorship.
Configuration OK.
```
显示`OK`就表示没问题了，可以开启本机的开机自启服务  
```bash
systemctl enable v2ray  #开机自启v2ray
systemctl start v2ray   #启动v2ray
```
  
#### 配置shell代理  
为了解决终端下载被墙服务器的安装包失败的问题，所以需要让终端也可以翻墙，顺便提升下载速度  
```bash
sudo gedit ~/.bashrc    #编辑.bashrc文件
```
在最后追加以下配置  
```bash
export http_proxy="socks5://127.0.0.1:1088"     #本地端口1088
export https_proxy="socks5://127.0.0.1:1088"
```
保存之后运行 `source ~/.bashrc` 命令让它生效
  
#### 设置PAC代理  
`PAC`代理需要生成一个`.pac`的文件，首先本机安装`pip`工具 
```bash
sudo pacman -S python-pip       #安装pip
pip -V      #查看pip版本
#-------------------------输出-----------------------------
pip 19.0.3 from /usr/lib/python3.7/site-packages/pip (python 3.7)
```
```bash
sudo pip install genpac     #使用pip安装genpac，需要root权限
#---------------------------输出-----------------------
Collecting genpac
  Downloading https://files.pythonhosted.org/packages/48/fb/b8f9cce57c4ea856e7dd1f9fb917df2896d7e62d43c50ed1af2e50a4e57d/genpac-2.1.0.tar.gz (102kB)
     |████████████████████████████████| 112kB 111kB/s 
Installing collected packages: genpac
  Running setup.py install for genpac ... done
Successfully installed genpac-2.1.0
```
最后执行以下命令，会生成一个auto.pac文件，无输出结果表示执行成功  
```bash
genpac --format=pac -o auto.pac --pac-proxy="SOCKS5 127.0.0.1:1088"
```
然后在系统中配置代理，以我本机的`gnome`桌面为例，图1、3是PAC代理，引导pac文件的绝对路径`file:///etc/v2ray/auto.pac`；图2是全局代理    
  
![](https://i.loli.net/2019/06/02/5cf3ad11830e456416.png)  
  
![](https://i.loli.net/2019/06/02/5cf3ad19058b190157.png)
  
![](https://i.loli.net/2019/06/02/5cf3ad1f944f214959.png)
  
