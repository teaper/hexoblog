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
  
#### 配置编辑器  
安装成功之后我们可以启动一下，随便创建个项目，打开之后会发现它的字体不太好看，然后我个人喜欢白色的 UI 主题，看着精神一点   
  
进入 [Color Themes](http://color-themes.com/) 下载你喜欢的编辑器代码高亮主题的 `.jar` 文件，将其存放到一个单独的文件夹里  
在菜单 File -> Import Settings 中导入 `.jar` 文件选择黑色主题（**Tangid**）或白色主题（**Notepad++ Like**）  
  
* Appearance&Behavior -> Appearance：字体（**Open sans**），字号**15**  
* Editor -> Font：字体（**MonoSpaced**），字号**15**，行高**1.1**
* Editor -> Color Scheme -> Color Scheme Font：字体（**MonoSpaced**），字号**15**，行高**1.1**
* Editor -> Color Scheme -> Console Font：字体（**MonoSpaced**），字号**13**，行高**1.1**
* Editor -> Code Style -> Java 中，选中 Wrapping and Braces 选项卡，在 Keep when reformatting 中勾选 **Ensure rigth margin is not exceeded** 实现代码自动换行
* Editor -> General -> Code Completion 中，取消勾选 **Match case** 实现代码提示不区分大小写
* Editor -> General -> Appearance 中勾选 **Show line numbers** 复选框显示行号
* Editor –> General –> Editor Tabs 中勾选 **Mark modified(×)**  复选框，可以将修改过的文件标星
* File -> Settings -> Build,Execution,Deployment -> Compiler 中勾选 `Builder project automatically` 复选框，组合键 `Shift+Ctrl+Alt+/`，选择 `Registry` ，勾选 `compiler.automake.allow.when.app.running` 。可以解决静态资源更新需要重启项目的问题
  
UI 主题选择 File -> Settings -> Plugins，MarketPlace 选项卡，搜 Material Theme UI 安装即可或者使用[自定义 UI 主题 Gray](https://blog.jetbrains.com/idea/2019/03/brighten-up-your-day-add-color-to-intellij-idea/) <span style="color:#E98B2A">（也可以在 Plugins 选项卡点击右边设置 -> Install plugin from disk -> 你下载的 jar 包文件）

<span style="color:#ff0000;">注意：上面的 MonoSpaced 字体是个人安装的，Windows 用户没有可以想办法去下载，或者换成别的字体也可以，这里就仁者见仁智者见智了</span>  
  
#### 注释模板  
IDEA 自带的注释模板不是太好用，所以需要我们自己配置一下  
打开 File -> settings -> Editor -> File and Code Templates -> File 选项卡，可以看到所有常用的文件模板，先介绍几个配置参数  
* <span style="color:#2EA9DF;">${NAME}</span>：获取类名
* <span style="color:#2EA9DF;">${USER}</span>：获取系统用户名
* <span style="color:#2EA9DF;">${DATE}</span>：获取创建日期
* <span style="color:#2EA9DF;">${TIME}</span>：获取创建时间
* <span style="color:#2EA9DF;">${PROJECT_NAME}</span>：获取项目名
* <span style="color:#2EA9DF;">TODO</span>：代办事项的标记，一般生成类或方法都需要添加描述
  
将 Class 文件和 Interface 文件，替换以下模板  
```
#if (${PACKAGE_NAME} && ${PACKAGE_NAME} != "")package ${PACKAGE_NAME};#end
#parse("File Header.java")
/**
  * Copyright(C) ${DATE} ${TIME} 广州××网络科技有限公司 Inc.ALL Rights Reserved.
  *
  * @ClassName ${NAME}
  * @Description TODO
  * @Author ${USER}
  * @Date ${DATE} ${TIME}
  * @Version v1.0
  */
public class ${NAME} {
}
```
  
#### jdk 版本  
点击 File -> Other Settings -> Structure for New Projects 菜单，在弹出的窗口中的 <span style="color:#ff0000;"><No SDK></span> 中选择你要用的 jdk 版本  
  
如果没有可选项，可以在下面一个 `SDKs` 选项卡点击 `+` 手动添加  
  
#### Tomcat  
这个是刚需，也就是说，只有你打开 java Web 项目的时候需要去单独给某个项目配置服务器。点击右上角工具栏的 `Edit Configurations...` 下拉菜单，在弹出的窗口中点击左上角 `+` 号，选择 Tomcat Server -> Local 即可，然后配置基本信息，一般用默认的即可  
  
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
  
#### 配置浏览器  
**谷歌浏览器**  
* 在 chrome 地址栏输入 `chrome://version/` 回车，找到页面中的 `Command Line	：` 对应的启动脚本地址 `/opt/google/chrome/google-chrome` ，将其复制
* 点击 IDEA 菜单 File -> Settings -> Tools -> Web Browsers 选项卡中，将上面的地址粘到 path 栏中  
  
**火狐浏览器**
* 使用 `gedit /usr/share/applications/firefox.desktop` 命令编辑火狐浏览器快捷方式，找到里面的 Exec 对应的启动地址 ` /usr/lib/firefox/firefox` ，将其复制
* 同上，粘贴到 火狐浏览器的 path 栏中即可
  
**欧朋浏览器**  
* 欧朋浏览器至于要安装了即可，path 栏默认 `opera` 即可
  

#### 配置 git & github  
在 File -> settings -> Version Control -> git 选项卡中点击 `Test` 按钮确定git可用，然后在下面一个 github 选项卡中登录你自己的账号  
  
<span style="color:#F94465;">如何从github导入工程项目到 IDEA中？</span>  
点击 File -> New -> Project from version control -> git 菜单，在弹出的询问框中输入 github 项目的 URL 地址，选择 `New Windows` 新窗口打开项目  

#### 配置 DTD 文件  
用过 MyBatis 的用户都知道，创建 xml 太麻烦，所以需要一个好用的模板来帮助我们快速开发，那么 IDEA 中如何配置 MyBatis 的 DTD 呢？  
  
在 File -> settings -> Languages & Frameworks -> Schemas and DTDs 选项卡中，点击右上角的 `+` 添加以下两个 DTD 文件，File 地址可以通过双击 Project Schemas 中的对应 `.dtd` 文件自动设置  
```bash
# config.xml 文件
URI：http://mybatis.org/dtd/mybatis-3-config.dtd

# mapper.xml 文件
URI：http://mybatis.org/dtd/mybatis-3-mapper.dtd
```
  
#### Lombok 插件
这个[插件](https://github.com/mplushnikov/lombok-intellij-plugin)是用注解来简化我们频繁的 get、set 操作的，在 File -> Settings -> Plugins 窗口中直接搜 `lombok`，点击 `install` 安装即可  
  
首先引入 jar 包 `lombok-plugin-xxxx.jar`  
```xml
<dependency>
	<groupId>org.projectlombok</groupId>
	<artifactId>lombok</artifactId>
	<version>1.18.8</version>
	<scope>provided</scope>
</dependency>
```
然后使用以下注解：
* **@Data**：注解在类上，将类提供的所有属性都添加 `get`、`set` 方法，并添加 `equals`、`canEquals`、`hashCode`、`toString` 方法
* **@Setter**：注解在类上，为所有属性添加 `set` 方法、注解在属性上为该属性提供 `set` 方法
* **@Getter**：注解在类上，为所有的属性添加 `get` 方法、注解在属性上为该属性提供 `get` 方法
* **@NotNull**：在参数中使用时，如果调用时传了 `null` 值，就会抛出空指针异常
* **@Synchronized**：用于方法，可以锁定指定的对象，如果不指定，则默认创建一个对象锁定
* **@Log**：作用于类，创建一个 `log` 属性
* **@Builder**：使用builder模式创建对象
* **@NoArgsConstructor**：创建一个无参构造函数
* **@AllArgsConstructor**：创建一个全参构造函数,替代 `@Autowired` 构造注入,多个 `bean` 注入时更加清晰
* **@ToString**：创建一个toString方法
* **@Accessors(chain = true)**：使用链式设置属性，set方法返回的是this对象
* **@RequiredArgsConstructor**：创建对象。例：在class上添加 `@RequiredArgsConstructor(staticName = "of")` 会创建生成一个静态方法
* **@UtilityClass**:工具类再也不用定义static的方法了，直接就可以Class.Method 使用
* **@ExtensionMethod**:设置父类
* **@FieldDefaults**：设置属性的使用范围，如private、public等，也可以设置属性是否被final修饰
* **@SneakyThrows**
* **@EqualsAndHashCode**：重写equals和hashcode方法
* **@Cleanup**: 清理流对象,不用手动去关闭流
  

#### 阿里巴巴代码规范插件
这个插件是用来规范我们的代码格式的，格式不符合规范会出现中文提示，在 File -> Settings -> Plugins 窗口中直接搜 `Alibaba Java Coding Guidelines`，点击 install 安装即可  
<span style="color:#ff0000">注意：如果无法搜索到插件，建议在 Plugins 窗口右上角齿轮图标上配置 HTTP Proxy ,至于如何翻墙，请参照我另一篇博客[学习利器V2ray了解一下](https://www.teaper.dev/2019/06/02/v2ray/)</span>  


  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g689op8piej31hc0u0dln.jpg)  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g689opes0hj31hc0u0n3u.jpg)  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g689oq6cczj31hc0u00yv.jpg)  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g689opv0twj31hc0u0wk3.jpg)  
  
