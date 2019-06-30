---
title: MyBatis框架
categories: [MyBatis]
tags: [MyBatis]
date: 2019-06-30 13:50:47
---
#### 前身  
iBatis,Apache的一个开源框架;实体类和SQL语句之间建立映射关系  
  
#### 特点  
基于SQL语法,简单易学  
开放源码,了解底层封装过程  
SQL语句封装在配置文件中,便于统一管理与维护,降低程序耦合度  
方便程序代码调试  
  
#### 优点  
与JDBC相比,减少50%以上代码量  
最简单的持久化框架,小巧并简单易学  
SQL代码从程序中分离,可重用  
提供XML标签,支持编写动态SQL  
提供映射标签,支持对象与数据库的ORM字段关系映射  
  
#### 缺点  
SQL语句编写量大,对开发人员有一定要求  
数据库移植性差  
  
#### 使用Mybatis的开发步骤  
[下载](https://github.com/mybatis/mybatis-3/releases)mybatis的jar包  
编写MyBatis核心配置文件(configuration.xml)  
创建实体类(pojo)  
DAO层-SQL映射文件(mapper.xml)  
创建测试类  
* 读取全局配置文件mybatis-config.xml  
* 创建sqlSessionFactory对象,读取文件  
* 创建SqlSession对象  
* 调用mapper文件进行数据操作  
  
#### MyBatis架包  
示例:mybatis-3.4.6
  
| 架包 | 说明 |
| ----------- | ---------- |
| [asm-5.2.jar](https://mvnrepository.com/artifact/org.ow2.asm/asm) | 操作Java字节码的类库 |
| [cglib-3.2.5.jar](https://mvnrepository.com/artifact/cglib/cglib) | 动态集成java类或实现接口 |
| [commons-logging-1.2.jar](https://mvnrepository.com/artifact/commons-logging/commons-logging) | 用于通用日志处理 |
| [javassist-3.22.0-GA.jar](https://mvnrepository.com/artifact/org.javassist/javassist) | 分析,编辑和创建Java字节码的类库 |
| [log4j-1.2.17.jar](https://mvnrepository.com/artifact/log4j/log4j) | 	日志系统 |
| [log4j-api-2.3.jar](https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-api) | 	日志系统的封装,对外提供统一的API接口 |
| [slf4j-log4j12-1.7.25.jar](https://mvnrepository.com/artifact/org.slf4j/slf4j-log4j12) | slf4对log4的相应驱动,完成slf4绑定log4 |
  
#### 导入jar包  
选取jar包导入工程:普通java项目Build Path,web项目放lib文件夹,maven项目配置pom.xml  
> [mybatis-3.4.6.jar](https://mvnrepository.com/artifact/org.mybatis/mybatis)  
> [log4j-api-2.3.jar](https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-api)  
> [mysql-connector-java-8.0.11.jar](https://mvnrepository.com/artifact/mysql/mysql-connector-java)  
  
#### MyEclipse中mybatis-3.4.6绑定源代码  
右击项目工程中的mybatis-3.4.6.jar -> Properties -> Java Source Attachment -> External File;找到下载的mybatis-3.4.6.zip文件<span style="color:#ff0000;">(maven项目会自动从仓库中绑定源码)<span>  
  
#### 添加DTD模板  
两个文件`mybatis-3-config.dtd` 和 `mybatis-3-mapper.dtd` 在 `mybatis-3.4.6/mybatis-3.4.6/org/apache/ibatis/builder/xml` 文件夹中,将它们拷贝到mybatis-3.4.6目录中;Windows -> Properties,输入xml,选择XML Catalog -> Add,单击File System按钮,选择dtd文件,Key的值,去 `mybatis-3.4.6/mybatis-3.4.6.pdf` 官方文档最后一章中找,当前版本: `-//mybatis.org//DTD Config 3.0//EN` 和 `-//mybatis.org//DTD Mapper 3.0//EN` ,对应config.dtd和mapper.dtd  
  
#### 编写mybatis-config.xml  
选择 resources 文件夹,右击新建 xml,选择 `XML(Basic Templates)`,输入文件名,选择 `Create XML file from a DTD file` 单选按钮,下一步,选择 `Select XML Catalog entry`,在列表中找到 `-//mybatis.org//DTD Config 3.0//EN` ;下一步,勾选最后两项复选框(默认是勾选的),完成!  
配置XML的节点是有顺序的,实在不知道其中顺序,可以在 `mybatis-config.xml` 文件下选择Design视图,右击configuration节点 -> Add Child,指定一个节点插入进去,可以保证节点位置匹配正确  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "mybatis-3-config.dtd" >
<configuration>
    <!-- 引入database.properties文件 -->
    <properties resource="database.properties"/>
     
    <!-- 配置mybatis的log实现Log4j -->
    <settings>
        <setting name="logImpl" value="LOG4J"/>
    </settings>
 
    <!-- 设置别名,mapper.xml中可以直接返回类型名称  -->
    <typeAliases>
        <typeAlias alias="Bill" type="cn.smbms.pojo.Bill"/>
        <typeAlias alias="Provider" type="cn.smbms.pojo.Provider"/>
        <typeAlias alias="Role" type="cn.smbms.pojo.Role"/>
        <typeAlias alias="User" type="cn.smbms.pojo.User"/>
    </typeAliases>
     
    <!-- 配置多套运行环境,environments default指定使用哪一套 -->
    <environments default="development">
        <!-- 第一套运行环境(开发) -->
        <environment id="development">
            <!-- 配置JDBC事务管理 -->
            <transactionManager type="JDBC"/>
            <!-- 配置数据源:POOLED(MyBatis自带) -->
            <dataSource type="POOLED">
                <!-- 参数匹配database.properties文件 -->
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="user" value="${user}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
         
        <!-- 第二套运行环境(部署) -->
        <environment id="deploy">
            <!-- 配置JDBC事务管理 -->
            <transactionManager type="JDBC"/>
            <!-- 配置数据源:POOLED(MyBatis自带) -->
            <dataSource type="POOLED">
                <!-- 参数匹配database.properties文件 -->
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="user" value="${user}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
     
    <!-- 将mapper文件加入到映射中 -->
    <mappers>
        <mapper resource="cn/smbms/dao/bill/BillMapper.xml"/>
        <mapper resource="cn/smbms/dao/provider/ProviderMapper.xml"/>
        <mapper resource="cn/smbms/dao/role/RoleMapper.xml"/>
        <mapper resource="cn/smbms/dao/user/UserMapper.xml"/>
    </mappers>
     
</configuration>
```
mybatis-config核心配置文件目录结构:  
```bash
|configuration #配置
|---properties #可配置在Java属性文件中
|---settings #修改MyBatis在运行时的行为方式
|---typeAliases #为Java类型命名一个别名(简称),用于Mapper.xml文件的返回类型
|---typeHandlers #类型处理器
|---objectFactory #对象工厂
|---plugins #插件
|---environment #环境
|   |---transactionManager #事务管理器
|   |---dataSource #数据源
|---mappers #映射器
```
  
#### 创建POJO实体类  
<details>
<summary>User类</summary>

```java
package cn.smbms.pojo;
 
import java.util.Date;
public class User {
    private Integer id;             //id
    private String userCode;        //用户编码
    private String userName;        //用户名称
    private String userPassword;    //用户密码
    private Integer gender;         //性别
    private Date birthday;          //出生日期
    private String phone;           //电话
    private String address;         //地址
    private Integer userRole;       //用户角色
    private Integer createdBy;      //创建者
    private Date creationDate;      //创建时间
    private Integer modifyBy;       //更新者
    private Date modifyDate;        //更新时间
     
    private  String idPicPath;
    private String workPicPath;
    private Integer age;//年龄
    private String userRoleName;    //用户角色名称
    public Integer getId() {
        return id;
    }
    public void setId(Integer id) {
        this.id = id;
    }
    public String getUserCode() {
        return userCode;
    }
    public void setUserCode(String userCode) {
        this.userCode = userCode;
    }
    public String getUserName() {
        return userName;
    }
    public void setUserName(String userName) {
        this.userName = userName;
    }
    public String getUserPassword() {
        return userPassword;
    }
    public void setUserPassword(String userPassword) {
        this.userPassword = userPassword;
    }
    public Integer getGender() {
        return gender;
    }
    public void setGender(Integer gender) {
        this.gender = gender;
    }
    public Date getBirthday() {
        return birthday;
    }
    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }
    public String getPhone() {
        return phone;
    }
    public void setPhone(String phone) {
        this.phone = phone;
    }
    public String getAddress() {
        return address;
    }
    public void setAddress(String address) {
        this.address = address;
    }
    public Integer getUserRole() {
        return userRole;
    }
    public void setUserRole(Integer userRole) {
        this.userRole = userRole;
    }
    public Integer getCreatedBy() {
        return createdBy;
    }
    public void setCreatedBy(Integer createdBy) {
        this.createdBy = createdBy;
    }
    public Date getCreationDate() {
        return creationDate;
    }
    public void setCreationDate(Date creationDate) {
        this.creationDate = creationDate;
    }
    public Integer getModifyBy() {
        return modifyBy;
    }
    public void setModifyBy(Integer modifyBy) {
        this.modifyBy = modifyBy;
    }
    public Date getModifyDate() {
        return modifyDate;
    }
    public void setModifyDate(Date modifyDate) {
        this.modifyDate = modifyDate;
    }
    public String getIdPicPath() {
        return idPicPath;
    }
    public void setIdPicPath(String idPicPath) {
        this.idPicPath = idPicPath;
    }
    public String getWorkPicPath() {
        return workPicPath;
    }
    public void setWorkPicPath(String workPicPath) {
        this.workPicPath = workPicPath;
    }
    public Integer getAge() {
        return age;
    }
    public void setAge(Integer age) {
        this.age = age;
    }
    public String getUserRoleName() {
        return userRoleName;
    }
    public void setUserRoleName(String userRoleName) {
        this.userRoleName = userRoleName;
    }
    public User(Integer id, String userCode, String userName, String userPassword, Integer gender, Date birthday,
            String phone, String address, Integer userRole, Integer createdBy, Date creationDate, Integer modifyBy,
            Date modifyDate, String idPicPath, String workPicPath, Integer age, String userRoleName) {
        super();
        this.id = id;
        this.userCode = userCode;
        this.userName = userName;
        this.userPassword = userPassword;
        this.gender = gender;
        this.birthday = birthday;
        this.phone = phone;
        this.address = address;
        this.userRole = userRole;
        this.createdBy = createdBy;
        this.creationDate = creationDate;
        this.modifyBy = modifyBy;
        this.modifyDate = modifyDate;
        this.idPicPath = idPicPath;
        this.workPicPath = workPicPath;
        this.age = age;
        this.userRoleName = userRoleName;
    }
    public User() {
        super();
    }
}
```
</details>
  
> Get,Set方法及构造方法,使用快捷键<span style="color:#ff0000;">Shift+Alt+S</span>  
> Get,Set:选择Generate Getters and Setters...命令,勾选Select All复选框,选择public单选按钮,完成  
> 构造方法:选择Generate Constructor using Fields...命令,勾选Select All复选框,选择public单选按钮,完成;再重复一次,勾选Deselect All复选框,完成!那么带参和不带参的构造方法都有了  
  
#### DAO层编写Interface接口  
```java
--------------------------------UserMapper.java-------------------------------
package cn.smbms.dao.user;
import java.sql.Connection;
import java.util.List;
import javax.annotation.Resource;
import org.apache.ibatis.annotations.Param;
import cn.smbms.pojo.User;
 
@Resource
public interface UserMapper {
    //无条件查询记录数
    public int getCount() throws Exception;
}
------------------------------------------------------------------------------
```
  
#### DAO层编写Mapper.xml映射  
```xml
----------------------------------------UserMapper.xml------------------------------------
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "mybatis-3-mapper.dtd" >
<mapper namespace="cn.smbms.dao.user.UserMapper">
         
    <resultMap type="User" id="userList">
        <result column="id" property="id" />
        <result column="roleName" property="roleName" />
        <result column="userCode" property="userCode" />
        <result column="userPassword" property="userPassword" />
        <result column="gender" property="gender" />
        <result column="birthday" property="birthday" />
        <result column="phone" property="phone" />
        <result column="address" property="address" />
        <result column="userRole" property="userRole" />
        <result column="createdBy" property="createdBy" />
        <result column="creationDate" property="creationDate" />
        <result column="modifyBy" property="modifyBy" />
        <result column="modifyDate" property="modifyDate" />
        <result column="idPicPath" property="idPicPath" />
        <result column="workPicPath" property="workPicPath" />
        <result column="age" property="age" />
        <result column="userRoleName" property="userRoleName" />
    </resultMap>
         
    <select id="Count" resultType="int">
        select count(*) as count from smbms_user
    </select>
</mapper>
--------------------------------------------------------------------------------------------
```
编写好之后,记得在mybats-config.xml中配置映射  
```java
----------------------------------------mybatis-config.xml----------------------------------------
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "mybatis-3-config.dtd" >
<configuration>
    <!-- 已省略其他配置信息 -->
 
    <!-- 将mapper文件加入到映射中 -->
    <mappers>
        <mapper resource="cn/smbms/dao/user/UserMapper.xml"/>
    </mappers>
</configuration>
---------------------------------------------------------------------------------------------------
```
  
#### 编写测试代码  
```java
-------------------------------app.java----------------------------------------
package cn.smbms;
import java.io.IOException;
import java.io.InputStream;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.apache.log4j.Logger;
import org.junit.Before;
import org.junit.Test;
 
//使用MyBatis(基础)
public class App{
    private Logger  logger =Logger.getLogger(App.class);
 
    //使用@Test必须引入junit架包
    @Test
    public void Test(){
        String resource="mybatis-config.xml";
        int count = 0;
        SqlSession sqlSession = null;
        try {
            //1 获取mybatis-config.xml的输入流
            InputStream is = Resources.getResourceAsStream(resource);
            //2 创建SqlSessionFactory对象,完成对配置文件的读取
            SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(is);
            //3 创建sqlSession
            sqlSession = factory.openSession();
            //4 调用mapper文件的namespace和子元素的id来找到相应SQL并且执行,必须将mapper文件引入到mybatis-config.xml中
            count = sqlSession.selectOne("smbms.dao.user.UserMapper.Count");
            logger.debug("COUNT等于:---->"+count);
        } catch (IOException e) {
            e.printStackTrace();
        }finally{
            //关闭sqlSession对象
            sqlSession.close();
        }
    }
}
-------------------------------------------------------------------------------
```
MyBatis的核心接口和类:  
<span style="color:#ff9900;">SqlSessionFactoryBuilder</span>  
> 用过即丢,其生命周期只存在于方法体内  
> 可重用其来创建多个SqlSessionFactory实例  
> 负责构建SqlSessionFactory,并提供多个build方法的重载  
  
<span style="color:#ff9900;">SqlSessionFactory</span>  
> SqlSessionFactory是每个MyBatis应用的核心  
> 作用:创建SqlSession实例  
> 作用域:Application  
> 生命周期与应用周期相同  
> 单例模式:存在与整个应用运行时,并且同时只存在一个对象实例  
  
<span style="color:#ff9900;">SqlSession</span>  
> 包含了执行SQL所需要的所有方法  
> 对应一次数据库会话,会话结束必须关闭  
> 线程级别,不能共享  
  
mybatis-config.xml 系统核心配置文件  
mapper.xml SQl映射文件  
  
#### 优化测试代码  
上面代码中,我们通过获取 `mybatis-config.xml` 的输入流;创建 `SqlSessionFactory` 对象,通过 `SqlSessionFactoryBuilder().build()` 方法创建factory工厂;使用工厂创建创建sqlSession;调用sqlSession的相应方法,还要输入namespace.id路径!  
步骤繁琐,代码冗余!我们可以通过创建工具类MyBatisUtil从而简化代码,使其高效可用  
```java
-------------------------------MyBatisUtil.java--------------------------------
package cn.smbms.tools;
import java.io.IOException;
import java.io.InputStream;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
 
public class MyBatisUtil {
    private static SqlSessionFactory factory;
    static{//静态代码块,factory只创建一次
        try {
            InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
            factory = new SqlSessionFactoryBuilder().build(is);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
 
    public static SqlSession creataSqlSession(){
        return factory.openSession(false);//true为自动提交事务
    }
 
    public static void closeSqlSession(SqlSession sqlSession){
        if(null != sqlSession)
            sqlSession.close();//关闭SqlSession
    }
}
-------------------------------------------------------------------------------
```
MyBatisUtil中提供了两个静态方法 `creataSqlSession()` 和 `closeSqlSession()`;那么我们app.java的代码中就不需要获取mybatis-config.xml的输入流及创建SqlSessionFactory对象了,而是可以直接通过MyBatisUtil.creataSqlSession()方法直接得到sqlSession对象,但是我们还是需要使用指定 `namespace.id` 路径进行数据查找,如果我们需要使用sqlSession.getMapper()方式,我们需要创建一个接口类,定义一个接口方法去指定Mapper.xml中的id  
```java
----------------------------UserMapper.java------------------------------------
package cn.smbms.dao.user;
import java.util.List;
import javax.annotation.Resource;
import org.apache.ibatis.annotations.Param;
import cn.smbms.pojo.User;
 
@Resource
public interface UserMapper {
    //查询记录数
    public int getCount() throws Exception;
}
-------------------------------------------------------------------------------
```
<span style="color:#ff0000;">注意:方法名(示例:getCount)必须与UserMapper.xml中的id值一一对应</span>  
```xml
----------------------------------------UserMapper.xml------------------------------------
<select id="getCount" resultType="int">
    select count(*) as count from smbms_user
</select>
--------------------------------------------------------------------------------------------
```
那么就需要把UserMapper.xml这里原来的id="Count"改为id="getCount"  
  
这样,我们就可以使用MyBatisUtil创建和关闭SqlSession,使用getMapper()方式指定Mapper文件id进行数据查找  
```java
-------------------------------app.java----------------------------------------
package cn.smbms;
import java.io.IOException;
import java.io.InputStream;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.apache.log4j.Logger;
import org.junit.Before;
import org.junit.Test;
 
//使用MyBatis(进阶)
public class App{
    private Logger  logger =Logger.getLogger(App.class);
 
    @Test
    public void testCount(){
        int count = 0;
        SqlSession sqlSession = null;
        try {
            //1 创建sqlSession
            sqlSession = MyBatisUtil.creataSqlSession();
            //2 使用getMapper方式,需要先创建interface方法,且interface方法名和mapper.xml中id保持一致
            count = sqlSession.getMapper(UserMapper.class).getCount();
            logger.debug("COUNT等于:---->"+count);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        } finally{
            //3 关闭sqlSession对象
            MyBatisUtil.closeSqlSession(sqlSession);
        }
    }
}
-------------------------------------------------------------------------------
```
  
#### 目录结构  
mybatis-config.xml目录结构如下：
```
|configuration
|---properties
|   |---property
|---settings
|   |---setting
|---typeAliases
|---typeHandlers
|---objectFactory
|---plugins
|---environment
|   |---transactionManager
|   |---dataSource
|---mappers
```
  
#### properties元素  
作用:描述外部可替代属性,获取属性有两种方法  
  
方法一:通过外部指定方式(database.properties)实现配置  
```xml
<!-- 引入database.properties文件 -->
<properties resource="database.properties"/>
 
......省略其他配置
 
<!-- 配置数据源:POOLED(MyBatis自带) -->
<dataSource type="POOLED">
    <!-- 参数匹配database.properties文件 -->
    <property name="driver" value="${driver}"/>
    <property name="url" value="${url}"/>
    <property name="username" value="${username}"/>
    <property name="password" value="${password}"/>
</dataSource>
```
  
方法二:直接配置为XML,实现动态配置  
```xml
<properties>
    <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://127.0.0.1:3306/smbms?useUnicode=true&characterEncoding=utf-8"/>
    <property name="username" value="root"/>
    <property name="password" value="123456"/>
</properties>
```
<span style="color:#ff0000;">优先级:方法一 > 方法二</span>  
原因:resource中的属性值会覆盖property元素的值  
  
#### settings元素  
作用:设置一些非常重要的设置选项,用于设置和改变MyBatis运行中的行为  
  
| 设置项 | 描述 | 允许值 | 默认值 |
| ------ | ------ | ------ | ------ |
| cacheEnabled | 对在此配置下的所有cache进行全局性开/关设置 | ture/false | ture |
| lazyLoadingEnabled |  全局性设置延迟加载,如果设置为false,则所有相关联的都会被初始化加载 | ture/false | ture |
| autoMappingBehavior | MyBatis对于resultMap自动映射的匹配级别 | NONE/PARTIAL/FULL | PARTIAL |
| ...其他省略(共9个) | ... | ... | ... |
  
配置方式:  
```xml
<settings>
    <setting name="logImpl" value="LOG4J"/>
</settings>
```
  
#### typeAliases元素  
作用:仅仅关联XML配置,简写冗长的Java类名,方便Mapper.xml使用  
配置方式:  
方法一:单独指定,适用于少量javabean  
```xml
<typeAliases>
    <typeAlias alias="Bill" type="cn.smbms.pojo.Bill"/>
    <typeAlias alias="Provider" type="cn.smbms.pojo.Provider"/>
    <typeAlias alias="Role" type="cn.smbms.pojo.Role"/>
    <typeAlias alias="User" type="cn.smbms.pojo.User"/>
</typeAliases>
```
方法二:指定包名,适用大量javabean,别名就是类名  
```xml
<typeAliases>
    <package name="cn.smbms.pojo"/>
</typeAliases>
```
<span style="color:#ff0000;">如果没有配置,那么在Mapper.xml中,resultType返回类型就需要写长路径</span>  
```xml
-------------------UserMapper.xml(未配置)-----------------------
<select id="getCount" resultType="cn.smbms.pojo.User">
    select * from smbms_user
</select>
---------------------------------------------------------------
```
配置后直接填别名或类名即可  
```xml
-------------------UserMapper.xml(已配置)-----------------------
<select id="getCount" resultType="User">
    select * from smbms_user
</select>
---------------------------------------------------------------
```
  
#### environment元素  
作用:用于配置多套运行环境,将SQl映射到不同数据库;一个environment子元素为一套运行环境,多套运行环境需要在default属性中指定子元素id名称,表示默认使用此运行环境  
```xml
<environments default="development">
    <!-- 第一套运行环境(开发) -->
    <environment id="development">
        <!-- 配置JDBC事务管理(JDBC/MANAGED) -->
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <property name="driver" value="${driver}"/>
            <property name="url" value="${url}"/>
            <property name="username" value="${username}"/>
            <property name="password" value="${password}"/>
        </dataSource>
    </environment>
 
    <!-- 第二套运行环境(测试) -->
    <environment id="test">
    ......省略
    </environment>
</environments>
```
子元素transactionManager(事务管理):  
* JDBC:使用JDBC提交和回滚功能,依赖于从数据源获得连接来管理事务的生命周期  
* MANAGED:几乎不执行任何操作,它永远不会提交或回滚连接;相反,它允许容器管理事务的整个生命周期  
  
子元素dataSource(数据源):  
* 作用:使用基本的JDBC数据源接口类配置JDBC连接对象的资源  
* 类型:  
    * UNPOOLED:只在每次请求时打开和关闭连接,有点慢,适用于不需要立即连接性能的简单应用程序  
    * POOLED:将JDBC Connection对象池化,以避免创建新Connection实例所需的初始连接和身份验证时间,这是并发Web应用程序实现最快响应的流行方法  
    * JNDI:此实现旨在与EJB或Application Server等容器一起使用,这些容器可以集中或外部配置DataSource,并在JNDI上下文中对其进行引用
  
#### mappers元素  
作用:定义SQL映射语句  
配置方式:  
方式一:使用类资源路径  
```xml
<mappers>
    <mapper resource="cn/smbms/dao/user/UserMapper.xml"/>
</mappers>
```
方式二:使用完全限定资源定位符（URL）  
```xml
<mappers>
    <mapper url="file:///var/mappers/AuthorMapper.xml"/>
</mappers>
```
方式三:使用映射器接口实现类的完全限定类名  
```xml
<mappers>
    <mapper class="org.mybatis.builder.AuthorMapper"/>
</mappers>
```
方式四:将包内的映射器接口实现全部注册为映射器  
```xml
<mappers>
    <package name="org.mybatis.builder"/>
</mappers>
```
  
#### 深入映射文件Mapper.xml  
特点:专注于SQl,功能强大,映射配置却相当简单  
顶级元素:  
* <span style="color:#ff9900;">cache</span> 为给定命名空间配置缓存  
* <span style="color:#ff9900;">cache-ref</span> 从另一个命名空间引用缓存配置  
* <span style="color:#ff9900;">resultMap</span> 描述如何从数据库结果集加载对象的最复杂和最强大的元素  
* <span style="color:#ff9900;">sql</span> 可重用的SQL块，可以被其他语句引用  
* <span style="color:#ff9900;">insert</span> 映射的INSERT语句  
* <span style="color:#ff9900;">update</span> 映射的UPDATE语句  
* <span style="color:#ff9900;">delete</span> 映射的DELETE语句  
* <span style="color:#ff9900;">select</span> 映射的SELECT语句  
  
#### seslect元素  
属性:  
* id:命名空间唯一标识符,接口方法与Mapper文件的SQL语句id一一对应  
* parameterType:传入SQL语句的参数类型的完美限定名或别名  
    * 基础数据类型:  
        * int,String,Date  
        * 只能传入一个,通过 `#{参数名}` 即可获取传入的值  
    * 复杂数据类型:  
        * Java实体类,Map等  
        * 通过 `#{属性名}` 或者 `#{Map的Key}` 即可获取传入的值  
* resultType:SQl语句返回值类型(类名/别名)  
  
#### 单条件查询  
> 示例:根据用户id查询用户信息  
  
```xml
------------------------------Mapper.xml-------------------------------------
<select id="getUserListByUserName" parameterType="String" resultType="User">
    select * from smbms_user where userName like concat ('%',#{userName},'%')
</select>
-----------------------------------------------------------------------------
```
说明:Mapper.xml中SQl语句拼接String参数,需要使用concat()方式,传参格式为#{},并且参数名称(示例:userName)需要和接口类的方法参数或@Param()值相同  
```java
---------------------------------------UserMapper.java---------------------------------------
public List<User> getUserListByUserName(@Param("userName") String userName) throws Exception;
---------------------------------------------------------------------------------------------
```
说明:@Param()配置参数别名,用于多参数入参,Mapper.xml中可以直接使用#{参数别名}  
```java
--------------------------------TeatUserMapper.java----------------------------------
@Test
public void testGetUserListByUserName(){
    List<User> userlist= null;
    SqlSession sqlSession = null;
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        userlist = sqlSession.getMapper(UserMapper.class).getUserListByUserName("明");
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    for(User user:userlist){
        logger.debug(user.getAddress()+"\t"+user.getUserName());
    }
}
--------------------------------------------------------------------------------------
```
  
#### 多条件查询  
<span style="color:#ff0000;">方法一:封装成Map对象进行入参</span>  
> 示例:根据Map查询用户信息  
  
```xml
-------------------------------------------Mapper.xml-------------------------------------------------
<select id="getUserListByMap" resultType="User" parameterType="Map">
    select * from smbms_user where userName like concat ('%',#{userName},'%') and userRole=#{userRole}
</select>
------------------------------------------------------------------------------------------------------
```
```java
-------------------------------UserMapper.java-----------------------------------
public List<User> getUserListByMap(Map<String,String> userMap) throws Exception;
---------------------------------------------------------------------------------
```
```java
--------------------------------TeatUserMapper.java---------------------------------
@Test
public void testGetUserListByMap(){
    Map<String,String> userMap=new HashMap<String, String>();
    userMap.put("userName", "明");
    userMap.put("userRole", "2");
    List<User> userlist= null;
    SqlSession sqlSession = null;
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        userlist = sqlSession.getMapper(UserMapper.class).getUserListByMap(userMap);
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    for(User _user:userlist){
        logger.debug(_user.getAddress()+"\t"+_user.getUserName());
    }
}
-------------------------------------------------------------------------------------
```
<span style="color:#ff0000;">方法二:封装成User对象进行入参</span>  
> 示例:根据封装的用户名称及用户类型查询用户信息  
  
```xml
------------------------------------------Mapper.xml---------------------------------------------------
<select id="getUserListByUser" resultType="User" parameterType="User">
    select * from smbms_user where userName like concat ('%',#{userName},'%') and userRole=#{userRole}
</select>
-------------------------------------------------------------------------------------------------------
```
```java
----------------------------UserMapper.java--------------------------------
public List<User> getUserListByUser(User user) throws Exception;
---------------------------------------------------------------------------
```
```java
----------------------------------TeatUserMapper.java--------------------------------
public void testGetUserListByUser(){
    User user = new User();
    user.setUserName("明");
    user.setUserRole(2);
    List<User> userlist= null;
    SqlSession sqlSession = null;
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        userlist = sqlSession.getMapper(UserMapper.class).getUserListByUser(user);
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    for(User _user:userlist){
        logger.debug(_user.getAddress()+"\t"+_user.getUserName());
    }
}
-------------------------------------------------------------------------------------
```
  
#### 使用resultMap连表查询  
> 示例:按条件查询用户列表,而用户表有外键userRole字段  
  
<span style="color:#ff0000;">方法一:修改User.java实体类,增加userRoleName属性</span>
```java
---------------------User.java---------------------
private String userRoleName;
 
public String getUserRoleName() {
    return userRoleName;
}
public void setUserRoleName(String userRoleName) {
    this.userRoleName = userRoleName;
}
---------------------------------------------------
```
```java
-------------------------UserMapper.java-------------------------
public List<User> getUserList(User user) throws Exception;
-----------------------------------------------------------------
```
```xml
----------------------------UserMapper.xml-------------------------------
<select id="getUserList" resultType="User" parameterType="User">
    select u.*,r.roleName as userRoleName from smbms_user u,smbms_role r
     where userName like concat ('%',#{userName},'%')
      and userRole=#{userRole} and u.userRole=r.id
</select>
-------------------------------------------------------------------------
```
```java
----------------------------------TeatUserMapper.java--------------------------------
@Test
public void testGetUserList(){
    User user = new User();
    user.setUserName("明");
    user.setUserRole(2);
    List<User> userlist= null;
    SqlSession sqlSession = null;
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        userlist = sqlSession.getMapper(UserMapper.class).getUserList(user);
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    for(User _user:userlist){
        logger.debug(_user.getAddress()+"\t"+_user.getUserName()+"\t"+_user.getUserRoleName());
    }
}
-------------------------------------------------------------------------------------
```
  
<span style="color:#ff0000;">方法二:使用resultMap自定义映射结果(推荐)</span>  
```xml
-----------------------------UserMapper.xml-----------------------------
<resultMap type="User" id="userList">
    <result column="id" property="id" />
    <result column="roleName" property="roleName" />
    <result column="address" property="address" />
    <result column="userRole" property="userRole" />
 
    <result column="userRoleName" property="userRoleName" />
</resultMap>
 
<select id="getUserList" resultMap="userList" parameterType="User">
    select u.*,r.roleName as userRoleName from smbms_user u,smbms_role r
     where userName like concat ('%',#{userName},'%')
      and userRole=#{userRole} and u.userRole=r.id
</select>
-------------------------------------------------------------------------
```
select属性的返回就不是resultType,而是resultMap,值对应resultMap的id属性  
其他地方不变,执行方法一中的测试方法testGetUserList()即可  
  
resultMap节点属性说明:  
* <span style="color:#ff9900;">resultMap</span>:描述如何将结果集映射到Java对象(属性名和字段名不一致的情况下使用)  
* <span style="color:#ff9900;">type</span>:pojo类名  
* <span style="color:#ff9900;">id</span>:resultMap别名  
* <span style="color:#ff9900;">column</span>:数据库字段名  
* <span style="color:#ff9900;">property</span>:pojo实体类属性名  
  
<span style="color:#ff0000;">比较resultType和resultMap</span>  
<span style="color:#ff9900;">resultType</span>:直接表示返回类型  
* 基础数据类型  
* 复杂数据类型  
  
<span style="color:#ff9900;">resultMap</span>:对外部resultMap的引用  
* 应用场景  
    * 数据库字段信息与对象属性不一致  
    * 复杂的联合查询,自由控制映射结果(自由创建和删除result)  
  
<span style="color:#ff0000;">注意:二者不能同时存在,本质上都是Map数据结构</span>  
  
> 示例:使用resultMap如何实现自由灵活的控制映射结果,从而达到只对关心的属性进行赋值?  
  
目前,我们在"方法二:使用resultMap自定义映射结果"中,UserMapper.xml中只有id roleName address userRole userRoleName五个属性  
  
在User.java新增年龄字段age,注意:数据库中没有age字段  
```java
-----------------------User.java-------------------------
private Integer age;//新增年龄
 
public Integer getAge() {
    Date date = new Date();
    Integer age = date.getYear()-birthday.getYear();
    return age;
}
---------------------------------------------------------
```
再修改测试方法  
```java
----------------------------------TeatUserMapper.java--------------------------------
@Test
public void testGetUserList(){
    User user = new User();
    user.setUserName("明");
    user.setUserRole(2);
    List<User> userlist= null;
    SqlSession sqlSession = null;
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        userlist = sqlSession.getMapper(UserMapper.class).getUserList(user);
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    for(User _user:userlist){
        //新增输出age的值
        logger.debug(_user.getAddress()+"\t"+_user.getUserName()+"\t"
        +_user.getUserRoleName()+"\t"+_user.getAge());
    }
}
-------------------------------------------------------------------------------------
```
运行结果如下  
```bash
------------------------------------------------------运行结果-------------------------------------------------
2018-09-18 15:14:41,361 [main] DEBUG [cn.smbms.test.user.TeatUserMapper] - 北京市东城区前门东大街9号    李明  经理  35
--------------------------------------------------------------------------------------------------------------
```
  
明明没有在resultMap中配置age,为什么还是会输出呢?这里就要说下resultMap自动映射级别了  

resultMap自动匹配前提:字段名与属性名一致  
  
resultMap的自动映射级别<span style="color:#ff9900;">(autoMappingBehavior)</span>    
> <span style="color:#ff9900;">PARTIAL(默认)</span>:自动匹配所有属性,有内部嵌套(association,collection)的除外  
> <span style="color:#ff9900;">NONE</span>:禁止自动匹配  
> <span style="color:#ff9900;">FULL</span>:自动匹配所有  
  
所以,age我们虽然没匹配,不过在getAge()方法中,birthday属性是数据中有的,通过默认自动映射进去了,从而计算出age的值,如果我们不想让他默认自动映射,就需要去mybatis-config.xml中配置自动映射级别  
```xml
--------------------mybatis-config.xml-----------------
<settings>
    <setting name="autoMappingBehavior" value="NONE"/>
</settings>
-------------------------------------------------------
```
  
#### 使用MyBatis增删改查  
##### insert语句  
> 示例:新增用户信息  
  
```java
--------------UserMapper.java---------------
public int add(User user) throws Exception;
--------------------------------------------
```
```xml
-----------------------------------------------------------UserMapper.xml-------------------------------------------------------------
<insert id="add"  parameterType="User">
    INSERT INTO smbms_user                                                             
    (userCode,userName,userPassword,gender,birthday,phone,address,userRole,createdBy,`creationDate`)
    VALUES(#{userCode},#{userName},#{userPassword},#{gender},#{birthday},#{phone},#{address},#{userRole},#{createdBy},#{creationDate})
 </insert>
---------------------------------------------------------------------------------------------------------------------------------------
```
```java
----------------------------------TeatUserMapper.java--------------------------------
@Test
public void testAdd(){
    SqlSession sqlSession = null;
    int count =0;
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        User user = new User();
        user.setUserCode("teaper");
        user.setUserName("茶巴");
        user.setUserPassword("1234567");
        Date birthday=new SimpleDateFormat("yyyy-MM-dd").parse("2017-5-6");
        user.setBirthday(birthday);
        user.setAddress("测试新增用户");
        user.setGender(1);
        user.setPhone("14588345567");
        user.setUserRole(1);
        user.setCreatedBy(1);
        user.setCreationDate(new Date());
        count = sqlSession.getMapper(UserMapper.class).add(user);
        //模拟异常,进行回滚
        sqlSession.commit();
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
        sqlSession.rollback();
        count=0;
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    logger.debug("是否添加成功------>"+count);
}
-------------------------------------------------------------------------------------
```
上面我们使用了回滚,因为我们在MyBatisUtil.java中开启了事务控制  
factory.openSession(false); //true为自动提交事务  
  
注意:增删改(insert,delete,update)操作数据库时,需要注意两点  
> 增删改操作返回执行SQL影响的行数(int)  
> insert,delete,update元素中没有resultType属性,只有select才有resultType/resultMap属性  
  
##### update语句  
> 示例:根据用户ID修改用户信息  
  
```java
--------------UserMapper.java---------------
public int modify(User user) throws Exception;
--------------------------------------------
```
```xml
------------------------------------------------UserMapper.xml-------------------------------------------------------
<update id="modify" parameterType="User">
    UPDATE smbms_user set userCode=#{userCode},userName=#{userName},userPassword=#{userPassword},gender=#{gender},
    birthday=#{birthday},phone=#{phone},address=#{address},userRole=#{userRole},modifyBy=#{modifyBy},
    modifyDate=#{modifyDate} where id=#{id}
</update>
---------------------------------------------------------------------------------------------------------------------
```
```java
----------------------------------TeatUserMapper.java--------------------------------
@Test
public void testModify(){
    SqlSession sqlSession = null;
    int count =0;
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        User user = new User();
        user.setId(16);
        user.setUserCode("teaper");
        user.setUserName("湘桥月");
        user.setUserPassword("1234567");
        Date birthday=new SimpleDateFormat("yyyy-MM-dd").parse("2017-5-6");
        user.setBirthday(birthday);
        user.setAddress("测试修改用户");
        user.setGender(1);
        user.setPhone("1458ee45567");
        user.setUserRole(1);
        user.setModifyBy(1);
        user.setModifyDate(new Date());
        count = sqlSession.getMapper(UserMapper.class).modify(user);
        //模拟异常,进行回滚
        sqlSession.commit();
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
        sqlSession.rollback();
        count=0;
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    logger.debug("是否修改成功------>"+count);
}
-------------------------------------------------------------------------------------
```
  
##### delete语句  
> 示例:根据用户ID删除用户  
  
```java
--------------UserMapper.java---------------
public int modify(User user) throws Exception;
--------------------------------------------
```
```xml
------------------------------------------------UserMapper.xml-------------------------------------------------------
<update id="modify" parameterType="User">
    UPDATE smbms_user set userCode=#{userCode},userName=#{userName},userPassword=#{userPassword},gender=#{gender},
    birthday=#{birthday},phone=#{phone},address=#{address},userRole=#{userRole},modifyBy=#{modifyBy},
    modifyDate=#{modifyDate} where id=#{id}
</update>
---------------------------------------------------------------------------------------------------------------------
```
```java
----------------------------------TeatUserMapper.java--------------------------------
@Test
public void testModify(){
    SqlSession sqlSession = null;
    int count =0;
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        User user = new User();
        user.setId(16);
        user.setUserCode("teaper");
        user.setUserName("湘桥月");
        user.setUserPassword("1234567");
        Date birthday=new SimpleDateFormat("yyyy-MM-dd").parse("2017-5-6");
        user.setBirthday(birthday);
        user.setAddress("测试修改用户");
        user.setGender(1);
        user.setPhone("1458ee45567");
        user.setUserRole(1);
        user.setModifyBy(1);
        user.setModifyDate(new Date());
        count = sqlSession.getMapper(UserMapper.class).modify(user);
        //模拟异常,进行回滚
        sqlSession.commit();
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
        sqlSession.rollback();
        count=0;
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    logger.debug("是否修改成功------>"+count);
}
-------------------------------------------------------------------------------------
```
  
##### delete语句  
示例:根据用户ID删除用户  
```java
----------------------UserMapper.java-------------------------
public int deleteUserById(@Param("id")int id)throws Exception;
--------------------------------------------------------------
```
```xml
----------------UserMapper.xml-------------
<delete id="deleteUserById">
    delete from smbms_user where id=#{id}
</delete>
-------------------------------------------
```
```java
----------------------------------TeatUserMapper.java--------------------------------
@Test
public void testDeleteUserById(){
    SqlSession sqlSession = null;
    int count =0;
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        count = sqlSession.getMapper(UserMapper.class).deleteUserById(16);
        //模拟异常,进行回滚
        sqlSession.commit();
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
        sqlSession.rollback();
        count=0;
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    logger.debug("是否删除成功------>"+count);
}
-------------------------------------------------------------------------------------
```
  
#### select高级结果映射  
<span style="color:#B900ff;">association</span>:<span style="color:#ff0000;">一对一</span>  
* 复杂数据类型关联:比如User(从)类中嵌套Role(主)对象  
* 内部嵌套  
    * 映射一个嵌套JavaBean属性
* 属性  
    * <span style="color:#ff9900;">property</span>:映射数据库列的实体对象的属性  
    * <span style="color:#ff9900;">javaType</span>:完整Java类名或者别名  
    * <span style="color:#ff9900;">resultMap</span>:引用外部resultMap  
* 子元素  
    * <span style="color:#ff9900;">id</span>  
    * <span style="color:#ff9900;">result</span>  
        * <span style="color:#ff9900;">property</span>:映射数据库列的实体对象的属性  
        * <span style="color:#ff9900;">column</span>:数据库列名或者别名  
  
> 示例:根据用户ID查询用户信息(association)  

注释掉userRoleName属性以及它的get,set方法  
```java
--------------------User.java--------------------------
//private String userRoleName;    //用户角色名称
//  public String getUserRoleName() {
//      return userRoleName;
//  }
//  public void setUserRoleName(String userRoleName) {
//      this.userRoleName = userRoleName;
//  }
 
private Role role; //添加角色role
public Integer getId() {
    return id;
}
public Role getRole() {
    return role;
}
-------------------------------------------------------
```
```java
------------------------------------UserMapper.java---------------------------------------
public List<User> getUserListByRoleId(@Param("userRole") Integer roleId) throws Exception;
------------------------------------------------------------------------------------------
```
```xml
----------------------------------------------------------UserMapper.xml-------------------------------------------------------------
<!-- 新增resultMap -->
<resultMap type="User" id="userRoleResult">
    <result column="id" property="id" />
    <result column="userCode" property="userCode" />
    <result column="userName" property="userName" />
    <result column="userRole" property="userRole" />
    <!-- 新增association节点 -->
    <association property="role" javaType="Role">
        <id column="a_id" property="id" />
        <result column="roleCode" property="roleCode" />
        <result column="roleName" property="roleName" />
    </association>
</resultMap>
<select id="getUserListByRoleId" resultMap="userList" parameterType="Integer">
    select u.* ,r.id,r.roleCode,r.roleName from smbms_user u ,smbms_role r where u.userRole=#{userRole} and u.userRole = r.id  
</select>
-------------------------------------------------------------------------------------------------------------------------------------
```
```java
                                ----------------------------------TeatUserMapper.java--------------------------------
@Test
public void testGetUserListByRoleId(){
    List<User> userlist= null;
    SqlSession sqlSession = null;
    Integer roleId=2;
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        userlist = sqlSession.getMapper(UserMapper.class).getUserListByRoleId(roleId);
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    for(User _user:userlist){
        logger.debug(_user.getUserName()+"\t"+_user.getRole().getId()+"\t"
    +_user.getRole().getRoleCode()+"\t"+_user.getRole().getRoleName());
    }
}
-------------------------------------------------------------------------------------
```
如果我们希望association的role结果映射可以复用,那么我们可以使用association提供的另一个属性resultMap进行扩展  
  
把association中内容抽调出来做个resultMap,通过association的resultMap属性引用  
```xml
----------------------------------------------------------UserMapper.xml-------------------------------------------------------------
<resultMap type="User" id="userRoleResult">
    <result column="id" property="id" />
    <result column="userCode" property="userCode" />
    <result column="userName" property="userName" />
    <result column="userRole" property="userRole" />
    <!-- association引用外部resultMap -->
    <association property="role" javaType="Role" resultMap="roleResult" />
</resultMap>
<!-- 把association中内容抽调出来做个resultMap -->
<resultMap type="Role" id="roleResult">
    <id column="a_id" property="id" />
    <result column="roleCode" property="roleCode" />
    <result column="roleName" property="roleName" />
</resultMap>
 
<!-- select语句不变 -->
<select id="getUserListByRoleId" resultMap="userList" parameterType="Integer">
    select u.* ,r.id,r.roleCode,r.roleName from smbms_user u ,smbms_role r where u.userRole=#{userRole} and u.userRole = r.id  
</select>
-------------------------------------------------------------------------------------------------------------------------------------
```
<span style="color:#B900ff;">collection</span>:<span style="color:#ff0000;">一对多</span>  
* 复杂类型集合:比如User(主)类中嵌套Address(从)集合类  
* 内部嵌套  
    * 映射一个嵌套结果集到一个列表  
* 属性  
    * <span style="color:#ff9900;">property</span>:映射数据库列的实体对象的属性  
    * <span style="color:#ff9900;">fType</span>:完整Java类名或者别名(集合所包括的类型)  
    * <span style="color:#ff9900;">resultMap</span>:引用外部resultMap  
* 子元素  
* <span style="color:#ff9900;">id</span>  
* <span style="color:#ff9900;">result</span>  
    * <span style="color:#ff9900;">property</span>:映射数据库列的实体对象的属性  
    * <span style="color:#ff9900;">column</span>:数据库列名或者别名  
  
> 示例:获取指定用户的相关信息和地址列表(collection)  
  
pojo实体类添加Address.java  
```java
---------------------------Address.java--------------------------
public class Address {
    private Integer id;             //id
    private String postCode;        //邮编
    private String contact;         //联系人
    private String addressDesc;     //地址
    private String tel;             //联系电话
    private Integer createdBy;      //创建者
    private Date creationDate;      //创建时间
    private Integer modifyBy;       //更新者
    private Date modifyDate;        //更新时间
    private Integer userId;         //用户Id
    //省略getter和setter方法
}
-----------------------------------------------------------------
```
```java
----------------------------User.java----------------------------
private List<Address> addressList;//用户地址列表
public List<Address> getAddressList() {
    return addressList;
}
public void setAddressList(List<Address> addressList) {
    this.addressList = addressList;
}
-----------------------------------------------------------------
```
```java
----------------------------UserMapper.java-----------------------------
public List<User> getAddressListByUserId(@Param("id")Integer userId);
------------------------------------------------------------------------
```
```xml
-------------------------------MyBatis-config.xml--------------------------
<typeAliases>
    <!-- 新增Address类映射 -->
    <typeAlias alias="Address" type="cn.smbms.pojo.Address"/>
</typeAliases>
---------------------------------------------------------------------------
```
```xml
-------------------------------------UserMapper.xml---------------------------------------
<resultMap type="User" id="userAddressResult">
    <result column="id" property="id" />
    <result column="userCode" property="userCode" />
    <result column="userName" property="userName" />
    <!-- 新增collection节点 -->
    <collection property="addressList" ofType="Address">
        <id column="a_id" property="id" />
        <result column="postCode" property="postCode" />
        <result column="tel" property="tel" />
        <result column="contact" property="contact" />
        <result column="addressDesc" property="addressDesc" />
    </collection>
</resultMap>
<select id="getAddressListByUserId" resultMap="userAddressResult" parameterType="Integer">
    select u.*,a.id as a_id ,a.contact,a.addressDesc,a.postCode,a.tel
    from smbms_user u,smbms_address a where u.id =a.userId and  u.id=#{id}
</select>
-------------------------------------------------------------------------------------------
```
```java
----------------------------------TeatUserMapper.java--------------------------------
@Test
public void testGetAddressListByUserId(){
    List<User> userlist= null;
    SqlSession sqlSession = null;
    Integer userId=1;
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        userlist = sqlSession.getMapper(UserMapper.class).getAddressListByUserId(userId);
    } catch (Exception e) {
        e.printStackTrace();
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    for(User _user:userlist){
        logger.debug(_user.getUserName()+"\t"+_user.getUserCode());
        for(Address address:_user.getAddressList()){
            logger.debug(address.getId()+"\t"+address.getContact()+"\t"+address.getAddressDesc()
            +"\t"+address.getTel()+"\t"+address.getPostCode());
        }
    }
}
-------------------------------------------------------------------------------------
```
同理,如果我们希望collection的Address结果映射可以复用,那么我们可以使用collection提供的resultMap属性进行扩展  
  
把collection中内容抽调出来做个resultMap,通过collection的resultMap属性引用  
```xml
-------------------------------------UserMapper.xml---------------------------------------
<resultMap type="User" id="userAddressResult">
    <result column="id" property="id" />
    <result column="userCode" property="userCode" />
    <result column="userName" property="userName" />
    <!-- 新增collection节点 -->
    <collection property="addressList" ofType="Address" resultMap="addressResult"/>
</resultMap>
<!-- 把collection中内容抽调出来做个resultMap -->
<resultMap type="Address" id="addressResult">
    <id column="a_id" property="id" />
    <result column="postCode" property="postCode" />
    <result column="tel" property="tel" />
    <result column="contact" property="contact" />
    <result column="addressDesc" property="addressDesc" />
</resultMap>
<!-- SQL语句不变 -->
<select id="getAddressListByUserId" resultMap="userAddressResult" parameterType="Integer">
    select u.*,a.id as a_id ,a.contact,a.addressDesc,a.postCode,a.tel
    from smbms_user u,smbms_address a where u.id =a.userId and  u.id=#{id}
</select>
-------------------------------------------------------------------------------------------
```
  
#### MyBatis缓存  
一级缓存:基于PerpetualCache(MyBatis自带)的HashMap本地缓存  
作用范围:session域内(当session被flush/close后,缓存清空)  
  
二级缓存:global caching;超出session范围,可以被SqlSession共享  
  
<span style="color:#ff0000;">注意:一级缓存缓存的是SQL语句,二级缓存缓存的是结果对象</span>  
  
##### 二级缓存配置  
<span style="color:#ff0000;">方法一:在mybatis-config.xml中配置全局cache</span>  
```xml
-------------------------mybatis-config.xml--------------------------
<settings>
    <setting name="cacheEnabled" value="true"/>
</settings>
---------------------------------------------------------------------
```
  
<span style="color:#ff0000;">方法二:在Mapper.xml中配置(默认:未开启)</span>  
```xml
-----------------------------mybatis-config.xml-------------------------------
<mapper namespace="cn.smbms.dao.user.UserMapper">
    <cache eviction="FIFIO" flushInterval="60000" size="512" readOnly="true"/>
</mapper>
------------------------------------------------------------------------------
```
在Mapper文件支持cache后,如果需要对个别查询进行调整,可以单独设置cache  
```xml
-----------------------------------------------UserMapper.xml----------------------------------------------
<!-- select节点中设置userCache属性为true即可 -->
<select id="getAddressListByUserId" resultMap="userAddressResult" parameterType="Integer" userCache="true">
    select u.*,a.id as a_id ,a.contact,a.addressDesc,a.postCode,a.tel
    from smbms_user u,smbms_address a where u.id =a.userId and  u.id=#{id}
</select>
-----------------------------------------------------------------------------------------------------------
```
  
#### 动态SQL  
基于OGNL表达式  
使用动态SQL完成多条件查询等逻辑实现  
动态SQL元素:  
* <span style="color:#ff9900;">if</span>:利用if实现简单的条件选择  
* <span style="color:#ff9900;">choose</span>:相当于Java中的switch语句,通常与where和otherwise搭配  
    * <span style="color:#ff9900;">where</span>  
    * <span style="color:#ff9900;">otherwise</span>  
* <span style="color:#ff9900;">where</span>:简化SQL语句中的where条件判断  
* <span style="color:#ff9900;">set</span>:解决动态更新语句  
* <span style="color:#ff9900;">trim</span>:可以灵活的去除多余关键字  
* <span style="color:#ff9900;">foreach</span>:迭代一个集合,通常用于in条件  
  
#### 多条件查询  
<span style="color:#ff0000;">方法一:if+where多条件查询</span>  
  
示例:改造查询用户列表示例,增加查询条件(用户角色id,用户名称"模糊查询")  
```java
----------------------User.java------------------------
private Integer userRole;       //用户角色
private String userRoleName;    //用户角色名称
//省略其getter/setter方法
-------------------------------------------------------
```
```java
-----------------------------------------------UserMapper.java-------------------------------------------------------
public List<User> getUserList(@Param("userName") String userName,@Param("userRole") Integer RoleId) throws Exception;
---------------------------------------------------------------------------------------------------------------------
```
```xml
----------------------------UserMapper.xml------------------------------
<select id="getUserList" resultMap="userList">
    select u.*,r.roleName as userRoleName from smbms_user u,smbms_role r
     where u.userRole=r.id
     <if test="userRole != null">
        and u.userRole=#{userRole}
     </if>
     <if test="userName !=null and userName !=''">
        and u.userName like concat ('%',#{userName},'%')
     </if>
</select>
------------------------------------------------------------------------
```
```java
----------------------------------TeatUserMapper.java--------------------------------
@Test
public void testGetUserList(){
    List<User> userlist= null;
    SqlSession sqlSession = null;
    //不论userName/RoleId设置为null还是"",都可以查询出相应结果
    String userName="明";
    Integer RoleId=null;
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        userlist = sqlSession.getMapper(UserMapper.class).getUserList(userName,RoleId);
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    for(User _user:userlist){
        logger.debug(_user.getAddress()+"\t"+_user.getUserName()+"\t"
        +_user.getUserRoleName()+"\t"+_user.getAge());
    }
}
-------------------------------------------------------------------------------------
```
从 `UserMapper.xml` 中可以发现,我们的where条件中是设置了`"where u.userRole=r.id"`,所以语句拼接没有问题,如果想让他自动控制where条件的and和or,我们可以使用where节点,改造上面的SQL  
```xml
----------------------------UserMapper.xml------------------------------
<select id="getUserList" resultMap="userList">
    select u.*,r.roleName as userRoleName from smbms_user u,smbms_role r
     <where>
        <if test="userName !=null and userName !=''">
            and u.userName like concat ('%',#{userName},'%')
        </if>
        <if test="userRole != null">
            and u.userRole=#{userRole}
        </if>
     </where>
</select>
------------------------------------------------------------------------
```
  
<span style="color:#ff0000;">方法二:if+trim多条件查询</span>  
  
trim属性:  
* <span style="color:#ff9900;">prefix</span>:前缀,作用是通过自动识别是否有返回值  
* <span style="color:#ff9900;">suffix</span>:后缀,作用是在trim包含的内容上加上后缀  
* <span style="color:#ff9900;">prefixOverrides</span>:对于trim内容的首部进行指定内容(例如:and | or)的忽略  
* <span style="color:#ff9900;">suffixOverrides</span>:对于trim包含内容的首尾部进行指定内容的忽略  
  
更灵活的去除多余关键字(where,and,or)  

替代where  
```xml
----------------------------UserMapper.xml------------------------------
<select id="getUserList" resultMap="userList">
    select u.*,r.roleName as userRoleName from smbms_user u,smbms_role r
     <trim prefix="where" prefixOverrides="and | or">
        <if test="userName !=null and userName !=''">
            and u.userName like concat ('%',#{userName},'%')
        </if>
        <if test="userRole != null">
            and u.userRole=#{userRole}
        </if>
     </trim>
</select>
------------------------------------------------------------------------
```
  
<span style="color:#ff0000;">方法三:choose(when,otherwise)多条件查询</span>  
示例:根据条件(用户名称,用户角色,用户编码,创建时间)查询用户表  
```java
-------------------------------UserMapper.java-------------------------
public List<User> getUserList_choose(@Param("userName")String userName,
        @Param("userRole")Integer roleId,
        @Param("userCode")String userCode,
        @Param("creationDate")Date creationDate)throws Exception;
-----------------------------------------------------------------------
```
```xml
-----------------------UserMapper.xml--------------------------
<select id="getUserList_choose" resultType="User">
    select * from smbms_user where 1=1
    <choose>
        <when test="userName !=null and userName !=''" >
            and userName like concat ('%',#{userName},'%')
        </when>
        <when test="userCode !=null and userCode !=''">
            and userCode like concat ('%',#{userCode},'%')
        </when>
        <when test="userRole != null">
            and userRole=#{userRole}
        </when>
        <otherwise>
            and YEAR(creationDate) = YEAR(#{creationDate})
        </otherwise>
    </choose>
</select>
---------------------------------------------------------------
```
```java
-------------------------------------------TeatUserMapper.java--------------------------------------------------------
@Test
public void testGetUserList_choose(){
    List<User> userlist= null;
    SqlSession sqlSession = null;
    String userName="明";
    Integer roleId=null;
    String userCode="";
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        Date creationDate =new SimpleDateFormat("yyyy-MM-dd").parse("2014-12-31");
        userlist = sqlSession.getMapper(UserMapper.class).getUserList_choose(userName, roleId, userCode, creationDate);
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    for(User _user:userlist){
        logger.debug(_user.getUserName()+"\t"+_user.getUserRole()+"\t"+_user.getCreationDate());
    }
}
------------------------------------------------------------------------------------------------------------------------
```
  
#### 动态SQL更新  
在之前的例子中,我们是直接set所有字段,若某个参数为null会导致更新错误,若要自动根据参数更新  
  
<span style="color:#ff0000;">方法一:if+set更新</span>  
示例:根据用户ID修改用户信息  
```java
--------------UserMapper.java---------------
public int modify(User user) throws Exception;
--------------------------------------------
```
```xml
----------------------------UserMapper.xml---------------------------------
<update id="modify" parameterType="User">
    UPDATE smbms_user
    <set>
        <if test="userCode != null">userCode=#{userCode},</if>
        <if test="userName != null">userName=#{userName},</if>
        <if test="userPassword != null">userPassword=#{userPassword},</if>
        <if test="gender != null">gender=#{gender},</if>
        <if test="birthday != null">birthday=#{birthday},</if>
        <if test="phone != null">phone=#{phone},</if>
        <if test="address != null">address=#{address},</if>
        <if test="userRole != null">userRole=#{userRole},</if>
        <if test="modifyBy != null">modifyBy=#{modifyBy},</if>
        <!-- 最后一行记得也要加上逗号,set会自动剔除多余逗号,不用担心会多余 -->
        <if test="modifyDate != null">modifyDate=#{modifyDate},</if>
    </set>
     where id=#{id}
</update>
----------------------------------------------------------------------------
```
```java
----------------------------------TeatUserMapper.java--------------------------------
@Test
public void testModify(){
    SqlSession sqlSession = null;
    int count =0;
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        User user = new User();
        user.setId(16);
        user.setUserCode("teaper");
        user.setUserName("湘桥月");
        user.setUserPassword("1234567");
        Date birthday=new SimpleDateFormat("yyyy-MM-dd").parse("2017-5-6");
        user.setBirthday(birthday);
        user.setAddress(null);//设置为null测试
        user.setGender(1);
        user.setPhone("");//设置为""测试
        user.setUserRole(1);
        user.setModifyBy(1);
        user.setModifyDate(new Date());
        count = sqlSession.getMapper(UserMapper.class).modify(user);
        //模拟异常,进行回滚
        sqlSession.commit();
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
        sqlSession.rollback();
        count=0;
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    logger.debug("是否修改成功------>"+count);
}
-------------------------------------------------------------------------------------
```
  
<span style="color:#ff0000;">方法二:if+trim更新</span>  
我们也可以使用trim元素替换set元素,更精准的剔除逗号,拼接前后缀  
```xml
----------------------------UserMapper.xml---------------------------------
<update id="modify" parameterType="User">
    UPDATE smbms_user
    <trim prefix="set" suffixOverrides="," suffix="where id=#{id}">
        <if test="userCode != null">userCode=#{userCode},</if>
        <if test="userName != null">userName=#{userName},</if>
        <if test="userPassword != null">userPassword=#{userPassword},</if>
        <if test="gender != null">gender=#{gender},</if>
        <if test="birthday != null">birthday=#{birthday},</if>
        <if test="phone != null">phone=#{phone},</if>
        <if test="address != null">address=#{address},</if>
        <if test="userRole != null">userRole=#{userRole},</if>
        <if test="modifyBy != null">modifyBy=#{modifyBy},</if>
        <if test="modifyDate != null">modifyDate=#{modifyDate},</if>
    </trim>>
</update>
----------------------------------------------------------------------------
```
  
#### MyBatis的foreach迭代  
<span style="color:#ff9900;">foreach</span>:迭代一个集合,通常用于in条件  
  
属性:  
* <span style="color:#ff9900;">item</span>:表示集合中每个元素进行迭代时的别名  
* <span style="color:#ff9900;">index</span>:指定一个名称,用于迭代过程中每次迭代到的位置  
* <span style="color:#ff9900;">collection</span>:必须指定,不同情况下的属性值不一样  
* <span style="color:#ff9900;">open</span>:表示该语句以什么开始(既然是in条件,必然以"("开始)  
* <span style="color:#ff9900;">separator</span>:表示每次进行迭代之间以什么符号作为分隔符(既然是in条件,必然以","分隔)  
* <span style="color:#ff9900;">close</span>:表示该语句以什么结束(既然是in条件,必然以")"结束)  
  
示例:根据用户角色列表,获取该角色下用户列表信息  
<span style="color:#ff0000;">方法一:入参为数组类型的foreach迭代</span>  
```java
--------------------------------UserMapper.java----------------------------------------
public List<User> getUserListByRoleId_foreach_array(Integer[] roleIds)throws Exception;
---------------------------------------------------------------------------------------
```
```xml
----------------------------UserMapper.xml---------------------------------
<resultMap type="User" id="userMapByRole">
    <result column="id" property="id" />
    <result column="userCode" property="userCode" />
    <result column="userName" property="userName" />
</resultMap>
<select id="getUserListByRoleId_foreach_array" resultMap="userMapByRole">
    select * from smbms_user where userRole in
    <foreach collection="array" item="roleIds" open="(" separator="," close=")">
        #{roleIds}
    </foreach>
</select>
----------------------------------------------------------------------------
```
```java
----------------------------------------TeatUserMapper.java------------------------------------------
@Test
public void testGetUserListByRoleId_foreach_array(){
    List<User> userlist= null;
    SqlSession sqlSession = null;
    Integer[] roleIds={2,3};
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        userlist = sqlSession.getMapper(UserMapper.class).getUserListByRoleId_foreach_array(roleIds);
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    for(User _user:userlist){
        logger.debug(_user.getId()+"\t"+_user.getUserCode()+"\t"
                +_user.getUserName()+"\t"+_user.getUserRole());
    }
}
------------------------------------------------------------------------------------------------------
```
<span style="color:#ff0000;">注意:UserMapper.xml中的select:getUserListByRoleId_foreach_array中没有指定parameterType,因为配置文件中的parameterType是可以不用配置的,MyBatis会自动把他封装成一个Map进行传入;但是,当入参为Collection时,不能直接传入Collection对象,需要将其转换为List或数组</span>  
  
<span style="color:#ff0000;">方法二:入参为List类型的foreach迭代</span>  
```java
----------------------------------UserMapper.java------------------------------------------
public List<User> getUserListByRoleId_foreach_list(List<Integer> roleList)throws Exception;
-------------------------------------------------------------------------------------------
```
```xml
----------------------------UserMapper.xml---------------------------------
<resultMap type="User" id="userMapByRole">
    <result column="id" property="id" />
    <result column="userCode" property="userCode" />
    <result column="userName" property="userName" />
</resultMap>
<select id="getUserListByRoleId_foreach_list" resultMap="userMapByRole">
    select * from smbms_user where userRole in
    <foreach collection="list" item="roleList" open="(" separator="," close=")">
        #{roleList}
    </foreach>
</select>
----------------------------------------------------------------------------
```
```java
----------------------------------TeatUserMapper.java--------------------------------
@Test
public void testGetUserListByRoleId_foreach_list(){
    List<User> userlist= null;
    SqlSession sqlSession = null;
    List<Integer> roleList=new ArrayList<Integer>();
    roleList.add(2);
    roleList.add(3);
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        userlist = sqlSession.getMapper(UserMapper.class).getUserListByRoleId_foreach_list(roleList);
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    for(User _user:userlist){
        logger.debug(_user.getId()+"\t"+_user.getUserCode()+"\t"
                +_user.getUserName()+"\t"+_user.getUserRole());
    }
}
-------------------------------------------------------------------------------------
```
<span style="color:#ff0000;">方法三:入参为Map类型的foreach迭代</span>  
  
示例:根据传入的用户角色列表和性别获取相应的用户信息  
```java
---------------------------------------UserMapper.java---------------------------------------------
public List<User> getUserListByRoleId_foreach_map(Map<String,Object> conditionMap)throws Exception;
---------------------------------------------------------------------------------------------------
```
```xml
--------------------------------UserMapper.xml------------------------------------
<resultMap type="User" id="userMapByRole">
    <result column="id" property="id" />
    <result column="userCode" property="userCode" />
    <result column="userName" property="userName" />
</resultMap>
<select id="getUserListByRoleId_foreach_map" resultMap="userMapByRole">
    select * from smbms_user where gender=#{gender} and userRole in
    <foreach collection="roleIds" item="roleMap" open="(" separator="," close=")">
        #{roleMap}
    </foreach>
</select>
-----------------------------------------------------------------------------------
```
```java
----------------------------------TeatUserMapper.java--------------------------------
public void testGetUserListByRoleId_foreach_map(){
    List<User> userlist= null;
    SqlSession sqlSession = null;
    Map<String,Object> conditionMap=new HashMap<String, Object>();
    List<Integer> roleList=new ArrayList<Integer>();
    roleList.add(2);
    roleList.add(3);
    conditionMap.put("gender", 1);
    conditionMap.put("roleIds", roleList);
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        userlist = sqlSession.getMapper(UserMapper.class).getUserListByRoleId_foreach_map(conditionMap);
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    for(User _user:userlist){
        logger.debug(_user.getId()+"\t"+_user.getUserCode()+"\t"
                +_user.getUserName()+"\t"+_user.getUserRole());
    }
}
-------------------------------------------------------------------------------------
```
  
#### MyBatis分页  
示例:分页查询用户列表  
```java
---------------------------UserMapper.java-------------------------
//查询总记录数
public int getCount() throws Exception;
/**
 * 查询用户列表(分页)
 * getCount() 之前已写过,获取总记录数
 * @param userName 用户名
 * @param roleId 角色编号
 * @param currentPageNo  起始位置
 * @param pageSize 页面大小
 */
public List<User> getUserList(@Param("userName") String userName,
        @Param("userRole") Integer roleId,
        @Param("from") Integer currentPageNo,
        @Param("pageSize") Integer pageSize) throws Exception;
-------------------------------------------------------------------
```
```xml
---------------------------------------UserMapper.xml-----------------------------------------
<resultMap type="User" id="userList">
    <result column="id" property="id" />
    <result column="userName" property="userName" />
    <result column="roleName" property="roleName" />
    <result column="userCode" property="userCode" />
    <result column="userPassword" property="userPassword" />
    <result column="gender" property="gender" />
    <result column="birthday" property="birthday" />
    <result column="phone" property="phone" />
    <result column="address" property="address" />
    <result column="userRole" property="userRole" />
    <result column="createdBy" property="createdBy" />
    <result column="creationDate" property="creationDate" />
    <result column="modifyBy" property="modifyBy" />
    <result column="modifyDate" property="modifyDate" />
    <result column="idPicPath" property="idPicPath" />
    <result column="workPicPath" property="workPicPath" />
    <result column="age" property="age" />
    <result column="userRoleName" property="userRoleName" />
</resultMap>
<!-- 获取总记录数 -->
<select id="getCount" resultType="int">
    select count(1) from smbms_user
</select>
<!-- 分页查询用户列表(降序) -->
<select id="getUserList" resultMap="userList">
    select u.*,r.roleName as userRoleName from smbms_user u,smbms_role r where u.userRole=r.id
    <if test="userName !=null and userName !=''">
        and u.userName like concat ('%',#{userName},'%')
    </if>
    <if test="userRole != null">
        and u.userRole=#{userRole}
    </if>
    order by creationDate DESC limit #{from},#{pageSize}
</select>
------------------------------------------------------------------------------------------------
```
```java
------------------------------------------------TeatUserMapper.java--------------------------------------------
//获取总记录数
@Test
public void testGetCount(){
    int count = 0;
    SqlSession sqlSession = null;
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        count = sqlSession.getMapper(UserMapper.class).getCount();
        logger.debug("COUNT等于:---->"+count);
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
}
 
//分页查询用户列表(降序)
@Test
public void testGetUserList(){
    List<User> userlist= null;
    SqlSession sqlSession = null;
    String userName=null;
    Integer roleId=null;
    Integer pageSize=5;
    Integer currentPageNo=0;
    try {
        sqlSession = MyBatisUtil.creataSqlSession();
        userlist = sqlSession.getMapper(UserMapper.class).getUserList(userName, roleId, currentPageNo, pageSize);
    } catch (IOException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
    } finally{
        MyBatisUtil.closeSqlSession(sqlSession);
    }
    for(User _user:userlist){
        logger.debug(_user.getId()+"\t"+_user.getUserCode()+"\t"+_user.getUserName()+"\t"+_user.getUserRoleName()
        +"\t"+_user.getAge());
    }
}
------------------------------------------------------------------------------------------------------------------
```
  
#### Log日志  
`pom.xml`中加入jar包  
```xml
<!-- log4j/sfl4j日志处理 
slf4j-api-1.7.21.jar
slf4j-log4j12-1.7.21.jar
log4j-1.2.17.jar
-->
 <dependency>
     <groupId>org.slf4j</groupId>
     <artifactId>slf4j-api</artifactId>
     <version>1.7.21</version>
 </dependency>
 <dependency>
     <groupId>org.slf4j</groupId>
     <artifactId>slf4j-log4j12</artifactId>
     <version>1.7.21</version> 
</dependency>
<dependency>
	<groupId>log4j</groupId>
	<artifactId>log4j</artifactId> 
	<version>1.2.16</version>
</dependency> 
```
在项目的 `resources` 文件夹创建 `login.properties` 文件  
```properties
log4j.rootLogger=info,debug,CONSOLE,file
#log4j.rootLogger=ERROR,ROLLING_FILE

log4j.logger.cn.appsys=info
log4j.logger.org.apache.ibatis=info
log4j.logger.org.mybatis.spring=info
log4j.logger.java.sql.Connection=info
log4j.logger.java.sql.Statement=info
log4j.logger.java.sql.PreparedStatement=info
log4j.logger.java.sql.ResultSet=info

######################################################################################
# Console Appender  \u65e5\u5fd7\u5728\u63a7\u5236\u8f93\u51fa\u914d\u7f6e
######################################################################################
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.Threshold=info
log4j.appender.CONSOLE.DatePattern=yyyy-MM-dd
log4j.appender.CONSOLE.Target=System.out
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern= - (%r ms) - %d{yyyy-M-d HH:mm:ss}%x[%5p](%F:%L) %m%n



######################################################################################
# Rolling File  \u6587\u4ef6\u5927\u5c0f\u5230\u8fbe\u6307\u5b9a\u5c3a\u5bf8\u7684\u65f6\u5019\u4ea7\u751f\u4e00\u4e2a\u65b0\u7684\u6587\u4ef6
######################################################################################
#log4j.appender.ROLLING_FILE=org.apache.log4j.RollingFileAppender
#log4j.appender.ROLLING_FILE.Threshold=INFO
#log4j.appender.ROLLING_FILE.File=${baojia.root}/logs/log.log
#log4j.appender.ROLLING_FILE.Append=true
#log4j.appender.ROLLING_FILE.MaxFileSize=5000KB
#log4j.appender.ROLLING_FILE.MaxBackupIndex=100
#log4j.appender.ROLLING_FILE.layout=org.apache.log4j.PatternLayout
#log4j.appender.ROLLING_FILE.layout.ConversionPattern=%d{yyyy-M-d HH:mm:ss}%x[%5p](%F:%L) %m%n

######################################################################################
# DailyRolling File  \u6bcf\u5929\u4ea7\u751f\u4e00\u4e2a\u65e5\u5fd7\u6587\u4ef6\uff0c\u6587\u4ef6\u540d\u683c\u5f0f:log2009-09-11
######################################################################################
log4j.appender.file=org.apache.log4j.DailyRollingFileAppender
log4j.appender.file.DatePattern=yyyy-MM-dd
#注意，ProjectName改成你的工程名字，日志文件会生成到Linux系统的 ~/ 目录
log4j.appender.file.File=${ProjectName.root}/logs/log.log 
log4j.appender.file.Append=true
log4j.appender.file.Threshold=info
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern= - (%r ms) - %d{yyyy-M-d HH:mm:ss}%x[%5p](%F:%L) %m%n

#DWR \u65e5\u5fd7
#log4j.logger.org.directwebremoting = ERROR

#\u663e\u793aHibernate\u5360\u4f4d\u7b26\u7ed1\u5b9a\u503c\u53ca\u8fd4\u56de\u503c
#log4j.logger.org.hibernate.type=DEBUG,CONSOLE 

#log4j.logger.org.springframework.transaction=DEBUG
#log4j.logger.org.hibernate=DEBUG
#log4j.logger.org.acegisecurity=DEBUG
#log4j.logger.org.apache.myfaces=TRACE
#log4j.logger.org.quartz=DEBUG

#log4j.logger.com.opensymphony=INFO  
#log4j.logger.org.apache.struts2=DEBUG  
#log4j.logger.com.opensymphony.xwork2=debug
```
在`web.xml`中配置加载器  
```xml
<!--配置log4j文件  -->
<context-param>
	<param-name>log4jConfigLocation</param-name>
	<param-value>classpath:log4j.properties</param-value>
</context-param>
<listener>
	<listener-class>
		org.springframework.web.util.Log4jConfigListener
	</listener-class>
</listener>
```
在需要使用的类中创建Log4j对象进行日志输出  
```java
public class EmpController {
	private Logger logger = Logger.getLogger(EmpController.class);
    logger.info("员工名字============》"+empName);
}
```









