---
title: 计算机入门概述
date: 2019-05-24 17:21:00
categories: [java基础]
tags: [java]
---
[<span style="color:#66327C;">准备工作</span>](#准备工作)  
[<span style="color:#66327C;">书籍推荐</span>](#书籍推荐)  
[<span style="color:#66327C;">计算机概述</span>](#计算机概述)  
[<span style="color:#66327C;">计算机硬件</span>](#计算机硬件)  
[<span style="color:#66327C;"></span>](#)  
  
#### 准备工作  
自学 java 编程，不出意外应该都是用的 Windows7 或 Windows10 的系统，当然我用的是 [Arch Linux](https://www.archlinux.org/download/) 系统，对于接下来的教程，我会分开写清楚。  
  
Windows 用户养成习惯软件装在 D 盘，项目或数据存 E 盘，其他学习资料或文档存 F 盘，重要文件及时备份；Linux 用户软件安装在 /opt 目录，Arch Linux 系列的系统，由于使用的是 pacman 包管理，位置默认就行。  
  
首先，给自己取个英文名，不要太长，例如我的 **“teaper”** ，用来设置 Windows / Linux 系统的用户名，再取个英文名，例如我的 **“Arch”** ，做为系统名<span style="color:#ff0000;">(中文系统名会造成后期 oracle 数据库无法正常安装)</span>，而且要养成习惯，编程软件尽量使用英文的，不要去做汉化；编码格式只认 **UTF-8** 。  
  
学习过程，建议视频＋书籍＋官方文档的方式，视频可以上[哔哩哔哩](https://www.bilibili.com/)，书籍有免费的 PDF 可以看，官方文档的话直接在官网看，后期深入可以看源码。  
  
边学边实践，每天做好总结，总结可以使用 [Markdown](https://markdown.tw/) 编写，存放在[有道云笔记](https://note.youdao.com/web/)中，以后还能整理成博客或书籍，给公司培养的新人参考。  
  
实践过程中的代码也不要丢弃，可以使用 [git](https://git-scm.com/) 将它提交到 [github](https://github.com/) 上进行托管，以后复习或许还用的上。  
  
如果遇到英文生词，可以借助[有道词典](https://www.youdao.com/)进行屏幕选词，同样还可以加入到你的生词本中；Linux 用户可以使用 [GoldenDict](http://goldendict.org/)  进行屏幕选词翻译；手机可以使用[谷歌翻译 APP ](https://play.google.com/store/apps/details?id=com.google.android.apps.translate&hl=zh_CN)，直接拍照翻译，好用的很。  
  
遇到问题直接谷歌搜索，自己搭建一个 SSR 服务器，进行翻墙，不会翻墙可以找我。  
  
最后送上一句话 **“输入 -> 转化 -> 输出 = 学会”**，有时间多帮别人解决Bug，对自身技术提升也是很有帮助的。
  
笔记和文档使用 Markdown 中标题一到标题六进行内容拆分，不需要进行首行缩进或首字下沉，对重要内容进行**加粗**，关键点附带好[超链接]()和图片，图片可以使用[新浪微博图床](https://chrome.google.com/webstore/detail/%E6%96%B0%E6%B5%AA%E5%BE%AE%E5%8D%9A%E5%9B%BE%E5%BA%8A/fdfdnfpdplfbbnemmmoklbfjbhecpnhf/reviews)或别的图床工具，对大篇幅的文章，也可以加入**锚链接**快速定位到标题内容。  
  
我们一般使用 [Eclips](https://www.eclipse.org/downloads/) / [MyEclipse](https://www.genuitec.com/products/myeclipse/download/) / [IntelliJ IDEA](https://www.jetbrains.com/idea/) 进行Java代码的编写，数据库会使用 [Navicat](https://www.navicat.com.cn/) / [DBeaver](https://dbeaver.io/download/) 客户端进行连接，前端网页会使用 [sublime text](https://www.sublimetext.com/) / [Visual Studio Code](https://code.visualstudio.com/) 进行编写，所以这些工具都要使用熟练。
  
#### 书籍推荐  
《程序员修炼之道》：坚持不下去的时候看一下，帮助制定学习进阶计划，提升效率  
《阿里巴巴Java开发手册》：国内开发者必看的编码规范  
《Java核心技术卷》：用于查阅，选看技术细节，适合有一定基础的开发者  
《Effective Java》：可以了解底层知识，不适合初学者  
《Java程序员修炼之道》：深入浅出了解Java，适合有一定基础的开发者  
《Java编程思想》：适用于工作一年的开发者，不适合初学者  
《编程珠玑》：面试必备书籍之一  
  
#### 计算机概述  
计算机包括[硬件(hardware)]()和[软件(software)]()两部分。硬件包括计算机中可以看得见的物理部分。而软件提供看不见的指令程序。这些指令控制硬件并使它完成特定的任务。  
  
##### 程序设计  
**定义：** 创建或开发软件，软件包含了指令，告诉计算机做什么。  
**应用场景：** 计算机、飞机、汽车、手机甚至烤面包机等。  
  
##### 程序设计语言  
软件开发人员在称为程序设计语言的强大工具的帮助下创建软件。常用的程序设计语言有 Java、C#、Python、C、C++、PHP等，这里我们使用Java语言。  
> 如何选择学习哪种程序设计语言？  
  
[程序设计语言](https://www.tiobe.com/tiobe-index/)有好多种，每一种都是为解决特定场景下的特定业务而专门打造的，例如 C 语言和 C++ 适合写系统应用，Java 适合服务层的安全开发，Python 适合进行数据分析和处理等。  
这里，我们也已经做出选择了，学习 Java 语言。事实上，**没有“世界上最好”的语言**，每种语言各有长处。  
优秀的开发人员善于使用各种语言解决问题，而且精通一门语言有助于快速上手其他编程语言。  
  
#### 计算机硬件  
台式计算机通过总线连接中央处理器(CPU)、内存、存储设备(固态硬盘或机械硬盘等)、输入设备(鼠标、键盘)、输出设备(显示器、打印机)、通信设备(调试解调器、网卡)等，当然，计算机发展到现在，还有显卡，指纹解锁，摄像头等硬件，但是大体还是逃不出[冯·诺伊曼体系结构](https://zh.wikipedia.org/wiki/%E5%86%AF%C2%B7%E8%AF%BA%E4%BC%8A%E6%9B%BC%E7%BB%93%E6%9E%84)  
  
![冯·诺伊曼体系结构](http://ww1.sinaimg.cn/large/006kWbIoly1g2q9dxsrg0j30ed08u0so.jpg)  
  
> [冯·诺伊曼体系结构](https://zh.wikipedia.org/wiki/%E5%86%AF%C2%B7%E8%AF%BA%E4%BC%8A%E6%9B%BC%E7%BB%93%E6%9E%84)是现代计算机的基础，冯·诺依曼也被称为 **“计算机之父”**  
  
**中央处理器(Central Processing Unit,CPU)**：是计算机的大脑，它从内存中获取指令，然后执行这些指令。它包括控制单元(control unit)和算数/逻辑单元(arithmetic/login unit)。控制单元用于控制和协调其他组件的动作。算数/逻辑单元用于完成数值运算(+、-、×、/)和逻辑运算(比较)。每台计算机都有一个内部时钟，该时钟以固定速度发射电子脉冲。时钟速度越快，在给定的时间段内执行的指令就越多。速度的计量单位是赫兹(Hz)，1Hz相当于每秒1个脉冲。随着CPU速度不断提高，目前以千兆赫兹(GHz)来表达。最初CPU只有一个核(Core)。核是处理器中实现指令读取和执行的部分。一个多核CPU是一个具有两个或者更多独立核的组价。可提高CPU的处理能力。  
```
1 KHz = 1024 Hz
1 MHz = 1024 KHz
1 GHz = 1024 MHz
                   -`                    teaper@Arch 
                  .o+`                   ----------- 
                 `ooo/                   OS: Arch Linux x86_64 
                `+oooo:                  Host: 80SH Lenovo XiaoXin 700-15ISK 
               `+oooooo:                 Kernel: 5.0.11-arch1-1-ARCH 
               -+oooooo+:                Uptime: 3 hours, 36 mins 
             `/:-:++oooo+:               Packages: 1142 (pacman) 
            `/++++/+++++++:              Shell: zsh 5.7.1 
           `/++++++++++++++:             Resolution: 1920x1080 
          `/+++ooooooooooooo/`           DE: GNOME 3.32.1 
         ./ooosssso++osssssso+`          WM: GNOME Shell 
        .oossssso-````/ossssss+`         WM Theme: Mojave-dark 
       -osssssso.      :ssssssso.        Theme: vimix-light-laptop-doder [GTK2/ 
      :osssssss/        osssso+++.       Icons: la-capitaine-icon-theme [GTK2/3 
     /ossssssss/        +ssssooo/-       Terminal: gnome-terminal 
   `/ossssso+/:-        -:/+osssso+-     CPU: Intel i5-6300HQ (4) @ 3.200GHz    //我第一台笔记本的主频3.2GHz 
  `+sso+:-`                 `.-/+oso:    GPU: Intel HD Graphics 530 
 `++:.                           `-/+/   GPU: NVIDIA GeForce GTX 950M 
 .`                                 `/   Memory: 2937MiB / 15835MiB 
```
  
**IT定律之计算机行业发展规律**  
> [摩尔定律(Moors's Law)](https://zh.wikipedia.org/wiki/%E6%91%A9%E5%B0%94%E5%AE%9A%E5%BE%8B)是由英特尔创始人之一戈登·摩尔提出的。其内容为：集成电路上可容纳的晶体管数目，约每隔两年便会增加一倍；经常被引用的“18个月”，是由英特尔首席执行官大卫·豪斯提出：预计18个月会将芯片的性能提高一倍。

> [安迪-比尔定律(Andy and Bll's Law)](https://baike.baidu.com/item/%E5%AE%89%E8%BF%AA%E6%AF%94%E5%B0%94%E5%AE%9A%E7%90%86?fromId=2066996)是对IT产业中软件和硬件升级换代关系的一个概括，意思是，硬件提高的性能，很快被软件消耗掉了。  

> [反摩尔定律(Reverse Moors's Law)](https://baike.baidu.com/item/%E5%8F%8D%E6%91%A9%E5%B0%94%E5%AE%9A%E5%BE%8B)是Google的前CEO埃里克·施密特提出的：如果你反过来看摩尔定律，一个IT公司如果今天和18个月前卖掉同样多的、同样的产品，它的营业额就要降一半。IT界把它称为反摩尔定律  
  
**存储设备**  
内存中的信息在断点后会丢失(当然现在已经不会了，会在每次输入后自动保存)，所以考虑将程序和数据保存到存储设备。当计算机需要数据的时候，再移入内存，因为从内存中读取数据比在硬盘上要快得多。  
> 存储设备主要有三种：[磁盘驱动器]()、[光盘驱动器]()、[USB闪存驱动器]()
  
**内存**  
了解内存之前，先搞清楚数据如何存储在计算机中的。  
计算机就是一系列电路的开关。每个开关存在两种状态：关(off)和开(on)。如果电路是开的，它的值就是1.如果电路是关的，它的值就是0。  
一个0或者一个1存储为一个(bit)，是计算机最小的存储单位。  
计算机中最基础的存储单元是字节(byte)。每个字节由8个比特构成。  
计算机的存储能力是以字节和多字节来衡量的。如下：  
```
千字节(Kilobyte，KB) = 1024B
兆字节(megabyte,MB) = 1024KB
千兆字节(gigabyte,GB) = 1024MB
万亿字节(terabyte,TB) = 1024GB
```
[内存(Random-Access Memory,RAM)]()：由一个有序的字节序列组成，用于存储程序及程序需要的数据。  
<span style="color:#ff0000;">一个程序和它的数据执行前必须移到计算机的内存中</span>  
每个字节都有唯一的地址。使用这个地址确定字节位置，以便于存储和获取数据。  
一个计算机具有的RAM越多，它的运行速度越快，但是此规律是有限制的。  
内存好CPU一样，也构建在表面嵌有数百万晶体管的硅半导体芯片上。但内存芯片更简单、更低速、更便宜。  
  
**输入/输出设备**  
常见的输入设备：键盘、鼠标  
常见的输出设备：显示器、打印机  
  
#### 学习经验 <span style="color:#ff0000;">(很重要)</span>  
锻炼“双核”处理，边听讲边思考，边做“笔记”  
纸上得来终觉浅，绝知此事要躬行！
> 第一层次：看得懂（依赖于视频、书、帖子）  
> 第二层次：练得熟（每天代码必须实现**2~3**遍）  
> 总结：**三分看，七分练**  
  
建立有效的学习方法  
> 学习编程的捷径：敲、狂敲   
> “模仿”好的编码习惯  
> 整理、回顾：每天花30分钟整理  
  
学习经验探讨：四种心态  
> 不是“没听懂”，而是“记不住”  
> 要为成功找理由，不为失败找借口  
> 战略上藐视“对手”，战术上重视“对手”  
> <span style="color:#2EA9DF;">“代码虐我千百遍，我视代码如初恋”</span>  
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g2qcjjmz2tj30mn08wmy3.jpg)  
  

