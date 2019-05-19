---
title: Markdown语法
date: 2019-05-19 22:45:06
categories: [Markdown]
tags: [Markdown]
---
[<span style="color:#B900ff;">标题</span>](#标题)  
[<span style="color:#B900ff;">列表</span>](#列表)  
[<span style="color:#B900ff;">引用</span>](#引用)  
[<span style="color:#B900ff;">图片与链接</span>](#图片与链接)  
[<span style="color:#B900ff;">粗体和斜体</span>](#粗体和斜体)  
[<span style="color:#B900ff;">删除线</span>](#删除线)  
[<span style="color:#B900ff;">表格</span>](#表格)  
[<span style="color:#B900ff;">内联代码</span>](#内联代码)  
[<span style="color:#B900ff;">语法突出</span>](#语法突出)  
[<span style="color:#B900ff;">任务列表</span>](#任务列表)  
[<span style="color:#B900ff;">表情符号</span>](#表情符号)  
  
#### 标题  
> ###### # 一级标题  
> ###### ## 二级标题  
> ###### ### 三级标题  
  
以此类推,总共六级,建议在`#`后面加上空格(标准写法);换行在当行后面加两个空格再回车  
  
#### 列表  
无序列表加 `-` 或 `*` 有序列表加 `1.` `2.` `3.` 符号  
##### 无序列表  
  
```
* 1          -1
* 2    或    -2
* 3          -3
```
* 1  
* 2  
* 3  
  
##### 有序列表  
```
1. Item1
2. Item2
3. Item3
```

1. Item1  
2. Item2  
3. Item3  
  
#### 引用  
如果你需要引用一小段别处的句子,就要用引用的格式 `>` 例如这样  
`> 这里是引用`  
> 这里是引用  
  
#### 图片与链接  
插入图片和插入链接很像,区别在于一个`!`号,插入图片需要`URL`地址  
注意:`[]`与`()`之间没有空格  
##### 插入链接  
`[TEAPER](https://www.teaper.cn)`  
[TEAPER](https://www.teaper.cn)  
  
##### 锚链接  
锚链接起始位置为：`[标题](#标题)`  
目标位置为：`#### 标题`  
> 注意：锚链接起始位置中的`(#标题)`，不论目标位置是几号标题，这里都只写一个`#`，并且后面不留空格，紧接着标题名字；并且`()`内标题名字中的大写字母需要转换成小写字母，有空格或`、`需要把空格或`、`替换成`-`，有`.`的需要删除`.`，并且标题名字中不能包含`&￥$【】？*%@!{}/\,:;"'<>~`等特殊符号；`[]`中的标题文本保持和目标位置一致即可
  
##### 插入图片  
`![icon](https://teaper.github.io/media/img/per/teaper.jpeg)`  
![icon](http://ww1.sinaimg.cn/large/006kWbIogy1g18uaas5syj30gf0gfn1b.jpg)  
  
#### 粗体和斜体  
Markdown的粗体和斜体也非常简单,用`**粗体**`或者两个`__粗体__`包含一段文字就是**粗体**,用一个`*斜体*`或者一个`_斜体_`包含一段文字就是_斜体_  
  
#### 删除线  
使用两个`～～`包含一段文字效果如下  
~~删除线~~
  
#### 表格  
表格可能是Markdown比较累人的地方,注意表格上下两行留白  
```
| Java | C# | Python |
| ---- | -- | ------ |
| 支持 | 支持 | 不支持 |
| 不支持 | 支持 | 支持 |
```
  
| Java | C# | Python |  
| ------ | ------- | ------ |  
| 支持 | 支持 | 不支持 |  
| 不支持 | 支持 | 支持 |  
  
#### 内联代码  
只需要用一个撇号(数字1左边那个键)包裹中间的代码,用Tap键缩进  
`Markdown`给了你多少钱,我双倍!  
  
#### 语法突出  
首行由三个撇号加代码检索标准(例如:java bash C++)开头;尾行三个撇号;代码在中间
```java
public void Markdown(){
    System.out.println("可以使用三个撇号包围代码,第一行撇号后面指定代码语言(推荐)");
    System.out.println("或者简单的将代码缩进四个空格");
}
```
  
#### 任务列表  
在[gitHub](https://github.com/)中,如果你想向某人发表评论,你可以在他们名字前面加`@`符号:hello@TEAPER  
  
#### 表情符号 
GitHub支持表情符号,语法`:ide:`,可以查看[表情大全](https://www.webpagefx.com/tools/emoji-cheat-sheet/)  
  
#### 徽章  
徽章一般使用一个开源项目[shields.io](https://shields.io/)制作  
  
[![LICENSE](https://img.shields.io/badge/license-Anti%20996-blue.svg)](https://github.com/996icu/996.ICU/blob/master/LICENSE) [![996.icu](https://img.shields.io/badge/link-996.icu-red.svg)](https://996.icu)  
![难点](https://img.shields.io/badge/-%E9%9A%BE%E7%82%B9-blueviolet.svg) ![重点](https://img.shields.io/badge/-%E9%87%8D%E7%82%B9-red.svg) ![基础](https://img.shields.io/badge/-%E5%9F%BA%E7%A1%80-important.svg) ![简单](https://img.shields.io/badge/-%E7%AE%80%E5%8D%95-sccess.svg) ![冷门](https://img.shields.io/badge/-%E5%86%B7%E9%97%A8-9cf.svg)  
![要求精通](https://img.shields.io/badge/%E8%A6%81%E6%B1%82-%E7%B2%BE%E9%80%9A-blueviolet.svg) ![要求熟悉](https://img.shields.io/badge/%E8%A6%81%E6%B1%82-%E7%86%9F%E6%82%89-red.svg) ![要求了解](https://img.shields.io/badge/%E8%A6%81%E6%B1%82-%E4%BA%86%E8%A7%A3-important.svg)  
```
[![LICENSE](https://img.shields.io/badge/license-Anti%20996-blue.svg)](https://github.com/996icu/996.ICU/blob/master/LICENSE) [![996.icu](https://img.shields.io/badge/link-996.icu-red.svg)](https://996.icu)  
![难点](https://img.shields.io/badge/-%E9%9A%BE%E7%82%B9-blueviolet.svg) ![重点](https://img.shields.io/badge/-%E9%87%8D%E7%82%B9-red.svg) ![基础](https://img.shields.io/badge/-%E5%9F%BA%E7%A1%80-important.svg) ![简单](https://img.shields.io/badge/-%E7%AE%80%E5%8D%95-sccess.svg) ![冷门](https://img.shields.io/badge/-%E5%86%B7%E9%97%A8-9cf.svg)  
![要求精通](https://img.shields.io/badge/%E8%A6%81%E6%B1%82-%E7%B2%BE%E9%80%9A-blueviolet.svg) ![要求熟悉](https://img.shields.io/badge/%E8%A6%81%E6%B1%82-%E7%86%9F%E6%82%89-red.svg) ![要求了解](https://img.shields.io/badge/%E8%A6%81%E6%B1%82-%E4%BA%86%E8%A7%A3-important.svg)
```
![](https://github.blog/wp-content/uploads/2019/01/Company@2x-2.png)
![](https://github.blog/wp-content/uploads/2019/01/Enterprise@2x-2.png)
![](https://github.blog/wp-content/uploads/2019/01/Community@2x.png)  

  
