#  MYBATIS

## 1. MyBatis 是持久层框架

MyBatis和SQL 结合， 使得对数据库的操作，增，删，改，查，更方便。

在Mybatis上的操作都是对数据库的操作，所以会影响数据库中的数据。

### 1.1. 作用：持久化存储

在程序运行过程中， 数据都是在内存（RAM,即内存条）中的，内存中的数据不是永久存储的， 例如程序可以对这些数据进行销毁， 或者由于断电也会导致内存中所有数据丢失！而把数据存储到硬盘中的某个文件中， 会使得这些数据永久的存储下来。常用做法，是存储到数据中。当然，也可以使用其他技术把数据存储到文本文件，XML文件等其他文件中，但是增删改查并不方便。

MyBatis 是持久层框架， 它是支持JDBC的,简化了持久层开发。

使用MyBatis框架时，只需要通过接口指定数据操作的抽象方法， 然后配置与之关联的SQL语句，即可完成

```java
//JDBC
Spring sql;
Connection com;
    PreparedStatement pstmt;
        executeUpdate/ executeQuery
ResultSet
    close
```

解决持久层数据处理的问题，主要是基于JDBC技术的原生代码比较繁琐，没有经过任何优化，开发甚至执行效率低下！

使用MyBatis框架时，不必关心JDBC技术如何实现，只需要编写需要执行的操作的抽象方法，例如`User findById(Integer id)`，然后，为这个方法映射所需执行的SQL语句即可。



## 2. MyBatis的配置

前提：在数据库系统中已经存在`tedu_ums`数据库，且存在`t_user`表，结构可参考昨天的作业。

### 2.1 创建项目 及配置

创建时，`Artifact Id`为`MYBATIS-01-USER`，`Group Id`为`cn.mybatis`。

创建过程与前次课程相同。需要和SpringMVC的前几个步骤一样。 注意： 请检查有没有多余的配置， 如果有，请删除， 例如， 在spring.xml 是否存在拦截器（JAVA类的使用）的配置。​  

##### a. web.xml中配置DispatcherServlet,字符编码过滤器

```java
<!--//配置DispatcherServlet -->
<servlet>
	<servlet-name>SpringMVC</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	
    <!--//1.配置Spring的配置文件 -->    
	<init-param>  <!--//初始化参数-->
	  <param-name>contextConfigLocation</param-name> <!--//用于加载上下文配置文件的属性名-->
	  <param-value>classpath:spring.xml</param-value>
        <!--//Spring的配置文件的文件名，在src/main/resource下的文件被访问都需要加classpath:-->
	</init-param>
	
	 <!--2. //启动Tomcat时即初始化Servlet -->
   < load-on-startup>1</load-on-startup>
</servlet>


<servlet-mapping>
	<servlet-name>SpringMVC</servlet-name>
	<url-pattern>*.do</url-pattern>
</servlet-mapping>

<!--//配置字符编码过滤器，以后使用SpringMVC 则需要也复制该配置-->
<filter>
	<filter-name>CharacterEncodingFilter</filter-name>
	<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
	<init-param>   <!-- //用初始化参数来为encoding属性赋值-->
		<param-name>encoding</param-name>
		<param-value>utf-8</param-value>
	</init-param>
</filter>

<filter-mapping>
	<filter-name>CharacterEncodingFilter</filter-name>
	<url-pattern> /* </url-pattern>   <!-- 表示所有路径-->
</filter-mapping>
```



### 2.2 Pom.xml 添加新的依赖Mybatis

此次使用MYBATIS, 需要增加新的依赖关系。

在SpringMVC的依赖关系的基础上，需要增加使用Mybatis的依赖关系

依赖的JAR包都是下载到本地

下载下来的依赖的JAR包都是下载到本地， 所以不需要每个项目都需要下载。

如果下载的依赖jar包是损坏的， 应该先关闭ECLIPSE，然后去本地文件中删除对应的JAR包文件， 再次启动Eclipse, 对项目点击右键，选择-maven-update project,在弹出的对话框中，更新需要把force update of snapshots/releases 打勾，就会重新下载JAR 包。



##### a. 添加依赖：SpringMVC, MyBatis, 整合mybatis-spring

MyBatis是一个独立的框架，即只添加该依赖就可以实现持久层编程， 但是， 开发过程相对比较繁琐，而实际应用中， 通常会与Spring. SpringMVC一起使用， 整合使用时，可以简化大量的配置， 使得开发更加简便。整合时，还需要添加相关依赖.

##### b. Spring JDBC 版本需要与spring-webmvc保持一致 

可直接把此前spring-webmvc的依赖代码复制一次，把spring-webmvc改为spring-jdbc即可

凡是Spring官方推出的以spring作为前缀的依赖，必须使用相同版本，否则，可能存在不兼容的风险，可能代码会出现问题.。

##### c. 数据库连接池

 在实际开发过程中，肯定得先建立与数据库的连接， 然后再继续编程， 所以， 还应该添加数据源管理的依赖，即数据库连接池。

##### d. MySQL数据库的连接驱动的依赖

由于本次将使用MySQL数据库，所以 ，还需要该数据库的连接驱动的依赖

```java
<!-- MyBatis -->
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis</artifactId>
	<version>3.4.6</version>
</dependency>

<!-- MyBatis与Spring整合 -->
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis-spring</artifactId>
	<version>1.3.2</version>
</dependency>

<!-- Spring JDBC，版本需要与spring-webmvc保持一致 -->
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-jdbc</artifactId>
	<version>4.3.9.RELEASE</version>
</dependency>

<!-- MySQL连接 -->
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>8.0.13</version>
</dependency>

<!-- 数据库连接池 -->
<dependency>
	<groupId>commons-dbcp</groupId>
	<artifactId>commons-dbcp</artifactId>
	<version>1.4</version>
</dependency>

<!-- Junit单元测试 -->
<dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
	<version>4.9</version>
</dependency>
```

注意：下载的新的依赖的jar可能是损坏的文件，如果保证代码正确的前提下，无法得到预期的运行效果，应该删除本地仓库中的jar包并重新更新！



### 2.3 创建数据库t_mybatis， 数据表t_user

在数据库控制台操作

##### A. 创建数据库t_mybatis

```
CREATE DATABASE t_user;
```



##### B. 创建数据表 t_user

​    包含：`id`、`username`、`password`、`age`、`phone`、`email`，字段约束可自行设计，并保留SQL语句。

a. CHARSET=UTF8 ：UTF-8中的 - 是运算符， 在写数据库时不能加- ， 直接写UTF8

b, id 都是PRIMARY KEY AUTO_INCREMENT 主键约束，

c.  用户名 ： UNIQUE NOT NULL 唯一非空约束 。 用户名不能有重复，同时不能为空

d. 密码：NOT NULL 非空约束

```java
CREATE TABLE t_user(
id INT 
username VARCHAR(20)
password  VARCHAR(20)
age INT
phone VARCHAR(20)
email VARCHAR(30)
PRIMARY KEY(id)
)
```

以下（1-8）不是结合mybatis的数据库操作, 复习以前的单独的数据库知识

1. 向`t_user`表添加不少于10条数据

   ```java
   insert into t_user (
   	username, password, age, phone, email
   ) values (
   	'root', '12r435r34', 21, '13800138001', 'root@tedu.cn'
   ), (
   	'admin', '12hghe34', 22, '13800138002', 'admin@tedu.cn'
   ), (
   	'spring', '1234', 23, '13800138003', 'spring@tedu.cn'
   ), (
   	'mybatis', '12cxv34', 24, '13800138004', 'mybatis@tedu.cn'
   ), (
   	'html', '12876734', 25, '13800138005', 'html@tedu.cn'
   ), (
   	'filter', '12132134', 26, '13800138006', 'filter@tedu.cn'
   ), (
   	'jdbc', '1221434334', 27, '13800138007', 'jdbc@tedu.cn'
   ), (
   	'java', '12r54334', 28, '13800138008', 'java@tedu.cn'
   ), (
   	'mvc', '12ferdaf34', 29, '13800138009', 'mvc@tedu.cn'
   ), (
   	'servlet', '12fdsaf34', 30, '13800138010', 'servlet@tedu.cn'
   );
   ```

2. 查询`t_user`表中所有数据

   ```
   select id, username, password, age, phone, email from t_user;
   ```

3. 获取`t_user`表中数据的数量

   ```
   select count(id) from t_user;
   ```

4. 获取`t_user`表指定`username`值为xx的数据

   ```
   select id, username, password, age, phone, email from t_user where username='mvc';
   ```

5. 获取`t_user`表中年龄从高到低排列的前5条数据

   ```
   select id, username, password, age,
   phone, email from t_user order by age desc limit 0, 5;
   ```

6. 删除`t_user`表中指定`username`值为xx的数据

   ```
   delete from t_user where username='mvc';
   ```

7. 将`t_user`表中年龄大于xx的数据的密码修改为xx

   ```
   update t_user set password='P@ssw0rd' where age>26;
   ```

8. 修改`t_user`表中指定`id`为xx的数据的电子邮箱是xx

   ```
   update t_user set email='user20@tedu.cn' where id=20;
   ```



### 2.4 配置文件中：数据库连接/ 数据源 #data-source

#### 1). db.properties 文件配置   与数据库连接相关的信息。

在`src\main\resources`下创建`db.properties`文件，用于**配置数据库连接的相关信息**：

 a. 配置的值以自己使用数据库为准。

b. initialSize 初始化连接， maxActive 最大连接数量

 c.  url=jdbc:mysql://本地网络：端口号/数据库名称？ useUnicode=true&characterEncoding=utf8&serverTimezone=UTC

由于数据传输过程（Tomcat服务器与mysql数据库的服务器不一定是装在同一台）可能会出现乱码，所以需要统一编码，指定字符编码 

 data-source

```java
url=jdbc:mysql://localhost:3306/tedu_ums?useUnicode=true&characterEncoding=utf8&serverTimezone=UTC
driver=com.mysql.cj.jdbc.Driver
username=root
password=root
initialSize=2
maxActive=10
```

url=jdbc:mysql://localhost:3306//t_mybatis?useUnicode=true&characterEncoding=utf8&serverTimezone=UTC



#### 2). spring.xml 配置数据源

在`src\main\resources`下使用`spring.xml`配置：

##### a.通过< <util:properties/>> 可以读取配置文件

以上配置需要被应用到项目中， 则在Spring的配置中通过< <util:properties/>>可以读取配置文件.

##### b.把这些配置应用到数据源 中即数据连接池

 然后，需要把这些配置应用到数据源 中即数据连接池，当前项目使用的是Apache的 commons-dbcp , 则对应的数据源是BasicDataSource 类。driverClassName，url等属性是是BasicDataSource中定义的名称 ,
这些属性的值是用<util:properties/>中读取来的， 所以需要为<util:properties/>定义一个id,（id名可以随便取），方便去引用 .

```java
<!-- 读取db.properties -->
<util:properties id="dbConfig"
	location="classpath:db.properties" />

<!-- 配置数据源 -->
<bean class="org.apache.commons.dbcp.BasicDataSource">
	<!-- driverClassName是BasicDataSource中定义的名称 -->
	<property name="driverClassName"
		value="#{dbConfig.driver}" />
	<property name="url"
		value="#{dbConfig.url}" />
	<property name="username"
		value="#{dbConfig.username}" />
	<property name="password"
		value="#{dbConfig.password}" />
	<property name="initialSize"
		value="#{dbConfig.initialSize}" />
	<property name="macActive"
		value="#{dbConfig.maxActive}" />
</bean>
```



c.. 完成了以上的配置后， 可以测试到目前为止的配置是否正确， 做法就是：

 **获取BasicDataSource的对象， 调用它的getConnection（）方法， 尝试在Java程序中获取与数据库的连接，如果能够正常连接，则配置无误，如配置有误，将无法获取连接。**

d.测试方法：测试到目前为止的配置是否正确

注意：数据库名称，和数据库密码会比较容易输错，可以检查一下

```java
public class SimpleTestCase {

	@Test
	public void getConnection() throws SQLException{
        // 加载配置文件
		AbstractApplicationContext ac
			= new ClassPathXmlApplicationContext(
					"spring.xml");
        
        //获取BasicDataSource的对象
		BasicDataSource ds= ac.getBean("dataSource", BasicDataSource.class);	
        
        //获取BasicDataSource的对象， 调用它的getConnection（）方法
		Connection conn=ds.getConnection（）
            
       // 如果能够正常连接，则配置无误，如配置有误，将无法获取连接。    
		System.out.println("url=" + conn);  //连接正常输出url的值
        
        //关配置文件资源
		ac.close();
	}	
}
```



### 2.5 通过MyBatis插入insert数据: MyBatis的编码模式

####  

#### 1) 编写方法：创建接口， 并声明数据访问的抽象方法

##### （1）创建实体类，添加与t_user数据表匹配的属性

首先，创建cn.mybatis.entity.User 实体类，并添加与t_user数据表匹配的属性。

通常，每张数据表都有与之匹配的实体类。在实体类中，有相同数量的属性，数据类型应该保持一致，属性名称与字段名应该一一对应（在Java中的属性名称应该采用驼峰命名法，而数据库领域中并不区分大小写），实体类所有的属性都应该是私有的，且都存在公有的SET/GET方法，重写ToString方法，整个实体类应该是实现了`Serializable`接口的！

```java
package cn.mybatis.entity;

public class User {
    private Integer id; //id 是基本数据类型
    private String username;
    private String password;
    private Integer age; //
    private String phone;
    private String email;
    
    ....//以下代码，需要写set /get 方法， 重写ToString方法
	}
}
```



##### （2）创建接口，接口 里面定义抽象方法

```java
package cn.mybatis.mapper;
import cn.mybatis.entity.User; //导入实体类

public Interface UserMapper{
    Integer Insert(User user);
}
```

##### （3）执行增、删、改 均返回`Integer`，表示受影响的行数

使用MyBatis时，无需自行编写JDBC相关代码，只需要创建Java接口文件，并将需要执行的数据操作的抽象方法添加在接口中即可！通常，建议按照“增 > 查 > 删 > 改”的顺序开发相关功能。

自定义创建cn.mybatis.mapper.UserMapper  接口，并在接口中声明抽象方法。调用该方法就会插入数据。

在MyBatis中， 执行insert/update/delete操作时，均返回收影响的行数。所以设计抽象方法时，如果对应的是这几种操作， 返回值均设计为Integer类型

```
insert into t_user (username, password, age, phone, email) values (
	'root', '12r435r34', 21, '13800138001', 'root@tedu.cn');
	数据库控制台： Query ok, 1 row affected
	
```

目标：向数据表中插入新的用户数据。

则创建`cn.mybatis.mapper.UserMapper`接口，然后，添加“插入新的用户数据”的抽象方法：

```
Integer insert(User user);
```

使用MyBatis时，执行的增、删、改操作均返回`Integer`，表示受影响的行数。



#### 2). 编写XML映射：配置与抽象方法对应的XML映射

#### （1）创建mappers文件夹放UserMapper.xml

通常， 一个完整的项目中， 会存在许多MyBatis的映射文件， 为了便于管理， 会在src\main\resources下 new other-folder 创建一个名为mappers的子文件夹，然后，从FTP下载somemapper.zip ，将解压得到XML文件复制到刚才创建的mappers文件夹中。其实，mappers下的映射文件的名称并不重要，但是项目中可能存在多个映射文件为了便于管理， 通常会使用与接口对应的名称，所以，将somemapper 重命名为`UserMapper.xml`。

通常，接口文件的数量与XML映射文件的数量是相同的，是一一对应的！



#### （2）编写映射文件：UserMapper.xml

使用MyBatis时，还需要与接口的抽象方法对应的SQL语句，该SQL语句是在XML文件中配置的！

##### a.根节点：< mapper namespace ="接口全称 ">

所有映射文件中，根节点都是< mapper>节点，且该节点必须配置名为 namespace 的属性,

然后，配置该XML映射文件：

```
<mapper namespace="cn.mybatis.mapper.UserMapper">
</mapper>
```

经过以上配置，指定了XML映射文件与接口文件的对应关系。（找到映射文件UserMapper.xml就可以找到UserMapper接口

##### b.子节点：<执行的操作的类型  id="抽象方法名"  parameterType="抽象方法的参数类型"> 

然后，在该文件内部，使用各级子节点配置与抽象方法的对应关系， 子节点名称的选取，取决于要执行的操作的类型， 例如要执行的数据操作是insert类型， 则使用< insert>节点。这些节点都必须制定id属性， 属性值是与之对应的抽象方法的方法名

```
<insert id="insert">		
</insert>
```

在< insert>节点中， 添加parameterType属性， 用于指定参数的类型，即抽象方法中的参数类型

```
<insert id="insert"  parameterType="cn.mybatis.entity.User">		
</insert>
```

然后，在节点内部， 编写需要执行的SQL语句.  执行SQL语句时的参数值均使用 #{} 类似的语法， 其中的名称是实体类User类中的属性名。插入的SQL语句可以随意换行

```
INSERT INTO t_user (  ) 
			VALUES (  )	
```

完整代码如下：

```java
<!-- //namespace：映射的接口的全名 :< mapper namespace ="接口全称 ">节点-->
    
	<!-- //根据执行的SQL种类选择节点 -->
	<!-- //id：抽象方法的名称 -->
	<!-- //parameterType：抽象方法的参数类型 -->
	<!-- //SQL语句中的参数使用#{}框住User类中的属性名 -->
	<!-- //SQL语句不需要使用分号表示结束 -->
       
<mapper namespace="cn.mybatis.mapper.UserMapper">  

	<insert id="insert" parameterType="cn.mybatis.entity.User">	
    
	        INSERT INTO t_user (username, password,age, phone, email)    
			VALUES ( #{username}, #{password}, #{age}, #{phone}, #{email} )	
    
	</insert>

</mapper>
```





### 2.6 spring.xml 完成MyBatis的配置--SqlSessionFactoryBean

需要让MyBatis 支持UserMapper.xml 文件在哪里， 如果它不知道xml文件在哪里，就不会解读该文件里面配置的内容， 也就不会执行对应SQL语句。最后， sql语句是怎么从JAVA程序中发送到 Tomcat服务器？？去执行，这个都是myBatis框架帮处理好的。所以得让MYbatis 知道有哪些配置文件，分别在哪里。

#### 1） SqlSessionFactoryBean --指定数据源与xml映射的文件位置

首先， 需要配置SqlSessionFactoryBean， 通过它指定数据源与XML映射的文件位置。在配置中， XML映射文件的位置使用了mappers/*.xml ，即在mappers 文件夹下的所有XML文件都应该是MyBatis 的映射文件， 所有，后续使用时， 不可以再这个文件夹中存放其他XML文件。

```java
<bean class="org.mybatis.spring.SqlSessionFactoryBean">	 
	<property name="dataSource"     <!-- //指定数据源，值为以上配置的数据源 -->
		      ref="dataSource" />   
	<property name="mapperLocations"    <!-- //指定XML映射文件的位置 >
		    value="classpath:mappers/*.xml" />
</bean>
```

#### 2) MapperScannerConfigurer 指定接口文件的位置

还需要告诉MYBATIS, 接口在哪里？ 虽然在xml映射文件中已经指定了接口，但是MYBATIS 还有基于这个接口去确定最终的实践代码。（比如在JAVA中，只有接口是不能执行的， 接口都要有对应的实现类。）而在xml映射文件中只是确定了对应关系，即只是告诉了这个接口叫什么，并没有告诉接口的文件的位置， 并没有告诉实现类在哪里以及怎么去创建实现类。真正实现功能的还是基于接口去实现功能。MYBATIS的底层原理并不是自动给它生成一个实现类，而是通过代理模式去做，这个我们可以不关心，但是我们要告诉MYBATIS,实现类是在哪里？

```java
<!--//1. MyBatis:SqlSessionFactoryBean -->
    
<bean class="org.mybatis.spring.SqlSessionFactoryBean">
	 <!-- 指定数据源，值为以上配置的数据源 -->
	<property name="dataSource"    
		ref="dataSource" />
    
	<!-- 指定XML映射文件的位置 
                            //value="classpath:mappers/UserMapper.xml"-->
    
	<property name="mapperLocations"
		value="classpath:mappers/*.xml" />
</bean>


<!-- //2. MyBatis:MapperScannerConfigurer 指定接口文件在哪里 -->

<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">

	<!--指定接口文件的位置：在那个包下 -->
	<property name="basePackage" 
		value="cn.mybatis.mapper" />
</bean>


```

至此， 配置完成

### 2.7 执行单元测试

在src/test/java下创建测试类，并添加测试方法， 以执行测试。

每次测试都是做4件事情

##### 1）加载Spring配置文件，获取Spring容器

AbstractApplicationContext ac = new ClassPathXmlApplicationContext("spring.xml");								

##### 2）从Spring容器中获取对象 ac.getBean("   ", ?.class);		

​    ac.getBean("   ",  ???.class);			

##### 3）测试功能

##### 4）释放资源

​	ac.close();

```java
public class UserMapperTestCase {

	@Test
	public void insert() {
       // 1）加载Spring配置文件，获取Spring容器
		AbstractApplicationContext ac
			= new ClassPathXmlApplicationContext(
					"spring.xml");
        
		//2）从Spring容器中获取对象  //bean id 与接口名一致，首字母为小写
		UserMapper userMapper = ac.getBean("userMapper", UserMapper.class); 
			
        
		//3）测试功能 ： 实体类：创建对象，并未该类属性设置值
		User user = new User(); //创建对象
		user.setUsername("mapper"); //为该类属性赋值
		user.setPassword("1234");
		user.setAge(31);
		user.setPhone("13900139001");
		user.setEmail("mapper@tedu.cn");
		
        // 调用接口里面的insert()方法即向数据库插入数据，所以能返回行数，说明了插入成功。
        //增，删，改 返回值Integer,行数
		Integer rows = userMapper.insert(user); 
        //userMapper.insert(user);  
        //也可以不写返回值， 该执行的还是会执行。有返回值可以方便判断是否成功
		System.out.println("rows=" + rows);
		//4）释放资源
		ac.close();
	}	
}
当执行一次，输出rows=某数字， 表明成功， 
再执行一次就会报错， 因为用户名是唯一非空约束的，不能重复，所以插入数据不成功
```

