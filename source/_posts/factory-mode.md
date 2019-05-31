---
title: 工厂模式
date: 2019-05-19 22:51:49
categories: [设计模式]
tags: [java]
---
<span style="color:#B900ff;">核心作用：实现了创建者和调用者的分离</span>  
<span style="color:#B900ff;">核心本质：实例化对象，用工厂方法代替`new`操作，将选择实现类、创建对象统一管理和控制，从而将调用者跟我们的实现类解耦</span>  
  
#### 常见的三种工厂模式  
* 简单工厂模式：用来生产同一等级结构中的任意产品（对于新的产品需要修改已有代码）
* 工厂方法模式：用来生产同一等级结构中的固定产品（支持任意产品）
* 抽象工厂模式：用来生产不同产品族的全部产品（对于增加新的产品无能为力，支持增加产品族）
  
#### 应用场景  
> JDK中的Calendar的getInstance方法  
> JDBC的connection 对象的获取  
> Hibernate中的sessionFactory的session创建  
> Spring 的 IOC容器创建管理bean对象  
> 反射中Class对象的newInstance  
  
#### 面向对象设计的基本原则  
* OCP（开闭原则 Open-Closed Principe）：一个软件的实体应当对拓展开放，对修改关闭  
* DIP（依赖倒转原型，Dependence Inversion Principle）：要针对接口编程，不要针对实现编程。
* LoD（迪米特法则，Law of Demeter）：只与你得朋友通信，而避免和陌生人通信
  
首先，我们需要一个车类接口，给比亚迪和奥迪继承，并实现车类接口的`run()`方法，输出什么车在跑  
```java
/**
 * 【工厂模式】车类接口
 * @author teaper
 *
 */
public interface Car {
	void run();
}
```
```java
/**
 * 【工厂模式】奥迪车类，继承车类接口
 * @author teaper
 *
 */
public class Audi implements Car{

	@Override
	public void run() {
		System.out.println("奥迪在跑！");
	}

}
```
```java
/**
 * 【工厂模式】比亚迪车类，继承车类接口
 * @author teaper
 *
 */
public class Byd implements Car {

	@Override
	public void run() {
		System.out.println("比亚迪在跑！");

	}

}
```
那么没有工厂模式的情况下，我们要如何输出对应车类的实现呢？  
```java
/**
 * 【工厂模式】在没有工厂模式的情况下
 * @author teaper
 * 
 */
public class Client {
	public static void main(String[] args) {
		Car c1 = new Audi();
		Car c2 = new Byd();
		c1.run();
		c2.run();
	}

}
```
从上面我们可以看出，如果我们要实现对应车类，需要每次都去`new`对应实现，如果车类很多，几百种，那么名称就记不过来，因此我们需要一个自己会判断的工厂，要什么就自动给什么
  
#### 简单工厂模式  
简单工厂模式 也可以叫做静态工厂模式，就是工厂一般使用静态方法 通过接收参数的形式来返回不同的对象实例  
  
**写法一：**  
直接采用`if`去判断需要的是什么车，然后将对象返回  
```java
/**
 * 【简单工厂模式】写法一
 * @param type
 * @return
 */
public class CarFactory {
	public static Car createCar(String type) {
		if("奥迪".equals(type)) {
			return new Audi();
		}else if("比亚迪".equals(type)){
			return new Byd();
		}else {
			return null;
		}
	}

}
```
```java
/**
 * 【简单工厂模式】测试写法一
 * @author teaper
 * 
 */
public class Client {
	public static void main(String[] args) {
		Car c1 = CarFactory.createCar("奥迪");
		Car c2 = CarFactory.createCar("比亚迪");
		c1.run();
		c2.run();
	}

}
```
**写法二：**  
```java
/**
 * 【简单工厂模式】写法二
 * @author teaper
 *
 */
public class CarFactory2 {
	public static Car createAudi() {
		return new Audi();
	}
	public static Car createByd() {
		return new Byd();
	}

}
```
```java
/**
 * 【简单工厂模式】测试写法二
 * @author teaper
 * 
 */
public class Client2 {
	public static void main(String[] args) {
		Car c1 = CarFactory2.createAudi();
		Car c2 = CarFactory2.createByd();
		c1.run();
		c2.run();
	}

}
```
从上面代码中不难看出，我们的工厂代码中需要知道各种车子类的实现，如果要添加新的车类，首先需要新建一个`.class`文件，然后还要修改`Factory.class`，在里面添加判断或`new`相应的对象，没有满足OCP原则 (开闭原则)  

<span style="color:#ff0000;">缺点：新增产品无能为力，不修改代码的话是无法拓展的</span>  
  
#### 工厂方法模式  
为了避免简单工厂模式不完全满足OCP原则的缺点，可以通过工厂方法模式来解决  
工厂方法模式和简单工厂模式最大的不同在于，简单工厂模式只有一个<span style="color:#ff0000;">（对于一个项目或者一个独立的模块而言）</span>工厂类，而工厂模式有一组实现了相同接口的工厂类  
  
首先创建一个工厂接口，给其他工厂类继承  
```java
/**
 * 【工厂方法模式】车类接口，实现创建车方法
 * @author teaper
 *
 */
public interface CarFactory {
	Car createCar();
}
```
```java
/**
 * 【工厂方法模式】奥迪车工厂，继承车类接口，创建奥迪车
 * @author teaper
 *
 */
public class AudiFactory implements CarFactory {

	@Override
	public Car createCar() {
		return new Audi();
	}

}
```
```java
/**
 * 【工厂方法模式】比亚迪车工厂，继承车类接口，创建比亚迪车
 * @author teaper
 *
 */
public class BydFactory implements CarFactory {

	@Override
	public Car createCar() {
		return new Byd();
	}

}
```
```java
/**
 * 【工厂方法模式】奔驰车工厂，继承车类接口，创建奔驰车
 * @author teaper
 *
 */
public class BenzFactory implements CarFactory {

	@Override
	public Car createCar() {
		return new Benz();
	}

}
```
```java
/**
 * 【工厂方法模式】测试两个工厂类
 * @author teaper
 *
 */
public class Client {
	public static void main(String[] args) {
		Car c1 = new AudiFactory().createCar();
		Car c2 = new BydFactory().createCar();
		c1.run();
		c2.run();
	}

}
```
如果我们需要添加一个奔驰车类，只需要创建一个奔驰车对象继承车类接口，再创建一个奔驰车工厂，继承车工厂类接口即可，不需要修改代码段，解决了简单工厂模式的弊端；但是其自身需要创建很多类，不利于管理  
  
#### 简单工厂模式和工厂方法模式比较
 **结构复杂度**  
 * 从这个角度比较，显然简单工厂要占优势;简单工厂只需要一个工厂类，而工厂方法模式的工厂类随着产品的个数增多而增加
 * 这样就会使类的个数越来越多，无疑是增加了结构的复杂程度  
   
 **代码复杂度**  
 * 代码复杂度和结构复杂度是一对矛盾，既然简单工厂模式在结构方面比较简洁，那么代码方面肯定是要比工厂方法模式的复杂的了
 * 简单工厂模式的工厂类随着产品的个数增多而增加很多方法。而工厂方法模式每个具体工厂只完成一项产品的任务，代码简洁
  
**客户端编制难度**  
 * 工厂方法模式虽然在工厂类结构中引入了接口从而满足了OCP，但是在客户端代码中需要对工厂进行实例化
 * 而简单工厂类是一个静态类。在客户端无法实例化  
  
**管理上的难度**  
 * 这是个关键的问题
 * 扩展，工厂方法模式完全满足于OCP,即他又非常良好的拓展性，那是否就说明了简单工厂模式就没有了拓展性呢？
 * 答案是否定的。简单工厂模式同样具有良好的拓展性。拓展的时候，仅需要修改少量的代码。修改工厂类的代码就可以满足拓展性的要求了
  
<span style="color:#ff0000;">根据设计理论建议：工厂方法模式。而实际上，我们一般还是使用**简单工厂模式**</span>
  
#### 抽象工厂模式  
用来生产不同产品族的全部产品<span style="color:#ff0000;">（对于增加新的产品无能为力，支持增加产品族）</span>  
抽象工厂模式是工厂模式的升级版本；在有多个业务品种，业务分类时，通过抽象工厂模式产生需要的对象是一种非常好的解决方式  
  
> 举个例子：我们需要多个产品族，且有一系列不同规格的产品  
  
| 产品1 | 产品2 | 产品3 | 规格 |
| ----- | ----- | ----- | ----- |
| 发动机 | 座椅 | 轮胎 | 高端产品族|
| 发动机 | 座椅 | 轮胎 | 中端产品族|
| 发动机 | 座椅 | 轮胎 | 低端产品族|
  
首先创建发动机、座椅、轮胎接口类，并实现对应的方法  
```java
/**
 * 【抽象工厂模式】发动机类接口
 * @author teaper
 *
 */
public interface Engine {
	void run();
	void start();
}

//高端发动机
class LuxuryEngine implements Engine{

	@Override
	public void run() {
		System.out.println("高端发动机时速：800km/h");
		
	}

	@Override
	public void start() {
		System.out.println("高端发动机启动时间：30s");
		
	}
	
}

//低端发动机
class LowEngine implements Engine{

	@Override
	public void run() {
		System.out.println("低端发动机时速：300km/h");
		
	}

	@Override
	public void start() {
		System.out.println("低端发动机启动时间：80s");
		
	}
	
}
```
```java
/**
 * 【抽象工厂模式】座椅类接口
 * @author teaper
 *
 */
public interface Seat {
	void massage();
}

//高端座椅
class LuxurySeat implements Seat{

	@Override
	public void massage() {
		System.out.println("高端座椅：支持自动按摩！");
		
	}
	
}
//低端座椅
class LowSeat implements Seat{

	@Override
	public void massage() {
		System.out.println("低端座椅：不支持自动按摩！");
		
	}
	
}
```
```java
/**
 * 【抽象工厂模式】轮胎类接口
 * @author teaper
 *
 */
public interface Tyre {
	void revolve();
}

//高端轮胎
class LuxuryTyre implements Tyre{

	@Override
	public void revolve() {
		System.out.println("高端轮胎：使用寿命5年");
		
	}
	
}

//低端轮胎
class LowTyre implements Tyre{

	@Override
	public void revolve() {
		System.out.println("低端轮胎：使用寿命2年");
		
	}
	
}
```
然后针对产品族，创建产品工厂，用来创建产品，然后高端产品工厂和低端产品工厂继承产品工厂生成高端或低端产品  
```java
/**
 * 【抽象工厂模式】创建产品族
 * @author teaper
 *
 */
public interface CarFactory {
	Engine createEngine();//创建发动机
	Seat createSeat();//创建座椅
	Tyre createTyre();//创建轮胎
}
```
```java
/**
 * 【抽象工厂模式】高端产品族工厂
 * @author teaper
 *
 */
public class LuxuryCarFactory implements CarFactory {

	@Override
	public Engine createEngine() {
		return new LuxuryEngine();

	}

	@Override
	public Seat createSeat() {
		return new LuxurySeat();

	}

	@Override
	public Tyre createTyre() {
		return new LuxuryTyre();

	}

}
```
```java
/**
 * 【抽象工厂模式】低端产品族工厂
 * @author teaper
 *
 */
public class LowCarFactory implements CarFactory {

	@Override
	public Engine createEngine() {
		return new LowEngine();

	}

	@Override
	public Seat createSeat() {
		return new LowSeat();

	}

	@Override
	public Tyre createTyre() {
		return new LowTyre();
	}
}
```
测试使用抽象工厂
```java
/**
 * 【抽象工厂模式】测试
 * @author teaper
 * 
 */
public class Client {
	public static void main(String[] args) {
		//高端产品
		CarFactory factory = new LuxuryCarFactory();
		Engine engine = factory.createEngine();
		Seat seat = factory.createSeat();
		Tyre tyre = factory.createTyre();
		engine.run();
		engine.start();
		seat.massage();
		tyre.revolve();
		
		//低端产品
		CarFactory factory2 = new LowCarFactory();
		Engine engine2 = factory2.createEngine();
		Seat seat2 = factory2.createSeat();
		Tyre tyre2 = factory2.createTyre();
		engine2.run();
		engine2.start();
		seat2.massage();
		tyre2.revolve();
	}

}
```
  
