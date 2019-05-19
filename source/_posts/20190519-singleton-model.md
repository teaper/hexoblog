---
title: 单例模式
date: 2019-05-19 22:48:31
categories: [设计模式]
tags: [java]
---
<span style="color:#B900ff;">核心作用：保证一个类只有一个示例，并且提供一个访问该实例的全局访问点</span>  
  
#### 应用场景  
> Windows的Task Manager（任务管理器）就是典型的单例模式  
> Windows的Recycle Bin（回收站）也是典型的单例模式，在整个系统中，回收站一直维护着一个实例  
> <span style="color:#ff0000;">项目中，读取配置文件的类，一般也只有一个对象，没有必要每次使用配置文件数据，每次new一个对象去读取</span>  
> 网站的计数器，一般也采用单例模式，否则难以同步  
> 应用程序的日志功能，一般都使用单例模式，由于共享的日志文件一直处于打开状态，因为只能一个实例去操作，否则内容不好追加  
> <span style="color:#ff0000;">数据库连接池的设计一般也采用单例模式，因为数据库连接是一种数据库资源</span>  
> 操作系统的文件系统，也是最大的单例模式的具体例子，一个操作系统只能有一个文件系统  
> Application 也是单例的典型应用（servlet 编程中会涉及到）  
> <span style="color:#ff0000;">在Spring框架中，每个bean默认就是单例的，这样做的优点就是方便Spring IOC容器进行管理</span>  
> 在servlet编程中，每个servlet也是单例  
> <span style="color:#ff0000;">在Spring MVC框架/struts2框架中，控制器对象也是单例</span>  
  
#### 单例模式的优点  
由于单例模式只生成一个实例，减少系统性能开销，当一个对象的产生需要比较多的资源时（例如：读取配置、产生其他依赖对象时），则可以通过在应用启动时直接产生一个单例对象，然后永久驻留内存的方式来解决  
  
单例模式可以在系统设置全局的访问点，优化环共享资源访问（例如：可以设计一个单例类，负责所有数据表的映射处理）  
  
#### 常见的五种单例模式实现方式：  
主要：  
* 饿汉式（线程安全，调用效率高；但是，不能延时加载）  
* 懒汉式（线程安全，调用效率不高；但是，可以延时加载）  
  
其他：  
* 双重检测锁式（用于JVM底层内部模型原因，偶尔出现问题，<span style="color:#ff0000;">不建议使用</span>）  
* 静态内部类式（线程安全，调用效率高，可以延时加载）
* 枚举式（线程安全，调用率高，不能延时加载，并且天然的防止反射和反序列化漏洞）  
  
#### 饿汉式实现  
```java
/**
 * 【单例模式】饿汉式
 * @author teaper
 * 解说：
 * 类初始化的时候，不管你要不要都提供一个静态的私有对象instance
 * 静态变量会在类装载时初始化，此时不会涉及，多个线程对象的访问该对象的问题
 * 虚拟机保证只转载一次该类，肯定不会发生并发访问的问题，因此可以省略synchronized关键字
 * 再私有化构造器，不让创建其他对象 
 * 只提供静态私有方法返回仅有的一个对象instance
 * 
 * 缺点：
 * 如果只是加载类，不调用类的getInstance()方法，会造成资源浪费
 */
public class SingletonDemo01 {
	//1、只留一个私有的对象
	private static /*final*/ SingletonDemo01 instance= new SingletonDemo01();	
	
	//2、私有化构造器
	private SingletonDemo01() {
		
	}		
	
	//3、提供私有方法返回私有对象
	public static /*synchronized*/ SingletonDemo01 getInstance() {
		return instance;	
	}
}
```
#### 懒汉式实现  
```java
/**
 * 【单例模式】懒汉式
 * @author teaper
 * 解说：
 * 类初始化的时候，只创建静态的私有对象instance，并没有进行初始化(默认值：null)
 * 虚拟机保证只转载一次该类，肯定不会发生并发访问的问题，因此可以省略synchronized关键字
 * 再私有化构造器，不让创建其他对象 
 * 提供静态私有方法，判断是否初始化，如果未初始化就给对象进行初始化（延时加载、懒加载、真正用的时候才加载）
 * 
 * 缺点：
 * 资源利用率高了，而且每次调用getInstance()方法都要等待其他对象调用完，并发效率低
 */
public class SingletonDemo02 {
	//1、创建一个静态私有对象，但是不初始化
	private static SingletonDemo02 instance;
	
	//2、私有化构造器
	private SingletonDemo02() {
		
	}
	
	//3、提供静态私有方法，判断是否初始化，如果未初始化就给对象进行初始化
	public static synchronized SingletonDemo02 getInstance() {
		if(instance == null) {
			instance = new SingletonDemo02();
		}
		return instance;
	}
}
```
#### 双重检测锁式实现  
```java
/**
 * 【单例模式】双重检测锁式
 * @author teaper
 * 解说：
 * 这个模式同步内容下方到if内部，提高了执行的效率
 * 不必每次获取对象时都进行同步，只有第一次才同步创建了以后就没必要
 * 
 * 缺点：
 * 由于编译器优化原因和JVM底层内部模型原因，偶尔会出现问题，所以不建议使用（仅做参考）
 */
public class SingletonDemo03 {
	//1、创建一个静态私有对象，但是不初始化
	private static SingletonDemo03 instance;
	
	//2、私有化构造器
	private SingletonDemo03() {
		
	}
	
	public static SingletonDemo03 getInstance() {
		if (instance == null) {
			SingletonDemo03 sc;
			synchronized (SingletonDemo03.class) { 
				sc = instance;
				if (sc == null) {
					synchronized (SingletonDemo03.class) {
						if (sc == null) {
							sc = new SingletonDemo03();
						}
					}
					instance = sc;
				}
			}
		}
		return instance;
	}

}
```
#### 静态内部类式实现  
```java
/**
 * 【单例模式】静态内部类式（懒加载）
 * @author teaper
 * 解说：
 * 外部类没有static属性，则不会像饿汉式那样立刻加载对象
 * 初始化SingletonDemo04类的时候，并不会立即初始化它的静态内部类  
 * 只有调用getInstance()方法，才会调用它的静态内部类去创建实例对象，实现延时加载（懒加载）
 * 加载类时是线程安全的，instance对象是static final类型，保证内存中只有一个实例存在，而且只能被赋值一次，从而保证线程安全
 * 兼并了并发高效调用和延时加载的优势
 */
public class SingletonDemo04 {
	//1、在静态内部类SingletonClassInstance中定义一个对象
	private static class SingletonClassInstance{
		private static final SingletonDemo04 instance = new SingletonDemo04();	//使用final禁止修改
	}
	
	//2、私有化构造器
	private SingletonDemo04() {	
		
	}
	
	//3、提供静态私有方法，返回内部类实例
	public static SingletonDemo04 getInstance() {
		return SingletonDemo04.SingletonClassInstance.instance;
	}	

}
```
#### 枚举式实现  
```java
/**
 * 【单例模式】枚举单例
 * @author teaper
 * 优点：
 * 实现简单
 * 枚举本身就是单例模式，由JVM从根本上提供保障，避免了反射和反序列化的漏洞，调用效率比较高
 * 
 * 缺点：
 * 没有延时加载（懒加载）
 */
public enum SingletonDemo05 {
	// 枚举元素本身就是单例对象，定义一个枚举的元素，它就代表单例的一个实例
	INSTANCE;
	
	// 添加自己需要的操作。
	public void singletonOperation() {
		//功能处理代码块
	}

}
```
<span style="color:#ff0000;">那么如何选用呢？</span>  
如果是单例对象，要求占用资源少，不需要延时加载的话
* **枚举式** 好于 **饿汉式**  
  
如果是单例对象，要求占用资源大，需要延时加载的话
* **静态内部类式** 好于 **懒汉式**  
  
#### 破解单例模式  
虽然说单例模式被我们设计成只有一个对象，不过我们依然可以使用**反射**、**反序列化**破解除枚举之外的其他四种单例模式，让它创建多个对象  
  
> 使用反射破解懒汉式  
  
```java
/**
 * 【单例模式】使用反射和反序列化破解单例模式
 * @author teaper
 *
 */
public class Client2 {
	public static void main(String[] args) throws Exception {
		//先输出两个懒汉式对象的值
		SingletonDemo06 instance1 = SingletonDemo06.getInstance();
		SingletonDemo06 instance2 = SingletonDemo06.getInstance();
		System.out.println(instance1+"\n"+instance2);
		
		//使用反射破解懒汉式
		Class<SingletonDemo06> clazz =  (Class<SingletonDemo06>) Class.forName("cn.teaper.www.singletonmode.SingletonDemo06");
		Constructor<SingletonDemo06> c = clazz.getDeclaredConstructor(null);
		c.setAccessible(true);
		SingletonDemo06 s3 = c.newInstance();
		SingletonDemo06 s4 = c.newInstance();
		System.out.println(s3+"\n"+s4);
	}

}
```
先输出`instance1`和`instance2`的值，确定是同一个对象，然后使用反射破解懒汉式，再输出两个对象，可以发现运行结果变成  
```bash
cn.teaper.www.singletonmode.SingletonDemo06@7b3300e5    #instance1
cn.teaper.www.singletonmode.SingletonDemo06@7b3300e5    #instance2
cn.teaper.www.singletonmode.SingletonDemo06@2e5c649     #s3
cn.teaper.www.singletonmode.SingletonDemo06@136432db    #s4
```
很明显利用反射可以直接破解懒汉式，那么**如何防止懒汉式被破解呢？**  
可以判断对象是否存在，不存在则**抛出运行时异常**，就此防止懒汉式被破解  
```java
/**
 * 【单例模式】测试懒汉式（如何防止反射和反序列化漏洞）
 * @author teaper
 *
 */
public class SingletonDemo06 {
	
	private static SingletonDemo06 instance;
	
	private SingletonDemo06() {
		//抛出异常防止反射破解
		if(instance!=null) {
			throw new RuntimeException();
		}
	}
	
	public static synchronized SingletonDemo06 getInstance() {
		if(instance == null) {
			instance = new SingletonDemo06();
		}
		return instance;
	}
}
```
> 使用反序列化破解懒汉式  
  
要使用反射，首先懒汉式的类就要继承`Serializable`类  
```java
/**
 * 【单例模式】测试懒汉式（如何防止反射和反序列化漏洞）
 * @author teaper
 *
 */
public class SingletonDemo06 implements Serializable {   //继承Serializable类
	
	//1、创建一个静态私有对象，但是不初始化
	private static SingletonDemo06 instance;
	
	//2、私有化构造器
	private SingletonDemo06() {
	
	}
	
	//3、提供静态私有方法，判断是否初始化，如果未初始化就给对象进行初始化
	public static synchronized SingletonDemo06 getInstance() {
		if(instance == null) {
			instance = new SingletonDemo06();
		}
		return instance;
	}

}
```
先使用序列化将值存储在`a.txt`中，然后再进行反序列化输出结果
```java
/**
 * 【单例模式】使用反射和反序列化破解懒汉式单例模式
 * @author teaper
 *
 */
public class Client2 {
	public static void main(String[] args) throws Exception {
		//先输出两个懒汉式对象的值
		SingletonDemo06 instance1 = SingletonDemo06.getInstance();
		SingletonDemo06 instance2 = SingletonDemo06.getInstance();
		System.out.println(instance1+"\n"+instance2);
		
		//使用反序列化破解懒汉式
		//序列化
		FileOutputStream fos = new FileOutputStream("../a.txt");
		ObjectOutputStream oos = new ObjectOutputStream(fos);
		oos.writeObject(instance1);
		oos.close();
		fos.close();
		
		//反序列化
		ObjectInputStream ois = new ObjectInputStream(new FileInputStream("../a.txt"));
		SingletonDemo06 s3 = (SingletonDemo06) ois.readObject();
		System.out.println(s3);
	}

}
```
可以看到输出结果是不同的  
```bash
cn.teaper.www.singletonmode.SingletonDemo06@7b3300e5    #instance1
cn.teaper.www.singletonmode.SingletonDemo06@7b3300e5    #instance2
cn.teaper.www.singletonmode.SingletonDemo06@7cf10a6f    #s3
```
那么如何**防止被反序列化破解**呢？  
只需要添加一个私有方法`reaObject()`返回instance对象即可  
```java
/**
 * 【单例模式】测试懒汉式（如何防止反射和反序列化漏洞）
 * @author teaper
 *
 */
public class SingletonDemo06 implements Serializable {
	
	//1、创建一个静态私有对象，但是不初始化
	private static SingletonDemo06 instance;
	
	//2、私有化构造器
	private SingletonDemo06() {
		//抛出异常破解反射
		if(instance!=null) {
			throw new RuntimeException();
		}
	}
	
	//3、提供静态私有方法，判断是否初始化，如果未初始化就给对象进行初始化
	public static synchronized SingletonDemo06 getInstance() {
		if(instance == null) {
			instance = new SingletonDemo06();
		}
		return instance;
	}
	
	//防止反序列化破解
	private Object reaObject() throws ObjectStreamException{
		return instance;//在反序列化时会直接调用reaObject()方法返回instance对象，而不会把反序列化的新对象返回
	}
}
```
#### 五种单例模式的执行效率  
这里我们采用的是多线程下进行的测试，不同环境下的值有所不同  
| 单例模式 | 执行时间 |
| -------- | -------- |
| 饿汉式 | 22ms |
| 懒汉式 | 636ms |
| 静态内部类式 | 28ms |
| 枚举式 | 32ms |
| 双重检测锁式 | 65ms |
  
要测试多线程下单例模式的执行效率，需要借助一个`CountDownLatch`类  
> 同步辅助类，在完成一组正在其他线程中执行的操作之前，它允许一个或多个线程一直等待  
> `countDown()`当前线程调用此方法，则计数减一（建议放在finally里执行）  
> `await()`调用此方法会一直阻塞当前线程，直到倒计时为零  
  
```java
/**
 * 【单例模式】测试多线程模式下五种单例模式的执行效率
 * @author teaper
 *
 */
public class Client3 {
	public static void main(String[] args) throws Exception{
		
		long start = System.currentTimeMillis();
		
		int threadNum = 10;
		final CountDownLatch countDownLatch = new CountDownLatch(threadNum);
		
		
		for (int i = 0; i < 10; i++) {
			new Thread(new Runnable() {
				@Override
				public void run() {
					for (int i = 0; i < 1000000; i++) {
						Object o = SingletonDemo02.getInstance();//SingletonDemo01可以换成你想要测试的单例类
					}
					countDownLatch.countDown();
				}
			}).start();
		}
		countDownLatch.await();//线程阻塞，直到计数器为0，才继续往下执行
		long end = System.currentTimeMillis();
		
		System.out.println("总耗时："+(end-start));
	}

}
```
