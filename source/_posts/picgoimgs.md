---
title: 图床工具PicGo+Github
date: 2019-06-01 00:31:00
categories: [PicGo]
tags: [PicGo,Github]
---
> 所谓图床工具，就是自动把本地图片上传到服务器，并获取其http/https链接的一款工具，让其可以在互联网上得到访问  
> 网络上有很多图床工具，就目前使用种类而言，[PicGo](https://molunerfinn.com/PicGo/) 算得上一款比较优秀的图床工具  
  
[PicGo](https://github.com/Molunerfinn/PicGo)基于 [Electron-vue](https://github.com/SimulatedGREG/electron-vue) 开发的软件，可以支持[微博](https://chrome.google.com/webstore/detail/%E6%96%B0%E6%B5%AA%E5%BE%AE%E5%8D%9A%E5%9B%BE%E5%BA%8A/fdfdnfpdplfbbnemmmoklbfjbhecpnhf)、七牛云、腾讯云COS、又拍云、GitHub、阿里云OSS、[SM.MS](https://link.juejin.im/?target=http%3A%2F%2FSM.MS)、imgur 等8种常用图床，功能强大，简单易用!  
  
#### 下载和安装PicGo  
下载地址参见[github发布页](https://github.com/Molunerfinn/PicGo/releases)  
> macOS用户请下载最新版本的dmg文件  
> windows用户请下载最新版本的exe文件  
> linux用户请下载AppImage文件  
  
Arch Linux用户使用 `aurman -S picgo-appimage` 安装，或者使用 [yay]() 安装  
```bash
yay -S picgo-appimage       #一般不是最新版本
```
安装成功，你就可以使用其中的 [SM.MS](https://link.juejin.im/?target=http%3A%2F%2FSM.MS) 图床，其他图床需要自行配置<span style="color:#ff0000;">(本教程搭建Github图床)</span>  
  
#### Github token  
登录你的 [Github](https://github.com/) 账号，新建一个公有仓库 `picgoimgs` ，或者使用已有仓库  
进入 [https://github.com/settings/tokens](https://github.com/settings/tokens)，点击 `Generate new token` 创建一个新的 `token`  
勾选 `repo` 复选框，Note说明随便写，我这里是 `For PicGo` ，表示用于 PicGo图床  
  
![](https://i.loli.net/2019/05/24/5ce7a447a047350146.png)  
  
点击最下方绿色 `Generate token` 的按钮生成`token`  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g3kyp69v3wj30rq0cgmz6.jpg)  
  
复制绿色背景的那条编码，将其存储起来，因为它只显示一次  
```bash
8c473a9580043gd33a8b45f3610c7f59646b662     #示范
```
  
#### 配置PicGo  
设定仓库名：teaper/picgoimgs  
设定分支名字：master  
设定Token：8c473a9580043gd33a8b45f3610c7f59646b662  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g3kyo9373rj30j40a6q3k.jpg)
  
存储路径和自定义域名可以不写，设定自定义域名需要在域名DNS下解析你的github服务器  
  
点击“设为默认图床”，当图片上传成功之后，可以看到仓库中多了一个文件  
  
![](https://raw.githubusercontent.com/teaper/picgoimgs/master/Screenshot%20from%202019-05-24%2016-19-25.png)





  

