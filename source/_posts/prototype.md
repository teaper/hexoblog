---
title: 原型模式
date: 2019-06-21 06:38:20
categories: [设计模式]
tags: [java]
---
<span style="color:#B900ff;">原型模式主要解决什么问题？</span>  
用过 git 的可能知道，git 有个命令 `git clone` ，将远程仓库的内容复制到本地，在实际开发中，我们会在克隆下来的项目中继续修改或添加；放在 Java 中，当我们需要使用 new 创建与已存在对象类似的对象时，是否可以以某个对象为原型去创建新对象，提高效率呢？  
  
<span style="color:#B900ff;">那么，什么是原型模式呢？</span>  
**原型模式**：就是Java的克隆技术，以某个对象为原型，复制出新的对象（新的对象具备原型对象的特点）  
  
<span style="color:#ff0000;">注意：克隆类似于 new，但是不同于 new。new 创建的对象采用的是默认值，克隆出的对象的属性值完全和原型对象相同，并且克隆出的新对象改变不会影响原型对象</span>  
  
<span style="color:#B900ff;">原型模式如何实现？</span>  
原型模式（Prototype）需要实现，就涉及到内存复制操作，所幸 Java 中提供了 `clone()` 方法替我们做了大部分事情  
  
#### 浅克隆  
首先，需要创建一个对象类，这里使用 Sheep（羊）来做例子  
```java
package cn.teaper.www.prototype;

import java.util.Date;

/**
 * 【原型模式】创建羊（Sheep）类（浅克隆）
 * @author teaper
 * 1997年英国克隆羊多利
 */
public class Sheep implements Cloneable{
	private String name;//名字
	private Date birthday;//生日
	
	//继承了 Cloneable 接口，记得重写 Object对象 的 clone() 方法
	@Override
	protected Object clone() throws CloneNotSupportedException {
		Object object = super.clone();	//这里使用 super，直接调用 object 对象的 clone() 方法；
		return object;
	}

    //get/set访问器和构造器
	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public Date getBirthday() {
		return birthday;
	}

	public void setBirthday(Date birthday) {
		this.birthday = birthday;
	}

	public Sheep(String name, Date birthday) {
		super();
		this.name = name;
		this.birthday = birthday;
	}

	public Sheep() {
		super();
	}
}
```
需要继承 Cloneable 接口，该接口是个**空接口**  
然后重写 Object 类的 `clone()` 方法  
  
在测试方法中创建“多利”羊为原型，在其基础上创建“少利”羊，少利羊属性和多利羊一致，修改少利羊属性不会影响多利羊  
```java
package cn.teaper.www.prototype;

import java.text.SimpleDateFormat;
import java.util.Date;

/**
 * 【原型模式】测试原型模式（浅克隆）
 * @author teaper
 * 1997年英国克隆羊多利
 */
public class Client {
	public static void main(String[] args) throws CloneNotSupportedException {
		Date date = new Date(12312321331L);
		SimpleDateFormat ft = new SimpleDateFormat ("yyyy-MM-dd hh:mm:ss");
		
		//创建多利羊原型
		Sheep sheep1 = new Sheep("多利", date);
		System.out.println("羊名字："+sheep1.getName()+"\t羊年龄："+ft.format(sheep1.getBirthday()));
		
		//以上面的多利羊为原型创建新羊少利
		Sheep sheep2 = (Sheep) sheep1.clone();
		System.out.println("羊名字："+sheep2.getName()+"\t羊年龄："+ft.format(sheep2.getBirthday()));
		sheep2.setName("少利");
		date.setTime(23432432423L);//修改时间
		System.out.println("羊名字："+sheep2.getName()+"\t羊年龄："+ft.format(sheep2.getBirthday()));
		System.out.println("羊名字："+sheep1.getName()+"\t羊年龄："+ft.format(sheep1.getBirthday()));
	}
}
```
<details>
<summary>运行结果</summary>

```bash
羊名字：多利	羊年龄：1970-05-23 08:05:21
羊名字：多利	羊年龄：1970-05-23 08:05:21
羊名字：少利	羊年龄：1970-09-29 01:00:32
羊名字：多利	羊年龄：1970-09-29 01:00:32
```
</details>
  
分析：首先创建的一个老的时间，设置好了时间输出格式 `yyyy-MM-dd hh:mm:ss`，然后创建了多利羊，输出信息就是多利羊的名字和老时间；这时候使用 `sheep1.clone()` 克隆多利羊创建少利羊，少利羊的信息和多利羊是一样的；然后我们说了，克隆出的对象是可以修改的，这时候修改少利羊的信息，修改 `date` 时间，会发现少利羊的时间也会被修改，而我们明明没有修改少利羊的 birthday 属性，也就是说，克隆没有深入到引用传递的值，通过 sheep1 克隆了 sheep2 ，但是 sheep1 和 sheep2 的某个引用类型属性值还是指向同一个 date；这种克隆我们称之为**浅克隆**   
  
那么什么叫深克隆呢？同理，只需要 sheep1 和 sheep2 的某个引用类型属性值(例如：date)也克隆了一份，就叫**深克隆**  
  
#### 深克隆  
**写法一：** 普通深克隆只需要在重写 `clone()` 方法的时候，把需要克隆的属性也克隆即可  
```java
//继承了 Cloneable 接口，记得重写 Object对象 的 clone() 方法
@Override
protected Object clone() throws CloneNotSupportedException {
	Object object = super.clone();	//这里使用 super，直接调用 object 对象的 clone() 方法；
	
	//添加如下代码实现深克隆（deep clone）
	Sheep sheep = (Sheep) object;
	sheep.birthday = (Date) this.birthday.clone();	//把属性也进行克隆
	
	return object;
}
```
<details>
<summary>运行结果</summary>

```bash
羊名字：多利	羊年龄：1970-05-23 08:05:21
羊名字：多利	羊年龄：1970-05-23 08:05:21
羊名字：少利	羊年龄：1970-05-23 08:05:21
羊名字：多利	羊年龄：1970-09-29 01:00:32
```
</details>
  
分析：修改少利羊之后的输出时间（第三行）和第一次的克隆时间保持了一致，也就是说克隆到了多利羊的时间指向的值  
  
**写法二：** 使用序列化和反序列化技术实现深克隆  
首先，Sheep 类要继承 `Serializable` 接口
```java
package cn.teaper.www.prototype;

import java.io.Serializable;
import java.util.Date;

/**
 * 【原型模式】创建羊（Sheep）类
 * @author teaper
 * 1997年英国克隆羊多利
 */
public class Sheep implements Serializable{
	private String name;//名字
	private Date birthday;//生日
	
	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public Date getBirthday() {
		return birthday;
	}

	public void setBirthday(Date birthday) {
		this.birthday = birthday;
	}

	public Sheep(String name, Date birthday) {
		super();
		this.name = name;
		this.birthday = birthday;
	}

	public Sheep() {
		super();
	}
}
```
使用 IO 流实现，需要抛出几个异常
```java
package cn.teaper.www.prototype;
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.text.SimpleDateFormat;
import java.util.Date;

/**
 * 【原型模式】测试原型模式
 * @author teaper
 * 1997年英国克隆羊多利
 */
public class Client {
	public static void main(String[] args) throws CloneNotSupportedException, IOException, ClassNotFoundException {
		Date date = new Date(12312321331L);
		SimpleDateFormat ft = new SimpleDateFormat ("yyyy-MM-dd hh:mm:ss");
		
		//创建多利羊原型
		Sheep sheep1 = new Sheep("多利", date);
		System.out.println("羊名字："+sheep1.getName()+"\t羊年龄："+ft.format(sheep1.getBirthday()));
		
		//使用序列化和反序列化技术实现深克隆，创建少利羊
		ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
		ObjectOutputStream objectOutputStream = new ObjectOutputStream(byteArrayOutputStream);
		objectOutputStream.writeObject(sheep1);
		byte[] byteArray = byteArrayOutputStream.toByteArray();
		
		ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(byteArray);
		ObjectInputStream objectInputStream = new ObjectInputStream(byteArrayInputStream);
		Sheep sheep2 = (Sheep) objectInputStream.readObject();
		
		//修改少利羊信息和 date 值
		System.out.println("羊名字："+sheep2.getName()+"\t羊年龄："+ft.format(sheep2.getBirthday()));
		sheep2.setName("少利");
		date.setTime(23432432423L);//修改时间
		System.out.println("羊名字："+sheep2.getName()+"\t羊年龄："+ft.format(sheep2.getBirthday()));
		System.out.println("羊名字："+sheep1.getName()+"\t羊年龄："+ft.format(sheep1.getBirthday()));
	}
}
```
<details>
<summary>运行结果</summary>

```bash
羊名字：多利	羊年龄：1970-05-23 08:05:21
羊名字：多利	羊年龄：1970-05-23 08:05:21
羊名字：少利	羊年龄：1970-05-23 08:05:21
羊名字：多利	羊年龄：1970-09-29 01:00:32
```
</details>
  
#### 测试原型模式运行效率  
```java
package cn.teaper.www.prototype;

/**
 * 【原型模式】测试原型模式运行效率
 * @author teaper
 * 普通new对象 V/S 原型模式创建对象
 */
public class Client2 {
	public static void main(String[] args) throws CloneNotSupportedException {
		//调用以下两个方法，创建1000个对象
		testNew(1000);
		testClone(1000);
	}
	
	//new方式
	public static void testNew(int size) {
		long start = System.currentTimeMillis();
		for (int i = 0; i < size; i++) {
			Laptop tLaptop = new Laptop();
		}
		long end = System.currentTimeMillis();
		System.out.println("new的方式创建耗时："+(end-start));
	}
	//clone方式
	public static void testClone(int size) throws CloneNotSupportedException {
		long start = System.currentTimeMillis();
		Laptop tLaptop = new Laptop();
		for (int i = 0; i < size; i++) {
			Laptop tLaptop2 = (Laptop) tLaptop.clone();
		}
		long end = System.currentTimeMillis();
		System.out.println("clone的方式创建耗时："+(end-start));
	}
}

//笔记本电脑类
class Laptop implements Cloneable{ 
	public Laptop(){
		try {
			Thread.sleep(10);	//线程暂停10毫秒代替耗时时间
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}
	
	// clone() 方法
	@Override
	protected Object clone() throws CloneNotSupportedException {
		Object object = super.clone();	
		return object;
	}
}
```
<details>
<summary>运行结果</summary>

```bash
new的方式创建耗时：10170
clone的方式创建耗时：11
```
</details>
  
如果需要短时间创建大量对象，并且 new 的过程比较耗时，则可以考虑原型模式，原型模式通常和工厂模式搭配
