---
title: Maven+Spring+SpringMVC+MyBatis整合
date: 2019-05-19 22:27:19
categories: [java]
tags: [Maven,Tomcat,Spring,SpringMVC,MyBatis,MySQL]
---
### 项目简介	  
#### 开发环境:  
系统：Arch Linux   
JDK-1.8   
Eclipse-4.10   
Tomcat-9.0   
浏览器：Chrome   
	
#### 技术:  
项目管理：Maven-3.6.0   
动态Web模块：Dynamic Web Module-4.0   
基础框架：Spring+SpringMVC+MyBatis   
数据库：10.1.37-MariaDB（ArchLinux下的MySQL）   
前端框架：bootstrap-3.3.7+jquery-3.3.1.min.js   
分页：pagehelper   
逆向工程：MyBatis Generator   

#### 功能:   
分页查询   
数据校验(前端:jquery校验+后端:JSR303校验)   
ajax请求   
Rest风格的URI   

#### 目录结构:   
```java
//Project Explorer视图下
|ssm 
|---src/main/java
|	|---cn.teaper.www   //包路径
|		|---controller      //控制器
|		|---dao         //数据库dao
|		|---pojo        //实体类
|		|---service     //服务层
|		|---test        //测试类
|		|---tools        //工具类
|---src/main/resources
|	|---mapper      //存放mapper文件
|	|---applicationContext.xml      //Spring核心配置文件
|	|---database.properties     //数据库连接信息
|	|---mybatis-config.xml      //
|	|---spring-mvc.xml          //
|---src/test/java       //手动创建(可选)
|---src/test/resources      //手动创建(可选)
|---JRE System Library[JavaSE-1.8]
|---Maven Dependencies
|---src
|   |---main
|       |---webapp
|           |---META-INF
|           |---static  //手动创建
|               |---bootstrap-3.3.7.dist    
|           |---WEB-INF     //重新生成
|               |---lib
|               |---views       //手动创建，用于存放视图文件
|           |---index.jsp   //重新创建
|---target
|---mbg.xml
|---pom.xml
|---ssm.sql     //数据库SQL文件
|---doc         //项目文档(gitbook)
```

### 项目环境搭建  
#### 创建Maven工程:  
单击 File -> New -> Maven Projct   
  
勾选 `Use defaut Workspace location` 复选框,`Browse..`项目存放位置,单击 `Next`   
选则相应模板 `org.apache.maven.archetypes maven-archetype-webapp 1.0`   
单击 `Next`,输入基本信息:  
> Group Id：组名字（例如：cn.teaper）  
> Artifact Id：项目名称（例如：ssm）  
> Version：版本号,默认0.0.1-SNAPSHOT  
> Packing：项目包结构（例如：cn.teaper.www）  
  
单击 `Finish` 完成  
  
把`src/main/webapp`下默认的文件和文件夹删除  
  
右击项目 -> Properties -> Project Facets 中修改 Java 版本为 1.8,取消勾选 `Dynamic Web Module(2.3)`，选择4.0，保存一下，再重新勾选，单击下方 `further configruation available`,输入`Content directory` 地址为 `src/main/webapp` ，勾选 `Generate~` 复选框，再保存一下;    
web.xml内容为以下样式即可：  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://xmlns.jcp.org/xml/ns/javaee" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd" id="WebApp_ID" version="4.0">
  <display-name>ssm</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
</web-app>
```
  
选择 `Java Build Path` ，在 Source 窗口下置输出路径 output folder ，确保每个文件夹都输出在 `*/target/classes` 目录中;选择 `Deployment Assembly` , Remove 掉不需要的设置 `(src/test/*)`  
<span style="color:#ff0000;">注意:如果`Project Facets`中无法修改版本信息，就在 `Navigator` 视图中编辑`org.eclipse.wst.common.project.facet.core.xml`文件，`Navigator` 视图在 windows > show viwe 中找</span>  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<faceted-project>
  <fixed facet="wst.jsdt.web"/>
  <installed facet="java" version="1.8"/>
  <installed facet="jst.web" version="3.1"/>
  <installed facet="wst.jsdt.web" version="1.0"/>
</faceted-project>
```
如果 src/main/java、src/test/java、src/test/resources 目录不存在；则在 Navigator 视图中手动创建上面三个文件夹( Navigator 视图在 windows -> show view 中打开)  
最终目录结构如上图所示  

#### 引入jar包:    
打开 pom.xml, 把[maven仓库](https://mvnrepository.com/)中的 dependency 信息粘贴到 pom.xml 的 dependencies 标签中，每个模块的 dependency 和它所包含的 jar 包如下  
```xml
SpringMVC模块:
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-webmvc</artifactId>
	<version>4.3.7.RELEASE</version>
</dependency>

spring-webmvc-4.3.7.RELEASE.jar
spring-aop-4.3.7.RELEASE.jar
spring-beans-4.3.7.RELEASE.jar
spring-context-4.3.7.RELEASE.jar
spring-core-4.3.7.RELEASE.jar
commons-logging-1.2.jar
spring-expression-4.3.7.RELEASE.jar
spring-web-4.3.7.RELEASE.jar

Spring-jdbc模块:
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-jdbc</artifactId>
	<version>4.3.7.RELEASE</version>
</dependency>

spring-jdbc-4.3.7.RELEASE.jar
spring-beans-4.3.7.RELEASE.jar
spring-core-4.3.7.RELEASE.jar
commons-logging-1.2.jar
spring-tx-4.3.7.RELEASE.jar

Spring面向切面编程:
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-aspects</artifactId>
	<version>4.3.7.RELEASE</version>
</dependency>

spring-aspects-4.3.7.RELEASE.jar
aspectjweaver-1.8.9.jar 

Spring单元测试模块:
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-test</artifactId>
	<version>4.3.7.RELEASE</version>
	<scope>test</scope>
</dependency>

spring-test-4.3.7.RELEASE.jar

mybatis模块:
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis</artifactId>
	<version>3.4.2</version>
</dependency>

mybatis-3.4.2.jar

mybatis逆向工程:
<dependency>
	<groupId>org.mybatis.generator</groupId>
	<artifactId>mybatis-generator-core</artifactId>
	<version>1.3.5</version>
</dependency>

mybatis-generator-core-1.3.5.jar

引入pagehelper分页插件: 
<dependency>
	<groupId>com.github.pagehelper</groupId>
	<artifactId>pagehelper</artifactId>
	<version>5.1.2</version>
</dependency>

pagehelper-5.1.2.jar
jsqlparser-1.0.jar 

controller返回JSON字符串的支持:
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.6</version>
</dependency>

jackson-databind-2.9.6.jar 
jackson-annotations-2.9.0.jar
jackson-core-2.9.6.jar
	
mybatis整合spring的适配包:
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis-spring</artifactId>
	<version>1.3.1</version>
</dependency>

mybatis-spring-1.3.1.jar

数据库连接池mysql驱动:
<dependency>
	<groupId>c3p0</groupId>
	<artifactId>c3p0</artifactId>
	<version>0.9.1.2</version>
</dependency>
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>5.1.41</version>
</dependency>		

c3p0-0.9.1.2.jar
mysql-connector-java-5.1.41.jar

其他:
<dependency>
	<groupId>jstl</groupId>
	<artifactId>jstl</artifactId>
	<version>1.2</version>
</dependency>
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>javax.servlet-api</artifactId>
	<version>3.1.0</version>
	<scope>provided</scope>
</dependency>
<dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
	<version>4.12</version>
	<scope>test</scope>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.8</version>
</dependency>

jstl-1.2.jar
javax.servlet-api-3.1.0.jar
juint4.12.jar 
jackson-databind-2.9.6.jar 返回JSON字符串的支持

<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>5.2.4.Final</version>
</dependency>

hibernate-validator-5.2.4.Final.jar     JSR303后端校验
validation-api-1.1.0.Final.jar      validation验证注解
jboss-logging-3.2.1.Final.jar
classmate-1.1.0.jar
```
	
#### 引入Bootstrap前端框架    
下载用于生产环境的[Bootstrap](https://getbootstrap.com/docs/3.3/),解压后得到bootstrap-3.3.7-dist文件夹，使用方法参考[官方文档](https://v3.bootcss.com/getting-started/#template)  
在项目的webapp目录下新建static文件夹,将bootstrap-3.3.7-dist文件夹放置在它下面，在static文件夹下新建css、js、img文件夹，用于存放其他网页文件  
下载[jquery](http://jquery.com/),将其解压得到的jquery-3.3.1.min.js放置在static/js文件夹中  
在webapp目录下新建index.jsp，在head标签中引入，注意jquery需要在最上面  
```xml
<!-- 引入jquery -->
<script type="text/javascript" src="static/js/jquery-3.3.1.min.js"></script>
<!-- 引入bootstrop样式 -->
<link rel="stylesheet" href="static/bootstrap-3.3.7-dist/css/bootstrap.min.css">
<script src="static/bootstrap-3.3.7-dist/js/bootstrap.min.js"></script>
```
  
#### 编写SSM整合的XML文件    
##### 编写web.xml  
让项目启动时就启动**Spring容器**，这时候就需要一个`contextConfigLocation`(使用快捷键`Alt+/`根据提示生成)，classpath指定配置文件路径  
```xml
<!-- needed for ContextLoaderListener -->
<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>classpath:applicationContext.xml</param-value>
</context-param>

<!-- Bootstraps the root web application context before servlet initialization -->
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```
  
这时候需要在src/main/resources创建出applicationContext.xml，如果我们没有模板  
> 需要先查看Eclipse的版本号：Help ->About Eclipse  
> 再去Spring[插件官网](http://spring.io/tools/sts/all)复制与Eclipse版本相对应的Update Sites
网址  
> 在Eclipse的Help -> Install New Software..中Add的Work  with地址，名字随便，地址填写上面复制的Update Sites网址  
> 勾选四个带/Spring IDE的复选框,finsh完成,静待安装完成,重启Eclipse  
  
创建applicationContext.xml可以右击选择New -> Other -> Spring Bean Configuration File;创建完回到web.xml继续进行配置  
  
配置**SpringMVC的前端控制器**,用于拦截所有请求,classpath指定SpringMVC配置文件路径，由于我的spring-mvc.xml在src/main/resources下，所以需要指定，如果不想使用init-param标签指定配置文件路径，则需要在web.xml的同级别目录(例如：webapp)下创建一个和`org.springframework.web.servlet.DispatcherServlet`同名的`springDispatcherServlet-servlet.xml`；需要拦截所有请求,把url-pattern设置为`/`  
```xml
<!-- The front controller of this Spring Web application, responsible for handling all application requests -->
<servlet>
	<servlet-name>springDispatcherServlet</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	<init-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:spring-mvc.xml</param-value>
	</init-param>
	<load-on-startup>1</load-on-startup>
</servlet>

<!-- Map all requests to the DispatcherServlet for handling -->
<servlet-mapping>
	<servlet-name>springDispatcherServlet</servlet-name>
	<url-pattern>/</url-pattern>
</servlet-mapping>
```
这里我们也先在src/main/resources创建Spring Bean Configuration File文件spring-mvc.xml  
  
配完SpringMVC，那么顺便把它所带的**字符编码过滤器**也一并配置了，init-param在初始化响应的时候指定encoding类型为utf-8，默认是false，在Spring3.0版本只有一个forceEncoding，由于我们用的4.3版本，需要配置forceRequestEncoding和forceReponseEncoding；/*拦截所有请求  
> `<filter-class>`路径可以通过`Ctrl+Shift+T`查找`CharacterEncodingFilter`获得相关类信息   
  
```xml
<filter>
	<filter-name>CharacterEncodingFilter</filter-name>
	<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
	<init-param>
		<param-name>encoding</param-name>
		<param-value>utf-8</param-value>
	</init-param>
	<init-param>
		<param-name>forceRequestEncoding</param-name>
   		<param-value>true</param-value>
	</init-param>
	<init-param>
		<param-name>forceReponseEncoding</param-name>
		<param-value>true</param-value>
	</init-param>
	<!-- <init-param>
		<param-name>forceEncoding</param-name>
   		<param-value>true</param-value>
	</init-param> -->
</filter>
<filter-mapping>
	<filter-name>CharacterEncodingFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```
  
因为我们要使用REST风格的URI,将页面普通的post请求转为指定的delete或者put请求，所以还需要一个过滤器  
<span style="color:red;">注意:当我们有多个过滤器的时候，字符编码过滤器一定要在所有过滤器之前</span>
```xml
<!-- 4、使用REST风格的URI，将页面普通的post请求转为指定的delete或者put请求 -->
<filter>
	<filter-name>HiddenHttpMethodFilter</filter-name>
	<filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>HiddenHttpMethodFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>

<!-- REST风格的PUT请求转换过滤器(可选) -->
<filter>
	<filter-name>HttpPutFormContentFilter</filter-name>
	<filter-class>org.springframework.web.filter.HttpPutFormContentFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>HttpPutFormContentFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```
  			
##### 编写spring-mvc.xml  
SpringMVC的配置文件主要负责网站的跳转逻辑配置  
打开spring-mvc.xml，在下方的Namespace视图下，勾选beans、context、mvc组件，回到Source视图  
我们需要扫描所有包，首先，在src/main/java下创建目录结构，结构如上图所示，包括dao、controller、pojo、service、test、tool等  
由于她是SpringMVC组件，我们不需要它扫描所有包，只需要扫描控制器Controller，所以通过`<context:include-filter/>`指定，所有带@Controller注解的就会被标识为控制器，被扫描进去  
使用`use-default-filters="false"`禁用默认扫描规则(默认扫描全部，包括@Controller)  
```xml
<context:component-scan base-package="cn.teaper.www" use-default-filters="false">
	<context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```
   
配置图解析器，方便页面返回，这里我们把所有JSP文件存放在/WEB-INF/views/文件夹下，这里需要手动创建文件夹views  
prefix指定视图文件所在路径，路径结尾必须有`/`  
suffix指定视图文件是以.jsp为后缀的文件  
```xml
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<property name="prefix" value="/WEB-INF/views/" />
	<property name="suffix" value=".jsp" />
</bean>
```
解析器配置完还需要**俩个标准**配置  
`default-servlet-handler`将spring不能处理的请求交给tomcat；它会像一个检查员，对进入`springDispatcherServlet`的URI进行筛查，如果发现是静态资源的请求，就将该请求转由Web应用服务器默认的Servlet处理，如果不是静态资源的请求，才由DispatcherServlet继续处理  
`annotation-driven`能支持Springmvc更高级的一些功能,例如:JSR303校验，ajax的映射动态请求   
```xml
<mvc:default-servlet-handler/>
<mvc:annotation-driven/>
```
   
##### 编写applicationContext.xml  
applicationContext.xml是Spring的核心配置文件，主要配置和业务逻辑有关系的，例如数据源，事务控制等  
打开applicationContext.xml，在下方的Namespace视图下，勾选aop、beans、context、tx组件，回到Source视图  
配置**数据源**，我们用的是c3p0，数据库连接信息来自于properties文件，所以我们需要在src/main/resources下创建database.properties，为了和其他配置信息区别开来，我们加上`jdbc.`前缀，结果如下  
```
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://127.0.0.1:3306/ssm?useUnicode=true&characterEncoding=utf-8
jdbc.user=root
jdbc.password=123
```
数据库我们使用MySQL，那么我们需要在MySQL数据库中创建一个叫ssm的database数据库，再创建两张表emp(员工表)和dept(部门表)   
  
**<center>dept(部门表)</center>**  
  
| 列名 | 数据类型 | 长度 | 非空？ | 主键？ | 自增？ | 注释 |
| ------ | ------ | ------ | ------ | ------| ------ | ------ |
| dept_id | int | 11 | yes | yes | yes | 部门编号 |
| dept_name | varchar | 255 | yes |  |  | 部门名称 |

**<center>emp(员工表)</center>**  
  
| 列名 | 数据类型 | 长度 | 非空？ | 主键？ | 自增？ | 注释 |
| ------ | ------ | ------ | ------ | ------| ------ | ------ |
| emp_id | int | 11 | yes | yes | yes | 员工编号 |
| emp_name | varchar | 255 | yes |  |  | 员工姓名 |
| gender | char | 1 |  |  |  | 性别M/F |
| email | varchar | 255 |  |  |  | 邮箱 |
| fk_dept_id | int | 11 |  |  |  | 部门编号 |

  	
要使用properties中的信息，就需要使用context标签读取database.properties文件，使用bean配置数据源  
```xml
<context:property-placeholder location="classpath:database.properties" />
<bean id="comboPooledDataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
	<property name="jdbcUrl" value="${jdbc.url}" />
	<property name="driverClass" value="${jdbc.driver}" />
	<property name="user" value="${jdbc.user}" />
	<property name="password" value="${jdbc.password}" />
</bean>
```
   	
数据源配置好了，那么我们所需要的**业务逻辑组件**pojo也需要被扫描进来，它和SpringMVC不同的是，SpringMVC只扫描控制器controller，它通过`<context:exclude-filter/>`扫描除controller之外的所有  
```xml
<context:component-scan base-package="cn.teaper.www">
	<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```
   
**配置和Myatis的整合**，配置一个sqlSessionFactory工厂，dataSource指定使用我们上面配置好的id为comboPooledDataSource的数据源；configLocation指定mybatis全局文件的位置；mapperLocations指定mybatis的mapper文件位置  
```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="comboPooledDataSource" />
	<property name="configLocation" value="classpath:mybatis-config.xml" />
	<property name="mapperLocations" value="classpath:mapper/*.xml" />
</bean>
```
这里我们需要在src/main/resources下新建mybatis-config.xml，创建方式可以通过配置DTD文件获得文件模板  
> 下载[mybatis-3-config.dtd](http://mybatis.org/dtd/mybatis-3-config.dtd)和[mybatis-3-mapper.dtd](http://mybatis.org/dtd/mybatis-3-mapper.dtd)文件  
> 然后打开window -> preferences下搜索xml catalog选项卡  
> 点击Add按钮添加dtd文件路径，Key的话，分情况填  
> mybatis-3-config.dtd的Key:`-//mybatis.org//DTD Config 3.0//EN`  
> mybatis-3-mapper.dtd的Key:`-//mybatis.org//DTD Mapper 3.0//EN`  
  
其次需要在src/main/resources下新建一个mapper文件夹，用于存放所有的Mapper.xml  
    
**配置扫描器**，将mybatis的dao接口实现加入到IOC容器中  
```xml
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	<property name="basePackage" value="cn.teaper.www.dao" />
</bean>
```
   
配置**事务管理器**DataSourceTransactionManager,负责数据源中的开启关闭回滚操作  
```xml
<bean id="dataSourceTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<!-- 指定被控制的数据源 -->
	<property name="dataSource" ref="comboPooledDataSource" />
</bean>
```
  
事务管理器配置好了,我们就可以开启基于注解的事务(常用)和使用XML配置形式的事务(重要)  
aop:pointcut定义切入点表达式：`..`表示包下以及其子包都可以切入；`(..)`表示方法内参数任意多也行  
`<aop:advisor>`配置事务增强(事务如何切入);引用下方`<tx:advice>`的id；`<pointcut-ref>`引用上方`<aop:pointcut>`的id  
`<tx:advice>`中的`*`表示所有方法都是事务方法，`get*`表示以get开始的所有方法，get方法假设都为查询，使用`read-only="true"`指定只读做些许优化，`transaction-manager`属性指定切入的事务管理器id    
其中`<aop:config/>`定义切入点文件,`<tx:advice/>`对切入点文件中的方法进行增强,transaction-manager指定切入数据的来源  
```xml
<aop:config>
	<aop:pointcut expression="execution(* cn.teaper.www.service..*(..))" id="pointcut" />
	<aop:advisor advice-ref="txAdvice" pointcut-ref="pointcut" />
</aop:config>
<tx:advice id="txAdvice" transaction-manager="dataSourceTransactionManager">
	<tx:attributes>
		<tx:method name="*"/>
		<tx:method name="get*" read-only="true" />
	</tx:attributes>
</tx:advice>
```

##### 编写mybatis-config.xml  
在全局configuration标签中配置一些冷门的MyBatis的设置`<settings>`  
```xml
<!-- 配置驼峰命名风格,value="true"开启,这样我们使用MyBatis逆向工程工具的时候就可以生成以驼峰命名风格的bean和Mapper -->
<settings>
	<setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>	
<!-- 配置所有bean来源 -->
<typeAliases>
	<package name="cn.teaper.www.pojo" />
</typeAliases>
```


#### MyBatis逆向工程工具    
使用[mybatis-generator](http://www.mybatis.org/generator/index.html)逆向生成bean和Mapper,第一步pom.xml中引入jar包`mybatis-generator-core-1.3.5.jar`
```xml
<dependency>
	<groupId>org.mybatis.generator</groupId>
	<artifactId>mybatis-generator-core</artifactId>
	<version>1.3.5</version>
</dependency>
```
  
编写在项目根目录创建mbg.xml,模板[参考](http://www.mybatis.org/generator/configreference/xmlconfig.html)  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
  PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
  "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
  <context id="DB2Tables" targetRuntime="MyBatis3">
  	<!-- 不生成注释 -->
 	<commentGenerator>
	  <property name ="suppressAllComments" value ="true"/>
	</commentGenerator>
  	<!-- 配置数据库连接信息 -->
	<jdbcConnection driverClass="com.mysql.jdbc.Driver"
		connectionURL="jdbc:mysql://127.0.0.1:3306/ssm?useSSL=true"
		userId="root"
		password="fvh2014">
	</jdbcConnection>

	<!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和 NUMERIC 类型解析为java.math.BigDecimal -->
	<javaTypeResolver >
	  <property name="forceBigDecimals" value="false" />
	</javaTypeResolver>

	<!-- 指定javabean生成的位置:targetProject=”.\src” 适用于windows;targetProject=”./src”适用于linux -->
	<javaModelGenerator targetPackage="cn.teaper.pojo" targetProject="./src/main/java">
	  <property name="enableSubPackages" value="true" />
	  <property name="trimStrings" value="true" />
	</javaModelGenerator>

	<!-- 指定SQl映射文件(mapper.xml)生成的位置 -->
	<sqlMapGenerator targetPackage="mapper"  targetProject="./src/main/resources">
	  <property name="enableSubPackages" value="true" />
	</sqlMapGenerator>

	<!-- 指定dao接口生成的位置(mapper接口) -->
	<javaClientGenerator type="XMLMAPPER" targetPackage="cn.teaper.dao"  targetProject="./src/main/java">
	  <property name="enableSubPackages" value="true" />
	</javaClientGenerator>

	<!-- table指定每个表的生成策略 -->
	<table tableName="emp" domainObjectName="Emp" ></table>
	<table tableName="dept" domainObjectName="Dept" ></table>
  </context>
</generatorConfiguration>
```
  
在tools包下创建测试的Java代码MBG.java,内容[参考](http://www.mybatis.org/generator/running/runningWithJava.html);运行成功后刷新项目,查看目录下是否自动生成了想要的bean和Mapper  
```java
public class MBG {
	public static void main(String[] args) throws Exception {
		List<String> warnings = new ArrayList<String>();
		boolean overwrite = true;
		File configFile = new File("mbg.xml");
		ConfigurationParser cp = new ConfigurationParser(warnings);
		Configuration config = cp.parseConfiguration(configFile);
		DefaultShellCallback callback = new DefaultShellCallback(overwrite);
		MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
		myBatisGenerator.generate(null);
	}
}
```
<span style="color:#ff0000;">注意：MyBatis逆向工程生成的Mapper还不能完全使用，缺少表连接查询，所以需要进一步修改；pojo中除了普通javabean，会有复杂的javaBeanExample.java文件，如果你不需要可以删除</span>  
  
#### 修改生成的pojo和Mapper信息  
由于逆向工程生成的SQL缺少联合表的CRUD操作，所以需要在其基础上稍作添加  
```java
//EmpMapper.java新增两个查询
int insertSelectiveWithDept(Emp record);
Emp selectByPrimaryKeyWithDept(Integer empId);
```
```java
//新增dept对象，希望查询员工的时候顺便查询其部门信息
private Dept dept;
public Dept getDept() {
	return dept;
}
public void setDept(Dept dept) {
	this.dept = dept;
}
```
```xml
<!-- 查询员工带部门信息的resultMap -->
<resultMap id="WithDeptResultMap" type="cn.teaper.www.pojo.Emp">
    <id column="emp_id" jdbcType="INTEGER" property="empId" />
    <result column="emp_name" jdbcType="VARCHAR" property="empName" />
    <result column="gender" jdbcType="CHAR" property="gender" />
    <result column="email" jdbcType="VARCHAR" property="email" />
    <result column="fk_dept_id" jdbcType="INTEGER" property="fkDeptId" />
    <!-- association指定联合查询部门字段的封装 -->
    <association property="dept" javaType="cn.teaper.www.pojo.Dept">
    	<id column="dept_id" property="deptId"/>
    	<result column="dept_name" property="deptName"/>
    </association>
</resultMap>

<!--查询员工带部门信息的SQL-->
<sql id="WithDept_Column_List">
    e.emp_id, e.emp_name, e.gender, e.email, e.fk_dept_id , d.dept_id , d.dept_name
</sql>

 <!-- 查询员工带部门信息的两个方法
  int insertSelectiveWithDept(Emp record);
  Emp selectByPrimaryKeyWithDept(Integer empId); -->
<select id="selectByExampleWithDept" parameterType="cn.teaper.www.pojo.EmpExample" resultMap="BaseResultMap">
    select
    <if test="distinct">
      distinct
    </if>
    <!--指定上方查询的信息，SQL修改成左连接查询-->
    <include refid="WithDept_Column_List" />
    FROM emp e 
    LEFT JOIN dept d ON e.fk_dept_id=d.dept_id
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
    <if test="orderByClause != null">
      order by ${orderByClause}
    </if>
</select>

<select id="selectByPrimaryKeyWithDept" parameterType="java.lang.Integer" resultMap="WithDeptResultMap">
    select 
    <include refid="WithDept_Column_List" />
    FROM emp e 
    LEFT JOIN dept d ON e.fk_dept_id=d.dept_id 
    where emp_id = #{empId,jdbcType=INTEGER}
</select>
```
#### 测试Mapper接口  
在`cn.teaper.www.test`中创建一个测试类`mapperTest.java`测试Dao层的工作  
  
由于我们的是Spring项目，所以这里推荐使用Spring单元测试，可以自动注入我们需要的组件，使用Spring单元测步骤如下：
```java
/**
 * 测试Dao层的工作
 * @author teaper
 *
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations={"classpath:applicationContext.xml"})
public class mapperTest {
	@Autowired
	DeptMapper deptMapper;
	/**
	 * 测试DeptMapper
	 * Spring项目推荐使用Spring单元测试，可以自动注入我们需要的组件
	 * 1、在pom.xml导入SpringTest的模块spring-test
	 * 2、测试类上添加@ContextConfiguration注解指定Spring配置文件的位置，帮我们创建出IOC容器；该注解有个属性locations指定配置文件
	 * 3、测试类上添加Juint的@RunWith注解指定使用哪个单元测试用例  
	 * 4、直接使用@Autowired要使用的bean
	 */
	@Test
	public void testDao() {
		//1、创建SpringIOC容器
		//ApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
		//2、从容器中获取Mapper
		//DeptMapper bean = ioc.getBean(DeptMapper.class);
		
		System.out.println(deptMapper);
	}
}
```
使用Juint Test运行mapperTest()方法没有报错，并且输出黑色的**org.apache.ibatis.binding.MapperProxy@26adfd2d**就说明正常，通过上面4步已经测试Dao层没有问题了，那么就可以插入一些数据来测试具体的CRUD方法  
  
测试插入部门功能insertSelective(Dept record)，需要入参一个dept对象，这里需要先去Dept.java添加两个构造方法  
```java
//为dept生成两个构造方法
public Dept(Integer deptId, String deptName) {
	super();
	this.deptId = deptId;
	this.deptName = deptName;
}
public Dept() {
	super();
}
```
然后在mapperTest.java的testDao()方法中追加两行命令，运行之后有添加进去就OK啦  
```java
//5、插入几个部门
deptMapper.insertSelective(new Dept(null,"公关部"));
deptMapper.insertSelective(new Dept(null,"教职部"));
```
同理，插入员工也是添加构造方法，调用empMapper的insertSelective()方法；为了后面方便有数据分页，我们使用可以批量操作的SqlSession**批量插入1000条数据**  

首先在applicationContext.xml中配置一个可以执行批量操作的SqlSession，创建一个sqlSession的bean，引入之前配置好的sqlSessionFactory，MyBatis执行器类型`executorType`设置为`BATCH`  
> MyBatis执行器类型如下：  
> SIMPLE：这个类型不做特殊的事情，它只为每个语句创建一个PreparedStatement  
> REUSE：这种类型将重复使用PreparedStatements  
> BATCH：这个类型批量更新，且必要地区别开其中的select 语句，确保动作易于理解  
  
```xml
<!-- 配置一个可以执行批量操作的SqlSession -->
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
	<constructor-arg name="sqlSessionFactory" ref="sqlSessionFactory"></constructor-arg>
	<constructor-arg name="executorType" value="BATCH"></constructor-arg>
</bean>
```
在testDao.java中@Autowired一个SqlSession对象，然后在之前的testDao()方法中追加以下内容  
使用javaJDK提供的UUID.randomUUID().toString()方法自动生成主键，截取前5个字符拼接变量`i`,将其拼接到insertSelective()入参中  
```java
//7、使用可以执行批量操作的SQLSession批量插入多个员工
EmpMapper empMapper = sqlSession.getMapper(EmpMapper.class);
for (int i = 0; i < 1000; i++) {
	String uid = UUID.randomUUID().toString().substring(0,5)+i;
	empMapper.insertSelective(new Emp(null,uid,"M",uid+"@teaper.cn",1));
}
```
#### 分页插件PageHelper  
[PageHelper](https://github.com/pagehelper/Mybatis-PageHelper)是Mybatis通用分页插件，首先在pom.xml中引入相关最新jar包，它包含了`pagehelper-5.1.2.jar`和`jsqlparser-1.0.jar`包  
```xml
<!-- 引入pagehelper分页插件 
	pagehelper-5.1.2.jar
	jsqlparser-1.0.jar
	-->
<dependency>
	<groupId>com.github.pagehelper</groupId>
	<artifactId>pagehelper</artifactId>
	<version>5.1.2</version>
</dependency>
```
然后在`mybatis-config.xml`中配置拦截器插件  
```xml
<!-- 
    plugins在配置文件中的位置必须符合要求，否则会报错，顺序如下:
    properties?, settings?, 
    typeAliases?, typeHandlers?, 
    objectFactory?,objectWrapperFactory?, 
    plugins?, 
    environments?, databaseIdProvider?, mappers?
-->
<plugins>
    <!-- com.github.pagehelper为PageHelper类所在包名 -->
    <plugin interceptor="com.github.pagehelper.PageInterceptor">
    
	</plugin>
</plugins>
```
然后就可以在Controller.java中调用`PageHelper.startPage(int pageNum, int pageSize);`方法；将startPage后面紧跟的查询`empService.getAll();`定义为一个分页查询  
分页查询的结果以及[分页参数](https://github.com/pagehelper/Mybatis-PageHelper/blob/master/wikis/zh/HowToUse.md)，可以使用PageInfo对象接收，然后使用model将其返回到前端页面   
```java
@RequestMapping("/emps")
public String getEmps(@RequestParam(value="pn",defaultValue="1")Integer pn,Model model) {
	//这不是一个分页查询
	//引入pageHelper分页插件
	//在查询之前只需要调用PageHelper.startPage(int pageNum, int pageSize);方法
	PageHelper.startPage(pn, 5);
	//startPage后面紧跟的查询empService.getAll();就是一个分页查询
	List<Emp> emps = empService.getAll();
	//因为PageInfo包含了非常全面的分页属性以及查询出来的数据，使用PageInfo包装查询结果后，就只需要讲PageInfo交给页面即可
	//传入要连续显示的页数5
	PageInfo page = new PageInfo(emps,5);
	model.addAttribute("pageInfo",page);
	return "list";
}
```
为了保险期间，我们需要事先测试下数据，这里使用Spring的单元测试模块**测试MVC**  
在test包下新建一个`MvcTest.java`文件，基本配置和上面的[测试Mapper接口](#测试mapper接口)过程是一样的，不过`@ContextConfiguration`中只有`applicationContext.xml`是不够的，还需要加入`spring-mvc.xml`  
```bash
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations={"classpath:applicationContext.xml","classpath:spring-mvc.xml"})
```
然后在MvcTest中创建一个`MockMvc`对象来虚拟MVC请求，获取处理结果；使用`MockMvc`对象之前还需要使用`MockMvcBuilders.webAppContextSetup().build()`方法初始化它，并且传入SpringMVC的ioc容器`WebApplicationContext`，SpringMVC容器里边的东西我们可以`@Autowired`自动装配，但是IOC容器自己需要在Class上使用`@WebAppConfiguration`才能装配进去；`initMokcMvc()`初始化方法我们每次都要使用，所以使用Juint的`@Before`注解标记它
```java
@RunWith(SpringJUnit4ClassRunner.class)
@WebAppConfiguration
@ContextConfiguration(locations={"classpath:applicationContext.xml","classpath:spring-mvc.xml"})
public class MvcTest {
	//传入SpringMVC的IOC
	@Autowired
	WebApplicationContext context;
	//虚拟MVC请求，获取处理结果
	MockMvc mockMvc;
	
	@Before
	public void initMokcMvc() {
		mockMvc = MockMvcBuilders.webAppContextSetup(context).build();
	}
}
```
然后我们就可以在class中编写测试方法`testPage()`了  
使用MockMvc的`.perform().andReturn()`方法拿到返回值，传入一个requestBuilder对象，这里我们可以使用`MockMvcRequestBuilders`模拟一个get/post请求(这里.get()是get请求)，`.param()`传入请求参数页面大小和页码，把该抛出异常抛出去  
请求成功以后，请求域中会有PageInfo，我们可以取出PageInfo中的数据，使用`result.getRequest()`拿到请求对象
> 这里安利一个快捷键`Ctrl+1 Enter`或者`Ctrl+2 L`可以自动可自动补全代码
  
使用`equest.getAttribute()`获取PageInfo对象，打印PageInfo中的内容，查询数据在PageInfo的`.getList()`方法中可以获取到  
```java
@Test
public void testPage() throws Exception {
	//模拟请求拿到返回值
	MvcResult result = mockMvc.perform(MockMvcRequestBuilders.get("/emps").param("pn", "2")).andReturn();
	//请求成功以后，请求域中会有PageInfo，我们可以取出PageInfo进行验证
	MockHttpServletRequest request= result.getRequest();
	PageInfo pi = (PageInfo) request.getAttribute("pageInfo");
	System.out.println("当前页码"+pi.getPageNum());
	System.out.println("总页码"+pi.getPages());
	System.out.println("总记录数"+pi.getTotal());
	System.out.println("在页面需要连续显示的页码");
	int[] nums = pi.getNavigatepageNums();
	for(int i : nums) {
		System.out.println(""+i);
	}
	
	//获取员工数据
	List<Emp> list = pi.getList();
	for(Emp emp : list) {
		System.out.println("ID:"+emp.getEmpId()+"==> NAME:"+emp.getEmpName());
	}
}
```
<span style="color:#ff0000;">注意：如果运行后报`java.lang.NoClass.DefFoundError:javax/servlet/SessionCookieConfig`错误，`javax/servlet`下找不到`SessionCookieConfig`这个类，原因是Spring4测试的时候需要Servlet3.0的支持，我们在pom中引入`javax.servlet-api`3.0以上版本的jar包</span>  
```xml
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>javax.servlet-api</artifactId>
	<version>3.1.0</version>
	<scope>provided</scope>
</dependency>
```
#### 页面数据展示  
项目运行后，首先访问`index.jsp`，然后`index.jsp`发送一个`/emps`请求给controller控制器  
```xml
<jsp:forward page="/emps"></jsp:forward>
```
controller控制器处理`/emps`请求，查到数据，来到`list.jsp`页面展示，所以我们直接创建一个`list.jsp`页面  
要使用Bootstrap需要引入相关样式和js，还有JQuery的js(Jquery.js必须在所有js之前)
```xml
<!-- 引入jQuery -->
<script type="text/javascript" src="static/js/jquery-3.3.1.min.js"></script>
<!-- 引入bootstrop样式 -->
<link rel="stylesheet" href="static/bootstrap-3.3.7-dist/css/bootstrap.min.css">
<script src="static/bootstrap-3.3.7-dist/js/bootstrap.min.js"></script>
```
> 安利一下路径问题：  
> 不以`/`开始的相对路径，找资源，以当前资源的路径为基准  
> 推荐使用以`/`开始的相对路径，找资源，以服务器的路径(`https://localhost:3306`)为基准，需要加上项目名，完整路径应该是(`https://localhost:3306/ssm/index.jsp`)  
  
当然服务器的路径不是写死的，所以可以直接jsp使用小脚本方式获取服务器路径  
```xml
<%
	pageContext.setAttribute("APP_PATH",request.getContextPath());
%>
```
然后使用`APP_PATH`替换文件路径中的服务器路径，**替换结果**如下：  
```xml
<!-- 引入jQuery -->
<script type="text/javascript" src="${APP_PATH}/static/js/jquery-3.3.1.min.js"></script>
<!-- 引入bootstrop样式 -->
<link rel="stylesheet" href="${APP_PATH}/static/bootstrap-3.3.7-dist/css/bootstrap.min.css">
<script src="${APP_PATH}/static/bootstrap-3.3.7-dist/js/bootstrap.min.js"></script>
```
接下来就可以借助bootstrap的[CSS样式](https://getbootstrap.com/docs/3.3/css/)和[组件](https://getbootstrap.com/docs/3.3/components/)编写list.jsp页面  
```html
<body>
	<!-- 搭建页面信息 -->
	<div class="container">
		<!-- 标题 -->
		<div class="row">
			<div class="col-md-12">
				<h1>Spring+SpringMVC+MyBatis框架整合</h1>
			</div>
		</div>
		<!-- 按钮 -->
		<div class="row">
			<div class="col-md-4 col-md-offset-8">
				<button type="button" class="btn btn-primary btn-sm">新增</button>
				<button type="button" class="btn btn-danger btn-sm">删除</button>
			</div>
		</div>
		<!-- 表格数据 -->
		<div class="row">
			<div class="col-md-12">
				<table class="table table-hover">
					<tr class="info">
						<th>#</th>
						<th>员工姓名(empName)</th>
						<th>性别(gender)</th>
						<th>邮箱(email)</th>
						<th>所在部门(deptName)</th>
						<th>操作</th>
					</tr>
					<tr>
						<td>1</td>
						<td>卜衔之</td>
						<td>F</td>
						<td>www@teaper.cn</td>
						<td>开发部</td>
						<td>
							<button type="button" class="btn btn-primary btn-sm">
								<span class="glyphicon glyphicon-pencil" aria-hidden="true"></span>编辑
							</button>
							<button type="button" class="btn btn-danger btn-sm">
								<span class="glyphicon glyphicon-trash" aria-hidden="true"></span>删除
							</button>
						</td>
					</tr>
				</table>
			</div>
		</div>
		<!-- 分页 -->
		<div class="row">
			<!-- 分页文字信息 -->
			<div class="col-md-6">当前记录数：×××</div>
			<!-- 分页条信息 -->
			<div class="col-md-6">
				<nav aria-label="Page navigation">
				  <ul class="pagination">
				  	<li><a href="#">首页</a></li>
				    <li>
				      <a href="#" aria-label="Previous">
				        <span aria-hidden="true">&laquo;</span>
				      </a>
				    </li>
				    <li><a href="#">1</a></li>
				    <li><a href="#">2</a></li>
				    <li><a href="#">3</a></li>
				    <li><a href="#">4</a></li>
				    <li><a href="#">5</a></li>
				    <li>
				      <a href="#" aria-label="Next">
				        <span aria-hidden="true">&raquo;</span>
				      </a>
				    </li>
				    <li><a href="#">末页</a></li>
				  </ul>
				</nav>
			</div>
		</div>
	
	</div>

</body>
```
![](http://ww1.sinaimg.cn/large/006kWbIoly1g2kew01il2j30wf07vmyi.jpg)  
前端页面写好了，我们需要把请求的数据解析出来，遍历在`<tr>`标签中，需要引入jstl和EL表达式  
```java
<%@taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
```
<span style="color:#ff0000;">注意：要使用jstl需要在pom.xml中引入相关jar文件</span>  
```xml
<dependency>
	<groupId>jstl</groupId>
	<artifactId>jstl</artifactId>
	<version>1.2</version>
</dependency>
```
那么遍历部门信息在列表里，之前的  
```html
<tr>
	<td>1</td>
	<td>卜衔之</td>
	<td>F</td>
	<td>www@teaper.cn</td>
	<td>开发部</td>
	<td>
		<button type="button" class="btn btn-primary btn-sm">
			<span class="glyphicon glyphicon-pencil" aria-hidden="true"></span>编辑
		</button>
		<button type="button" class="btn btn-danger btn-sm">
			<span class="glyphicon glyphicon-trash" aria-hidden="true"></span>删除
		</button>
	</td>
</tr>
```
就要修改成  
```html
<c:forEach items="${pageInfo.list }" var="emp">
	<tr>
		<td>${emp.empId }</td>
		<td>${emp.empName }</td>
		<td>${emp.gender=="M"?"女":"男" }</td>
		<td>${emp.email }</td>
		<td>${emp.dept.deptName }</td>
		<td>
			<button type="button" class="btn btn-primary btn-sm">
				<span class="glyphicon glyphicon-pencil" aria-hidden="true"></span>编辑
			</button>
			<button type="button" class="btn btn-danger btn-sm">
				<span class="glyphicon glyphicon-trash" aria-hidden="true"></span>删除
			</button>
		</td>
	</tr>
</c:forEach>
```
分页信息(来源pageInfo)修改为  
```html
<!-- 分页文字信息 -->
<div class="col-md-6">总${pageInfo.pages}页 当前第${pageInfo.pageNum}页 总${pageInfo.total}条数据</div>
```
分页条连续显示的页码(pageInfo.navigatepageNums)修改为  
```html
<li><a href="${APP_PATH }/emps?pn=1">首页</a></li>
  <c:if test="${pageInfo.hasPreviousPage ==true}">
  	<li>
      <a href="${APP_PATH }/emps?pn=${pageInfo.pageNum-1}" aria-label="Previous">
        <span aria-hidden="true">&laquo;</span>
      </a>
    </li>
  </c:if>
<c:forEach items="${pageInfo.navigatepageNums }" var="page_Num">
	<c:if test="${page_Num == pageInfo.pageNum }">
		<li class="active"><a href="#">${page_Num }</a></li>
	</c:if>
	<c:if test="${page_Num != pageInfo.pageNum }">
		<li><a href="${APP_PATH }/emps?pn=${page_Num}">${page_Num }</a></li>
	</c:if>
</c:forEach>
<c:if test="${pageInfo.hasNextPage ==true}">
	<li>
      <a href="${APP_PATH }/emps?pn=${pageInfo.pageNum+1}" aria-label="Next">
        <span aria-hidden="true">&raquo;</span>
      </a>
    </li>
</c:if>
<li><a href="${APP_PATH }/emps?pn=${pageInfo.pages}">末页</a></li>
```
> 点击发送请求方式：${APP_PATH }/emps?pn=1  
> pn：页码  
> hasPreviousPage：是否有上一页  
> hasNextPage：是否有下一页  
> navigatepageNums：所有导航页号  
> pageNum：当前页
  
#### 使用ajax返回json方式修改list.jsp  
注释掉`Empcontroller.java`中的`@RequestMapping("/emps")`，我们重新写过一个返回JSON格式的请求  
> `@ResponseBody`需要在`pom.xml`中导入`jackson-databind`架包  
  
```java
//2、以JSON方式显示查询结果
@RequestMapping("/emps")
@ResponseBody
public PageInfo getEmpsWithJson(@RequestParam(value="pn",defaultValue="1")Integer pn) {
	PageHelper.startPage(pn, 5);
	List<Emp> emps = empService.getAll();
	PageInfo page = new PageInfo(emps,5);
	return page;
}
```
查询方式不变，只不过之前是使用一个`Model`对象返回查询结果，现在只需要加上`@ResponseBody`注解，就可以直接返回`JSON`格式的字符串[jason](http://jason.sourceforge.net/wp/)**(不清楚看下图)**   
  
浏览器打开[http://localhost:8080/ssm/emps?pn=1](http://localhost:8080/ssm/emps?pn=1)后会显示一连串的JSON格式信息(推荐一个谷歌插件[JSON Viewer](https://chrome.google.com/webstore/detail/json-viewer/gbmdgpbipfallnflgajpaliibnhdgobh/related)可以格式化显示)  
![](http://ww1.sinaimg.cn/large/006kWbIogy1g1g5it7e1qj31gv0qadib.jpg)  
  
使用`getEmpsWithJson()`方法直接返回查询结果是OK的  
但是我们Controller中不只有查询方法，还有update,insert,delete操作  
所以，我们除了返回查询结果外还要返回失败或成功的提示消息  
所以在`pojo`包下新建一个`Msg.java`，定义两个静态变量`code`和`msg`以及两个返回成功/失败方法  
为了可以把PageInfo中的查询结果也绑定到Msg对象上，并且后面需要用ajax的date字段(<k,v>类型的),所以需要定义一个<key,value>类型的`add()`方法  
```java
package cn.teaper.www.pojo;

import java.util.HashMap;
import java.util.Map;

/**
 * 通用的update,insert,delete返回执行结果消息的类(执行成功还是失败)
 * @author teaper
 *
 */
public class Msg {
	//状态码：1（成功） 0(失败)
	private int code;
	//提示信息
	private String msg;
	//用户要返回给浏览器的数据  
	private Map<String,Object> extend = new HashMap<String,Object>();
	
	public int getCode() {
		return code;
	}
	public void setCode(int code) {
		this.code = code;
	}
	public String getMsg() {
		return msg;
	}
	public void setMsg(String msg) {
		this.msg = msg;
	}
	public Map<String, Object> getEXtend() {
		return extend;
	}
	public void setEXtend(Map<String, Object> extend) {
		this.extend = extend;
	}
	
	//定义两个成功||失败方法
	public static Msg success() {
		Msg result = new Msg();
		result.setCode(1);
		result.setMsg("处理成功！");
		return result;
	}
	public static Msg fail() {
		Msg result = new Msg();
		result.setCode(0);
		result.setMsg("处理失败！");
		return result;
	}
	
	//定义一个可以快捷绑定pageInfo信息的方法
	public Msg add(String Key,Object value) {
		this.getEXtend().put(Key, value);
		return this;
	}
}
```
修改`getEmpsWithJson()`方法，不返回`PageInfo`对象了，修改成`Msg`对象，return方式也从`return page;`改成`return Msg.success().add("pageInfo",page);`
```java
//2、以JSON方式显示查询结果，@ResponseBody需要在pom.xml中导入jackson-databind架包
@RequestMapping("/emps")
@ResponseBody
public Msg getEmpsWithJson(@RequestParam(value="pn",defaultValue="1")Integer pn) {
	PageHelper.startPage(pn, 5);
	List<Emp> emps = empService.getAll();
	PageInfo page = new PageInfo(emps,5);
	return Msg.success().add("pageInfo",page);
}
```
查看执行结果[http://localhost:8080/ssm/emps?pn=5](http://localhost:8080/ssm/emps?pn=5)如下：  
![](http://ww1.sinaimg.cn/large/006kWbIogy1g1g6tepwdij31gu0qbtb9.jpg)  
已经比之前的JSON多出了`code`、`msg`、`extend`信息  
  
#### 使用JavaScript的DOM对象处理JSON数据，返回到页面  
改造`index.jsp`，为了留下原来的数据，把原来的`index,jsp`重命名为`index-off.jsp`  
重新在`webapp`下创建一个`index.jsp`，把`list.jsp`的内容复制到`index.jsp`中  
把原来以JSTL和EL表达式遍历的`${pageInfo.list}`下的所有数据删除，包括表格数据遍历个分页条显示  
<!--然后在`</body>`上引入一个js文件`index.js`  -->
```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>员工列表</title>
<%
	pageContext.setAttribute("APP_PATH",request.getContextPath());
%>
<!-- 引入jQuery -->
<script type="text/javascript" src="${APP_PATH}/static/js/jquery-3.3.1.min.js"></script>
<!-- 引入bootstrop样式 -->
<link rel="stylesheet" href="${APP_PATH}/static/bootstrap-3.3.7-dist/css/bootstrap.min.css">
<script type="text/javascript" src="${APP_PATH}/static/bootstrap-3.3.7-dist/js/bootstrap.min.js"></script>
</head>
<body>
	<!-- 搭建页面信息 -->
	<div class="container">
		<!-- 标题 -->
		<div class="row">
			<div class="col-md-12">
				<h1>Spring+SpringMVC+MyBatis框架整合</h1>
			</div>
		</div>
		<!-- 按钮 -->
		<div class="row">
			<div class="col-md-4 col-md-offset-8">
				<button type="button" class="btn btn-primary btn-sm">新增</button>
				<button type="button" class="btn btn-danger btn-sm">删除</button>
			</div>
		</div>
		<!-- 表格数据 -->
		<div class="row">
			<div class="col-md-12">
			    <!-- 为了jquery获取对象，这里需要一个id:emps_tables -->
				<table class="table table-hover" id="emps_tables">
					<thead>
						<tr class="info">
							<th>#</th>
							<th>员工姓名(empName)</th>
							<th>性别(gender)</th>
							<th>邮箱(email)</th>
							<th>所在部门(deptName)</th>
							<th>操作</th>
						</tr>
					</thead>
					<tbody>
						<tr class="info">
							
						</tr>
					</tbody>
				</table>
			</div>
		</div>
		<!-- 分页 -->
		<div class="row">
			<!-- 分页文字信息 -->
			<div class="col-md-6" id="page_info_area">总页 当前第页 总条数据</div>
			<!-- 分页条信息 -->
			<div class="col-md-6" id="page_nav_area">
				
			</div>
		</div>
	</div>
	
</body>
</html>
```
接下来在`index.jsp`的`</body>`标签上编写js代码，需要用到Ajax来做异步调用，不会Ajax参照[jQuery API 中文文档](http://jquery.cuishifeng.cn/jQuery.Ajax.html)    
```html
<script type="text/javascript">
//1、页面加载完成以后，直接发送Ajax请求，获取分页数据
$(function(){
	$.ajax({
		url: "${APP_PATH}/emps",
		data: "pn=1",
		type: "GET",
		success: function(result){
			console.log(result);
		}
	});
});

</script>
```
刷新[http://localhost:8080/ssm/](http://localhost:8080/ssm/)页面，在谷歌检索中查看`Network`面板中的请求信息  
![](http://ww1.sinaimg.cn/large/006kWbIogy1g1g8ptifd0j31hc0fumzh.jpg)  
可以看到有一条`emps?pn=1`的请求，单击之后可以看到请求信息，接下来**解析请求信息**并显示到对应标签下  
本页面有分页信息和表格信息，所以创建三个方法  
> build_emps_table() 解析显示员工数据  
> build_page_info() 解析显示分页信息  
> build_page_nav() 解析显示分页条  
  
```js
//1、页面加载完成以后，直接发送Ajax请求，获取分页数据
$(function(){
	$.ajax({
		url: "${APP_PATH}/emps",
		data: "pn=1",
		type: "GET",
		success: function(result){
			//console.log(result);
			//1、解析显示员工数据 build_emps_table()
			build_emps_table(result);
			//2、解析显示分页信息 build_page_info()
			build_page_info(result);
			//3、解析显示分页条 build_page_nav()
			build_page_nav(result);
		}
	});
});
```
我们先**解析员工信息**  
获取`result.extend.pageInfo.list`下的员工信息赋值给`emps`对象  
使用JQuery提供的遍历方法遍历信息`emps`对象，每次遍历都要绑定一个function事件  
function中定义每条数据的变量，然后用`<td></td>`去接收  
创建按钮标签，一层一层`append`进去，最后交由`<tr></tr>`标签封装好  
`appendTo`定义好的`id`为`emps_tables`的`<table></table>`标签中，然后显示到页面  
```js
function build_emps_table(result){
	var emps = result.extend.pageInfo.list;//所有员工数据
	$.each(emps,function(index,item){
		//alert(item.empName);
		var empIdTd = $("<td></td>").append(item.empId);
		var empNameTd = $("<td></td>").append(item.empName);
		var genderTd = $("<td></td>").append(item.gender=='M'?"女":"男");
		var emailTd = $("<td></td>").append(item.email);
		var deptNameTd = $("<td></td>").append(item.dept.deptName);
		//编辑和删除按钮
		/* <button type="button" class="btn btn-primary btn-sm">
			<span class="glyphicon glyphicon-pencil" aria-hidden="true"></span>编辑
		</button> 
		<button type="button" class="btn btn-danger btn-sm">
			<span class="glyphicon glyphicon-trash" aria-hidden="true"></span>删除
		</button> */
		var editBtn = $("<button></button>").addClass("btn btn-primary btn-sm")
			.append($("<span></span>").addClass("glyphicon glyphicon-pencil")).append("编辑");
		var delBtn = $("<button></button>").addClass("btn btn-danger btn-sm")
			.append($("<span></span>").addClass("glyphicon glyphicon-trash")).append("删除");
		var btnTd = $("<td></td>").append(editBtn).append(" ").append(delBtn);
		//append方法执行完成以后还会返回原来的元素，所以可以一直.append()下去
		$("<tr></tr>").append(empIdTd)
			.append(empNameTd)
			.append(genderTd)
			.append(emailTd)
			.append(deptNameTd)
			.append(btnTd)
			.appendTo("#emps_tables tbody");
	});
}
```
接下来**解析显示分页信息**  
分页数据在`result.extend.pageInfo`下，我们只在在`id`为`page_info_area`的标签下`append`就行了
```
function build_page_info(result){
	$("#page_info_area").append("总"+result.extend.pageInfo.pages+"页 当前第"+result.extend.pageInfo.pageNum+"页 总"+result.extend.pageInfo.total+"条数据")
}
```
构建**导航条信息**  
根据原来的导航条静态HTML构建
```js
function build_page_nav(result){
	/* <nav aria-label="Page navigation">
	  <ul class="pagination">
	  	<li><a href="#">首页</a></li>
	    <li>
	      <a href="#" aria-label="Previous">
	        <span aria-hidden="true">&laquo;</span>
	      </a>
	    </li>
	    <li><a href="#">1</a></li>
	    <li><a href="#">2</a></li>
	    <li><a href="#">3</a></li>
	    <li><a href="#">4</a></li>
	    <li><a href="#">5</a></li>
	    <li>
	      <a href="#" aria-label="Next">
	        <span aria-hidden="true">&raquo;</span>
	      </a>
	    </li>
	    <li><a href="#">末页</a></li>
	  </ul>
	</nav> */
	var ul = $("<ul></ul>").addClass("pagination");
	var firstPageLi = $("<li></li>").append($("<a></a>").append("首页").attr("href","#"));
	var prePageLi = $("<li></li>").append($("<a></a>").append($("<span></span>").append("&laquo;")).attr("href","#"));
	
	var nextPageLi = $("<li></li>").append($("<a></a>").append($("<span></span>").append("&raquo;")).attr("href","#"));
	var lastPageLi = $("<li></li>").append($("<a></a>").append("末页").attr("href","#"));
	ul.append(firstPageLi).append(prePageLi);
	//页码12345,遍历给UL添加页码提示
	$.each(result.extend.pageInfo.navigatepageNums,function(index,item){
		var numLi = $("<li></li>").append($("<a></a>").append(item).attr("href","#"));
		ul.append(numLi);
	});
	ul.append(nextPageLi).append(lastPageLi);
	var navEle = $("<nav></nav>").append(ul).appendTo("#page_nav_area");
}
```
构建好还需要完善一下，例如没有上一页数据，首页和上一页按钮不可点，当前页面添加class属性`active`等，完善后如下：  
```js
//解析显示分页条
function build_page_nav(result){
	var ul = $("<ul></ul>").addClass("pagination");
	var firstPageLi = $("<li></li>").append($("<a></a>").append("首页").attr("href","#"));
	var prePageLi = $("<li></li>").append($("<a></a>").append($("<span></span>").append("&laquo;")).attr("href","#"));
	//如果没有上一页数据，首页和上一页按钮设置不可点属性disabled
	if(result.extend.pageInfo.hasPreviousPage == false){
		firstPageLi.addClass("disabled");
		prePageLi.addClass("disabled");
	}
	
	var nextPageLi = $("<li></li>").append($("<a></a>").append($("<span></span>").append("&raquo;")).attr("href","#"));
	var lastPageLi = $("<li></li>").append($("<a></a>").append("末页").attr("href","#"));
	//如果没有下一页数据，末页和下一页按钮设置不可点属性disabled
	if(result.extend.pageInfo.hasNextPage == false){
		nextPageLi.addClass("disabled");
		lastPageLi.addClass("disabled");
	}
	ul.append(firstPageLi).append(prePageLi);
	//页码12345,遍历给UL添加页码提示
	$.each(result.extend.pageInfo.navigatepageNums,function(index,item){
		var numLi = $("<li></li>").append($("<a></a>").append(item).attr("href","#"));
		//如果当前页是活动页，设置标签属性为active
		if(result.extend.pageInfo.pageNum == item){
			numLi.addClass("active");
		}
		ul.append(numLi);
	});
	ul.append(nextPageLi).append(lastPageLi);
	var navEle = $("<nav></nav>").append(ul).appendTo("#page_nav_area");
}
```
为了每次进行ajax请求，我们把原来的ajax部分抽取成to_page(pageNum)方法，指定默认加载第一页数据  
```js
//1、页面加载完成以后，直接发送Ajax请求，获取分页数据
$(function(){
	//去首页
	to_page(1);
});


function to_page(pn){
	$.ajax({
		url: "${APP_PATH}/emps",
		data: "pn="+pn,
		type: "GET",
		success: function(result){
			//console.log(result);
			//1、解析显示员工数据 build_emps_table()
			build_emps_table(result);
			//2、解析显示分页信息 build_page_info()
			build_page_info(result);
			//3、解析显示分页条 build_page_nav()
			build_page_nav(result);
		}
	});
}
```
导航条绑定单击事件，调用`to_page()`方法，传入参数`pageNum`  
```js
$.each(result.extend.pageInfo.navigatepageNums,function(index,item){
	var numLi = $("<li></li>").append($("<a></a>").append(item).attr("href","#"));
	//如果当前页是活动页，设置标签属性为active
	if(result.extend.pageInfo.pageNum == item){
		numLi.addClass("active");
	}
	//绑定单击事件，调用to_page()方法
	numLi.click(function(){
		to_page(item);
	});
	ul.append(numLi);
});
```
并且在每个标签`append()`数据前清空原来的数据，这里我就直接写相关代码了    
```js
//每次加载前清空表格数据
$("#emps_tables tbody").empty();
```
```js
//每次加载前清空分页信息
$("#page_info_area").empty();
```
```js
//每次加载前清空分页条
$("#page_nav_area").empty();
```
导航条单击时间搞定了，那么首页、上一页、下一页、末页也绑定单击事件  
```
//如果没有上一页数据，首页和上一页按钮设置不可点属性disabled
if(result.extend.pageInfo.hasPreviousPage == false){
	firstPageLi.addClass("disabled");
	prePageLi.addClass("disabled");
}else{
	firstPageLi.click(function(){
		to_page(1);
	});
	prePageLi.click(function(){
		to_page(result.extend.pageInfo.pageNum-1);
	});
}

//如果没有下一页数据，末页和下一页按钮设置不可点属性disabled
if(result.extend.pageInfo.hasNextPage == false){
	nextPageLi.addClass("disabled");
	lastPageLi.addClass("disabled");
}else{
	lastPageLi.click(function(){
		to_page(result.extend.pageInfo.pages);
	});
	nextPageLi.click(function(){
		to_page(result.extend.pageInfo.pageNum+1);
	});
}
```
为了正确性，分页插件提供了一个[**分页合理化参数**](https://github.com/pagehelper/Mybatis-PageHelper/blob/master/wikis/zh/HowToUse.md)  
> `reasonable`：分页合理化参数，默认值为`false`  
> 当该参数设置为 `true` 时，`pageNum<=0` 时会查询第一页  
> `pageNum>pages`（超过总数时），会查询最后一页  
> 默认`false` 时，直接根据参数进行查询  
  
只需要在`mybatis-config.xml`中的分页插件部分添加一个`<property>`  
```xml
<plugins>
    <!-- com.github.pagehelper为PageHelper类所在包名 -->
    <plugin interceptor="com.github.pagehelper.PageInterceptor">
        <!-- 分页参数合理化 -->
	    <property name="reasonable" value="true"/>
	</plugin>
</plugins>
```
#### 新增功能  
在`index.jsp`中添加[模态框代码](https://getbootstrap.com/docs/3.3/javascript/#modals-usage)，指定模态框`id`为`empAddModal`，通过js绑定添加按钮单击事件来激活  
```html
<!-- 新增按钮模态框 -->
<div class="modal fade" id="empAddModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span></button>
        <h4 class="modal-title" id="myModalLabel">Modal title</h4>
      </div>
      <div class="modal-body">
        ...
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
        <button type="button" class="btn btn-primary">Save changes</button>
      </div>
    </div>
  </div>
</div>
```
为新增按钮添加id属性`emp_add_modal_btn`  
```html
<button type="button" class="btn btn-primary btn-sm" id="emp_add_modal_btn">新增</button>
```
使用js绑定单击事件  
```js
//新增按钮单击事件
$("#emp_add_modal_btn").click(function(){
	$('#empAddModal').modal({
		backdrop:"static"
	})
});
```
修改模态框成我们想要的样子，`modal-title`修改为`员工添加`，`modal-body`中的省略号修改成bootstrap的表单，指定表单每个元素的`id`和`name`属性(name属性和javBean保持一致)，`placeholder`设置默认显示内容，`checked="checked"`默认选中一个单选按钮  
```html
<!-- 新增按钮模态框 -->
<div class="modal fade" id="empAddModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span></button>
        <h4 class="modal-title" id="myModalLabel">员工添加</h4>
      </div>
      <div class="modal-body">
      	<!-- 表单 -->
        <form class="form-horizontal">
		  <div class="form-group">
		    <label class="col-sm-2 control-label">用户名(empName)</label>
		    <div class="col-sm-10">
		      <input type="text" name="empName" class="form-control" id="empName_add_input" placeholder="empName">
		      <span class="help-block"></span>
		    </div>
		  </div>
		  <div class="form-group">
		    <label class="col-sm-2 control-label">邮箱(email)</label>
		    <div class="col-sm-10">
		      <input type="email" name="email" class="form-control" id="email_add_input" placeholder="email@teaper.cn">
		      <span class="help-block"></span>
		    </div>
		  </div>
		  <div class="form-group">
		    <label class="col-sm-2 control-label">性别(gender)</label>
		    <div class="col-sm-10">
		      	<label class="radio-inline">
				  <input type="radio" name="gender" id="gender1_add_input" value="F" checked="checked"> 男
				</label>
				<label class="radio-inline">
				  <input type="radio" name="gender" id="gender2_add_input" value="M"> 女
				</label>
		    </div>
		  </div>
		  <div class="form-group">
		    <label class="col-sm-2 control-label">部门名称(deptName)</label>
		    <div class="col-sm-4">
		      	<select class="form-control" name="fkDeptId" id="dept_add_select">
				  <!-- <option value="fkDeptId"></option> -->
				</select>
		    </div>
		  </div>
		</form>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-default" data-dismiss="modal">关闭</button>
        <button type="button" class="btn btn-primary">保存</button>
      </div>
    </div>
  </div>
</div>
```
![新增模态框](http://ww1.sinaimg.cn/large/006kWbIoly1g1jj196pdej30we0cnabj.jpg)
加载模态框之前，我们需要发送`ajax`请求**获取部门信息**的`json`  
抽取`ajax`请求为`getDepts()`方法，在弹出模态框之前调用请求  
```js
//新增按钮单击事件
$("#emp_add_modal_btn").click(function(){
	//发送ajax请求，查出部门信息，显示在下拉列表中
	getDepts();
	//弹出模态框
	$('#empAddModal').modal({
		backdrop:"static"
	})
});

//查出所有部门信息并显示在下拉列表中
function getDepts(){
	$.ajax({
		url:"${APP_PATH}/depts",
		type:"GET",
		success: function(result){
			console.log(result);
		}
	});
}
```
编写`DeptController.java`，自动装配`DeptService`对象，调用它的`getDepts()`方法，封装到`Msg`中返回给页面  
```java
/**
 * 处理部门有关的请求
 * @author teaper
 *
 */
@Controller
public class DeptController {
	@Autowired
	private DeptService deptService;
	
	//返回所有的部门信息
	@RequestMapping("/depts")
	@ResponseBody
	public Msg getDepts() {
		//查出的所有部门信息
		List<Dept> list = deptService.getDepts();
		return Msg.success().add("depts",list);
	}
}
```
编写`DeptService.java`需要装配`DeptMapper`，调用它的`selectByExample()`方法
```java
@Service
public class DeptService {
	@Autowired
	private DeptMapper deptMapper;
	
	public List<Dept> getDepts() {
		List<Dept> list = deptMapper.selectByExample(null);
		return list;
	}
}
```
```xml
<select id="selectByExample" parameterType="cn.teaper.www.pojo.DeptExample" resultMap="BaseResultMap">
    select
    <if test="distinct">
      distinct
    </if>
    <include refid="Base_Column_List" />
    from dept
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
    <if test="orderByClause != null">
      order by ${orderByClause}
    </if>
</select>
```
运行之后可以看到请求信息  
![获取部门信息](http://ww1.sinaimg.cn/large/006kWbIoly1g1jl0b26i3j31hc0kw0uc.jpg)
获取到数据之后我们就可以把数据绑定到下拉列表中  
`$("#dept_add_select").empty();`清空下拉列表中原来的信息  
遍历`depts`中的信息到循环输出的`<option></option>`标签中  
为了后面添加数据，需要绑定每个下拉选项的`value`属性为部门编号
```js
//查出所有部门信息并显示在下拉列表中
function getDepts(){
	$.ajax({
		url:"${APP_PATH}/depts",
		type:"GET",
		success: function(result){
			//console.log(result);
			$("#dept_add_select").empty();
			//显示部门信息在下拉列表中
			$.each(result.extend.depts,function(){
				$("#dept_add_select").append($("<option></option>").append(this.deptName).attr("value",this.deptId))
			});
		}
	});
}
```
接下来，我们就可以开发**保存功能**  
首先给保存按钮绑定单击事件，发送ajax的POST请求，这里我们约定一下Rest风格URI请求类型  
> URI:  
> /emp/{id}	GET查询员工  
> /emp      POST保存员工  
> /emp/{id}	PUT修改员工  
> /emp/{id}	DELETE删除员工  
  
<span style="color:#ff0000;">查询的时候发**get**请求，新增数据发**post**请求，修改的时候发**put**请求，删除发**delete**请求</span>  
FORM表单`$("#empAddModal form")`的`serialize()`方法可以获取表单中需要提交的数据  
```js
$("#emp_save_btn").click(function(){
	//单击模态框中的保存按钮，讲数据提交给服务器
	//发送ajax请求保存员工
	//alert($("#empAddModal form").serialize());
	$.ajax({
		url:"${APP_PATH}/emp",
		type:"POST",
		data:$("#empAddModal form").serialize(),
		success: function(result){
			alert(result.msg);
			//1、关闭模态框
			$("#empAddModal").modal("hide");
			//2、来到末页，显示刚才添加的数据
			//发送ajax请求显示最后一页的数据即可
			to_page(totalRecord);
		}
	});
});
```
保存成功之后需要关闭模态框，跳转到最后一页，所以需要发送请求查询最后一页数据，由于不知道最后一页数据是第几页，所以我们定义一个全局变量`totalRecord`去接收，再每次解析分页数据的时候给它赋值，由于我们不知道具体的页数，所以取最大(即多少条数据就多少页)，因为之前我们配置过**分页参数合理化**，当`pageNum`大于页数的时候查询最后一页  
```js
var totalRecord;//总记录数
```
```js
//解析显示分页信息
function build_page_info(result){
	//每次加载前清空分页信息
	$("#page_info_area").empty();
	
	$("#page_info_area").append("总"+result.extend.pageInfo.pages+"页 当前第"+result.extend.pageInfo.pageNum+"页 总"+result.extend.pageInfo.total+"条数据")
	totalRecord = result.extend.pageInfo.total;//保存总记录数
}
```
请求发送到`EmpController.java`，指定请求类型为`method = RequestMethod.POST`  
为了知道执行结果成功与否，我们也封装成`Msg`进行返回  
```java
//员工保存
@RequestMapping(value="/emp",method = RequestMethod.POST)
@ResponseBody
public Msg saveEmp(Emp emp) {
	empService.saveEmp(emp);
	return Msg.success();
}
```
`EmpService.java`调取`empMapper`中的`insertSelective()`方法  
```java
//保存员工对象
public void saveEmp(Emp emp) {
	empMapper.insertSelective(emp);
}
```
`insertSelective()`方法我们之前[测试Mapper接口](#测试mapper接口)的时候测试过了，并且批量插入了1000条数据  
  
员工添加基本完成，但是还为了安全性，还需要**进行校验**，例如：已添加的员工不允许再添加，名字格式，邮箱格式等问题  
  
**前端校验**  
在向数据库提交请求之前，对要提交给服务器的表单数据格式进行校验，创建`validate_add_form()`方法，返回true/false，校验失败`return false;`退出方法，不执行后面的ajax数据提交  
```js
//2、对要提交给服务器的表单数据格式进行校验
if(!validate_add_form()){
	return false;
};
```
定义`validate_add_form()`方法  
通过id属性拿到校验数据，定义正则表达式`/(^[a-zA-Z0-9_-]{6,16}$)|(^[\u2E80-\u9FFF]{2,5})/`，调用正则表达式的test()方法，判断是否满足格式要求  
如果校验失败，给个提示信息，放回`false`
```js
//表单数据校验方法
function validate_add_form(){
	//1、拿到校验数据，使用正则表达式校验
	var empName = $("#empName_add_input").val();
	var regName = /(^[a-zA-Z0-9_-]{6,16}$)|(^[\u2E80-\u9FFF]{2,5})/;
	//alert(regName.test(empName));
	if(!regName.test(empName)){
		//alert("用户名可以是2~5位中文或者6~16位英文和数字的组合");
		show_validate_msg("#empName_add_input","error","用户名可以是2~5位中文或者6~16位英文和数字的组合");
		return false;
	}else{
		show_validate_msg("#empName_add_input","success","");
	};
	//校验邮箱信息
	var empEmail = $("#email_add_input").val();
	var regEmail = /^([a-z0-9_\.-]+)@([\da-z\.-]+)\.([a-z\.]{2,6})$/;
	if(!regEmail.test(empEmail)){
		//alert("邮箱格式不正确");
		show_validate_msg("#email_add_input","error","邮箱格式不正确");
		return false;
	}else{
		show_validate_msg("#email_add_input","success","");
	};
	return true;
}
```
上面的`show_validate_msg()`方法对提示信息进行按需选择  
修改输入框的边框颜色，需要在输入框的父元素中添加class属性，所以修改前需要先清楚一下样式  
根据前面`validate_add_form()`方法传递的参数，设置父元素的颜色属性，还有输入框的下一个标签`<span></span>`标签中的内容
```js
//显示校验结果的提示信息
function show_validate_msg(ele,status,msg){
	//清楚当前元素的校验状态
	$(ele).parent().removeClass("has-success has-error")
	$(ele).next("span").text("");
	
	if("success" == status){
		$(ele).parent().addClass("has-success");
		$(ele).next("span").text(msg);
	}else if("error" == status){
		$(ele).parent().addClass("has-error");
		$(ele).next("span").text(msg);
	};
}
```
**后端校验**  
在输入`empName`之后，发送一个ajax请求查询数据是否存在，不存在的话添加，存在的话不允许添加  
在前端校验后面紧跟后端校验判断，判断保存按钮`#emp_save_btn`的`ajax-va`属性值是否为`error`，是的话就返回`false`退出保存
```js
//3、对用户名是否可用进行校验
if($(this).attr("ajax-va")=="error"){
	return false;
}
```
那么在什么时候给保存按钮添加属性呢？在我们输入完`empName`之后，输入框的`change()`事件(文本内容改变之后运行)绑定一个方法，发送ajax查询用户是否存在，不存在就给保存按钮的`ajax-va`属性绑定`error`  
  
获取输入框中的值，发送ajax信息，传入参数值`empName`，根据最后的Msg返回结果判断用户名是否可用，可用给保存按钮自定义属性`ajax-va`赋值`success`，否则赋值`error`，并且调用提示信息方法`show_validate_msg()`输出提示信息
```js
//校验用户名是否可用
$("#empName_add_input").change(function(){
	var empName = this.value;
	//发送ajax请求校验用户名是否存在
	$.ajax({
		url:"${APP_PATH}/checkuser",
		type:"POST",
		data:"empName="+empName,
		success: function(result){
			if(result.code == 1){
				show_validate_msg("#empName_add_input","success","用户名可用");
				$("#emp_save_btn").attr("ajax-va","success");
			}else{
				show_validate_msg("#empName_add_input","error",result.extend.va_msg);
				$("#emp_save_btn").attr("ajax-va","error");
			}
		}
	});
});
```
编写`EmpController`，查询用户名是否可用，指定请求方式`method`为`post`请求(和ajax中保持一致)  
返回Msg对象，使用`@RequestParam()`传入参数，定义一个和js中一致的正则表达式`regx`  
> 注意：java中的正则表达式没有`/正则表达式/`中的`/`  
  
使用`matches()`方法判断用户名的格式，格式错误我们就不查询用户名了，直接调用Msg的请求失败方法，返回失败信息  
调用`empService`的`checkUser()`方法，查询用户是否存在，根据boolean类型返回值，给Msg对象绑定结果  
```java
/**
 * 检查用户名是否可用
 * @param empName
 * @return
 */
@RequestMapping(value="/checkuser",method = RequestMethod.POST)
@ResponseBody
public Msg checkUser(@RequestParam("empName")String empName) {
	//先判断用户名是否是合法表达式
	String regx = "(^[a-zA-Z0-9_-]{6,16}$)|(^[\\u2E80-\\u9FFF]{2,5})";
	if(!empName.matches(regx)) {
		return Msg.fail().add("va_msg", "用户名可以是2~5位中文或者6~16位英文和数字的组合");
	}
	//数据库用户名重复校验
	boolean b = empService.checkUser(empName);
	if(b) {
		return Msg.success();
	}else {
		return Msg.fail().add("va_msg", "用户名已存在");
	}
}
```
`empService.checkUser()`方法中，可以使用`empMapper`的`countByExample()`方法查询用户存不存在，但是它的入参是`EmpExample`类型，所以还需要new一个`EmpExample`类型的对象`example`  
使用`example`对象的`createCriteria()`方法给`example`对象添加查询条件，使用`andEmpNameEqualTo()`绑定用户名，然后将`example`传入方法中，最后返回一个long类型的对象，直接返回它是否等于0的布尔值(0表示零条数据，其他数字表示已存在)  
```js
/**
 * 检验用户名是否可用
 * @param empName
 * @return true:代表当前姓名可用，否则不可用
 */
public boolean checkUser(String empName) {
	EmpExample example = new EmpExample();
	Criteria createCriteria = example.createCriteria();
	createCriteria.andEmpNameEqualTo(empName);
	long count = empMapper.countByExample(example);
	return count == 0;
}
```
```xml
<select id="countByExample" parameterType="cn.teaper.www.pojo.EmpExample" resultType="java.lang.Long">
    select count(*) from emp
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
</select>
```
表单验证完成了，但是还有一些小问题，例如我们第一次点击新增输入模态框中的内容，在第二次打开模态框之后还在，当我们点击保存之后，他就不会再次发送ajax请求去验证数据，所以我们在每次点击新增按钮的时候清除一下表单数据`reset_form("#empAddModal form");`  
```js
//新增按钮单击事件
$("#emp_add_modal_btn").click(function(){
	//清空表单样式及内容
	reset_form("#empAddModal form");
	//发送ajax请求，查出部门信息，显示在下拉列表中
	getDepts();
	//弹出模态框
	$("#empAddModal").modal({
		backdrop:"static"
	})
});
```
因为jquery没有reset()方法，而该方法是javascript的DOM对象的，所以加`[0]`取出`#empAddModal form`的DOM对象，进行重置，并且还要清除表单中所有标签的`has-success has-error`类样式清除，还有所有class为`help-block`的标签内容
```js
//清空表单样式及内容
function reset_form(ele){
	$(ele)[0].reset();
	//清空表单样式
	$(ele).find("*").removeClass("has-success has-error");
	$(ele).find(".help-block").text("");
}
```
前端`jquery`校验完成了，`ajax`用户名重复校验也完成了，但是，还记得保存按钮`#emp_save_btn`的`ajax-va`属性吗？这个好像没有改，如果第一次失败，被设置成`error`，下次添加就无法保存数据，所以我们还需要进行**JSR303后端校验**  
  
第一步：`pom.xml`中导入`hibernate-validator`组件包  
```xml
<!-- JSR303后端校验
hibernate-validator-5.2.4.Final.jar
validation-api-1.1.0.Final.jar
jboss-logging-3.2.1.Final.jar
classmate-1.1.0.jar
-->
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>5.2.4.Final</version>
</dependency>
```
第二步：给`Emp.java`的bean属性添加验证注解  
```java
@Pattern(regexp="(^[a-zA-Z0-9_-]{6,16}$)|(^[\\u2E80-\\u9FFF]{2,5})",message="用户名可以是2~5位中文或者6~16位英文和数字的组合")
private String empName;

//@Email或@Pattern
@Pattern(regexp="^([a-z0-9_\\.-]+)@([\\da-z\\.-]+)\\.([a-z\\.]{2,6})$",message="邮箱格式不正确")
private String email;
```
第三步：在保存员工方法中，给参数加`@Valid`注解，并且会有一个返回值`BindingResult`类型的`result`，调用`result.hasErrors()`方法判断是否匹配正则表达式，匹配就保存，不匹配的话还需要返回错误信息到前端，需要创建一个Map对象，将遍历的错误信息封装成Msg对象返回
```java
/**
 * 员工保存
 * @param emp
 * @return
 * 1、支持JSR303校验
 * 2、导入Hibernate-Validator
 */
@RequestMapping(value="/emp",method = RequestMethod.POST)
@ResponseBody
public Msg saveEmp(@Valid Emp emp,BindingResult result) {
	if(result.hasErrors()) {
		Map<String,Object> map = new HashMap<>();
		//校验失败
		List<FieldError> errors = result.getFieldErrors();
		for (FieldError fieldError : errors) {
			//System.out.println("错误的字段名"+fieldError.getField());
			//System.out.println("错误信息"+fieldError.getDefaultMessage());
			map.put(fieldError.getField(), fieldError.getDefaultMessage());
		}
		return Msg.fail().add("errorField", map);
	}else {
		empService.saveEmp(emp);
		return Msg.success();
	}
}
```
第四步：前端保存数据，先把`jquery`格式校验注释掉，保存员工的时候做判断，`result.extend.errorField`中的`email`和`empName`有没有数据，有数据说明有错误，需要设置提示框样式；没数据正常进行保存操作  
  
<span style="color:#ff0000;">成功之后记得把注释的内容解开</span>
```js
//保存员工
$("#emp_save_btn").click(function(){
	//1、单击模态框中的保存按钮，将数据提交给服务器
	//2、对要提交给服务器的表单数据格式进行校验
	/* if(!validate_add_form()){
		return false;
	}; */
	//3、对用户名是否可用进行校验
	if($(this).attr("ajax-va")=="error"){
		return false;
	}
	//4、校验成功发送ajax请求保存员工
	//alert($("#empAddModal form").serialize());
	$.ajax({
		url:"${APP_PATH}/emp",
		type:"POST",
		data:$("#empAddModal form").serialize(),
		success: function(result){
			//alert(result.msg);
			if(result.code == 1){
				//员工保存成功
				//1、关闭模态框
				$("#empAddModal").modal("hide");
				//2、来到末页，显示刚才添加的数据
				//发送ajax请求显示最后一页的数据即可
				to_page(totalRecord);
			}else{
				//校验失败，显示失败信息
				//console.log(result);
				if(undefined != result.extend.errorField.email){
					//显示邮箱错误信息
					show_validate_msg("#email_add_input","error",result.extend.errorField.email);
					$("#emp_save_btn").attr("ajax-va","error");
				}
				if(undefined != result.extend.errorField.empName){
					//显示员工名字错误信息
					show_validate_msg("#empName_add_input","error",result.extend.errorField.empName);
					$("#emp_save_btn").attr("ajax-va","error");
				}
			}
			
		}
	});
});
</script>
```
#### 编辑功能  
创建模态框（和新增模态框一样），修改一些标签的`id`属性名字  
```html
<!-- 编辑按钮模态框 -->
<div class="modal fade" id="empUpdateModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span></button>
        <h4 class="modal-title" id="">员工修改</h4>
      </div>
      <div class="modal-body">
      	<!-- 表单 -->
        <form class="form-horizontal">
		  <div class="form-group">
		    <label class="col-sm-2 control-label">用户名(empName)</label>
		    <div class="col-sm-10">
		      <input type="text" name="empName" class="form-control" id="empName_update_input" placeholder="empName">
		      <span class="help-block"></span>
		    </div>
		  </div>
		  <div class="form-group">
		    <label class="col-sm-2 control-label">邮箱(email)</label>
		    <div class="col-sm-10">
		      <input type="email" name="email" class="form-control" id="email_update_input" placeholder="email@teaper.cn">
		      <span class="help-block"></span>
		    </div>
		  </div>
		  <div class="form-group">
		    <label class="col-sm-2 control-label">性别(gender)</label>
		    <div class="col-sm-10">
		      	<label class="radio-inline">
				  <input type="radio" name="gender" id="gender1_update_input" value="F" checked="checked"> 男
				</label>
				<label class="radio-inline">
				  <input type="radio" name="gender" id="gender2_update_input" value="M"> 女
				</label>  
		    </div>
		  </div>
		  <div class="form-group">
		    <label class="col-sm-2 control-label">部门名称(deptName)</label>
		    <div class="col-sm-4">
		      	<select class="form-control" name="fkDeptId" id="dept_add_select">
				  <!-- <option value="fkDeptId"></option> -->
				</select>
		    </div>
		  </div>
		</form>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-default" data-dismiss="modal">关闭</button>
        <button type="button" class="btn btn-primary" id="emp_update_btn">更新</button>
      </div>
    </div>
  </div>
</div>
```
在解析员工数据的时候，就给每个按钮添加一个`class`，编辑按钮叫`edit_btn`，删除按钮叫`del_btn`
```js
var editbtn = $("<button></button>").addClass("btn btn-primary btn-sm edit_btn").append($("<span></span>").addClass("glyphicon glyphicon-pencil")).append("编辑");
var delBtn = $("<button></button>").addClass("btn btn-danger btn-sm del_btn").append($("<span></span>").addClass("glyphicon glyphicon-trash")).append("删除");
```
给**编辑按钮绑定单击事件**，可以在解析员工数据的时候绑定，不过太耦合，所以只能在创建按钮之后绑定单击事件  
这里jquery提供了两个方法，`live()`适用于`jquery 1.7`以下版本，我们用的是`jquery-3.3.1.min.js`，可以使用`on()`方法  
```js
$(document).on("click",".edit_btn",function(){
	alert("编辑");
});
```
确认可以弹出对话框后，发送`ajax`查询部门列表并显示在下拉列表中
```js
//编辑按钮单击事件
$(document).on("click",".edit_btn",function(){
	//alert("编辑");
	//1、查出员工信息，显示员工信息
	//2、查出部门信息，并显示部门列表
	
	//发送ajax请求，查出部门信息，显示在下拉列表中
	getDepts("#empUpdateModal select");
	//弹出模态框
	$("#empUpdateModal").modal({
		backdrop:"static"
	})
	
});
```
由于之前的查询部门信息的`getDepts()`方法不是带参数的，而是写死在添加按钮的，这里我们改成传入按钮id参数的方法
```js
//查出所有部门信息并显示在下拉列表中
function getDepts(ele){
	//清空之前下拉列表的值
	$(ele).empty();
	$.ajax({
		url:"${APP_PATH}/depts",
		type:"GET",
		success: function(result){
			//console.log(result);
			$("#dept_add_select").empty();
			//显示部门信息在下拉列表中
			$.each(result.extend.depts,function(){
				var optionEle = $("<option></option>").append(this.deptName).attr("value",this.deptId);
				optionEle.appendTo(ele);
			});
		}
	});
}
```
相应的添加用户按钮弹出模态框也要修改修改，这里我就不再累赘了  
  
弹出模态框后，调用`getEmp()`方法，**查询并且显示员工**在模态框中  
```js
//编辑按钮单击事件
$(document).on("click",".edit_btn",function(){
	//alert("编辑");
	//1、查出部门信息，并显示部门列表
	getDepts("#empUpdateModal select");
	//2、查出员工信息，显示员工信息
	getEmp($(this).attr("edit-id"))
	//弹出模态框
	$("#empUpdateModal").modal({
		backdrop:"static"
	})
});
```
需要一个员工ID作为参数，由于不知道员工ID，所以，我们在创建编辑和删除按钮的时候就给按钮自定义一个`edit-id`属性绑定id  
```js
var editBtn = $("<button></button>").addClass("btn btn-primary btn-sm edit_btn").append($("<span></span>").addClass("glyphicon glyphicon-pencil")).append("编辑");
//为编辑按钮添加自定义的属性，来表示当前员工的id
editBtn.attr("edit-id",item.empId);

var delBtn = $("<button></button>").addClass("btn btn-danger btn-sm del_btn").append($("<span></span>").addClass("glyphicon glyphicon-trash")).append("删除");
//为删除按钮添加自定义的属性，来表示当前员工的id
delBtn.attr("del-id",item.empId);
```
发送ajax的GET请求查询员工信息  
`input[name=gender]`表示所有`name`属性为`gender`的输入框，`val()`方法会自动匹配数组元素  
```js
//查出员工信息，显示员工信息
function getEmp(id){
	$.ajax({
		url:"${APP_PATH}/emp/"+id,
		type:"GET",
		success: function(result){
			//console.log(result);
			var empData = result.extend.emp;
			$("#empName_update_static").text(empData.empName);
			$("#email_update_input").val(empData.email);
			$("#empUpdateModal input[name=gender]").val([empData.gender]);
			$("#empUpdateModal select").val([empData.fkDeptId]);
		}
	})
}
```
`EmpController.java`使用`@PathVariable()`传入`GET`请求中的`id`  
```java
//按照员工ID查询员工
@RequestMapping(value="/emp/{id}",method = RequestMethod.GET)
@ResponseBody
public Msg getEmp(@PathVariable("id")Integer id) {
	Emp emp = empService.getEmp(id);
	return Msg.success().add("emp",emp);
}
```
```java
//按照员工ID查询员工
public Emp getEmp(Integer id) {
	Emp emp = empMapper.selectByPrimaryKey(id);
	return emp;
}
```
```xml
<select id="selectByPrimaryKey" parameterType="java.lang.Integer" resultMap="BaseResultMap">
    select 
    <include refid="Base_Column_List" />
    from emp
    where emp_id = #{empId,jdbcType=INTEGER}
</select>
```
至此，查询和显示完成了，接下来就是点击更新按钮**修改员工信息**  
用户名不让修改，所以我们只需要校验邮箱格式，使用之前的表单数据校验方法里的代码即可，然后改下id名字为`#email_update_input`  
```js
//点击更新按钮更新员工信息
$("#emp_update_btn").click(function () {
	//验证邮箱是否合法
	//1、校验邮箱信息
	var empEmail = $("#email_update_input").val();
	var regEmail = /^([a-z0-9_\.-]+)@([\da-z\.-]+)\.([a-z\.]{2,6})$/;
	if(!regEmail.test(empEmail)){
		//alert("邮箱格式不正确");
		show_validate_msg("#email_update_input","error","邮箱格式不正确");
		return false;
	}else{
		show_validate_msg("#email_update_input","success","");
	};
	//2、发送ajax请求保存员工数据
	$.ajax({
		url:"${APP_PATH}/emp/"+$(this).attr("edit-id"),
		//type:"POST",
		//data:$("#empUpdateModal form").serialize()+"&_method=PUT",
		type:"PUT",
		data:$("#empUpdateModal form").serialize(),
		success:function(result){
			//alert(result.msg);
			//1、关闭对话框
			$("#empUpdateModal").modal("hide");
			//2、回到本页面
			to_page(currentPage);
		}
	});
});
```
发送ajax请求，更新数据，需要发送一个员工id，这里我们可以在弹出模态框之前，给更新按钮绑定一个自定义属性`edit-id`,这样ajax请求的URL就可以使用`$(this).attr("edit-id")`把值提交  
```js
//编辑按钮单击事件
$(document).on("click",".edit_btn",function(){
	//3、把员工id传递给模态框更新按钮
	$("#emp_update_btn").attr("edit-id",$(this).attr("edit-id"));
	
	//4、弹出模态框
	$("#empUpdateModal").modal({
		backdrop:"static"
	})
});
```
按照Rest风格的URI，后端`EmpController.java`中就需要发送PUT请求  
<span style="color:#ff0000;">注意：这里的`RequestMapping`参数不能简单写id，而是要和`EmpMapper.xml`中的SQL保持一致，写`empId`，否则SQL会拼接错误</span>
```java
/**
 * 更新员工信息
 * @param pn
 * @return
 */
@RequestMapping(value = "/emp/{empId}",method = RequestMethod.PUT)
@ResponseBody
public Msg saveEmp(Emp emp) {
	empService.updateEmp(emp);
	return Msg.success();
}
```
`EmpService.java`实现如下
```java
/**
 * 员工更新
 * @param emp
 */
public void updateEmp(Emp emp) {
	empMapper.updateByPrimaryKeySelective(emp);
}
```
但是如此一来，前端ajax请求发送方式就有两种方案  
**方案一：** 前端发POST请求，请求数据拼接`&_method=PUT`  
```js
//2、发送ajax请求保存员工数据
$.ajax({
	url:"${APP_PATH}/emp/"+$(this).attr("edit-id"),
	type:"POST",
	data:$("#empUpdateModal form").serialize()+"&_method=PUT",
	success:function(result){
		alert(result.msg);
	}
});
```

**方案二(推荐)：** 前端发送PUT请求，表单数据体无法封装成Map，tomcat引发血案  
```js
//2、发送ajax请求保存员工数据
$.ajax({
	url:"${APP_PATH}/emp/"+$(this).attr("edit-id"),
	type:"PUT",
	data:$("#empUpdateModal form").serialize(),
	success:function(result){
		alert(result.msg);
	}
});
```
需要在web.xml配置上HttpPutFormContentFilter过滤器  
```xml
<!-- REST风格的PUT请求转换过滤器 -->
<filter>
	<filter-name>HttpPutFormContentFilter</filter-name>
	<filter-class>org.springframework.web.filter.HttpPutFormContentFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>HttpPutFormContentFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```
`HttpPutFormContentFilter`会将请求体中的数据解析包装成一个Map，request会被重新包装，request.getParameter()方法会被重写，最后就会从自己封装的Map中取数据，而不是使用Tomcat只能封装的POST请求数据  
  
接下来就是**关闭模态框和刷新结果**，刷新结果直接调用`to_page()`方法，不过需要传一个当前页码`currentPage`  
```js
success:function(result){
	//alert(result.msg);
	//1、关闭对话框
	$("#empUpdateModal").modal("hide");
	//2、回到本页面
	to_page(currentPage);
}
```
当前页码和总记录数一样，直接定义成全局变量，在解析分页信息的时候赋值即可  
```js
var totalRecord,currentPage; //总记录数,当前页数
```
```js
//解析显示分页信息
function build_page_info(result){
    //每次加载前清空分页信息
	$("#page_info_area").empty();
	$("#page_info_area").append("总"+result.extend.pageInfo.pages+"页 当前第"+result.extend.pageInfo.pageNum+"页 总"+result.extend.pageInfo.total+"条数据")
	
	totalRecord = result.extend.pageInfo.total;//保存总记录数
	currentPage = result.extend.pageInfo.pageNum;   //保存当前页码
}
```
  
### 迁移至MySQL8.0+数据库  
需要在`pom.xml`中修改`mysql-connector-java.jar`的版本为对应的mysql8.0+版本(例如：8.0.16)  
```xml
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<!-- <version>5.1.41</version> -->
	<version>8.0.16</version>
</dependency>	
```
在`database.properties`的`jdbc.url`上追加`&useSSL=true`，开启证书验证  
```bash
#jdbc.url=jdbc:mysql://127.0.0.1:3306/ssm?useUnicode=true&characterEncoding=utf-8
jdbc.url=jdbc:mysql://127.0.0.1:3306/ssm?useUnicode=true&characterEncoding=utf-8&useSSL=true
```
