---
title: VPS主机搭建Telegram机器人
categories: [Telegram]
tags: [VPS,Telegram,RSS]
date: 2019-07-16 06:01:12
---
之前我搭建过 SSR / V2ray ，在 [vultr](https://my.vultr.com/) 还有一个VPS主机闲置着，刚好最近想搞个 [Telegram](https://telegram.org/) 机器人，思来想去，还是决创建个 [RSS](https://zh.wikipedia.org/zh/RSS) 订阅的机器人，帮助订阅网络专栏，顺便同步到电报群里!  
> 如果还不了解RSS，推荐——[【如何使用RSS - 阮一峰的网络日志】](http://www.ruanyifeng.com/blog/2006/01/rss.html)  
  
#### BotFather  
Telegram 中管理所有机器人的用户叫 `BotFather` ，它本质也是一个机器人，我们通过它创建子机器人，以及配置机器人信息和 API 接口等，所以首先需要找到它  
  
在 Telegram 搜索框搜 `@BotFather` 会查询出相关的用户，选择第一个带官方星号的  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g517i12g03j30ai0azmy3.jpg)  
  
在聊天窗口，输入 `/newbot` 按回车发送消息，会提示输入机器人名称<span style="color:#ff0000">（名称可以中文）</span>，我这里是 `rssBot` ，之后会提示输入机器人用户名<span style="color:#ff0000">（用户名只能英文，并且没有被人使用，必须以 Bot结尾）</span>，我这里是 `teaperRssBot` ，本来想用 `rssBot` ，被人使用了，之后会出现一段机器人相关信息，点击蓝色的 [t.me/teaperRssBot](http://t.me/teaperRssBot) 可以打开机器人聊天界面  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g517tim9mtj30y70h9jvi.jpg)  
  
其中的 `809357392:AAFdLHw96jES6BEgljDgc0t-lbkHona5qZs` 是 `token` 信息，记得保存好，不要透露给别人<span style="color:#ff0000">（像博主这样不打码是要打屁股的）</span>，之后会用到  
  
#### 配置机器人  
输入 `/mybots` 会列出你所有的机器人，我只有一个，所以直接跳过，进入 `teaperRssBot` 设置  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g517zgdittj30y708p75h.jpg)  
  
出现六个按钮，我简单说明一下  
> Edit Name：设置机器人名称，例如修改为 `白素贞` ，直接点击按钮，输入名称回车即可  
> Edit Description：设置机器人说明  
> Edit About：设置关于机器人，会显示在机器账号简介中  
> Edit Botpic：设置机器人头像，图片最好是正方形的，需要通过截图/复制图片的方式复制到剪贴板，然后粘贴到输入框中发送才可以  
> Edit Commands：设置机器人命令，可以显示在输入框里，如下图所示  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g518casj8yj30xm069gm7.jpg)  
  
除了最后一个 `Edit Commands` 之外，前面四个都可以现在设置  
  
#### 搭建RssBot 
使用 `ssh` 登录 VPS 服务器  
```bash
ssh root@139.180.166.23     #登录服务器
```
登录成功创建一个 RSS 文件夹，用于存放相关脚本  
```bash
mkdir rss && cd rss     #创建并进入rss文件夹
```
浏览器进入 [RssBot 发布页](https://github.com/iovxw/rssbot/releases)查看最新版本的 ZIP 压缩包，使用 `wget` 下载  
```bash
wget https://github.com/iovxw/rssbot/releases/download/v1.4.4/rssbot-v1.4.4-linux.zip   #下载压缩包
unzip rssbot-v1.4.4-linux.zip   #解压缩到当前文件夹
./rssbot DATAFILE TELEGRAM-BOT-TOKEN  #测试运行
```
最后一条测试运行命令的 `TELEGRAM-BOT-TOKEN` 替换成电报机器人的 **token** ，也就是上面的 `809357392:AAFdLHw96jES6BEgljDgc0t-lbkHona5qZs` ，完整命令如下：  
>  ./rssbot DATAFILE 809357392:AAFdLHw96jES6BEgljDgc0t-lbkHona5qZs  
  
测试运行看不到日志输出的，不用担心，回到电报用户 `@BotFather` 聊天框，设置 `@teapeRssBot` 的命令，点击 `Edit Commands` 按钮，将下方命令粘贴到输入框回车即可  
```bash
rss       - 显示当前订阅的 RSS 列表，加 raw 参数显示链接
sub       - 订阅一个 RSS: /sub http://example.com/feed.xml
unsub     - 退订一个 RSS: /unsub http://example.com/feed.xml
unsubthis - 使用此命令回复想要退订的 RSS 消息即可退订, 不支持 Channel
export    - 导出为 OPML
```
这时候，你再进入 [t.me/teaperRssBot](http://t.me/teaperRssBot) 的聊天框，就可以看到输入框有快捷命令了，我们可以发送命令进行订阅  
```
/sub https://rsshub.app/bilibili/live/room/15053080     #链接地址是RSS地址，不是普通网址
```
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g518zc0x0sj30xs08hgn5.jpg)  
  
确定没有问题之后，我们在服务器中将脚本挂到后台去运行  
```bash
nohup ./rssbot DATAFILE TELEGRAM-BOT-TOKEN > /dev/null 2>&1 &
```
  
#### 添加到群组  
这个很简单，直接在电报机器人主页中，点击 `添加到群组` 按钮，选择一个群组即可  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g5196h6f4lj31hc0sen8q.jpg)  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g51987bblvj31hc0sfgxw.jpg)  
  
