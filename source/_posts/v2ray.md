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
<summary>服务器端的 config.json 文件</summary>

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
使用gedit编辑`/etc/v2ray/config.json`文件，替换为我这个模板，修改一下参数即可，注意修改我注释的地方，其他地方不变，实再不会可以用[配置生成器](https://intmainreturn0.com/v2ray-config-gen/)  
<details>
<summary>ArchLinux下的v2ray/config.json</summary>

```bash
{
    "log":{
        "loglevel":"warning"
    },
    "inbound":{
        "port":1088,    #本地端口，1080 被我 ssr 用了
        "listen":"127.0.0.1",
        "protocol":"socks",     #网络传输协议
        "settings":{
            "auth":"noauth",
            "udp":true
        }
    },
    "outbounds":[
        {
            "tag":"proxy",
            "protocol":"vmess",
            "settings":{
                "vnext":[
                    #一号服务器
                    {
                        "address":"139.182.212.71", #IP地址
                        "port":32456,   #端口
                        "users":[
                            {
                                "id":"8b9658de-a0s5-408j-a3b3-27a297e4f40b",   #UUID
                                "alterId":64
                            }
                        ]
                    },
                    #二号服务器（可选）
                    {
                        "address":"45.77.180.110",
                        "port":22071,
                        "users":[
                            {
                                "id":"13e61d3a-9ce7-428d-a7b6-67e9f8e0bf12",
                                "alterId":64
                            }
                        ]
                    }
                ]
            }
        }
    ],
    "policy":{
        "levels":{
            "0":{
                "uplinkOnly":0
            }
        }
    }
}
```
V2ray 支持配置多个服务器，那么多个服务器配置的时候，它其实是轮询访问的，也就是说第一个数据包走第一个服务器，第二个数据包走第二个服务器，而不是我们一般认为的第一个服务器被墙了就自动切换到第二个服务器，然后继续传输

路由规则参照 [domain-list-community](https://github.com/v2ray/domain-list-community) 域名列表，如果该项目中有一整套 `google` 顶级域名及其子域名，则用 `geosite` 写成 `geosite:google` ，如果是普通网站，则使用 `domain` 写成 `domain:google.com`
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
然后在系统中配置代理，以我本机的`gnome`桌面为例，图1、3是PAC代理，引导pac文件的绝对路径`file:///etc/v2ray/auto.pac`；图2是全局代理，也可以通过以下命令来设置  
```bash
gsettings set org.gnome.system.proxy autoconfig-url file:///etc/v2ray/auto.pac
gsettings set org.gnome.system.proxy mode 'auto'

gsettings set org.gnome.system.proxy.socks host '127.0.0.1'
gsettings set org.gnome.system.proxy.socks port 1088
```

<details>
<summary style="color:#ff0000">其他搭建方式：一键脚本方式搭建</summary>

服务器使用 `bash <(curl -L -s https://install.direct/go.sh)` 的搭建方式是官方最简洁，最基础的搭建方法，如果 `vmess` 协议被识别的话，很快就会被墙掉，而且会出现非常严重的**断流**现象！目前最安全的方式是使用 `Vmess` 协议加 `WebSocket` 和 `TLS` ，通过 `Nginx` 转发，最后伪装成一个网站 `Website`，最后配上证书去支持 `https` 的安全访问！

首先准备一个域名（例如：teaper.dev）,解析一个二级域名到主机上  

| 类型 | 名称 | 主机 | TTL(秒) |
| --- | ---- | ---- | ----- |
| A | v2 | 139.180.166.23 | 3600 |

然后使用 `ssh root@139.180.166.23` 登录到主机上，然后运行 `wulabing` 大佬的[一键安装脚本](https://github.com/wulabing/V2Ray_ws-tls_bash_onekey)
```bash
wget -N --no-check-certificate -q -O install.sh "https://raw.githubusercontent.com/wulabing/V2Ray_ws-tls_bash_onekey/master/install.sh" && chmod +x install.sh && bash install.sh
```
他会下载一个 `install.sh` 到本地，并且提示输入数字选择功能  
```bash
当前已安装版本:None

—————————————— 安装向导 ——————————————
0.  升级 脚本
1.  安装 V2Ray (Nginx+ws+tls)
2.  安装 V2Ray (http/2)
3.  升级 V2Ray core
—————————————— 配置变更 ——————————————
4.  变更 UUID
5.  变更 alterid
6.  变更 port
7.  变更 TLS 版本(仅ws+tls有效)
—————————————— 查看信息 ——————————————
8.  查看 实时访问日志
9.  查看 实时错误日志
10. 查看 V2Ray 配置信息
—————————————— 其他选项 ——————————————
11. 安装 4合1 bbr 锐速安装脚本
12. 证书 有效期更新
13. 卸载 V2Ray
14. 退出 

请输入数字：
```
我们首先输入 `11` 安装一个 `bbr` 加速  
```bash
TCP加速 一键安装管理脚本 [v1.3.2]
  -- 就是爱生活 | 94ish.me --
  
 0. 升级脚本
————————————内核管理————————————
 1. 安装 BBR/BBR魔改版内核
 2. 安装 BBRplus版内核 
 3. 安装 Lotserver(锐速)内核
————————————加速管理————————————
 4. 使用BBR加速
 5. 使用BBR魔改版加速
 6. 使用暴力BBR魔改版加速(不支持部分系统)
 7. 使用BBRplus版加速
 8. 使用Lotserver(锐速)加速
————————————杂项管理————————————
 9. 卸载全部加速
 10. 系统配置优化
 11. 退出脚本
————————————————————————————————

 当前状态: 未安装 加速内核 , 未启动

 请输入数字 [0-11]:
```
这里我们输入 `2` 安装 `BBRplus` 版内核，然后他会下载内核并且安装，安装过程中会弹出一个是否删除原有内核的面板，记得一定按方向键选择**No**  
安装成功之后会提示重启，选择 `yes`  
重启之后重新使用 `ssh` 登录面板，运行 `./install.sh` 命令，输入 `11` 进入上面的选项，你会发现提示内核已经安装，但是没有启动，这时候再输入 `7` 使用 `BBRplus` 版加速，运行成功会退出脚本，就此内核安装和加速已经完成  

之后再次运行 `./install.sh` 选择 `1` 安装 V2Ray (Nginx+ws+tls)  
首先会提示同步时间，因为时间对于 v2ray 特别重要  
* 请输入连接端口（default:443）:**443**
* 请输入alterID（default:2 仅允许填数字）:**32**
* 请输入你的域名信息(eg:www.wulabing.com):**[v2.teaper.dev](https://v2.teaper.dev/)**
* 请选择支持的 TLS 版本（default:1）:**1**

安装成功时候会生成一个二维码和 `v2ray` 的配置信息  
```bash
地址（address）: v2.teaper.dev 
端口（port）： 443 
用户id（UUID）： c3abf69a-a1e5-4f4b-8e53-6916b3ebc27b
额外id（alterId）： 32
加密方式（security）： 自适应 
传输协议（network）： ws 
伪装类型（type）： none 
路径（不要落下/）： /3095b9df/ 
底层传输安全： tls 
URL导入链接:vmess://ewogICJ2IjogIjIiLAogICJwcyI6ICJ3dWxhYmluZ192Mi50ZWFwZXIuZGV2IiwKICAiYWRkIjogInYyLnRlYXBlci5kZXYiLAogICJwb3J0IjogIjQ0MyIsCiAgImlkIjogImMzYWJmNjlhLWExZTUtNGYzYi04ZTUzLTy6MTZiM2ViYzI3YiIsCiAgImFpZCI6ICIzMiIsCiAgIm5ldCI6ICJ3cyIsCiAgInR5cGUiOiAibm9uZSIsCiAgImhvc3QiOiAidjIudGVhcGVyLmRldiIsCiAgInBhdGgiOiAiLzMwOTViOWRmLyIsCiAgInRscyI6ICJ0bHMiCn0K
```
由于我们服务器那边使用了 `TLS` 及 `WebSocket` ，所以客户端 `config.json` 的配置就不能使用原版 `V2ray` 的方式去配置  
这里我们可以参照[vTemplate](https://github.com/KiriKira/vTemplate)中的对应技术的一键脚本配置方式[config_client.json](https://github.com/KiriKira/vTemplate/blob/master/websocket%2BNginx%2BTLS/config_client.json)文件，根据服务器生成的信息将本地电脑的 `config.json` 修改一下
```bash
{
  "outbound": {
    "protocol": "freedom",
    "settings": {},
    "tag": "direct"
  },
  "inboundDetour": [
    {
      "port": 1088,     #本地电脑端口（建议1080，我是因为1080给了 SSR）
      "listen": "127.0.0.1",
      "protocol": "socks",
      "settings": {
        "auth": "noauth",
        "timeout": 300,
        "udp": true
      }
    }
  ],
  "outboundDetour": [
    {
      "mux": {
        "concurrency": 6,
        "enabled": true
      },
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "users": [
              {
                "id": "c3abf69a-a1e5-4f4b-8e53-6916b3ebc27b",   #UUID（修改）
                "level": 0,
                "alterId": 32,  #alterID（修改）
                "security": "aes-128-cfb"
              }
            ],
            "address": "v2.teaper.dev",     #域名地址（修改）
            "port": 443     #端口
          }
        ]
      },
      "streamSettings": {
        "tlsSettings": {
          "allowInsecure": false
        },
        "wsSettings": {
          "headers": {
            "Host": "v2.teaper.dev"     #域名地址（修改）
          },
          "path": "/3095b9df/"      #静态资源路径（修改）
        },
        "network": "ws",
        "security": "tls"
      },
      "tag": "proxy"
    },
    {
      "protocol": "blackhole",
      "settings": {},
      "tag": "block"
    }
  ],
  "dns": {
    "servers": [
      "8.8.8.8",
      "8.8.4.4"
    ]
  },
  "inbound": {
    "port": 1087,
    "listen": "127.0.0.1",
    "protocol": "http",
    "settings": {
      "timeout": 300
    }
  },
  "policy": {
    "levels": {
      "0": {
        "uplinkOnly": 0,
        "downlinkOnly": 0,
        "connIdle": 150,
        "handshake": 4
      }
    }
  },
  "routing": {
    "settings": {
      "rules": [
        {
          "type": "field",
          "domain": [
            "geosite:cn"
          ],
          "outboundTag": "direct"
        },
        {
          "type": "field",
          "domain": [
            "google",
            "facebook",
            "youtube",
            "twitter",
            "instagram",
            "gmail",
            "domain:twimg.com",
            "domain:t.co"
          ],
          "outboundTag": "proxy"
        },
        {
          "type": "field",
          "ip": [
            "8.8.8.8/32",
            "8.8.4.4/32",
            "91.108.56.0/22",
            "91.108.4.0/22",
            "109.239.140.0/24",
            "149.154.164.0/22",
            "91.108.56.0/23",
            "67.198.55.0/24",
            "149.154.168.0/22",
            "149.154.172.0/22"
          ],
          "outboundTag": "proxy"
        },
        {
          "type": "field",
          "ip": [
            "192.168.0.0/16",
            "10.0.0.0/8",
            "172.16.0.0/12",
            "127.0.0.0/8",
            "geoip:cn"
          ],
          "outboundTag": "direct"
        }
      ],
      "domainStrategy": "IPIfNonMatch"
    },
    "strategy": "rules"
  }
}
```
</details>
  
![](https://i.loli.net/2019/06/02/5cf3ad11830e456416.png)  
  
![](https://i.loli.net/2019/06/02/5cf3ad19058b190157.png)
  
![](https://i.loli.net/2019/06/02/5cf3ad1f944f214959.png)
  

  


 



  

