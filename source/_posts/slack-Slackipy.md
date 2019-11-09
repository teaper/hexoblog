---
title: 为 Slack 工作区添加自动邀请
categories: [Slack]
tags: [Slack,Slackipy]
date: 2019-11-09 17:37:22
---
[Slack](https://slack.com/intl/en-cn/) 是国外一个非常有名的团队协作神器，国内的钉钉，企业微信都或多或少受其启发。有不少开发朋友都知道 [Telegram](https://telegram.org/)，我也加入了不少优秀的电报群，由于电报群没有人数限制，不像 QQ 群最高两千人，而且匿名聊天，随来随走，世界通用，不少电报群少则千人，多则万人；技术氛围比某些培育机构 QQ 群好太多，很容易见到技术大佬！

但是电报群不适合团队协作，顶多作为一个极致的交流工具，而不能作为协作工具
* Telegram 需要翻墙才能访问，这就阻止了大部分初级开发人员
* Telegram 需要搭建很多机器人才能实现诸如打卡、订阅、监控、投票等功能
* Telegram 没有群空间，不能存放文件、素材等
* Telegram 无法让开发人员、运维人员、产品经理等各不同职位的人协作
  
但是这些 Slack 都可以做到，有很多大学的社团是建立在 QQ 群、微信群上的，一个活动一个群，几十个群下去看不到头，群成员不知道其他群的存在，每次都需要管理员去拉人，效率之低，令人乍舌！  

那么有了 Slack，QQ、微信就可以丢了，安全性和 Telegram 一样高、也不用担心聊天记录像 QQ、微信一样被监管，没有群成员上限、而且拥有比 Telegram 更优秀的机器人集成，基本国外大部分工具都可以使用，还能监控 github 的团队项目、甚至持续集成服务器状态！

Slack 作为国外的软件，我个人觉得有两个问题不太友好
* 界面是英文的，英文不好的朋友比较抵触，但是多看几遍就熟悉了
* 团队工具，想加入需要通过邮箱联系工作区所有者，然后管理者发送邀请链接，这就限制了只能在交友圈使用

#### 那么我们要解决的就是第二个问题
[Slackipy](https://github.com/avinassh/slackipy) 是一个小型 Web 服务器，可帮助您自动执行对 Slack 团队的用户邀请，让其可以像电报群一样自愿加入，不需要每次向管理员提供邮箱，然后管理员去邀请

**第一步：** 注册并且登录 [Heroku](https://dashboard.heroku.com/apps) 平台，新用户会显示 `Create new app`，在这里创建 `Slackipy` App 是免费的  

**第二步：** 打开 `github` 上的 [Slackipy](https://github.com/avinassh/slackipy) 项目，在它的 `README.md` 文件中有搭建方法，我么就使用最简单的 `Heroku` 方式，点击 `README.md` 中的这个图标  

[![](https://camo.githubusercontent.com/83b0e95b38892b49184e07ad572c94c8038323fb/68747470733a2f2f7777772e6865726f6b7563646e2e636f6d2f6465706c6f792f627574746f6e2e737667)](https://heroku.com/deploy?template=https://github.com/avinassh/slackipy/tree/master)

会自动跳转到已经登录的 `Heroku` 用户页面，并且打开一个创建 `app` 页面，需要填写以下信息：
* **App name：** 搭建的项目名称，一般用工作区的名字，搭建成功会作为 `herokuapp.com` 的二级域名前缀（示例：`nut-edu`）
* **FLASK_SECRET_KEY：** 任意长度字符串，随便输入几十个26字母配合数字即可
* **SLACK_API_TOKEN：** Slack 工作区的令牌，在 [https://api.slack.com/custom-integrations/legacy-tokens](https://api.slack.com/custom-integrations/legacy-tokens) 中创建一个 `Token`，复制过来
  
![Screenshot from 2019-10-04 22-55-21.png](http://ww1.sinaimg.cn/large/006kWbIoly1g7mk6m8jvhj30hw05xt93.jpg)
<span style="color:#ff0000">注意：创建 Token 成功之后，会在工作区的 App Manage 中出现一个名字为 `Slack API Tester` 的 App，注意删除 App 的时候不要把它删除了，否则 Token 也会自动被删除</span>
  
* **SLACK_TEAM_ID：** 你的 Slack 工作区名称（示例：`nut-edu`）
  
输入完成单击 `Deploy app` 开始安装，安装完成点击 `view` 按钮打开页面 [https://nut-edu.herokuapp.com/](https://nut-edu.herokuapp.com/) 观察效果  
  
![Screenshot from 2019-11-09 17-30-36.png](http://ww1.sinaimg.cn/large/006kWbIoly1g8rx3n4j12j30ok0bf3z0.jpg)
  
出现页面之后就可以把[链接](https://nut-edu.herokuapp.com/)分享给其他人，想进入工作区的，只需要在 [https://nut-edu.herokuapp.com/](https://nut-edu.herokuapp.com/) 填入邮箱，然后填入的邮箱会收到验证信息，点击验证信息中的链接，新建密码、确认密码，就可以一并注册 Slack 账号并加入工作区！  

> 如果想加入我们打卡学习编程的朋友，欢迎点击下图加入我们的 Slack 工作区
  
[![Screenshot from 2019-11-09 16-50-26.png](http://ww1.sinaimg.cn/large/006kWbIoly1g8rx6cd76cj31hc0u0ajf.jpg)](https://nut-edu.herokuapp.com/)
  
建议 Slack 移动端 APP 和[电脑版客户端]()一起食用，更爽！
  




