---
title: ArchLinux系统如何直播哔哩哔哩
categories: [Linux]
tags: [ArchLinux,哔哩哔哩,obs]
date: 2019-07-09 05:56:34
---
Linux下直播，一般都使用开源免费的直播工具[OBS](https://obsproject.com/)，因为[哔哩哔哩](https://www.bilibili.com/)官方的直播姬没有Linux版本  
  
但是我发现B站居然有Linux版本的[弹幕库](http://bilibili.danmaku.live/#/)，目前已经更新到了`v2.1.1`，之前要使用弹幕，都是借助类似于 `YouTube Live Chat` 的开源工具 [bilibili-live-chat](https://github.com/Tsuk1ko/bilibili-live-chat) 来实现的，具体使用方法参照博客——[【OBS用B站直播弹幕展示】](https://moe.best/projects/bilibili-live-chat.html)  
  
为什么这里要提到`bilibili-live-chat`呢？  
主要是因为哔哩哔哩弹幕库并不是一个成熟的应用，功能还不全面，并且它的弹幕展示还借助了`bilibili-live-chat`的思路，使用OBS浏览器插件 `BrowserSource` 去获取弹幕信息，爬取出来进行展示  

废话不多说，ArchLinux/Manjaro用户睁大眼睛哦～  
  
#### 安装OBS Studio  
![](https://obsproject.com/assets/images/OBSDemoApp2321.png)  
  
使用以下命令安装  
```bash
sudo pacman -S obs-studio    #OBS
```
启动后，obs 会根据你电脑性能自动配置出一套默认设置，我们直播主要是推流，所以和视频录制有所区别，需要适当修改一下  
* 进入 file -> settings 打开设置面板，点击左边列表的 Output 选项卡  
* 设置 Video Bitrate 比特率为 5000 
  * 录制视频的话，比特率可以高一些，设置为 5000 ，这样视频清晰度会比较高  
  * 直播的话，比特率等于网络上行速度(单位KB/s)乘以 8 ，并且最终结果要小于用户的下行网速，否则观众姥爷那边会比较卡，除此之外，最大也不要超过[哔哩哔哩平台](https://link.bilibili.com/p/help/index#/device-setting)服务器的带宽，否则一样会造成卡顿
  
#### <center>直播比特率 = (上行KB/s × 8) < 用户下行 < 平台带宽</center>  
  
* Encoder选择带 NVENC 的N卡驱动  
* 勾选 Enable Advanced Encoder Settings 复选框  
* 点击左边列表的 Video 选项卡,设置Output(Scaled)Resolution输出像素为1920×1080  
  

<details>
<summary style="color:#ff0000;">常见问题：OBS 录制屏幕黑屏，但是可以看见鼠标</summary>

首先使用终端，输入 `obs` 命令进行启动，如果第一行日志显示
```bash
Warning: Ignoring XDG_SESSION_TYPE=wayland on Gnome. Use QT_QPA_PLATFORM=wayland to run on Wayland anyway.
```
那么输入以下命令，检查你目前使用的是 `x11` 还是 `wayland`
```bash
echo $XDG_SESSION_TYPE
```
如果是 `wayland` 的话，那么我们的问题是一致的，编辑 `/etc/gdm/custom.conf` 文件，取消 `WaylandEnable=false` 的注释，将强制启动使用 `xorg` 作为窗口显示
</details>  
  
接下来就是安装哔哩哔哩官方的[弹幕库](http://bilibili.danmaku.live/#/)  
  
#### 安装弹幕库  
![](https://s5.danmaku.live/web/sample.png)  
  
点击[链接](http://bilibili.danmaku.live/#/)进入下载，我这里的版本是`v2.1.1`，下载之后解压到本地文件夹，目录结构如下  
```bash
.
├── blink_image_resources_200_percent.pak
├── content_resources_200_percent.pak
├── content_shell.pak
├── icudtl.dat
├── libffmpeg.so
├── libnode.so
├── LICENSE
├── LICENSES.chromium.html
├── locales
│   ├── am.pak
│   ├── ar.pak
│   ├── bg.pak
│   ├── bn.pak
│   ├── ca.pak
│   ├── cs.pak
│   ├── da.pak
│   ├── de.pak
│   ├── el.pak
│   ├── en-GB.pak
│   ├── en-US.pak
│   ├── es-419.pak
│   ├── es.pak
│   ├── et.pak
│   ├── fake-bidi.pak
│   ├── fa.pak
│   ├── fil.pak
│   ├── fi.pak
│   ├── fr.pak
│   ├── gu.pak
│   ├── he.pak
│   ├── hi.pak
│   ├── hr.pak
│   ├── hu.pak
│   ├── id.pak
│   ├── it.pak
│   ├── ja.pak
│   ├── kn.pak
│   ├── ko.pak
│   ├── lt.pak
│   ├── lv.pak
│   ├── ml.pak
│   ├── mr.pak
│   ├── ms.pak
│   ├── nb.pak
│   ├── nl.pak
│   ├── pl.pak
│   ├── pt-BR.pak
│   ├── pt-PT.pak
│   ├── ro.pak
│   ├── ru.pak
│   ├── sk.pak
│   ├── sl.pak
│   ├── sr.pak
│   ├── sv.pak
│   ├── sw.pak
│   ├── ta.pak
│   ├── te.pak
│   ├── th.pak
│   ├── tr.pak
│   ├── uk.pak
│   ├── vi.pak
│   ├── zh-CN.pak
│   └── zh-TW.pak
├── natives_blob.bin
├── resources
│   ├── app.asar
│   └── electron.asar
├── ui_resources_200_percent.pak
├── v8_context_snapshot.bin
├── version
├── views_resources_200_percent.pak
└── 弹幕库      #启动脚本
```
右击启动脚本`弹幕库`就可以启动软件了，但是，我们发现它的文件夹名字和启动脚本的名字是中文，为了不引起路径错误，我们重命名一下  
> 文件夹名：`弹幕库-linux-x64` 改成 `bilibilidanmuku-linux-x64`  
> 启动脚本：`弹幕库` 改成 `danmuku`  
  
修改完之后，将文件夹移动到`/opt`目录  
```bash
sudo mv bilibilidanmuku-linux-x64 /opt
cd /opt/bilibilidanmuku-linux-x64  
```
由于没有快捷方式，我们手动创建一个，图标[danmuku.png](https://i.loli.net/2019/11/21/MZA3mrQsW8OwNYo.png)点击链接下载到文件夹中  
```bash
sudo gedit danmuku.desktop  #创建快捷方式，内容如下
```
```bash
[Desktop Entry]
Encoding=UTF-8
Name=Bilibili Danmuku
Comment=BiliBili live assistant
Exec=/opt/bilibilidanmuku-linux-x64/danmuku
Icon=/opt/bilibilidanmuku-linux-x64/danmuku.png
Categories=Application;
Version=2.1.1
Type=Application
Terminal=0
```
添加运行权限  
```bash
sudo chmod a+x danmuku.desktop  #添加运行权限
sudo cp danmuku.desktop /usr/share/applications/  #复制到快捷方式文件夹
```
至此，OBS 和弹幕库都安装好了，可以正常直播，但是无法发送弹幕，以及接受弹幕到屏幕上，根据弹幕库的提示，我们需要在OBS中添加浏览器源(BrowserSource)  
> 使用说明: 在OBS中添加浏览器源(BrowserSource) 复制上方链接填写在URL中 宽度高度可根据需要自行设置（不要在预览视图中拖拽缩放浏览器源的宽高） FPS建议60 (不超过推流FPS) CSS保持默认
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g4t5czdrjkj30qp0gqgp6.jpg)  
  
但是，Linux 平台下并没有 `BrowserSource` 这个 OBS 插件，并且在我们安装的 OBS 来源中也找不到选项，参考 OBS 官网的帮助之后，我瞄上了另一个插件[obs-linuxbrowser](https://github.com/bazukas/obs-linuxbrowser/releases)  
```bash
yay -S obs-linuxbrowser     #ArchLinux直接安装社区包
:: There are 2 providers available for obs-linuxbrowser:
:: Repository AUR
    1) obs-linuxbrowser 2) obs-linuxbrowser-bin 

Enter a number (default=1): 2
```
这里有两个版本，并且在社区最近都有更新，我一开始是选择第一个，下载成功，编译成功，安装却失败了  
后来我使用第二个`obs-linuxbrowser-bin`完美安装，安装之后，重启 OBS 就可以添加来源了，将弹幕库中提供的链接配置进去，其他的都可以不需要改  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g4t5p52j0hj30tw0jo3zw.jpg)  
  
最后就可以开播测试了，我的效果如下  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g4t5s99uu1j31hc0u04qp.jpg) 






