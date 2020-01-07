---
title: Hexo＋Github搭建静态博客
date: 2019-05-31 17:00:40
categories: [Hexo]
tags: [hexo,github pages,node.js,npm,git,rss,sitemap,google Analytics,Live2D,disqus,gitalk]
---
<span style="color:#B900ff;">什么是Hexo？</span>  
[Hexo](https://hexo.io/docs/)是一个快速，简单和强大的博客框架。您使用[Markdown](http://daringfireball.net/projects/markdown/)（或其他语言）撰写帖子，Hexo会在几秒钟内生成具有漂亮主题的静态文件  
  
#### 安装Hexo  
安装Hexo之前需要首先安装以下依赖  
> [Node.js](http://nodejs.org/)（至少应该是nodejs 6.9）  
> [Git](http://git-scm.com/)  
  
如果`Node.js`和`Git`在你本机安装好了，可以直接使用以下命令安装`Hexo`  
```bash
sudo npm install -g hexo-cli    #安装hexo
```
<span style="color:#ff0000;">如果还没有安装`Node.js`和`Git`，请先安装好，再执行上面的安装命令安装`hexo`</span>  
  
##### 安装Git及SSH  
注意：这里我是以`ArchLinux`为例，`Debian`系的`Ubuntu`、`Deepin`替换安装命令`pacman`为`apt`即可
```bash
sudo pacman -S git #安装git
git config --global user.name "Your Name"   #配置你的git用户名
git config --global user.email "youremail@example.com"      #配置你的git邮箱
git config --list   #查看

sudo pacman -S openssh   #安装ssh服务
ssh-keygen -t rsa -C "youremail@example.com"    #邮箱同上，一路按Enter
cd ~/.ssh   #进入.ssh隐藏文件夹
gedit id_rsa.pub       #打开公钥并复制id_rsa.pub文件中的内容
```
进入你自己的[github](https://github.com) 
进入`Settings` -> `SSH and GPG keys` -> `New SSH key`  
新建一个key，名字随便，把复制的内容粘贴上去  
  
##### 安装Node.js及npm包  
```bash
yay -S nodejs npm       #安装
  
node -v     #查看node.js版本
npm -v      #查看npm版本

sudo npm config set registry https://registry.npm.taobao.org    #更换npn镜像为淘宝镜像
sudo npm config list    #查看下配置是否生效
```
  
#### 建立博客文件夹
手动创建一个文件夹，示例：`hexoblog`  
```bash
hexo init hexoblog    #初始化文件夹
cd hexoblog   #进入文件夹
npm install #安装必备的hexo组件
```
然后可以看到文件夹中自动生成了一些文件  
```bash
.
├── _config.yml     #网站的配置信息，您可以在此配置参数
├── node_modules    #npm安装的所有程序包
├── package.json    #npm安装的应用程序的信息
├── package-lock.json   #针对和package.json中的应用程序提供更多详细信息
├── scaffolds       #文章模版文件夹
├── source          #资源文件夹，以下划线开头的文件/文件夹会被忽略
|   ├── _drafts
|   └── _posts
├── themes          #主题文件夹，Hexo会根据主题来生成网页
|   └── landscape   #原始的hexo默认主题
├── yarn-error.log  #日志文件
└── .gitignore      #忽略文件，用于配置忽略条件
```
接下来运行以下两个命令，查看效果  
```bash
hexo g  #生成静态网页
hexo s  #打开本地服务器
```
生成的静态文件会存放在`hexoblog`下的`public`文件夹中，每次生成，里面的文件会替换掉原来的文件  
然后终端会提示打开 [http://localhost:4000](http://localhost:4000) 预览效果，使用浏览器打开可以看到，**好丑好丑**，是不是？  
  
#### 更换主题  
由于默认的 hexo 主题`landscape`实在太丑了，所以我们可以去 [hexo 主题](https://hexo.io/themes/)中找一个自己喜欢的主题，例如我用的是 [Replica](https://github.com/sabrinaluo/hexo-theme-replica) ，一款模范 github 的博客主题，在它的 `README.md` 文件中已经给出了[效果预览](https://sabrinaluo.github.io/tech/)，以及相关小部件 <span style="color:#ff0000">(npm应用程序)</span>  
```bash
git clone git@github.com:sabrinaluo/hexo-theme-replica.git themes/replica     #将主题克隆到主题文件夹
```
在 `hexoblog`(项目根目录) 的 `_config.yml` 中指定主题为 `replica`  
```bash
theme: replica
```
它还提供了一个 `_config.yml` 模板文件，我们就用它这个替换掉原来的 `_config.yml` 中的内容，`_config.yml` 文件如下  
<details>
<summary>_config.yml 模板文件</summary>

```bash
# Hexo Configuration
## Docs: http://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site
title: TEAPER       #网页title标题栏
description: 一个人学会思考，比学会啥都强！     #网站描述
author: teaper      #您的名字
language: zh-CN     #网站使用的语言
timezone: Asia/Shanghai     #网站时区。Hexo 默认使用您电脑的时区
favicon: https://avatars2.githubusercontent.com/u/37693348?s=460&v=4        #网站标题栏logo

# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: https://www.teaper.dev     #网址
root: /         #网站根目录	
permalink: :year/:month/:day/:title/    #文章的网址链接格式
permalink_defaults:     #永久链接中各部分的默认值

# Directory
source_dir: source      #资源文件夹，这个文件夹用来存放内容
public_dir: public      #公共文件夹，这个文件夹用于存放生成的站点文件
tag_dir: tags           #标签文件夹
archive_dir: archives       #归档文件夹
category_dir: categories        #分类文件夹
code_dir: downloads/code        #Include code 文件夹
i18n_dir: :lang     #国际化（i18n）文件夹
skip_render:        #跳过指定文件的渲染，您可使用 glob 表达式来匹配路径

# Writing
new_post_name: :year:month:day-:title.md    #新文章的文件名称格式
default_layout: post        #预设布局
titlecase: false        #把标题转换为 title case     
external_link: true     #在新标签中打开链接
filename_case: 0        #把文件名称转换为 (1) 小写或 (2) 大写
render_drafts: false        #是否显示草稿
post_asset_folder: false    #启动 Asset 文件夹
relative_link: false        #把链接改为与根目录的相对位址
future: true        #显示未来的文章
highlight:      #代码块的设置
  enable: true
  line_number: true
  auto_detect: false
  tab_replace:

# Category & Tag
default_category: uncategorized     #默认分类
category_map:       #分类别名
tag_map:        #标签别名

# Date / Time format
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD     #日期格式
time_format: HH:mm:ss       #时间格式

# Pagination
## Set per_page to 0 to disable pagination
per_page: 0     #每页显示的文章量 (0 = 关闭分页功能)
pagination_dir: page        #分页目录

# Extensions
## Plugins: http://hexo.io/plugins/
## Themes: http://hexo.io/themes/
theme: replica      #要使用的主题

# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:     #部署部分的设置
  type: git
  repo: git@github.com:teaper/hexoblog.git
  branch: gh-pages

# Google Analytics
ga: UA-140493383-1 # GA code UA-XXXXXXXX-X

#marked setting for markdown
marked:
  gfm: true
  pedantic: false
  sanitize: false
  tables: true
  breaks: true
  smartLists: true
  smartypants: true

gcs: 014633199185561276043:bpv9wnr4qhc # GOOGLE CUSTOM SEARCH
baidutongji:  # BAIDU TONGJI CODE
disqus: teaperdev

location: 广州      #地址
email: www@teaper.dev       #邮箱

avatar: https://avatars2.githubusercontent.com/u/37693348?s=460&v=4     #头像
social:         #图标链接
  github: https://github.com/teaper     
  weibo: https://www.weibo.com/5806191772
  linkedin: https://twitter.com/TEAPERS

# flagcounter 收集来自世界各地的旗帜（我博客最下方的国旗）,注册成功提供以下参数
flagcounter_href: https://info.flagcounter.com/MrmG 
flagcounter_img_src: https://s04.flagcounter.com/count2/MrmG/bg_FFFFFF/txt_6A737D/border_FFFFFF/columns_7/maxflags_14/viewers_3/labels_1/pageviews_1/flags_0/percent_0/ 


# RSS  ---------------------------------------------------------


# Nofollow Config
nofollow:
     enable: true
     exclude: https://github.com/teaper 

# SiteMap
sitemap:
    path: sitemap.xml
baidusitemap:
    path: baidusitemap.xml
```
</details>
  
具体的参数信息，可以参考 [hexo配置](https://hexo.io/zh-cn/docs/configuration)，我们在其基础上改改，然后重新运行一下  
```bash
hexo cl  #清除原来生成在public文件夹中的网页文件
hexo g && hexo s    #重新生成文件以及启动服务
```
可以看到效果如下  
  
![](https://i.loli.net/2019/05/29/5cee92d3321bd20362.png)
  
但是有一些小问题，例如：  
> 菜单栏：类别Categories、标签Tag、RSS都不能用  
> Social Media下面我不要Linkedin  
  
##### 解决类别Categories、标签Tag不能用问题？  
在`source`文件夹下创建以下文件以及文件夹  
```bash
.
├── categories      #文件夹
│   └── index.md        #文件
└── tags
    └── index.md
```
其中`categories/index.md`内容如下：
```bash
---
title: categories
date: 2016-01-21 18:46:15
---
```
其中`tags/index.md`内容如下：
```bash
---
title: tags
date: 2016-01-21 18:45:55
---
```
重新运行`hexo g && hexo s`命令之后，就可以使用了，但是没有一个标签/分类  
  
##### 如何添加分类/标签  
查看 `_config.yml` 中的 `default_layout:` 参数，上方模板中的是 `post`，那么就在模板文件夹 `scaffolds` 中修改 `post.md` 文件，添加`categories:`和`tags:`两个模块  
```bash
---
title: {{ title }}
date: {{ date }}
categories: 
tags: 
---
```
在创建博客文件的时候就会套用这个模板，给`categories:`和`tags:`两个模块填入参数即可  
  
#### 新建博文  
```bash
hexo new helloHexo      #创建一篇博文
#----------------------------输出提示--------------------------------------
INFO  Created: ~/Downloads/hexoblog/source/_posts/20190529-helloHexo.md #提示创建的文件在这个路径
```
使用 `gedit` 编辑这个文件，随便写点东西，再加个分类和标签  
```bash
---
title: 你好，Hexo
date: 2019-05-29 22:37:20
categories: [hexo]
tags: [hexo,markdown,github]
---
## Hexo＋Github搭建静态博客
```
注意，分类(categories) 只能有一个，写在`[]`中，标签 `tags` 可以有多个，中间用英文逗号隔开，保存后运行 `hexo g && hexo s` 重新生成文件即可看到效果  
  
注意：修改博客直接编辑`.md`文件，之后一定要先运行 `hexo g` 之后才能运行 `hexo cl` 清除缓存
  
#### 添加RSS  
解决菜单栏 `RSS` 不能使用的问题，首先安装 `hexo-generator-feed` 包
```bash
npm install hexo-generator-feed
hexo g  #生成文件
```
可以看到，在`public`文件夹中多了一个`atom.xml`文件；然后在`_config.yml`文件中配置该插件
```bash
# RSS
feed:
    type: atom
    path: atom.xml
    limit: 20
    hub:
    content:
    content_limit:
    content_limit_delim: ' '
```
重新运行 `hexo g && hexo s` 命令就可以看到效果了，单击会在浏览器中打开 `atom.xml` 文件，如果想订阅别人的 RSS ，可以参考——[【如何用RSS订阅？】](https://juejin.im/post/5c382a326fb9a049f15469eb)  

    
#### 部署博客  
安装 `hexo-deployer-git` 包 
```
npm install hexo-deployer-git  #git部署插件
```
然后在`_config.yml`文件中配置该插件
```bash
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: git     
  repo: https://github.com/teaper/hexoblog.git      #github public仓库地址，没有的话去github创建一个
  branch: gh-pages      #要部署到仓库的分支，master分支用来存md文件
```
然后就可以使用以下命令一键上线  
```bash
hexo d  #一键部署
```
部署成功，在 `hexoblog` 仓库设置中，找到 [Github Pages]() ,选择 `gh-pages` 分支作为主页，然后勾选 `Enfore HTTPS` 复选框，上面会提示一个地址  Your site is published at https://www.teaper.dev/ 可以打开预览效果 <span style="color:#ff0000">(开心吧？快给点掌声)</span> 
  
![](https://i.loli.net/2019/05/29/5cee9ff324f4e50085.png)
  
可能你已经发现了，我的地址不一样，是自定义的域名`teaper.dev`，这就是我接下来要说的  
  
#### 自定义域名  
我这个`.dev`的域名是谷歌美国时间2019年2月19日开始开放注册，[阿里云](https://www.aliyun.com)注册不到，你可以在[Google Domains](https://domains.google/tld/dev/)或[Godaddy](https://account.godaddy.com)注册，当然，你也可以在阿里云注册其他顶级域名  
> 提示：我是Godaddy注册的，解析 A 记录到阿里云轻量服务器的时候会有问题  
  
购买好域名后，在 DNS 处添加解析，相关信息如下  
  
| 类型 | 名称 |	值 | TTL |
| ---- | ---- | -- | --- |
| CNAME | www |	teaper.github.io | 1 小时 |
  
名称 `www`会称为子域名`www.teaper.dev`，值填写你 `Github Page` 最后提示的网址前部分，`teaper.github.io`中的`teaper`是我github用户名，你对应的就是你自己的用户名  
  
到此为止，输入 [http://www.teaper.dev](http://www.teaper.dev) 就会自动去找 github 服务器，但是github服务器找不到我们的博客位置，这时候就需要在 `hexoblog/public` 下创建一个 `CNAME` 文件标记博客地址，填入你要显示的子域名  
```bash
www.teaper.dev      
```
最后，重新使用 `hexo d` 命令提交到 github 即可使用自定义域名访问  
  
#### 站点地图  
站点地图是提供给搜索引擎快速收录网站信息用的，里面包含了该网站所有可访问的网址链接，这里我们一般就使用谷歌和百度  
```bash
npm install hexo-generator-sitemap --save   #谷歌
npm install hexo-generator-baidu-sitemap --save     #百度
```
在`_config.yml`中添加以下配置  
```
# SiteMap
sitemap:
    path: sitemap.xml
baidusitemap:
    path: baidusitemap.xml
```
使用`hexo g && hexo s`之后，会看到 `public` 文件夹下多出了`sitemap.xml`和`baidusitemap.xml`这两个文件，使用 `hexo d` 将它们提交到 `github`  
在地址栏输入[http://www.teaper.dev／sitemap.xml](http://www.teaper.dev／sitemap.xml)和[http://www.teaper.dev／baidusitemap.xml](http://www.teaper.dev／baidusitemap.xml)可以看到文件就成功了  
  
**谷歌**去 [Search Console](https://search.google.com/search-console )添加一个资源，名称就是 [www.teaper.dev](www.teaper.dev) ，点击左侧列表中的**站点地图**，添加新的站点，输入站点文件的位置 `http://www.teaper.dev/sitemap.xml` ，注意不是 https 哦！提交之后谷歌会抓取，一天之后会有结果的～  
  
**百度**去[百度站长平台](https://link.jianshu.com/?t=http://zhanzhang.baidu.com/dashboard/index)注册一个账号，然后需要使用 DNS 解析验证以下网站所有权，然后也是添加站点，指定站点地图文件的网址，由于百度实在垃圾，收录不到 github 的站点信息，所以我就不写了  
  
![](https://i.loli.net/2019/05/30/5ceeb0550664b92514.png)  
  
#### 谷歌自定义搜索  
进入[谷歌自定义搜索](https://link.juejin.im/?target=https%3A%2F%2Fcse.google.com%2F)，然后新增一个搜索引擎，输入 `www.teaper.dev` ，搜索引擎名称也叫 `www.teaper.dev` ；然后在**修改搜索引擎**中的设置选项卡，添加你要搜索的网址，最少写个 `https://www.teaper.dev` 吧，其他的留给你自己折腾  
  
#### Google Analytics分析  
主要是为了统计数一下网址的浏览量和阅读量  
[Google Analytics](https://analytics.google.com)注册一个账号，然后创建资源和媒体，添加网址，成功之后可以看到一个 `UA-140493383-1` 的 `GA Code` 
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g3inq43qlnj30my06ajry.jpg)
  
将它配置在 `_config.yml` 中即可
```
# Google Analytics
ga: UA-140493383-1      
```
追进程序代码可以不用写，因为 `hexoblog/themes/replica/layout/_partial/head.ejs`中已经有以下这段代码
```
<%- partial('_widget/google-analytics') %>
```
  
#### Live2D 血小板  
进入`hexoblog/themes/replica/source`主题文件夹，克隆[血小板](https://github.com/JIAOBANTANG/live2d-xuexiaoban)项目源码  
```
git clone git@github.com:JIAOBANTANG/live2d-xuexiaoban.git
```
会有一个 `live2d` 文件夹和一个 `index.html` 文件，我们要做的就是把 `index.html` 中的内容写入到对应模板文件中  
  
在`themes/replica/layout/_partial/head.ejs`中引入css样式
```xml
<link rel="stylesheet" href="<%= config.root %>live2d/css/live2d.css">
```
在`head.ejs`同级的`footer.ejs`中的Body标签内<span style="color:#ff0000">(最外层)</span>追加以下 div 块  
```xml
<!-- Live2D血小板 -->
<div id="landlord">
    <div class="message" style="opacity:0"></div>
    <canvas id="live2d" width="560" height="500" class="live2d"></canvas>
    <!-- <div class="hide-button">隐藏</div> -->
</div>

<script type="text/javascript" src="https://cdn.bootcss.com/jquery/2.2.4/jquery.min.js"></script>
<script type="text/javascript">
    var message_Path = '/live2d/'
    var home_Path = 'https://www.teaper.dev/'
</script>
<script type="text/javascript" src="/live2d/js/live2d.js"></script>
<script type="text/javascript" src="/live2d/js/message.js"></script>
<script type="text/javascript">
    loadlive2d("live2d", "/live2d/model/xiaoban/model.json");
</script>
```
使用 `hexo g && hexo s` 重新生成文件后就可以看到效果了，有没有小激动啊？嘤嘤嘤～～～
  
#### 音乐控件  
使用jQuery音乐插件[QPlayer](http://www.jq22.com/demo/QPlayer-master20170104/)，找到它的[github](https://github.com/Jrohy/QPlayer)地址，将其克隆到电脑上 <span style="color:#ff0000">(随便一个地方，只要不在 hexoblog 中就行)<span>  
```bash
git clone git@github.com:Jrohy/QPlayer.git
```
进入文件夹，可以看到有这些文件  
```bash
.
├── css
│   └── player.css  #播放器样式
├── img
│   ├── 2.png       #上/下一曲按钮
│   └── audio_sprite.png        #其他按钮
├── index.html  #演示页面（需要使用Firefox打开）
├── js
│   ├── jquery.marquee.min.js   #不明
│   ├── jquery.min.js   #普通jquery
│   └── player.js   #播放器功能实现
└── README.md   #没啥用的
```
经过一段时间的兼容性比对，确定没问题之后，我决定使用以下方案：  
  
将`player.css`复制到`hexoblog/themes/replica/source/css`中  
将`img`文件夹整个复制到`hexoblog/themes/replica/source`中  
将`jquery.marquee.min.js`和`player.js`复制到`hexoblog/themes/replica/source/js`中 <span style="color:#ff0000">(jquery.min.js就不要了)</span>  
  
将`index.html`中的以下`<script></script>`部分全部拷贝，粘贴在新建的`hexoblog/themes/replica/source/js/playlist.js`中，然后html上使用 src 引入该 js 文件  
  
由于它自带的这些音乐列表无法使用，所以我把我的分享出来  
<details>
<summary>我的playlist.js歌单列表</summary>

```js
<script>
var playlist = [
{title:"Lovely Day",artist:"朴信惠",mp3:"http://fs.open.kugou.com/2925e0aa5e1dbbac899c4dc95661bad6/5ceb6418/G006/M07/1C/18/poYBAFS5wk6AXMkqADCFZFnRisU820.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20170711/20170711145703533.jpg?param=106x106",},
{title:"起风了",artist:"买辣椒也用券",mp3:"http://fs.open.kugou.com/d6cde41d2513b5d55822d9b736f069d5/5ceb64f5/G117/M04/09/11/VZQEAFyLqDSAMZdUAEimgFeiNtg723.mp3",cover:"http://p2.music.126.net/diGAyEmpymX8G7JcnElncQ==/109951163699673355.jpg?param=106x106",},
{title:"心如止水",artist:"Ice Paper",mp3:"http://fs.open.kugou.com/b3f975be33635ba5306f0487ddbc0074/5ceb64d3/G117/M08/13/1F/tQ0DAFx5A3uAfPOBAC1GYIo2lU0616.mp3",cover:"http://p1.music.126.net/MLQl_7poLz2PTON6_JZZRQ==/109951163938219545.jpg?param=106x106",},
{title:"临安初雨",artist:"小旭音乐",mp3:"http://fs.open.kugou.com/f059a518f3a31cd91be1aaf3070ccae9/5ceb5466/G012/M02/09/04/rIYBAFUJqVSAfHZcAB13gGBS5S4671.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20130531/20130531162457903.jpg?param=106x106",},
{title:"一百万个可能",artist:"Christine Welch",mp3:"http://fs.open.kugou.com/7b0fea66de0212e7f36c0557b4a54f0d/5ceb550a/G001/M01/1D/1D/QQ0DAFSNgAeAPiAIAENKHp_Pbbw776.mp3",cover:"https://y.gtimg.cn/music/photo_new/T002R300x300M000004IWoIx34J0fT.jpg?param=106x106",},
{title:"神谕法则",artist:"SING女团",mp3:"http://fs.open.kugou.com/1d629a349060ed2fbe736d6fa7a34b8a/5ceb567a/G141/M08/0B/10/zQ0DAFujeo2APYF4ADhbc0IcpIY564.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20190218/20190218200851155.jpg?param=106x106",},
{title:"轮回之境",artist:"CRITTY",mp3:"http://fs.open.kugou.com/62485366c101badd2b1aa1ba8b5efbd8/5ceb56d5/G013/M01/05/0D/TQ0DAFUKu3GAEIQ5AD1w-bfK0P4695.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20190322/20190322004553123011.jpg?param=106x106",},
{title:"404 not found",artist:"REOL",mp3:"http://fs.open.kugou.com/7cab120be79c215f84bb504ddfb95006/5ceb5500/G157/M05/17/13/fZQEAFy4nK6AQzP-ADxLRpnEs6Q820.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20181226/20181226152454854.jpg?param=106x106",},
{title:"Because of You",artist:"Kelly Clarkson",mp3:"http://fs.open.kugou.com/5978f3fda40537f06a5f88f9544eba20/5ceb576f/G009/M04/1C/09/qYYBAFT9zkiAGaRUADWa8vjlrM0384.mp3",cover:"https://upload.wikimedia.org/wikipedia/en/thumb/c/cb/Because_of_You_Single.PNG/220px-Because_of_You_Single.PNG?param=106x106",},
{title:"China-X",artist:"徐梦圆",mp3:"http://fs.open.kugou.com/f81d9488f62defdc4a1ae36eeca32124/5ceb5930/G056/M04/1D/10/2IYBAFakY0iAB4D0ADc4AfWXG_g876.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20170413/20170413190130260.jpg?param=106x106",},
{title:"Cry On My Shoulder",artist:"Superstar",mp3:"http://fs.open.kugou.com/265c1bdd202e4240f394e1dff6776ad9/5ceb5990/G076/M09/04/17/7IYBAFgullaARyg6ADoevPs0ye4394.mp3",cover:"https://i.ytimg.com/vi/46VPqRVD9C8/hqdefault.jpg?param=106x106",},
{title:"I Could Be The One",artist:"Donna Lewis",mp3:"http://fs.open.kugou.com/f1d0f08b04c35638ef1141097d9ddfdf/5ceb5a01/G008/M07/14/05/SA0DAFUK1DuAPFNoADc38GF12LY937.mp3",cover:"http://pic.pimg.tw/locking1218/1387317563-867639266_n.png?param=106x106",},
{title:"Love home",artist:"纯音乐",mp3:"http://fs.open.kugou.com/d9e6bb48091953cc7b97732c92aafd2d/5ceb5b0a/G060/M01/1F/01/HJQEAFcFCnmAPq5MACZrRhHLltY343.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20130531/20130531162457903.jpg?param=106x106",},
{title:"Lydia",artist:"F.I.R.",mp3:"http://fs.open.kugou.com/67e04c0b2648bcd8bbd9567e8f2e5cce/5ceb5a12/G009/M03/14/1B/SQ0DAFT7tOSAZfkgADpd7U9r--c363.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20181219/20181219180413855.jpg?param=106x106",},
{title:"Mermaid girl",artist:"森永真由美",mp3:"http://fs.open.kugou.com/1de7882c5c53c56f05b20259b877ec6b/5ceb5ca7/G057/M0A/05/13/2YYBAFZ9w6aAckLxACwlF6vARdM570.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20190114/20190114120339434.jpg?param=106x106",},
{title:"My Love",artist:"Westlife",mp3:"http://fs.open.kugou.com/f9685fd7284d0c543ebfba327aabc5b8/5ceb59e6/G011/M03/1A/18/q4YBAFUJpaOAP5qMADkJjLkKC14038.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20190110/20190110173511430.jpg?param=106x106",},
{title:"PLANET",artist:"ラムジ",mp3:"http://fs.open.kugou.com/fcace83c0b69e90a0967c0301e24c29b/5ceb5d70/G097/M08/03/1B/oQ0DAFkGqw6AdT6_ADtw0uWrvgc250.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20151219/20151219102713856.jpg?param=106x106",},
{title:"Sakura",artist:"いきものがかり",mp3:"http://fs.open.kugou.com/b6b3c9825a502250eaefd6603757bd29/5ceb5df5/G015/M09/0B/09/r4YBAFUbB-CAN7FRAFZ5gzCshG0445.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20160911/20160911161922354.jpg?param=106x106",},
{title:"~君がくれたもの~",artist:"茅野愛衣、戸松遥",mp3:"http://fs.open.kugou.com/e019ea8d77a3d29b5da4d0284a9d6fa0/5ceb5e49/G012/M05/0E/04/TA0DAFUHca-Afk1DAFYYaiaECms593.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20150227/20150227114508898329.jpg?param=106x106",},
{title:"Take Me Hand",artist:"DAISHI DANCE",mp3:"http://fdfs.xmcdn.com/group42/M0B/03/80/wKgJ81rjI5jSyYh7AB--YNV-lLs148.mp3",cover:"http://imagev2.xmcdn.com/group42/M0A/F0/1D/wKgJ9Frhz9jzNZANAAA_25jQd_Y118.jpg!op_type=3&columns=640&rows=640?param=106x106",},
{title:"不怨",artist:"袁姗姗",mp3:"http://fs.open.kugou.com/2711214e5ef74b5bc500480161ecea82/5ceb63d2/G011/M0A/04/15/q4YBAFULdx2AE4H9AENngUa7Wo4830.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20180913/20180913164746762.jpg?param=106x106",},
{title:"参商",artist:"不才",mp3:"http://fs.open.kugou.com/c3ced3e8f5053f6b5e89ce33888925a0/5ceb642a/G070/M05/14/19/JpQEAFe1rZCAR9AkAEb7mM3NYbQ587.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20150821/20150821160527934958.jpg?param=106x106",},
{title:"抽离",artist:"徐良、刘丹萌",mp3:"http://fs.open.kugou.com/3f0f9ca0256fa4a8ecb41830da96dc0b/5ceb653d/G011/M08/13/01/Sw0DAFUKP6yAJYifAD8vjhnvGXs436.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20190311/20190311135913203.jpg?param=106x106",},
{title:"渡情",artist:"鞠婧祎",mp3:"http://fs.open.kugou.com/896dfc68690ed5ba3b64fd15e8315377/5ceb6637/G158/M08/1C/11/PocBAFyh1vGAYZuyACiPb1k99KI437.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20190110/20190110174817500.jpg?param=106x106",},
{title:"诀别诗",artist:"胡彦斌",mp3:"http://fs.open.kugou.com/a3bcceb8c79bf85be0abbf5a63918ebc/5ceb6587/G007/M02/13/18/p4YBAFS4GimAK4jGADw8R0t4sj8307.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20170921/20170921171126282.jpg?param=106x106",},
{title:"七秀坊",artist:"月之门",mp3:"http://fs.open.kugou.com/04cb4548a0ec4ad79feaad68a4084f3c/5ceb66c5/G009/M08/15/17/qYYBAFUPfnWAM9_tAD79Msb3GDk803.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20150716/20150716204240137592.jpg?param=106x106",},
{title:"City",artist:"羽肿",mp3:"http://www.170hi.com/kw/other.web.nf01.sycdn.kuwo.cn/resource/n2/32/74/1955672015.mp3",cover:"https://images.shazam.com/coverart/t446909531-b1440714641_s400.jpg?param=106x106",},
{title:"さくら~あなたに",artist:"兰草薄荷",mp3:"http://fs.open.kugou.com/1a703819efa017badd4d83f1c71ec749/5cec9b2f/G110/M04/0C/03/TpQEAFl2WkSARXuyAEkReouicLU314.mp3",cover:"https://images-na.ssl-images-amazon.com/images/I/416sZkxLvxL._SX355_.jpg?param=106x106",},
{title:"爱丫爱丫",artist:"By2",mp3:"http://fs.open.kugou.com/02d2ec41fcdb134f54b3c2636f19b85f/5cec9b8e/G010/M05/12/0F/Sg0DAFUKNk-AZfDxADilOF7pdfM473.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20170602/20170602141818448.jpg?param=106x106",},
{title:"風の住む街",artist:"磯村由紀子",mp3:"http://fs.open.kugou.com/59144ec64af815622b3df4554a0c68bd/5cec9b46/G012/M05/03/18/TA0DAFUJEmuAPmOxAEXoAIFZ2qs558.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20150430/20150430163440818299.jpg?param=106x106",},
{title:"该怎么",artist:"楼一萱",mp3:"http://fs.open.kugou.com/1a06cd43353e4746b5dac57c45cc6749/5cec9d49/G114/M09/1C/05/UpQEAFvlVceACD-ZADVdCPwr-iA411.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20190322/20190322042458484661.jpg?param=106x106",},
{title:"歌に形はないけれど",artist:"花たん",mp3:"http://fs.open.kugou.com/385ce61a125c8080023133eb57a443c5/5cec9be0/G004/M03/04/11/pIYBAFT9fuKAUY6FAFCQthJknCc986.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20151219/20151219145533755.jpg?param=106x106",},
{title:"黑白颠倒",artist:"夏婉安",mp3:"http://fs.open.kugou.com/347ad36442427cf0ecda097f3781b3e3/5cec9e04/G141/M01/1E/0F/LYcBAFufUyCAatIcAD0voHvO4cw539.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20190417/20190417172223938.jpg?param=106x106",},
{title:"红昭愿",artist:"音阙诗听",mp3:"http://fs.open.kugou.com/630026d94f59d394b76128295e0f88ee/5cec9b31/G089/M05/0F/18/-YYBAFlqur6AKhY_ACpPcyzNiC0500.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20170721/20170721162945806.jpg?param=106x106",},
{title:"后会无期",artist:"徐良、汪苏泷",mp3:"http://fs.open.kugou.com/5501a5fc48fbe9a8d4378f1a174bc1e8/5cec9d0f/G006/M07/14/17/poYBAFS_vVqAUNBEADO387ANGhg481.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20190311/20190311135913203.jpg?param=106x106",},
{title:"花开半夏",artist:"爱朵女孩",mp3:"http://fs.open.kugou.com/d5dba4b64ecd202259247ad0a13caf7b/5cec9f28/G011/M03/07/05/q4YBAFT64JeASLqEAC1HoCCRI34922.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20190322/20190322000546528435.jpg?param=106x106",},
{title:"嫁衣",artist:"吴虹飞&幸福大街",mp3:"http://fs.open.kugou.com/c08023b444083ff486a88ba731d9310a/5cec9f6f/G121/M05/0D/1B/uQ0DAFo6DNyAAdj1AFDSyM4wM0M117.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20111123/20111123150044954.jpg?param=106x106",},
{title:"解药",artist:"孙紫晴",mp3:"http://fs.open.kugou.com/e09caaf84b38439c8ac76f9530c05ad6/5cec9c53/G135/M06/01/07/Z5QEAFuKgSyAZYInADpA09oxkQk172.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20130531/20130531161511554.jpg?param=106x106",},
{title:"矜持",artist:"姚贝娜",mp3:"http://fs.open.kugou.com/3a3059c31a353951d48de294c6a9c75d/5ceca028/G009/M06/0D/1F/qYYBAFUPb-uAEZ9FAD87WofR9HQ889.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20140304/20140304140958863665.jpg?param=106x106",},
{title:"锦鲤抄",artist:"银临、云の泣",mp3:"http://fs.open.kugou.com/638010b766f2c9887dbe83f82f1e86f2/5cec9ddc/G010/M06/1C/14/Sg0DAFUJgEeAMyZoADvachXfuK0962.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20160815/20160815163511387.jpg?param=106x106",},
{title:"看到风",artist:"冯提莫",mp3:"http://fs.open.kugou.com/5ed4d8fe7238de9fe612fa8669b69461/5cec9d9a/G157/M06/0D/00/PYcBAFyjBc-AO1lkAE3-W_SdmR4725.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20190102/20190102180610282.jpg?param=106x106",},
{title:"佛系少女",artist:"冯提莫",mp3:"http://fs.open.kugou.com/580d96772ae5c347197378774d633f45/5ceca13d/G110/M03/14/10/TpQEAFvuN3aAP7ezADCfVNXrcYE490.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20190102/20190102180610282.jpg?param=106x106",},
{title:"可不可以",artist:"张紫豪",mp3:"http://fs.open.kugou.com/9325794f4bf6d4962273458b3a6cfe21/5cec9e56/G130/M05/12/00/YpQEAFrxTvOAQ8I-ADrUXaC6U88058.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20180829/20180829150149484.jpg?param=106x106",},
{title:"客官不可以",artist:"徐良、小凌",mp3:"http://fs.open.kugou.com/2690d6871e18645aa36a5630d998b213/5cec9e79/G006/M09/17/19/poYBAFTAPymAFwZsADhhbRy94q8040.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20190311/20190311135913203.jpg?param=106x106",},
{title:"仙剑问情",artist:"萧人凤",mp3:"http://fs.open.kugou.com/c910346617457a39c89f76bf9d535953/5ceca4b8/G001/M01/08/13/QQ0DAFS3AtGAK1RmAD3r0yHdNHY032.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20150112/20150112102446121545.jpg?param=106x106",},
{title:"桃源恋歌",artist:"GARNiDELiA",mp3:"http://fs.open.kugou.com/e2cf4d84ad7ab534608a0d10690c9a84/5ceca446/G123/M09/0C/00/uw0DAFq6hR2AHseOADgmvMarDHg157.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20181101/20181101183056556.jpg?param=106x106",},
{title:"美丽的神话",artist:"白冰、胡歌",mp3:"http://fs.open.kugou.com/c6a73961afc6cf436901c57e32ebd00c/5ceca27f/G003/M06/17/0F/Qw0DAFS5DVaADRFnAFCJpAjWxrM130.mp3",cover:"http://singerimg.kugou.com/uploadpic/pass/softhead/100/20110704/20110704111056705.jpg?param=106x106",},
/*{title:"",artist:"",mp3:".mp3",cover:".jpg?param=106x106",},*/
];
var isRotate = true;
var autoplay = false;
</script>
```
</details>
  
如果想添加歌曲，推荐一个网站[音乐外链获取](http://www.biaobaishike.com/yinyue.php)，选择酷狗音乐搜索  
  
文件都复制好了，就要把播放器植入到网页中，还是在主题模板文件中  
  
将以下代码插入到`head.ejs`的`<head>`标签内  
```xml
<link rel="stylesheet" href="<%= config.root %>css/player.css">
```
将QPlayer音乐播放插件主要代码插入到`footer.ejs`中，放在[血小板](#live2d-血小板)插件前面即可，然后将`jquery.marquee.min.js`换成了全网通用的  
```xml
<!-- QPlayer音乐播放器 -->
<div id="QPlayer">
<div id="pContent">
	<div id="player">
		<span class="cover"></span>
		<div class="ctrl">
			<div class="musicTag marquee">
				<strong>Title</strong>
				 <span> - </span>
				<span class="artist">Artist</span>
			</div>
			<div class="progress">
				<div class="timer left">0:00</div>
				<div class="contr">
					<div class="rewind icon"></div>
					<div class="playback icon"></div>
					<div class="fastforward icon"></div>
				</div>
				<div class="right">
					<div class="liebiao icon"></div>
				</div>
			</div>
		</div>
	</div>
	<div class="ssBtn">
	        <div class="adf"></div>
    </div>
</div>
<ol id="playlist"></ol>
</div>
<!-- <script src="js/jquery.min.js"></script> -->
<!-- <script src="js/jquery.marquee.min.js"></script> -->
<script type="text/javascript" src="https://cdn.bootcss.com/jquery/2.2.4/jquery.min.js"></script>
<script type='text/javascript' src='//cdn.jsdelivr.net/jquery.marquee/1.4.0/jquery.marquee.min.js'></script>
<!-- 歌单列表playlist.js -->
<script src="/js/playlist.js"></script>
<script src="/js/player.js"></script>
<script>
function bgChange(){
	var lis= $('.lib');
	for(var i=0; i<lis.length; i+=2)
	lis[i].style.background = 'rgba(246, 246, 246, 0.5)';
}
window.onload = bgChange;
</script>
```
好了，启动服务后应该就可以预览效果了，如果要添加歌曲，直接在配置文件加就行，缺点就是国外用户无法听歌，因为有墙，他们访问不到酷狗  
  
#### 评论插件  
在`themes/replica/layout/_widget` 文件夹中可以看到，该主题提供了两个评论插件[disqus](https://disqus.com/)和[gitalk](https://github.com/gitalk/gitalk#install)，默认使用`gitalk`，否则使用`disqus`  
  
<span style="color:#ff0000">注意：默认使用 gitalk ,只有翻墙的用户不能使用 gitalk 时才能使用 disqus，稍后在源码中可以看到逻辑，所以我这里就用`gitalk`示范</span>  
  
根据github上的[说明文档](https://github.com/gitalk/gitalk/blob/master/readme-cn.md)，有两种安装方式：  
  
**方式一**：直接在模板文件中引入
```bash
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.css">
<script src="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.min.js"></script>

  <!-- or -->

<link rel="stylesheet" href="https://unpkg.com/gitalk/dist/gitalk.css">
<script src="https://unpkg.com/gitalk/dist/gitalk.min.js"></script>
```
**方式二**：使用npm进行安装  
```bash
npm i --save gitalk
```
```bash
import 'gitalk/dist/gitalk.css'
import Gitalk from 'gitalk'
```
然后，需要在页面模板文件引入相关`<div>`标签，通过以下`javascript`进行调用  
```xml
<div id="gitalk-container"></div>
```
```js
var gitalk = new Gitalk({
  clientID: 'GitHub Application Client ID',
  clientSecret: 'GitHub Application Client Secret',
  repo: 'GitHub repo',
  owner: 'GitHub repo owner',
  admin: ['GitHub repo owner and collaborators, only these guys can initialize github issues'],
  id: location.pathname,      // Ensure uniqueness and length less than 50
  distractionFreeMode: false  // Facebook-like distraction free mode
})

gitalk.render('gitalk-container')
```
好在`replica`主题下的`layout/_widget/gitalk.ejs`文件中已经帮助我们配置好了，不需要我们操作上面两步，不过还需要进一步改进一下，由于 `github issue` 的 `label` 长度被限制在了50个字，所以我们采用 `Md5` 对 `id` 进行加密 
<details>
<summary>gitalk.ejs文件内容</summary>

```xml
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.css">
<script src="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.min.js"></script>
<!-- 引入MD5的js -->
<script src="https://cdn.bootcss.com/blueimp-md5/2.10.0/js/md5.min.js"></script>
<div class='container' id="gitalk-container"></div>
<script>
    var gitalk = new Gitalk({
        clientID: '<%= config.gitalk.client_id %>',
        clientSecret: '<%= config.gitalk.client_secret %>',
        repo: '<%= config.gitalk.repo %>',
        owner: '<%= config.gitalk.owner %>',
        admin: ['<%= config.gitalk.owner %>'],
        /*添加id属性，使用MD5加密，注意后面的逗号*/
	    id: md5(location.pathname),
        distractionFreeMode: true
    })
    gitalk.render('gitalk-container')
</script>
</script>
```
</details>
  
并且在`replica`主题下的`layout/_partial/article.ejs`中已经引入`div标签`  
```xml
<!-- if-else逻辑，如果gitalk不能使用才使用disqus，但是disqus只有翻墙之后的用户才能使用，并且满足前一条if-else逻辑 -->
<% if (config.gitalk ) { %>
    <%- partial('_widget/gitalk') %>
<% }else{ %>
    <%- partial('_widget/disqus') %>
<% } %>
```
从上面可以看到一个判断，如果使用`config.gitalk`有值就引入`gitalk`的`div`以及相关样式，否则使用`disqus`  
> Disqus使用方法：去[Disqus](https://disqus.com/)创建一个网站，将名字配置到`_config.yml`即可  
  
```bash
disqus: teaperdev   #配置disqus
```
不多说，继续`gitalk`配置，在`hexoblog/_config.yml`中配置如下参数  
```bash
# gitalk
gitalk:
  enable: true      
  client_id: 79a82d93fcd761032f45        
  client_secret: b784392d66d81a991565a56598f2d369e71e21ad    
  repo: hexoblog	#你要新建一个仓库来保存这些comments，这里repo就使用当前博客的
  owner: teaper    #管理员用户和仓库所有者(gitalk.ejs中admin和owner都是owner的值，所以这里只写owner)
```
其中`Client Id`需要去[github创建应用程序](https://github.com/settings/applications/new)获取，`Authorization callback URL` 主页地址和回调地址填写你的网站地址<span style="color:#ff0000">(注意：两个地址后面都要加上/，https或http无所谓，例如我的：https://www.teaper.dev/)</span>  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g3ky8py2asj31hc156jvr.jpg)  
  
配置好，使用`hexo g && hexo s`重新运行服务后应该可以看到效果了，不过还需要使用`hexo d`命令进一步将其提交到服务器，才能知道回调地址是否有效  
  
#### 站内搜索  
目前站内搜索方案基本逻辑都一样，通过一个 `XML` 文件收录站点信息，查询的时候直接检索 `XML` 文件即可，我这博客用的是 `hexo-generator-search`  
```bash
npm install --save hexo-generator-search    #安装
```
安装成功之后在 `hexoblog/package.json` 中就会有一条 `"hexo-generator-search": "^2.4.0"`记录，然后将以下搜索框标签插入到你想要显示的 `ejs` 文件中的位置  
```html
<div id="site_search">
    <input type="text" id="local-search-input" style="" results="0"/>
    <div id="local-search-result"></div>
</div>
```
其中 `<input type="text" id="local-search-input" style="" results="0"/>` 是输入框，它的 `id` 值不要修改的；`<div id="local-search-result"></div>` 是查询之后，用来显示结果的 `div` 块，可以不需要放在 `input` 标签下，可以移动到页面任何位置  

然后就是设置查询结果的 `div` 元素他们需要的样式  
```css
ul.search-result-list {
  border: 1px solid #404449;
  margin-top:21px;
  padding: 10px;
  list-style-type:none;
  list-style:none;
}

a.search-result-title {
  font-weight: bold;
}

p.search-result {
  color=#555;
}

em.search-keyword {
  background-color: #8dd603;
  font-weight: bold;
  color:#ffffff;
}
```
编写一个 `search.js` 来完成这个功能，你可以直接复制我的
```js
var searchFunc = function(path, search_id, content_id) {
    'use strict'; //使用严格模式
    $.ajax({
        url: path,
        dataType: "xml",
        success: function( xmlResponse ) {
            // 从xml中获取相应的标题等数据
            var datas = $( "entry", xmlResponse ).map(function() {
                return {
                    title: $( "title", this ).text(),
                    content: $("content",this).text(),
                    url: $( "url" , this).text()
                };
            }).get();
            //ID选择器
            var $input = document.getElementById(search_id);
            var $resultContent = document.getElementById(content_id);
            $input.addEventListener('input', function(){
                var str='<ul class=\"search-result-list\">';                
                var keywords = this.value.trim().toLowerCase().split(/[\s\-]+/);
                $resultContent.innerHTML = "";
                if (this.value.trim().length <= 0) {
                    return;
                }
                // 本地搜索主要部分
                datas.forEach(function(data) {
                    var isMatch = true;
                    var content_index = [];
                    var data_title = data.title.trim().toLowerCase();
                    var data_content = data.content.trim().replace(/<[^>]+>/g,"").toLowerCase();
                    var data_url = data.url;
                    var index_title = -1;
                    var index_content = -1;
                    var first_occur = -1;
                    // 只匹配非空文章
                    if(data_title != '' && data_content != '') {
                        keywords.forEach(function(keyword, i) {
                            index_title = data_title.indexOf(keyword);
                            index_content = data_content.indexOf(keyword);
                            if( index_title < 0 && index_content < 0 ){
                                isMatch = false;
                            } else {
                                if (index_content < 0) {
                                    index_content = 0;
                                }
                                if (i == 0) {
                                    first_occur = index_content;
                                }
                            }
                        });
                    }
                    // 返回搜索结果
                    if (isMatch) {
                    //结果标签
                        str += "<li><a href='"+ data_url +"' class='search-result-title' target='_blank'>"+ "> " + data_title +"</a>";
                        var content = data.content.trim().replace(/<[^>]+>/g,"");
                        if (first_occur >= 0) {
                            // 拿出含有搜索字的部分
                            var start = first_occur - 6;
                            var end = first_occur + 6;
                            if(start < 0){
                                start = 0;
                            }
                            if(start == 0){
                                end = 10;
                            }
                            if(end > content.length){
                                end = content.length;
                            }
                            var match_content = content.substr(start, end); 
                            // 列出搜索关键字，定义class加高亮
                            keywords.forEach(function(keyword){
                                var regS = new RegExp(keyword, "gi");
                                match_content = match_content.replace(regS, "<em class=\"search-keyword\">"+keyword+"</em>");
                            })
                            str += "<p class=\"search-result\">" + match_content +"...</p>"
                        }
                    }
                })
                $resultContent.innerHTML = str;
            })
        }
    })
};
var path = window.location.protocol+"//"+window.location.host+"/search.xml";
searchFunc(path, 'local-search-input', 'local-search-result');
```
最后别忘记在 `footer.ejs` 中引入该文件
```html
<!-- 站内搜索 -->
<script src="/js/search.js"></script>
```

#### 日常维护  
使用 `hexo d`命令只是将 `public` 文件夹中的静态资源文件提交到 `github page`，并没有把原始的整个博客存起来，所以我们要把 `hexoblog` 中的内容提交到 `master` 分支，相信大家都会  
  
其次，要注意，如果你像我一样，对`themes`下的某个主题做了自定义，一定要将修改过得主题用 git 提交到一个单独的仓库中，因为上面那步，**把 `hexoblog` 中的内容提交到 `master` 分支**时，是不会把主题也给提交上去的 <span style="color:#ff0000">(不要试图去改什么忽略文件，巨坑)</span>  
> 最后附上我修改后的主题：[https://github.com/teaper/replica.git](https://github.com/teaper/replica)  
  
##### 重新部署博客需要哪些步骤？  
假设你系统挂了，需要重新部署博客，首先去克隆你 `master` 分支上的 `hexoblog`  
```bash
git clone https://github.com/teaper/hexoblog.git    #克隆博客
cd hexoblog #文件夹
git co master   #切换到master分支
```
你会发现它没有`node_modules`文件夹，也没有日志文件`yarn-error.log`，主题文件夹下的 `replica` 主题下居然没有主题，别急，一个一个来  
  
首先克隆 `replica` 主题，现在知道保存修改之后的主题的重要性了吧？  
```bash
cd themes   
git clone https://github.com/teaper/replica.git     #克隆主题
cd ..   #回到上级目录hexoblog
```
接下来就要把我们以前 `npm` 安装过的包重新安装回来  
```bash
npm install     #安装博客依赖包
hexo g && hexo s        #生成静态资源并且启动服务
```
是不是和之前的一样，**有木有？有木有？**  
剩下的事，不用我说了吧？日常使用～～emmmmmmm  
  
##### hexo博客样式问题多怎么办？
有时候启动服务就乱了，我的办法是重复执行`hexo cl`和`hexo g && hexo s`，直到正常为止  
还有第二种可能性就是，主题自带的插件包太老了，例如：`hexo-renderer-marked`，渲染`markdown`乱七八糟，解决方法如下：  
```bash
npm un hexo-renderer-marked --save      #卸载它
npm install hexo-renderer-marked --save     #重新安装新版
```




