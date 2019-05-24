---
title: 建造者模式
date: 2019-05-24 17:21:00
categories: [设计模式]
tags: [java]
---
<span style="color:#B900ff;">分离了对象子组件的单独构造<span style="color:#ff0000;">(由`Builder`建造者负责)</span>和装配<span style="color:#ff0000;">(由`Director`装配者负责)</span>的分离；从而可以构造出复杂的对象  
这个模式适用于：某个对象的构造过程复杂的情况下使用  
由于实现了构建和装配的解耦，不同的构造器，相同的装配，也可以做出不同的对象，相同的构造器，不同的顺序装配也可以做出不同的对象，这也就是实现了构建算法，装配算法的解耦，实现了更好的复用</span>  
  
#### 应用场景  
> 构建复杂产品之后的组装工作，通常和工厂模式配合使用  
> StringBuilder类的append方法  
> JDOM中、DomBuilder、SaxBuilder  
  
#### 模式结构  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g3chltu6iaj30sa0i8dgm.jpg)  
  
#### 示例代码  
建造者模式中有建造者和装配者，首先建造者要建造什么产品，需要一个对象 `AirShip` 宇宙飞船类，宇宙飞船由三个组件 (轨道舱、发动机、逃逸塔) 组成，并且提供一个 `launch()` 方法发射飞船  
<span style="color:#ff0000;">注意：一般我们直接将组件写成多个`.class`文件，而不是全放在一个类文件里，这里简写一下而已</span>  
```java
/**
 * 【建造者模式】建造者
 * @author teaper
 * 宇宙飞船类
 */
public class AirShip {
	private OrbitalModel orbitalModel;    //轨道舱
	private Engine engine;   //发动机
	private EscapeTower escapeTower;  //逃逸塔
	
	public void launch() {
		System.out.println("发射！");
	}
	
	
	public OrbitalModel getOrbitalModel() {
		return orbitalModel;
	}
	public void setOrbitalModel(OrbitalModel orbitalModel) {
		this.orbitalModel = orbitalModel;
	}
	public Engine getEngine() {
		return engine;
	}
	public void setEngine(Engine engine) {
		this.engine = engine;
	}
	public EscapeTower getEscapeTower() {
		return escapeTower;
	}
	public void setEscapeTower(EscapeTower escapeTower) {
		this.escapeTower = escapeTower;
	}
}

//轨道舱类
class OrbitalModel{
	private String name;

	public OrbitalModel(String name) {
		super();
		this.name = name;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}
		
}

//发动机
class Engine{
	private String nameString;

	public Engine(String nameString) {
		super();
		this.nameString = nameString;
	}

	public String getNameString() {
		return nameString;
	}

	public void setNameString(String nameString) {
		this.nameString = nameString;
	}
	
}

//逃逸塔
class EscapeTower{
	private String name;

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public EscapeTower(String name) {
		super();
		this.name = name;
	}
	
}
```
有具体组件了，就可以开始创建我们的创造者，创造者负责创造组件，那么就需要一个创造者接口 `AirShipBuilder` ，用于建造各种器件；以及它的实现类 `SxtAirShipBuilder` 去建造具体的器件属性  
```java
/**
 * 【建造者模式】建造者接口
 * @author teaper
 * 帮助构建子组件类
 */
public interface AirShipBuilder {
	Engine builderEngine();		//构建发动机
	OrbitalModel builderOrbitalModel();			//	构建轨道舱
	EscapeTower builEscapeTower();		//构建逃逸塔
}
```
```java
/**
 * 【建造者模式】建造者实现类，继承建造者类
 * @author teaper
 * 说明：带Builder的接口名都是使用了建造者模式，例如：StringBuilder、DomBuilder、SaxBuilder  
 * 以后项目中如果使用到设计模式，也要这样去命名
 */
public class SxtAirShipBuilder implements AirShipBuilder {

	@Override
	public Engine builderEngine() {
		System.out.println("构建发动机组件");
		return new Engine("奔腾发动机");
	}

	@Override
	public OrbitalModel builderOrbitalModel() {
		System.out.println("构建轨道舱");
		return new OrbitalModel("灵动轨道舱");
	}

	@Override
	public EscapeTower builEscapeTower() {
		System.out.println("构建逃逸塔");
		return new EscapeTower("华容逃逸塔");
	}
}
```
一般我们看到的带 `Builder` 的接口名都是使用了建造者模式，根据《阿里巴巴Java开发手册》，当我们在项目中使用了相应设计模式的时候，也要拼接对应模式的英文，达到见名知意  
  
建造者完成了具体器件的建造，需要交给装配者进行器件的组装，这里定义一个装配者接口 `AirShipDirector` (见名知意：建造者接口名带Director) 以及它的实现类 `SxtAirShipDirector`   
```java
/**
 * 【建造者模式】装配者接口
 * @author teaper
 * 
 */
public interface AirShipDirector {
	AirShip directorAirShip();	//组装飞船对象
}
```
```java
/**
 * 【建造者模式】装配者实现类，继承装配者接口
 * @author teaper
 *
 */
public class SxtAirShipDirector implements AirShipDirector{
	
	private AirShipBuilder builder;
	
	public SxtAirShipDirector(AirShipBuilder builder) {
		super();
		this.builder = builder;
	}

	@Override
	public AirShip directorAirShip() {
		//调用建造者接口，创造组件
		Engine engine = builder.builderEngine();
		OrbitalModel orbitalModel= builder.builderOrbitalModel();
		EscapeTower escapeTower = builder.builEscapeTower();
		
		//将组件组装成飞船对象
		AirShip airShip = new AirShip();
		airShip.setEngine(engine);
		airShip.setOrbitalModel(orbitalModel);
		airShip.setEscapeTower(escapeTower);
		
		return airShip;
	}
}
```
装配者实现类需要具体组件，所以需要定义一个组件接口 `AirShipBuilder` ，使用该接口获取具体组件信息，将其封装在一个宇宙飞船对象 `AirShip` 中，返回给需要飞船的客户  
  
客户需要飞船，只需要通过装配者 `AirShipDirector` 以及他的实例 `SxtAirShipDirector` 获取一个宇宙飞船对象，`SxtAirShipDirector` 实例需要一个建造者对象`AirShipBuilder`，这里就直接 `new` 一个即可  
```java
/**
 * 【建造者模式】测试建造者模式，创造飞船
 * @author teaper
 *
 */
public class Client {
	public static void main(String[] args) {
		AirShipDirector airShipDirector = new SxtAirShipDirector(new SxtAirShipBuilder());
		AirShip airShip = airShipDirector.directorAirShip();
		System.out.println(airShip.getEngine().getNameString());
		airShip.launch();
	}

}
```
拿到飞船对象，直接通过它的建造者的建造方法 `directorAirShip()` 去组装飞船即可，最后可以输出以下结果  
```bash
构建发动机组件
构建轨道舱
构建逃逸塔
奔腾发动机
发射！
```

  


