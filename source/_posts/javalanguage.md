---
title: Java 语言概述
categories: [Java]
tags: [java]
date: 2019-06-30 12:45:04
---
[<span style="color:#66327C;">软件开发介绍</span>](#软件开发介绍)  
[<span style="color:#66327C;">计算机编程语言介绍</span>](#计算机编程语言介绍)  
[<span style="color:#66327C;">Java语言概述</span>](#java语言概述)  
[<span style="color:#66327C;">运行机制及运行过程</span>](#运行机制及运行过程)  
[<span style="color:#66327C;">Java的环境搭建</span>](#java的环境搭建)  
[<span style="color:#66327C;">开发体验——HelloWorld</span>](#开发体验helloworld)  
[<span style="color:#66327C;">注释</span>](#注释)  
[<span style="color:#66327C;">Java API的文档</span>](#java-api的文档)  
[<span style="color:#66327C;">良好的编程风格</span>](#良好的编程风格)  
[<span style="color:#66327C;"></span>](#)  
  
#### 软件开发介绍  
**软件开发**：即一系列按照特定组织顺序的计算机数据指令的集合。有系统软件和应用软件分。  
  
**人机交互方式**：
> 图形化界面(Graphical User Interface **GUI**)：这种方式简单直观，使用者易于接受，容易上手操作。  
  
> 命令行方式(Commend Line Interface **CLI**)：需要一个控制台，输入特定指令，让计算机完成一些操作。较为麻烦，需要记录一些命令。
  
Windows下常用的DOS命令  
```bash
dir #列出当前目录下的文件及文件夹
md  #创建目录
rd  #删除目录
cd  #进入指定目录
cd..    #退回到上一级目录
cd\     #退回根目录
del     #删除文件
exit    #退出dos命令行
echo 1.doc  #创建1.doc文件
```
  
#### 计算机编程语言介绍  
> 什么是计算机语言？  
  
语言：是人与人之间用于沟通的一种方式  
计算机语言：人与计算机交流的方式，计算机语言有很多种。如：C、C++、Java、PHP、Kotlin、Python、Scala等  
  
第一代语言：机器语言，指令二进制代码形式存在  
第二代语言：汇编语言，使用助记符表示一条机器指令  
第三代语言：高级语言  
* C、Pascal、Fortran面向过程的语言  
* C++面向过程/面向对  
* Java跨平台的纯面向对象的语言  
* .NET跨语言的平台  
* Python、Scala大数据语言  
  
[TIOBE](https://www.tiobe.com/tiobe-index/)是一个流行编程语言排行，每月更新，排名权重基于世界范围内工程师的数量，课程数量，和第三方供应商数量
    
#### Java语言概述  
Java语言是SUM(Stanford University Network，斯坦福大学网络公司)1995年退出的一门高级编程语言。  
  
是一种面向Internet的编程语言。Java一开始富有吸引力是因为Java程序可以在Web浏览器中运行。这些Java程序被称为Java小程序(applet)。applet使用现代的图形用户界面与web用户进行交互。applet内嵌在HTML代码中。
  
随着Java技术在web方面不断的成熟，已经成为web应用程序的首选开发语言  
  
> Java语言简史  
```
1991年  Green项目，开发语言最初命名为Oak(橡树)  
1994年  开发组意识到Oak非常适合于互联网  
1996年  发布jdk1.0，约8.3万个网页应用Java技术来制作
1997年  发布jdk1.1，JavaOne会议召开，创当时全球同类会议规模之最
1998年  发布jdk1.2，同年发布企业平台J2EE
1999年  Java分成J2SE、J2EE和J2ME，JSP/Servlet技术诞生
2004年  发布里程碑式版本jdk1.5，为突出此版本重要性，更名为jdk5.0
2005年  J2SE -> JavaSE,J2EE -> JavaEE,J2ME -> JavaME
2009年  Oracle公司收购SUN，交易价格74亿美元
2011年  发布jdk7.0
2014年  发布jdk8.0，是继jdk5.0以来变化最大的版本(目前使用最多)
2017年  发布jdk9.0，最大限度实现模块化
2018年3月   发布jdk10.0，版本号也称18.3
2018年9月   发布jdk11.0，版本号也成18.9
2019年3月   发布jdk12.0
```
**Java技术体系**  
Java SE(Java Standard Edition)标准版：支持面向桌面级应用(如Windows下的应用程序)的Java平台，提供完整的Java核心API，此版本以前成为J2SE  
  
Java EE(Java Enterprise Edition)企业版：是为开发企业环境下的应用程序提供一套解决方案。该技术体系中包含的技术如servle、jsp等，主要针对于web应用程序开发。版本以前称为J2EE  
  
Java ME(java Micro Edition)小型版：支持Java程序运行在移动终端(手机、PDA)上的平台，对Java API有所精简，并加入了针对移动终端的支持，此版本以前称为J2ME  
  
Java Card：支持一些Java小程序(Appplets)运行在小内存设备(如智能卡)上的平台  
  
#### 运行机制及运行过程  
**Java语言的特点**
* 特点一：面向对象
  * 两个基本概念：类、对象  
  * 三大特性：封装、继承、多态  
* 特点二：健壮性  
  * 吸收了C/C++语言的优点，但去掉了其影响程序健壮性的部分(如指针、内存的申请与释放等)，提供了一个相对安全的内存管理和访问机制  
* 特点三：跨平台性  
  * 跨平台性：通过Java语言编写的应用程序在不同的系统平台上都可以运行，本质上是通过不同系统的JVM实现跨平台的。**Write once，Run Anywhere**   
  * 原理：只要在需要运行Java应用程序的操作系统上，先安装一个Java虚拟机(JVM java Virtual Machine)即可。由JVM来负责Java程序在该系统中的运行。
  
**Java两种核心机制**  
* Java虚拟机(java virtal machine)  
* 垃圾收集机制(Garbag Collection)  
  * 在C/C++等语言中，由程序员负责回收无用内存  
  * Java 语言消除了程序员回收无用内存空间的责任：它提供一种系统级线程跟踪存储空间的分配情况。并在JVM空闲时，检查并释放那些可被释放的存储空间。
  * 垃圾回收在java程序运行过程中自动进行，程序员无法精确控制和干预。
  * <span style="color:#ff0000;">注意：Java程序还会出现内存泄漏和内存溢出问题吗？Yes</span>  
  

#### Java的环境搭建  
<span style="color:#66327C;">注意：目前市场上用的最多的还是java8，虽然出到了java12，但是企业里求稳定，所以基本不会使用最新版</span>  
> 什么是jdk、jre  
  
**jdk(java Development kit Java开发工具包)**：jdk是提供给Java开发人员使用的，其中包含了Java的开发工具，也包括了jre运行环境。所以安装了jdk，就不需要单独安装jre了。其中的开发工具：编译工具(javac.exe)打包工具(jar.exe)等  
  
**jre(java Runtime Environment java运行环境)**：包括Java虚拟机(JVM java virtual machine)和Java程序所需要的核心类库等，如果想要运行一个开发好的Java程序，计算机中只需要安装jre即可  
  
<span style="color:#ff0000;">简单来说，使用jdk开发完成的程序交给jre运行</span>  
```none
jdk = jre + 开发工具集(例如javac编译工具等)  
jre = jvm + java SE标准类库
```
![](http://ww1.sinaimg.cn/large/006kWbIoly1g2qidegpevj30on0eun17.jpg)  
  
**下载并安装JDK**  
官方下载地址：[https://www.oracle.com](https://www.oracle.com/technetwork/java/javase/downloads/index.html)、[https://java.sun.com](https://www.oracle.com/technetwork/java/index.html)  
  
> windows安装jdk  
  
详细的安装步骤参考————[【在Windows环境下安装JDK】](https://segmentfault.com/a/1190000018132781)   
  
> debian系Linux安装jdk  
  
[下载地址](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)  
```bash
sudo mkdir /usr/jvm  #创建jvm文件夹
sudo tar -xzvf jdk-8u172-linux-x64.tar.gz -C /usr/jvm  #解压jdk1.8到jvm文件夹
sudo tar -xzvf jdk-11.0.1_linux-x64_bin.tar.gz -C /usr/jvm/  #解压jdk11到jvm文件夹(可选)

sudo gedit /etc/profile     #配置环境变量(配置默认jdk)
-------------------------------末尾追加---------------------------------
export JAVA_HOME=/usr/jvm/jdk1.8.0_172
export JRE_HOME={JAVA_HOME}/jre
export CLASSPATH=.:{JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=$JAVA_HOME/bin:$PATH
-----------------------------------------------------------------------
#jdk1.8
sudo update-alternatives  --install /usr/bin/java java /usr/jvm/jdk1.8.0_172/bin/java 300
sudo update-alternatives  --install /usr/bin/javac javac /usr/jvm/jdk1.8.0_172/bin/javac 300  
#jdk11(可选)
sudo update-alternatives  --install /usr/bin/java java /usr/jvm/jdk-11.0.1/bin/java 300
sudo update-alternatives  --install /usr/bin/javac javac /usr/jvm/jdk-11.0.1/bin/javac 300

sudo update-alternatives --config java  #输入数字切换多个jdk版本
java -version  #查看jdk版本
```
  
> archlinux系Linux安装jdk  
```bash
yaourt -S jdk8
```

#### 开发体验——HelloWorld  
步骤：  
1. 将 Java 代码编写到扩展名为 `.java` 的文件中  
2. 通过 javac 命令对该 java 文件进行编译  
3. 通过 java 命令对生成的 class 文件进行运行  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g2qj6aa36zj312y0gfn4h.jpg)  
  
用记事本创建`HelloWorld.java`文件，内容如下：
```java
class HelloWorld{
	public static void main(String [] args){
		System.out.println("HelloWorld");
	}
}
```
使用 `javac HelloWorld.java`命令编译该文件，会在`HelloWorld.java`文件同级目录下生成`HelloWorld.class`文件，然后使用`java HelloWorld`运行编译后`HelloWorld.class`文件，最后会在控制台输出`HelloWorld`  
  
<span style="color:#ff0000;">注意：Windows系统使用`javac`编译`HelloWorld.java`文件是不区分大小写的，也就是说命令写成`javac helloworld.java`也是可以成功编译成字节码文件的；但是`Java`是区分大小写的，而且要求类名和`.java`文件名要保持一致，那么我们使用`java`命令运行字节码文件的时候，就严格要求大小写，否则找不到要运行的字节码文件</span>  
> 除了记事本，这里推荐两个Windows下的编辑工具，[EditPlus](https://www.editplus.com/download.html)、[Notepad++](https://notepad-plus-plus.org/)；Linux下使用gedit和vim就好了  
  
#### 注释  
**注释**用于解释说明一段代码用途，阐述程序逻辑，方便别人或自己快速了解代码功能，你可以理解为代码中的笔记；也可以用于调试代码，用于排除错误代码段  
  
Java中的注释类型有三种：**单行注释**、**多行注释**、**文档注释**<span style="color:#ff0000;">(Java特有)</span>  
```java
/**
 * 【文档注释】
 * @author teaper	
 * @version 1.0
 * 使用@author注解指定程序作者
 * 使用@version指定源文件版本
 */
public class HelloWorld {
	/*【多行注释】：对所写程序进行解释说明，增加可读性
	 * static void main(String[] args)方法是Java程序的入口，且格式是固定的
	 * args数组存储的是bash中的值，args是arguments(参数)的缩写
	 */
	public static void main(String[] args) {
		//【单行注释】：对所写程序进行解释说明，增加可读性
		System.out.println("Hello World");  //有ln的会自动换行
		System.out.print("Hello World");    //没有ln的不换行
	}
}
  
//一个.java源文件可以声明多个class类，但是只能有一个public类，且该public类名必须和.java文件名一致
class Person{
    
}
```
<span style="color:#ff0000;">**注意：**  
1. 单行注释和多行注释不参与编译，也就是说编译`.java`文件时，不会将注释内容写进字节码文件中用于运行  
2. 多行注释不可嵌套使用</span>  
  
文档注释可以被JDK所提供的javadoc所解析，生成一套以网页文件形式体现的该程序的说明文档，操作命令如下：  
```bash
javadoc -d nydoc -author -version HelloWorld.java   #执行此命令
#----------------------------输出信息---------------------------
Loading source file HelloWorld.java...
Constructing Javadoc information...
Creating destination directory: "nydoc/"
Standard Doclet version 1.8.0_202
Building tree for all the packages and classes...
Generating nydoc/test/HelloWorld.html...
Generating nydoc/test/package-frame.html...
Generating nydoc/test/package-summary.html...
Generating nydoc/test/package-tree.html...
Generating nydoc/constant-values.html...
Building index for all the packages and classes...
Generating nydoc/overview-tree.html...
Generating nydoc/index-all.html...
Generating nydoc/deprecated-list.html...
Building index for all classes...
Generating nydoc/allclasses-frame.html...
Generating nydoc/allclasses-noframe.html...
Generating nydoc/index.html...
Generating nydoc/help-doc.html...
```
会在该`HelloWorld.java`同级目录生成一个叫做`nydoc`的文件夹，用浏览器打开其中的`index.html`，如图所示 
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g30tyqypugj31ha0qdjs6.jpg)
  
可以看到相关包名，类名，版本等信息～  
  
#### Java API的文档  
API(Application Programming Interface，应用程序编程接口)是Java提供的基本编程接口  
  
Java语言提供了大量的基础类，因此Oracle也为这些基础类提供了相应的API文档，用于告诉开发者如何使用这些类，以及类里包含的方法  
  
[Java8API在线英文版](https://docs.oracle.com/javase/8/docs/api/)  
[Java8API在线中文版](http://www.matools.com/api/java8)  
  
#### 良好的编程风格  
正确的注释和注释风格  
* 使用文档注释来注释整个类或整个方法  
* 如果注释方法中的某一个步骤，使用单行或多行注释
  
正确的缩进和空白  
* 使用tab操作，实现缩进
* 运算符两边习惯性加一个空格，比如：`2 + 4 * 5`
  
块的风格  
* Java API 源代码选择了行尾风格，C#是独行风格  
```java
public class HelloWorld {
	public static void main(String[] args) {
		System.out.print("行尾风格");
	}
}
//----------------------------分隔符----------------------------
public class HelloWorld 
{
	public static void main(String[] args) 
	{
		System.out.print("独行风格");
	}
}
```

