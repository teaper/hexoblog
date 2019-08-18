---
title: IntelliJ IDEA 完美配置
categories: [软件]
tags: [jetbrains]
date: 2019-08-18 10:24:50
---
IntelliJ IDEA 官网提供了两个版本，完整版（Ultimate）和社区版（Community）；其中完整版需要付费，社区版免费，但是功能被阉割，我这里就以<span style="color:#B900ff;"> ArchLinux 完整版 </span>为例，对其进行[配置](https://www.jetbrains.com/help/idea/installation-guide.html)  
  
#### 下载
Windows/Mac 用户直接进入[官网](https://www.jetbrains.com/idea/)下载安装包进行安装  
ArchLinux/Manjaro 用户使用[AUR](https://aur.archlinux.org/packages/intellij-idea-ultimate-edition/)源安装  
```bash
yaourt -S intellij-idea-ultimate-edition    #安装
```
<span style="color:#ff0000;">注意：安装之前，Java 运行环境是预先配置好的，这个我就不累赘了，重点将放在 IntelliJ IDEA 的配置上</span>  
  
#### 安装
  
下载成功之后执行运行程序，会进入安装页面  
点击 `next` 下一步，选择 `Do not import settings` 单选按钮，点击 `ok`  
阅读条款，将滑块拉到最下面，点击 `Accept` 接受  
选择你喜欢的 UI 主题，点击 `Next:Desktop Entry`  
选择启动方式，点击 `Next:Launcher Script`  
自定义一个路径，一般默认即可，点击 `Next:Default plugins`  
然后会进入一个功能选择界面，去勾选你需要的功能模块  
* **Java Frameworks**：Hibernate、Spring、Struts、JavaEE、Velocity
* **Build Tools**：Maven
* **Web Developent**：HTML、JavaScript、CSS
* **Version Controls**：Git、GitHub
* **Test Tools**：JUnit
* **Application Servers**：Tomcat and TomEE
* **Clouds**：DisableAll (一个都不勾)
* **Swing**：Disable
* **Android**：Disable
* **Other Tools**：UML
  
选择完记得重新点开确认一遍，有时候它会自己改回去，确认之后点击 `Next:Featured plugins`  
等待安装完成就可以点击 `　Start using IntelliJ IDEA` 启动啦！  
  
#### 破解  
启动之后，如果是完整版，会提示需要许可协议，我的建议是去淘宝买一个号，一年才 15 元<span style="color:#ff0099;">（+微信：wobupeilianai）</span>，而且软件更新不用担心破解失效，大学生的话可以通过校园邮箱去申请，有点麻烦！  
  
不舍得花钱的，就只能通过 [idea.lanyus.com](http://idea.lanyus.com/) 去获取一个破解码  
> 注意：激活前清除hosts中屏蔽域名, 激活后请将“0.0.0.0 account.jetbrains.com”及“0.0.0.0 www.jetbrains.com”添加到hosts文件中
  
#### 编辑器  
安装成功之后我们可以启动一下，随便创建个项目，打开之后会发现它的字体不太好看，然后我个人喜欢白色的 UI 主题，看着精神一点   
  
进入 [Color Themes](http://color-themes.com/) 下载你喜欢的编辑器代码高亮主题的 `.jar` 文件，将其存放到一个单独的文件夹里  
在菜单 File -> Import Settings 中导入 `.jar` 文件选择黑色主题（Tangid）或白色主题（Relax Your Eyes）  
  
* Appearance&Behavior -> Appearance：字体（**Open sans**），字号**15**  
* Editor -> Font：字体（**MonoSpaced**），字号**15**，行高**1.1**
* Editor -> Color Scheme -> Color Scheme Font：字体（**MonoSpaced**），字号**18**，行高**1.1**
* Editor -> Color Scheme -> Console Font：字体（**MonoSpaced**），字号**12**，行高**1.1**
* Editor -> Code Style -> Java 中，选中 Wrapping and Braces 选项卡，在 Keep when reformatting 中勾选 **Ensure rigth margin is not exceeded** 实现代码自动换行
* Editor -> General -> Code Completion 中，取消勾选 **Match case** 实现代码提示不区分大小写
* Editor -> General -> Appearance 中勾选 **Show line numbers** 复选框显示行号
* Editor –> General –> Editor Tabs 中勾选 **Mark modified(×)**  复选框，可以将修改过的文件标星
  
UI 主题选择 File -> Settings -> Plugins，MarketPlace 选项卡，搜 Material Theme UI 安装即可或者使用[自定义 UI 主题 Gray](https://blog.jetbrains.com/idea/2019/03/brighten-up-your-day-add-color-to-intellij-idea/) <span style="color:#E98B2A">（也可以在 Plugins 选项卡点击右边设置 -> Install plugin from disk -> 你下载的 jar 包文件）

<span style="color:#ff0000;">注意：上面的 MonoSpaced 字体是个人安装的，Windows 用户没有可以想办法去下载，或者换成别的字体也可以，这里就仁者见仁智者见智了</span>  
  
#### Maven
电脑安装好 Maven 后，在 File -> Settings -> Build,Execution,Deployment -> Build Tools -> Maven 下设置 Maven 配置文件位置和本地仓库地址，由于我是使用默认地址，所以 User settings file 是 `/home/teaper/.m2/settings.xml`；Local repository 地址是 `/home/teaper/.m2/repository`   
  
<span style="color:#ff0000;">注意：这里的 `teaper` 是我的系统用户名，`/home/teaper` 就是我的主页目录，也就是 Linux中的 `~/` 目录，`.m2` 是 Maven 的隐藏目录</span>  
  
> <span style="color:#0089A7;">常见问题：在使用 Maven 的部署项目的过程中，当我们修改 pom.xml 文件，导致没有自动导入 jar 包，或 jar 包出现错误</span>  
  
解决方案如下：
* 手动删除 File -> Project Structure... -> Project Settings -> Libraries 中的内容  
* 在右侧的 Maven Project 的试图里 clean 一下，删除之前编译过的文件  
* 项目右键 -> Maven -> Reimport，不出意外，此时发现依赖已经建立  
  
#### 快捷键  
对于从 Eclipse 转 IDEA 的用户，第一不适应的就是原来使用的 Eclipse 快捷键都不能正常使用，所以可以在 File -> Settings -> Keymap 选项卡中，在 Keymap 下拉列表中选择 `Eclipse` ，然后会有一些快捷键冲突，需要在 Keymap 选项卡里的列表中设置一下  
* Keymap -> Main menu -> Code -> Completion -> Basic 修改为 **Ctrl+Alt+Enter**  
* Keymap -> Main menu -> Edit -> Find 修改 Find... 和 Replace... 分别改为 **Ctrl+F** 和 **Ctrl+R**
* Keymap -> Editor Actions -> Complete Current Statement 修改为 **Ctrl+**

  
保存时把冲突的快捷键 Remove 掉即可。当然，最好还是使用原汁原味的，多使用，多看看，总会熟悉的   
  

  





<span style="color:#;"></span>
<span style="color:#;"></span>
<span style="color:#;"></span>
