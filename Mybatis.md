### 1 Mybatis

### 1.1  Overview of Mybatis

#### 1.1.1  The Mybatis concept

https://mybatis.org/mybatis-3/

MyBatis is a first class persistence framework with support for custom SQL, stored procedures and advanced mappings. MyBatis eliminates almost all of the JDBC code and manual setting of parameters and retrieval of results. MyBatis can use simple XML or Annotations for configuration and map primitives, Map interfaces and Java POJOs (Plain Old Java Objects) to database records.

https://mybatis.org/mybatis-3/zh/index.html

MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

**持久层([persistent layer](javascript:;))：**

* 负责将数据到保存到数据库的那一层代码。
  Responsible for saving data to the database

  Mybatis就是对jdbc代码进行了封装。

  Myatis encapsulates the jdbc code

* JavaEE三层架构：表现层、业务层、持久层
JavaEE has three layers: presentation layer, business layer and persistence layer

#### 1.1.2  JDBC 缺点 JDBC Drawbacks

下面是 JDBC 代码，我们通过该代码分析都存在什么缺点：
Here's the JDBC code to see what the drawbacks are:

<img src="assets/image-20210726203656847.png" alt="image-20210726203656847" style="zoom:70%;" />

* 硬编码 hard coded

  * 注册驱动、获取连接 register driver、get connection

    上图标1的代码有很多字符串，而这些是连接数据库的四个基本信息，以后如果要将Mysql数据库换成其他的关系型数据库的话，这四个地方都需要修改，如果放在此处就意味着要修改我们的源代码。

    The code in icon 1 on the top has a lot of strings, and these are the four basic information to connect to the database. In the future, if we want to change the Mysql database to another relational database, these four places need to be modified, if put here, it means to modify our source code

  * SQL statements

    上图标2的代码。如果表结构发生变化，SQL语句就要进行更改。这也不方便后期的维护。
    
    If the table structure changes, the SQL statement needs to change. This is also inconvenient for later maintenance.

* 操作繁琐 cumbersome operation

  * 手动设置参数 Manually setting parameters

  * 手动封装结果集 Manually encapsulate the result set

    上图标4的代码是对查询到的数据进行封装，而这部分代码是没有什么技术含量，而且特别耗费时间的。
    The code shown in Figure 4 is to wrap the query data, and this part of the code is not technical, and particularly time-consuming

#### 1.1.3  Mybatis 优化 Mybatis optimization

* 硬编码可以配置到==配置文件== Hardcoding can be configured into a configuration file
* 操作繁琐的地方mybatis都==自动完成== mybatis automates the cumbersome code

如图所示

<img src="assets/image-20210726204849309.png" alt="image-20210726204849309" style="zoom:80%;" />

下图是持久层框架的使用占比。
The following figure shows the percentage of the persistence layer framework used

<img src="assets/image-20210726205328999.png" alt="image-20210726205328999" style="zoom:80%;" />

### 1.2  Mybatis Quick Start

**需求：查询user表中所有的数据**

Requirement: Query all data in the user table

* create user table，adding data

  ```sql
  create database mybatis;
  use mybatis;
  
  drop table if exists tb_user;
  
  create table tb_user(
  	id int primary key auto_increment,
  	username varchar(20),
  	password varchar(20),
  	gender varchar(10),
  	email varchar(30)
  );
  
  INSERT INTO `tb_user` VALUES (1, 'tom', '123', 'male', 'tom@gmail.com');
  INSERT INTO `tb_user` VALUES (2, 'jerry', '345', 'male', 'jerry@gmail.com');
  INSERT INTO `tb_user` VALUES (3, 'lisa', '567', 'female', 'lisa@gmail.com');
  ```

* create maven project named mybatis-quickstart，import dependencies in pom.xml

  ```xml
  <dependencies>
      <dependency>
          <groupId>org.mybatis</groupId>
          <artifactId>mybatis</artifactId>
          <version>3.5.9</version>
      </dependency>
      <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
          <version>8.0.28</version>
      </dependency>
      <!--junit-->
      <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <version>4.13</version>
          <scope>test</scope>
      </dependency>
  </dependencies>
  ```

* 编写 MyBatis 核心配置文件 -- > 替换连接信息解决硬编码问题
  Write the MyBatis core configuration file -- > Replace connection information to solve hard coding problems
  
  在模块下的 resources 目录下创建mybatis的配置文件 `mybatis-config.xml`，内容如下：
  
  create `mybatis-config.xml` configuration file in `resources` folder, the contents are as follows:
  
  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuration>
  
      <!--
      environments：配置数据库连接环境信息。可以配置多个environment，通过default属性切换不同的environment
      Configure the database connection environment information. You can configure multiple environments
      and switch between different environments through the default attribute
      -->
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
                  <!--database connection information-->
                  <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                  <property name="url" value="jdbc:mysql:///mybatis?characterEncoding=UTF-8&amp;serverTimezone=Asia/Shanghai"/>
                  <property name="username" value="root"/>
                  <property name="password" value="1234"/>
              </dataSource>
          </environment>
      </environments>
      <mappers>
          <!--Load the sql mapping file-->
          <mapper resource="com/huat/mapper/UserMapper.xml"/>
      </mappers>
  </configuration>
  ```
  
* create User.java class in  `com.huat.pojo` package

  ```java
  public class User {
      private int id;
      private String username;
      private String password;
      private String gender;
      private String email;
      
      //setter and getter
      //toString()
  }
  ```

* 接下来定义与SQL映射文件和同名的Mapper接口，并且将Mapper接口和SQL映射文件放置在同一目录下。如下图：
  Next, define the SQL mapping file and the Mapper interface with the same name, and place the Mapper interface and the SQL mapping file in the same directory. As shown below:

  ![image-20221207213802549](assets/image-20221207213802549.png)

* 在 `com.huat.mapper` 包下创建 UserMapper接口，在 UserMapper接口中定义方法。

  Create UserMapper.java interface in com.huat.mapper package, and define methods in it.

  方法名就是SQL映射文件中sql语句的id，并保持参数类型和返回值类型一致。
  The method name is the id of the SQL statement in the sql mapping file, keeping the parameter type consistent with the return value type. 

  ```java
  public interface UserMapper {
      // query all users
      List<User> selectAll();
      // query user by id
      User selectById(int id);
  }
  ```

  * 编写 SQL 映射文件 --> 统一管理sql语句，解决硬编码问题
    Write SQL mapping file --> Manage sql statements uniformly to solve the problem of hard coding

    在 `resources` 下创建 `com/huat/mapper` 目录，并在该目录下创建 UserMapper.xml 映射配置文件。
    Create the `com/huat/mapper` directory under `resources` and create the `UserMapper.xml` mapping configuration file in that directory.

    设置SQL映射文件的`namespace`属性为`UserMapper`接口全限定名
    Set the `namespace` property of the SQL mapping file to the fully qualified name of the `UserMapper` interface

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <!--
        namespace:must be the fully qualified name of the corresponding interface
    -->
    <mapper namespace="com.huat.mapper.UserMapper">
    
        <select id="selectAll" resultType="com.huat.pojo.User">
            select * from tb_user;
        </select>
        
        <select id="selectById" resultType="com.huat.pojo.User">
            select * from tb_user where id = #{id}
        </select>
    
    </mapper>
    ```

* 在 `com.huat.test` 包下创建 MyBatisDemo测试类，代码如下：
  create MyBatisDemo.java testing class in com.huat.test package, the code as follows:
  
  ```java
  public class MyBatisDemo {
  
      @Test
      public void testSelectAll() throws IOException {
          //1. Load the mybatis core configuration file and get SqlSessionFactory object
          String resource = "mybatis-config.xml";
          InputStream inputStream = Resources.getResourceAsStream(resource);
          SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
  
          //2. Gets the SqlSession object and uses it to execute sql
          SqlSession sqlSession = sqlSessionFactory.openSession();
          //3. Gets the proxy object for the UserMapper interface
          UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
          //4. Call the mapper method of the proxy object 
          List<User> users = userMapper.selectAll();
          System.out.println(users);
          //5. release resources
          sqlSession.close();
      }
  
      @Test
      public void testSelectById() throws IOException {
          //1. Load the mybatis core configuration file and get SqlSessionFactory object
          String resource = "mybatis-config.xml";
          InputStream inputStream = Resources.getResourceAsStream(resource);
          SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
  
          //2. Gets the SqlSession object and uses it to execute sql
          SqlSession sqlSession = sqlSessionFactory.openSession();
          //3. Gets the proxy object for the UserMapper interface
          UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
          //4. Call the mapper method of the proxy object 
          User user = userMapper.selectById(1);
          System.out.println(user);
          //5. release resources
          sqlSession.close();
      }
  }
  ```

==Notice：==

如果Mapper接口名称和SQL映射文件名称相同，并在同一目录下，则可以使用包扫描的方式简化SQL映射文件的加载。
If the Mapper interface name is the same as the SQL mapping file name and is in the same directory, you can use package scanning to simplify the loading of the SQL mapping file.

```xml
<mappers>
    <!--Load the sql mapping file-->
    <!-- <mapper resource="com/itheima/mapper/UserMapper.xml"/>-->
    <package name="com.huat.mapper"/>
</mappers>
```

- 查看SQL日志信息 View SQL log information, import dependencies

  ```xml
  	<dependency>
          <groupId>ch.qos.logback</groupId>
          <artifactId>logback-classic</artifactId>
          <version>1.2.3</version>
      </dependency>
  ```

  注意：需要在项目的 resources 目录下创建logback.xml配置文件
  Note: You need to create the logback.xml configuration file in the resources directory of your project

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <configuration>
      <!--console:表示当前的日志信息是可以输出到控制台的
      console: Indicates that the current log message is available for output to the console
      -->
      <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
          <encoder>
              <pattern>[%level] %blue(%d{HH:mm:ss.SSS}) %cyan([%thread]) %boldGreen(%logger{15}) - %msg %n</pattern>
          </encoder>
      </appender>
  
      <!--
          日志输出级别(优先级高到低): Log output level (high to low priority)
          error: 错误 - 系统的故障日志 Error - The failure log of the system
          warn: 警告 - 存在风险或使用不当的日志 Warning - There are risks or improperly used logs
          info: 一般性消息 General message
          debug: 程序内部用于调试信息 Used internally in a program to debug information
          trace: 程序运行的跟踪信息 Information tracing the execution of the program
       -->
      <root level="debug">
          <appender-ref ref="console"/>
      </root>
  </configuration>
  ```

### 1.3  MyBatis Core Configuration file

核心配置文件中现有的配置之前已经给大家进行了解释，而核心配置文件中还可以配置很多内容。我们可以通过查询官网看可以配置的内容
The existing configuration in the core configuration file has been explained before, and there is much more that can be configured in the core configuration file. We can check the official website to see what can be configured

https://mybatis.org/mybatis-3/configuration.html

<img src="assets/image-20210726221454927.png" alt="image-20210726221454927" style="zoom:80%;" />

#### 1.3.1  多环境配置 Multiple Environments

在核心配置文件的 `environments` 标签中其实是可以配置多个 `environment` ，使用 `id` 给每段环境起名，在 `environments` 中使用 `default='name'` 来指定使用哪儿段配置。我们一般就配置一个 `environment` 即可。

```xml
<environments default="development">
    <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <!--database connection information-->
            <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql:///mybatis?characterEncoding=UTF-8&amp;serverTimezone=Asia/Shanghai"/>
            <property name="username" value="root"/>
            <property name="password" value="1234"/>
        </dataSource>
    </environment>

    <environment id="test">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <!--database connection information-->
            <property name="driver" value="com.mysql.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql:///mybatis?useSSL=false"/>
            <property name="username" value="root"/>
            <property name="password" value="1234"/>
        </dataSource>
    </environment>
</environments>
```

#### 1.3.2  TypeAliases

在映射配置文件中的 `resultType` 属性需要配置数据封装的类型（类的全限定名）。而每次这样写是特别麻烦的，Mybatis 提供了 `类型别名`(typeAliases) 可以简化这部分的书写。
The `resultType` property in the mapping configuration file needs to configure the type of data encapsulation (the fully qualified name of the class). While this is particularly cumbersome to write every time, Mybatis provides typeAliases to simplify this part of writing.

首先需要现在核心配置文件中配置类型别名，也就意味着给pojo包下所有的类起了别名（别名就是类名），不区分大小写。内容如下：
First, you need to configure the type alias in the core configuration file, which means that all classes in the pojo package are aliased (aliases are class names), not case sensitive. The contents are as follows:

```xml
<typeAliases>
    <!--The value of the name attribute is the package in which the entity class resides-->
    <package name="com.huat.pojo"/> 
</typeAliases>
```

通过上述的配置，我们就可以简化映射配置文件中 `resultType` 属性值的编写
With the above configuration, we can simplify the writing of the `resultType`property value in the mapping configuration file

```xml
<mapper namespace="com.huat.mapper.UserMapper">
    <select id="selectAll" resultType="user">
        select * from tb_user;
    </select>
</mapper>
```

**解决SQL映射文件的警告提示：**

在入门案例映射配置文件中存在报红的情况。问题如下：

<img src="assets/image-20210726212621722.png" alt="image-20210726212621722" style="zoom:80%;" />

* 产生的原因：Idea和数据库没有建立连接，不识别表信息。但是大家一定要记住，它并不影响程序的执行。
* 解决方式：在Idea中配置MySQL数据库连接。

IDEA中配置MySQL数据库连接

* 点击IDEA右边框的 `Database` ，在展开的界面点击 `+` 选择 `Data Source` ，再选择 `MySQL`

  <img src="assets/image-20210726213046072.png" alt="image-20210726213046072" style="zoom:80%;" />

* 在弹出的界面进行基本信息的填写

  <img src="assets/image-20210726213305893.png" alt="image-20210726213305893" style="zoom:80%;" />

* 点击完成后就能看到如下界面

  <img src="assets/image-20210726213541418.png" alt="image-20210726213541418" style="zoom:80%;" />

  而此界面就和 `navicat` 工具一样可以进行数据库的操作。也可以编写SQL语句

<img src="assets/image-20210726213857620.png" alt="image-20210726213857620" style="zoom:80%;" />



## 2 MyBatis Practice

**Purpose **

> * ##### 能够使用映射配置文件实现CRUD操作 Ability to implement CRUD operations using a mapping configuration file
> * 能够使用注解实现CRUD操作 Ability to implement CRUD operations using annotations

### Configuration files implement CRUD

产品原型地址 Product prototype address

https://www.pmdaniu.com/storages/122645/74ccff58678d80583ea43a55547173eb-1818/start.html

![image-20210729111159534](assets/image-20210729111159534.png)

如上图所示产品原型，里面包含了品牌数据的 `查询` 、`按条件查询`、`添加`、`删除`、`批量删除`、`修改` 等功能，而这些功能其实就是对数据库表中的数据进行CRUD操作。接下来我们就使用Mybatis完成品牌数据的增删改查操作。

The prototype, shown above, includes`query `, `query by condition`,`add`,`delete`,`batch delete`, `modify`functions for brand data, which are basically CRUD operations on the data in the database table. Next, we will use Mybatis to complete this functions of brand data.

Create project named `mybatis-brand`, refer to the `mybatis-quickstart` project for project initialization.

Here's the list of function we want to complete:

> * Query
>   * query all data
>   * query detail
>   * query by condition
> * Add
> * Update
>   * update all fields
>   * update dynamic fields
> * delete
>   * delete one
>   * batch delete

### 2.1  Environment preparation

* 数据库表（tb_brand）及数据准备

  ```sql
  -- 删除tb_brand表
  drop table if exists tb_brand;
  -- 创建tb_brand表
  create table tb_brand
  (
      -- id 主键
      id           int primary key auto_increment,
      -- 品牌名称
      brand_name   varchar(20),
      -- 企业名称
      company_name varchar(20),
      -- 排序字段
      ordered      int,
      -- 描述信息
      description  varchar(100),
      -- 状态：0：禁用  1：启用
      status       int
  );
  -- 添加数据
  -- 添加数据
  insert into tb_brand (brand_name, company_name, ordered, description, status)
  values
  ('华为', '华为技术有限公司', 100, '万物互联', 1),
  ('小米', '小米科技有限公司', 50, 'are you ok', 1),
  ('格力', '格力电器股份有限公司', 30, '让世界爱上中国造', 1),
  ('阿里巴巴', '阿里巴巴集团控股有限公司', 10, '买买买', 1),
  ('腾讯', '腾讯计算机系统有限公司', 50, '玩玩玩', 0),
  ('百度', '百度在线网络技术公司', 5, '搜搜搜', 0),
  ('京东', '北京京东世纪贸易有限公司', 40, '就是快', 1),
  ('小米', '小米科技有限公司', 50, 'are you ok', 1),
  ('三只松鼠', '三只松鼠股份有限公司', 5, '好吃不上火', 0),
  ('华为', '华为技术有限公司', 100, '万物互联', 1),
  ('小米', '小米科技有限公司', 50, 'are you ok', 1),
  ('格力', '格力电器股份有限公司', 30, '让世界爱上中国造', 1),
  ('阿里巴巴', '阿里巴巴集团控股有限公司', 10, '买买买', 1),
  ('腾讯', '腾讯计算机系统有限公司', 50, '玩玩玩', 0),
  ('百度', '百度在线网络技术公司', 5, '搜搜搜', 0),
  ('京东', '北京京东世纪贸易有限公司', 40, '就是快', 1),
  ('华为', '华为技术有限公司', 100, '万物互联', 1),
  ('小米', '小米科技有限公司', 50, 'are you ok', 1),
  ('格力', '格力电器股份有限公司', 30, '让世界爱上中国造', 1),
  ('阿里巴巴', '阿里巴巴集团控股有限公司', 10, '买买买', 1),
  ('腾讯', '腾讯计算机系统有限公司', 50, '玩玩玩', 0),
  ('百度', '百度在线网络技术公司', 5, '搜搜搜', 0),
  ('京东', '北京京东世纪贸易有限公司', 40, '就是快', 1),
  ('小米', '小米科技有限公司', 50, 'are you ok', 1),
  ('三只松鼠', '三只松鼠股份有限公司', 5, '好吃不上火', 0),
  ('华为', '华为技术有限公司', 100, '万物互联', 1),
  ('小米', '小米科技有限公司', 50, 'are you ok', 1),
  ('格力', '格力电器股份有限公司', 30, '让世界爱上中国造', 1),
  ('阿里巴巴', '阿里巴巴集团控股有限公司', 10, '买买买', 1),
  ('腾讯', '腾讯计算机系统有限公司', 50, '玩玩玩', 0),
  ('百度', '百度在线网络技术公司', 5, '搜搜搜', 0),
  ('京东', '北京京东世纪贸易有限公司', 40, '就是快', 1),
  ('华为', '华为技术有限公司', 100, '万物互联', 1),
  ('小米', '小米科技有限公司', 50, 'are you ok', 1),
  ('格力', '格力电器股份有限公司', 30, '让世界爱上中国造', 1),
  ('阿里巴巴', '阿里巴巴集团控股有限公司', 10, '买买买', 1),
  ('腾讯', '腾讯计算机系统有限公司', 50, '玩玩玩', 0),
  ('百度', '百度在线网络技术公司', 5, '搜搜搜', 0),
  ('京东', '北京京东世纪贸易有限公司', 40, '就是快', 1),
  ('小米', '小米科技有限公司', 50, 'are you ok', 1),
  ('三只松鼠', '三只松鼠股份有限公司', 5, '好吃不上火', 0),
  ('华为', '华为技术有限公司', 100, '万物互联', 1),
  ('小米', '小米科技有限公司', 50, 'are you ok', 1),
  ('格力', '格力电器股份有限公司', 30, '让世界爱上中国造', 1),
  ('阿里巴巴', '阿里巴巴集团控股有限公司', 10, '买买买', 1),
  ('腾讯', '腾讯计算机系统有限公司', 50, '玩玩玩', 0),
  ('百度', '百度在线网络技术公司', 5, '搜搜搜', 0),
  ('京东', '北京京东世纪贸易有限公司', 40, '就是快', 1)
  ;
  ```

* Entity Class Brand.java

  create Brand.java in `com.huat.pojo` package

  ```java
  public class Brand {
      // id 主键
      private Integer id;
      // 品牌名称
      private String brandName;
      // 企业名称
      private String companyName;
      // 排序字段
      private Integer ordered;
      // 描述信息
      private String description;
      // 状态：0：禁用  1：启用
      private Integer status;
      
      //省略 setter and getter。自己写时要补全这部分代码
  }
  ```

* 编写测试用例

  测试代码需要在 `test/java` 目录下创建包及测试用例。项目结构如下：

  <img src="assets\image-20221123202204560.png" alt="image-20221123202204560" style="zoom:80%;" />

* 安装 MyBatisX 插件

  * MybatisX 是一款基于 IDEA 的快速开发插件，为效率而生。

  * 主要功能

    * XML映射配置文件 和 接口方法 间相互跳转
    * 根据接口方法生成 statement 

  * 安装方式

    点击 `file` ，选择 `settings` ，就能看到如下图所示界面

    <img src="assets/image-20210729113304743.png" alt="image-20210729113304743" style="zoom:80%;" />

    > 注意：安装完毕后需要重启IDEA

  * 插件效果

    <img src="assets/image-20210729164450524.png" alt="image-20210729164450524" style="zoom:70%;" />

    红色头绳的表示映射配置文件，蓝色头绳的表示mapper接口。在mapper接口点击红色头绳的小鸟图标会自动跳转到对应的映射配置文件，在映射配置文件中点击蓝色头绳的小鸟图标会自动跳转到对应的mapper接口。也可以在mapper接口中定义方法，自动生成映射配置文件中的 `statement` ，如图所示

    ![image-20210729165337223](assets/image-20210729165337223.png)

### 2.2  query all data

<img src="assets/image-20210729165724838.png" alt="image-20210729165724838" style="zoom:80%;" />

如上图所示就页面上展示的数据，而这些数据需要从数据库进行查询。接下来我们就来讲查询所有数据功能，而实现该功能我们分以下步骤进行实现：

The image above shows the data displayed on the page, and the data needs to be queried from the database. Next, let's look at the query all data function, which we can implement in the following steps:

* Writing interface methods：Mapper interface

  * parameter：none

    查询所有数据功能是不需要根据任何条件进行查询的，所以此方法不需要参数。

    Querying all data does not require any criteria, so this method does not require parameters

    <img src="assets/image-20210729171208737.png" alt="image-20210729171208737" style="zoom:80%;" />

  * result：List<Brand>

    我们会将查询出来的每一条数据封装成一个 `Brand` 对象，而多条数据封装多个 `Brand` 对象，需要将这些对象封装到List集合中返回。

    We will be querying each piece of data into a `Brand` object, and multiple pieces of data to encapsulate multiple `Brand` objects, these objects need to be wrapped in a List collection.
  
    <img src="assets/image-20210729171146911.png" alt="image-20210729171146911" style="zoom:80%;" />
  
  * 执行方法、测试 Execute methods, tests

#### 2.2.1  writing interface methods

在 `com.huat.mapper` 包写创建名为 `BrandMapper` 的接口。并在该接口中定义 `List<Brand> selectAll()` 方法。

create `BrandMapper` interface in `com.huat.mapper` package, define `List<Brand> selectAll()` method in it.

```java
public interface BrandMapper {

    /**
     * query all brand
     */
    List<Brand> selectAll();
}
```

#### 2.2.2  Writing SQL Mapping file

在 `reources` 下创建 `com/huat/mapper` 目录结构，并在该目录下创建名为 `BrandMapper.xml` 的映射配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.huat.mapper.BrandMapper">
    <select id="selectAll" resultType="brand">
        select *
        from tb_brand;
    </select>
</mapper>
```

#### 2.2.3  writing test method

writing test method in `MybBatisTest.java` class

```java
@Test
public void testSelectAll() throws IOException {
    //1. 获取SqlSessionFactory
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    //2. 获取SqlSession对象
    SqlSession sqlSession = sqlSessionFactory.openSession();

    //3. 获取Mapper接口的代理对象
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    //4. 执行方法
    List<Brand> brands = brandMapper.selectAll();
    System.out.println(brands);

    //5. 释放资源
    sqlSession.close();

}
```

The result of executing the test method is as follows:

![image-20210729172544230](assets/image-20210729172544230.png)

从上面结果我们看到了问题，有些数据封装成功了，而有些数据并没有封装成功。为什么这样呢？

From the above result, we can see the problem: some data is successfully encapsulated, and some data is not. Why is that?

这个问题可以通过两种方式进行解决：

This problem can be solved in two ways:

* 给字段起别名 give an alias for fields
* 使用resultMap定义字段和属性的映射关系 Use resultMap to define mappings between table fields and entity class properties

#### 2.2.4  起别名解决上述问题 Aliasing solves this problem

从上面结果可以看到 `brandName` 和 `companyName` 这两个属性的数据没有封装成功，查询 实体类 和 表中的字段 发现，在实体类中属性名是 `brandName` 和 `companyName` ，而表中的字段名为 `brand_name` 和 `company_name`，如下图所示 。那么我们只需要保持这两部分的名称一致这个问题就迎刃而解。

From the above results, we can see that the data of the `brandName` and `companyName`property are not encapsulated successfully. The query of the entity class and the field in the table shows that the property names in the entity class are `brandName` and `companyName`. The fields in the table are named `brand_name` and `company_name` as shown below. Then we just need to keep the names of the two parts consistent and the problem will be solved.

<img src="assets/image-20210729173210433.png" alt="image-20210729173210433" style="zoom:80%;" />

我们可以在写sql语句时给这两个字段起别名，将别名定义成和属性名一致即可。

We can alias these two fields in our sql statement by defining the alias to match the property name.

```xml
<select id="selectAll" resultType="brand">
    select
    id, brand_name as brandName, company_name as companyName, ordered, description, status
    from tb_brand;
</select>
```

而上面的SQL语句中的字段列表书写麻烦，如果表中还有更多的字段，同时其他的功能也需要查询这些字段时就显得我们的代码不够精炼。Mybatis提供了`sql` 片段可以提高sql的复用性。

The list of fields in the preceding SQL statement is difficult to write, and if there are more fields in the table, and other functions need to query those fields, our code will not be concise. Mybatis provides` sql `fragment  to improve sql reusability.

**SQL fragment ：**

* 将需要复用的SQL片段抽取到 `sql` 标签中
  Extract the SQL fragment you want to reuse into the `sql` tag

  ```xml
  <sql id="brand_column">
  	id, brand_name as brandName, company_name as companyName, ordered, description, status
  </sql>
  ```

  id属性值是唯一标识，引用时也是通过该值进行引用。
  The id attribute value is a unique identifier and is referenced by this value.

* 在原sql语句中进行引用
  References sql tags in the original sql statement
  
  使用 `include` 标签引用上述的 SQL 片段，而 `refid` 指定上述 SQL 片段的id值。
  
  Use the `include` tag to refer to the above SQL fragment, and `refid` to specify the id value of the above SQL fragment.
  
  ```xml
  <select id="selectAll" resultType="brand">
      select
      <include refid="brand_column" />
      from tb_brand;
  </select>
  ```

#### 2.2.5  使用resultMap解决上述问题 Use resultMap to solve the above problems

起别名 + sql片段的方式可以解决上述问题，但是它也存在问题。如果还有功能只需要查询部分字段，而不是查询所有字段，那么我们就需要再定义一个 SQL 片段，这就显得不是那么灵活。

The alias + sql fragment approach solves the above problem, but it has its problems. If we also have functionality that needs to query some fields instead of all fields, then we need to define a second SQL fragment , which is less flexible.

我们也可以使用resultMap来定义字段和属性的映射关系的方式解决上述问题。

We can also solve the above problem by using resultMap to define mappings between fields and properties.

* 在映射配置文件中使用resultMap定义 字段 和 属性 的映射关系
  The mapping of fields and properties is defined using resultMap in the mapping configuration file

  ```xml
  <resultMap id="brandResultMap" type="brand">
      <!--
              id：完成主键字段的映射 Complete the primary key field mapping
                  column：表的列名 The column name of the table
                  property：实体类的属性名 The property name of the entity class
              result：完成一般字段的映射 Complete the mapping of generic fields
                  column：表的列名 The column name of the table
                  property：实体类的属性名 The property name of the entity class
          -->
      <id column="id" property="id" />
      <result column="brand_name" property="brandName"/>
      <result column="company_name" property="companyName"/>
  </resultMap>
  ```

  > 注意：在上面只需要定义 字段名 和 属性名 不一样的映射，而一样的则不需要专门定义出来。
  >
  > Note: You only need to define mappings where the field name is different from the property name, but the same one does not need to be defined.

* SQL语句正常编写

  ```xml
  <select id="selectAll" resultMap="brandResultMap">
      select *
      from tb_brand;
  </select>
  ```

#### 2.2.6  小结 brief summary

实体类属性名 和 数据库表列名 不一致，不能自动封装数据

The entity class property name and the database table column name are inconsistent, and the data can not be automatically encapsulated

* ==起别名：==在SQL语句中，对不一样的列名起别名，别名和实体类属性名一样
  In SQL statements, different column names are aliased, the same as the entity class property names
  * 可以定义 <sql>片段，提升复用性  You can define <sql> fragments to improve reusability
* ==resultMap：==定义<resultMap> 完成不一致的属性名和列名的映射
  Define <resultMap> to map inconsistent property names and column names

而我们最终选择使用 resultMap的方式。映射配置文件中查询所有的 statement 书写如下：

And we end up using resultMap. The statement for querying all the brands in the mapping configuration file is written as follows:

```xml
<resultMap id="brandResultMap" type="brand">
    <!--
            id：完成主键字段的映射 Complete the primary key field mapping
                column：表的列名 The column name of the table
                property：实体类的属性名 The property name of the entity class
            result：完成一般字段的映射 Complete the mapping of generic fields
                column：表的列名 The column name of the table
                property：实体类的属性名 The property name of the entity class
        -->
    <id column="id" property="id" />
    <result column="brand_name" property="brandName"/>
    <result column="company_name" property="companyName"/>
</resultMap>

<select id="selectAll" resultMap="brandResultMap">
    select * from tb_brand;
</select>
```

### 2.3  查询详情 Query Details

<img src="assets/image-20210729180118287.png" alt="image-20210729180118287" style="zoom:80%;" />

有些数据的属性比较多，在页面表格中无法全部实现，而只会显示部分，而其他属性数据的查询可以通过 `查看详情` 来进行查询，如上图所示。

Some of the properties of the data are more, in the page table can not be all realized, but only a part of the display, and other properties data query can click `view details` to query, as shown in the figure above.

查看详情功能实现步骤：

* 编写接口方法：Mapper接口

  <img src="assets/image-20210729180604529.png" alt="image-20210729180604529" style="zoom:80%;" />

  * 参数：id

    查看详情就是查询某一行数据，所以需要根据id进行查询。而id以后是由页面传递过来。

  * 结果：Brand

    根据id查询出来的数据只要一条，而将一条数据封装成一个Brand对象即可

* 编写SQL语句：SQL映射文件

  <img src="assets/image-20210729180709318.png" alt="image-20210729180709318" style="zoom:80%;" />

* 执行方法、进行测试

#### 2.3.1  编写接口方法

在 `BrandMapper` 接口中定义根据id查询数据的方法 

```java
/**
  * 查看详情：根据Id查询
  */
Brand selectById(int id);
```

#### 2.3.2  编写SQL语句

在 `BrandMapper.xml` 映射配置文件中编写 `statement`，使用 `resultMap` 而不是使用 `resultType`

```xml
<select id="selectById"  resultMap="brandResultMap">
    select * from tb_brand where id = #{id};
</select>
```

> 注意：上述SQL中的 #{id}先这样写，一会我们再详细讲解

#### 2.3.3  编写测试方法

在 `test/java` 下的 `com.huat.test`  包下的 `MybatisTest类中` 定义测试方法

```java
@Test
public void testSelectById() throws IOException {
    //接收参数，该id以后需要传递过来
    int id = 1;

    //1. 获取SqlSessionFactory
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    //2. 获取SqlSession对象
    SqlSession sqlSession = sqlSessionFactory.openSession();

    //3. 获取Mapper接口的代理对象
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    //4. 执行方法
    Brand brand = brandMapper.selectById(id);
    System.out.println(brand);

    //5. 释放资源
    sqlSession.close();
}
```

执行测试方法结果如下：

<img src="assets/image-20210729182223137.png" alt="image-20210729182223137" style="zoom:70%;" />

#### 2.3.4  参数占位符 Parameter placeholder

查询到的结果很好理解就是id为1的这行数据。而这里我们需要看控制台显示的SQL语句，能看到使用？进行占位。说明我们在映射配置文件中的写的 `#{id}` 最终会被？进行占位。接下来我们就聊聊映射配置文件中的参数占位符。

It's pretty easy to understand that the result of the query is this row with id 1. Here we need to look at the SQL statement displayed on the console, and you can see that the question mark(?) is used for placeholder. Note The '#{id}' we wrote in the mapping configuration file will eventually be occupied by the question mark(?). Next let's talk about parameter placeholders in mapping configuration files.

mybatis提供了两种参数占位符：
mybatis provides two parameter placeholders:

* #{} ：执行SQL时，会将 #{} 占位符替换为？，将来自动设置参数值。从上述例子可以看出使用#{} 底层使用的是 `PreparedStatement`
  When SQL is executed, the #{} placeholder is replaced with a question mark(?), which automatically sets the parameter values in the future. From the example above, you can see that using placeholders(#{}) is using a `java.sql.PreparedStatement` underneath

* ${} ：拼接SQL。底层使用的是 `Statement`，会存在SQL注入问题。如下图将 映射配置文件中的 #{} 替换成 ${} 来看效果
  Concatenating SQL. The underlying use is ` java.sql.Statement ` , which is subject to SQL injection issues. To see this in action, replace #{} with ${} in our mappings configuration file
  
  ```xml
  <select id="selectById"  resultMap="brandResultMap">
      select *
      from tb_brand where id = ${id};
  </select>
  ```
  
  重新运行查看结果如下：
  Rerun and see the following:
  
  <img src="assets/image-20210729184156019.png" alt="image-20210729184156019" style="zoom:70%;" />

> ==注意：==从上面两个例子可以看出，以后开发我们使用 #{} 参数占位符。
>
> Note: As you can see from the above two examples, in future development we will use #{} parameter placeholders.

#### 2.3.5  parameterType

对于有参数的mapper接口方法，我们在映射配置文件中应该配置 `ParameterType` 来指定参数类型。只不过该属性都可以省略。

For mapper interface methods with parameters, we should configure `ParameterType` in the mapping configuration file to specify the parameter type. These attributes can be omitted. 

```xml
<select id="selectById" parameterType="int" resultMap="brandResultMap">
    select *
    from tb_brand where id = ${id};
</select>
```

#### 2.3.6  SQL语句中特殊字符处理 Special character handling in SQL statements

以后肯定会在SQL语句中写一下特殊字符，比如某一个字段大于某个值，如下图

You will definitely write some special characters in your SQL statements, such as when a field is greater than a certain value, as shown in the following screenshot

<img src="assets/image-20210729184756094.png" alt="image-20210729184756094" style="zoom:80%;" />

可以看出报错了，因为映射配置文件是xml类型，而 > < 等这些字符在xml中有特殊含义，所以此时我们需要将这些符号进行转义，可以使用以下两种方式进行转义

You can see that there is an error, because the mapping configuration file is xml type, and characters such as > < have special meanings in xml, so we need to escape these symbols at this point, which can be done in one of two ways

* 转义字符 escape character

  下图的 `&lt;` 就是 `<` 的转义字符。

  The following `&lt; `is the escape character for` < `.

  <img src="assets/image-20210729185128686.png" alt="image-20210729185128686" style="zoom:60%;" />

* <![CDATA[内容]]>

  <img src="assets/image-20210729185030318.png" alt="image-20210729185030318" style="zoom:60%;" />

### 2.4  多条件查询 Multi-condition query

![image-20210729203804276](assets/image-20210729203804276.png)

我们经常会遇到如上图所示的多条件查询，将多条件查询的结果展示在下方的数据列表中。而我们做这个功能需要分析最终的SQL语句应该是什么样，思考两个问题

We will often encounter a multi-conditional query like the one above. The results of the multi-conditional query will be displayed in the data list below. To do this, we need to analyze what the final SQL statement should look like, Think about two questions

* 条件表达式 condition expression
* 如何连接 How to connect

条件字段 `企业名称`  和 `品牌名称` 需要进行模糊查询，所以条件应该是：

The condition fields`company_name` and `brand_name` need to be fuzzy, so the condition should be:

where status=? and company_name like ? and brand_name like ?

<img src="assets/image-20210729204458815.png" alt="image-20210729204458815" style="zoom:70%;" />

简单的分析后，我们来看功能实现的步骤：

After a brief analysis, let's look at the steps of function implementation:

* 编写接口方法 Writing interface methods
  * Parameter：All Query Conditions
  * Result：List<Brand>
* 在映射配置文件中编写SQL语句 Write SQL statements in the mapping configuration file

* 编写测试方法并执行 Write test methods and execute them

#### 2.4.1  编写接口方法 Writing interface methods

在 `BrandMapper` 接口中定义多条件查询的方法。

In the `BrandMapper` interface to define the multi-conditional query method.

而该功能有三个参数，我们就需要考虑定义接口时，参数应该如何定义。Mybatis针对多参数有多种实现

The method takes three parameters. There are several implementations of Mybatis for multiple parameters

* 使用 `@Param("参数名称")` 标记每一个参数，在映射配置文件中就需要使用 `#{参数名称}` 进行占位
  Mark each parameter with `@Param("parameter name")`, and use `#{parameter name}` as a placeholder in the mapping configuration file

  ```java
  List<Brand> selectByCondition(@Param("status") int status, @Param("companyName") String companyName,@Param("brandName") String brandName);
  ```

* 将多个参数封装成一个 实体对象 ，将该实体对象作为接口的方法参数。该方式要求在映射配置文件的SQL中使用 `#{parameter name}` 时，里面的内容必须和实体类属性名保持一致。
  Encapsulate multiple parameters into a single entity object that is used as a method parameter for the interface. This method requires that when `#{parameter name}` is used in the SQL mapping configuration file, the parameter name must be the same as the entity class property name.

  ```java
  List<Brand> selectByCondition(Brand brand);
  ```

* 将多个参数封装到map集合中，将map集合作为接口的方法参数。该方式要求在映射配置文件的SQL中使用 `#{parameter name}` 时，里面的内容必须和map集合中键的名称一致。
  Encapsulate multiple parameters into a map set, using the map set as a method parameter for the interface. This method requires that when `#{parameter name}` is used in the SQL of the mapping configuration file, the parameter name must be the same as the name of the key in the map collection.
  
  ```
  List<Brand> selectByCondition(Map map);
  ```

#### 2.4.2  编写SQL语句 Writing SQL Statements

在 `BrandMapper.xml` 映射配置文件中编写 `statement`，使用 `resultMap` 而不是使用 `resultType`

Write sql statement in `BrandMapper.xml` mapping configuration file, using `resultMap` instead of a `resultType`

```xml
<select id="selectByCondition" resultMap="brandResultMap">
    select *
    from tb_brand
    where status = #{status}
    and company_name like #{companyName}
    and brand_name like #{brandName}
</select>
```

#### 2.4.3  编写测试方法 Writing test methods

在 `test/java` 下的 `com.huat.test`包下的 `MybatisTest`类中 定义测试方法

```java
@Test
public void testSelectByCondition() throws IOException {
    // Receiving parameters
    int status = 1;
    String companyName = "华为";
    String brandName = "华为";

    // processing parameter
    companyName = "%" + companyName + "%";
    brandName = "%" + brandName + "%";

    //1. 获取SqlSessionFactory
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    //2. 获取SqlSession对象
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //3. 获取Mapper接口的代理对象
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    //4. 执行方法
	//Method One ：Interface method parameters are annotated with @Param
    //List<Brand> brands = brandMapper.selectByCondition(status, companyName, brandName);
    //Method Two ：Interface method parameters are entity class objects
     // Encapsulate objects
    /* Brand brand = new Brand();
        brand.setStatus(status);
        brand.setCompanyName(companyName);
        brand.setBrandName(brandName);*/
    
    //List<Brand> brands = brandMapper.selectByCondition(brand);
    
    //Method Three ：Interface method parameters are map collection objects
    Map map = new HashMap();
    map.put("status" , status);
    map.put("companyName", companyName);
    map.put("brandName" , brandName);
    List<Brand> brands = brandMapper.selectByCondition(map);
    System.out.println(brands);

    //5. 释放资源
    sqlSession.close();
}
```

#### 2.4.4  Dynamic SQL

上述功能实现存在很大的问题。用户在输入条件时，肯定不会所有的条件都填写，这个时候我们的SQL语句就不能那样写

There are big problems with the implementation of the above functions. When the user enters the criteria, we will not fill in all the criteria, and then our SQL statement cannot be written that way

例如用户只输入 当前状态 时，SQL语句就是

For example, when the user enters only the `state`, the SQL statement is

```sql
select * from tb_brand where status = #{status}
```

而用户如果只输入企业名称时，SQL语句就是

If the user only enters the `company_name`, the SQL statement is

```sql
select * from tb_brand where company_name like #{companName}
```

而用户如果输入了 `当前状态` 和 `企业名称 ` 时，SQL语句又不一样

And if the user enters the `status` and `company_name`, the SQL statement is not the same

```sql
select * from tb_brand where status = #{status} and company_name like #{companName}
```

针对上述的需要，Mybatis对动态SQL有很强大的支撑：

In view of the above requirements, Mybatis provides dynamic SQL functions:

> * if
>
> * choose (when, otherwise)
>
> * trim (where, set)
>
> * foreach

我们先学习 if 标签和 where 标签：

Let's start with the if tag and the where tag

* if tag: Conditional judgment

  * test 属性：逻辑表达式
    test property: Logical expression

  ```xml
  <select id="selectByCondition" resultMap="brandResultMap">
      select *
      from tb_brand
      where
          <if test="status != null">
              and status = #{status}
          </if>
          <if test="companyName != null and companyName != '' ">
              and company_name like #{companyName}
          </if>
          <if test="brandName != null and brandName != '' ">
              and brand_name like #{brandName}
          </if>
  </select>
  ```

  上面的这种SQL语句就会根据传递的参数值进行动态的拼接。如果此时status和companyName有值那么就会值拼接这两个条件。

  The above SQL statement is dynamically concatenated based on the passed parameter values. If the `status` and `companyName` have values at this point, the values will concatenate the two conditions.

  执行结果如下：

  ![image-20210729212510291](assets/image-20210729212510291.png)

  但是它也存在问题，如果此时给的参数值是

  But it also has a problem, if the value of the parameter given here is

  ```java
  Map map = new HashMap();
  // map.put("status" , status);
  map.put("companyName", companyName);
  map.put("brandName" , brandName);
  ```

  拼接的SQL语句就变成了

  The concatenated SQL statement becomes

  ```sql
  select * from tb_brand where and company_name like ? and brand_name like ?
  ```

  而上面的语句中 where 关键后直接跟 and 关键字，这就是一条错误的SQL语句。

  In the above statement, the where keyword is directly followed by the and keyword. This is an incorrect SQL statement.

  这时就可以使用 where 标签来解决

  You can use the where tag to solve this problem

* where tag

  * function：
    * 替换 `where` 关键字 Replace the `where` keyword
    * 会动态的去掉第一个条件前的 `and`关键字 It dynamically removes the `and` keyword before the first condition
    * 如果所有的参数没有值则不加 `where`关键字 If all parameters have no value, the `where` keyword is omitted

  ```xml
  <select id="selectByCondition" resultMap="brandResultMap">
      select *
      from tb_brand
      <where>
          <if test="status != null">
              and status = #{status}
          </if>
          <if test="companyName != null and companyName != '' ">
              and company_name like #{companyName}
          </if>
          <if test="brandName != null and brandName != '' ">
              and brand_name like #{brandName}
          </if>
      </where>
  </select>
  ```

  > 注意：需要给每个条件前都加上 `and` 关键字。
  >
  > Note: Each condition needs to be preceded by the `and` keyword.

### 2.5 单个条件（动态SQL）Single condition (dynamic SQL)

<img src="assets/image-20210729213613029.png" alt="image-20210729213613029" style="zoom:80%;" />

如上图所示，在查询时只能选择 `品牌名称`、`当前状态`、`企业名称` 这三个条件中的一个，但是用户到底选择哪儿一个，我们并不能确定。这种就属于单个条件的动态SQL语句。 

As shown in the figure above, only one of the three criteria of `brand_name`, `status`, and `company_name ` can be selected during the query, but we are not sure which one the user chooses. This is the dynamic SQL statement of a single condition.

这种需求需要使用到  `choose（when，otherwise）标签`  实现，  而 `choose` 标签类似于Java 中的switch语句。

This requirement requires the implementation of the `choose (when, otherwise) tag`, which is similar to the switch statement in Java.

通过一个案例来使用这些标签

Take a case study to learn how to use these tags

#### 2.5.1  编写接口方法 Writing interface methods

在 `BrandMapper` 接口中定义单条件查询的方法。

In the `BrandMapper` interface defines a single conditional query method.

```java
/**
  * Single condition dynamic query
  * @param brand
  * @return
  */
List<Brand> selectByConditionSingle(Brand brand);
```

#### 2.5.2  编写SQL语句 Writing SQL Statements

在 `BrandMapper.xml` 映射配置文件中编写 `statement`，使用 `resultMap` 而不是使用 `resultType`

Write a `statement` in a `BrandMapper.xml` mapping configuration file, using a `resultMap` instead of a `resultType`

```xml
<select id="selectByConditionSingle" resultMap="brandResultMap">
    select *
    from tb_brand
    <where>
        <choose><!--相当于switch-->
            <when test="status != null"><!--相当于case-->
                status = #{status}
            </when>
            <when test="companyName != null and companyName != '' "><!--相当于case-->
                company_name like #{companyName}
            </when>
            <when test="brandName != null and brandName != ''"><!--相当于case-->
                brand_name like #{brandName}
            </when>
        </choose>
    </where>
</select>
```

#### 2.5.3  编写测试方法

在 `test/java` 下的 `com.huat.mapper`  包下的 `MybatisTest类中` 定义测试方法

```java
@Test
public void testSelectByConditionSingle() throws IOException {
    //接收参数
    int status = 1;
    String companyName = "华为";
    String brandName = "华为";

    // 处理参数
    companyName = "%" + companyName + "%";
    brandName = "%" + brandName + "%";

    //封装对象
    Brand brand = new Brand();
    //brand.setStatus(status);
    brand.setCompanyName(companyName);
    //brand.setBrandName(brandName);

    //1. 获取SqlSessionFactory
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    //2. 获取SqlSession对象
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //3. 获取Mapper接口的代理对象
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);
    //4. 执行方法
    List<Brand> brands = brandMapper.selectByConditionSingle(brand);
    System.out.println(brands);

    //5. 释放资源
    sqlSession.close();
}
```

执行测试方法结果如下：

<img src="assets/image-20210729214548756.png" alt="image-20210729214548756" style="zoom:70%;" />

### 2.6  Add Brand

<img src="assets/image-20210729214917317.png" alt="image-20210729214917317" style="zoom:70%;" />

如上图是我们平时在添加数据时展示的页面，而我们在该页面输入想要的数据后添加 `提交` 按钮，就会将这些数据添加到数据库中。接下来我们就来实现添加数据的操作。

As shown in the figure above, we usually show the page when adding data. When we input the desired data on the page, we press the `submit` button, which will add the data to the database. Now let's implement the operation of adding data.

#### 2.6.1  Writing interface methods

在 `BrandMapper` 接口中定义添加方法。

define add method in `BrandMapper` interface

```java
void add(Brand brand);
```

#### 2.6.2  Writing SQL statement

在 `BrandMapper.xml` 映射配置文件中编写添加数据的 `statement`

writing sql `statement` in `BrandMapper.xml` configuration file

```xml
<insert id="add">
    insert into tb_brand (brand_name, company_name, ordered, description, status)
    values (#{brandName}, #{companyName}, #{ordered}, #{description}, #{status});
</insert>
```

#### 2.6.3  Writing test methods

在 `test/java` 下的 `com.huat.mapper`  包下的 `MybatisTest类中` 定义测试方法

Define test method in `MyBatisTest` class of `com.huat.mapper` package

```java
@Test
public void testAdd() throws IOException {
    // receive parameter
    int status = 1;
    String companyName = "波导手机";
    String brandName = "波导";
    String description = "手机中的战斗机";
    int ordered = 100;

    // encapsulate data
    Brand brand = new Brand();
    brand.setStatus(status);
    brand.setCompanyName(companyName);
    brand.setBrandName(brandName);
    brand.setDescription(description);
    brand.setOrdered(ordered);

    //1. get SqlSessionFactory
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    //2. get SqlSession object
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //set automatic commit transaction, so you needn't manual commit transaction
    //SqlSession sqlSession = sqlSessionFactory.openSession(true); 
    //3. get Mapper interface proxy object
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);
    //4. execute method
    brandMapper.add(brand);
    // commit transaction
    sqlSession.commit();
    //5. release resources
    sqlSession.close();
}
```

执行结果如下：

![image-20210729220348255](assets/image-20210729220348255.png)

#### 2.6.4  Adding Brand - return primary key value

在数据添加成功后，有时候需要获取插入数据库数据的主键（主键是自增长）。

After the data has been successfully added,  sometimes you need to get the primary key (which is auto_increment) after inserted  data.

比如：添加订单和订单项，如下图就是京东上的订单

<img src="assets/image-20210729221207962.png" alt="image-20210729221207962" style="zoom:80%;" />

订单数据存储在订单表中，订单项存储在订单项表中。

The order data is stored in the order table, and the order items are stored in the order items table.

* 添加订单数据 
  adding order data

  <img src="assets/image-20210729221049462.png" alt="image-20210729221049462" style="zoom:80%;" />

* 添加订单项数据，订单项中需要设置所属订单的id 
  adding the order item data where you need to set the id of the order 
  
  <img src="assets/image-20221125151038032.png" alt="image-20221125151038032" style="zoom: 67%;" />

明白了什么是 `主键返回` 。接下来我们简单模拟一下，在添加完数据后打印id属性值，能打印出来说明已经获取到了。

Understand what `primary key return` is. Let's do a simple simulation and print the id property value after adding the data. If the ID property is printed, it indicates that it has been obtained.

我们将上面添加品牌数据的案例中映射配置文件里 `statement` 进行修改，如下

modify the add method in BrandMapper SQL statement

```xml
<insert id="add" useGeneratedKeys="true" keyProperty="id">
    insert into tb_brand (brand_name, company_name, ordered, description, status)
    values (#{brandName}, #{companyName}, #{ordered}, #{description}, #{status});
</insert>
```

> 在 insert 标签上添加如下属性：
> add the following attributes on the insert tag
>
> * useGeneratedKeys： is the primary key that can be used to get automatic growth. true means get the primary key
> * keyProperty  ：specifies which property to wrap the obtained primary key

### 2.7  Update Brand

<img src="assets/image-20210729222642700.png" alt="image-20210729222642700" style="zoom:80%;" />

如图所示是修改页面，用户填写需要修改的数据后，点击 `提交` 按钮，就会将数据库中对应的数据进行修改。接下来我们就具体来实现

As shown in the picture is the modification page. After the user fills in the data to be modified, click the `submit` button, and the corresponding data in the database will be modified. Now let's do that in detail

#### 2.7.1  writing interface method

在 `BrandMapper` 接口中定义修改方法。

define update method in `BrandMapper` interface

```java
void update(Brand brand);
```

> 上述方法参数 Brand 就是封装了需要修改的数据，而id肯定是有数据的，这也是和添加方法的区别。
>
> The method parameter Brand encapsulates the data that needs to be modified, the id property certainly has data, which is different from adding methods.

#### 2.7.2  writing sql statment

在 `BrandMapper.xml` 映射配置文件中编写修改数据的 `statement`。

writing sql `statement` in `BrandMapper.xml` configuration file

```xml
<update id="update">
    update tb_brand
    <set>
        <if test="brandName != null and brandName != ''">
            brand_name = #{brandName},
        </if>
        <if test="companyName != null and companyName != ''">
            company_name = #{companyName},
        </if>
        <if test="ordered != null">
            ordered = #{ordered},
        </if>
        <if test="description != null and description != ''">
            description = #{description},
        </if>
        <if test="status != null">
            status = #{status}
        </if>
    </set>
    where id = #{id};
</update>
```

> *set* 标签可以用于动态包含需要更新的列，忽略其它不更新的列。
>
> The *set* tag can be used to dynamically contain columns that need to be updated, ignoring other columns that are not updated.

#### 2.7.3  writing test method

在 `test/java` 下的 `com.huat.mapper`  包下的 `MybatisTest类中` 定义测试方法

define test method in `MyBatisTest` class of `com.huat.mapper` package

```java
@Test
public void testUpdate() throws IOException {
    //接收参数
    int status = 0;
    String companyName = "波导手机";
    String brandName = "波导";
    String description = "波导手机,手机中的战斗机";
    int ordered = 200;
    int id = 6;

    //封装对象
    Brand brand = new Brand();
    brand.setStatus(status);
    //        brand.setCompanyName(companyName);
    //        brand.setBrandName(brandName);
    //        brand.setDescription(description);
    //        brand.setOrdered(ordered);
    brand.setId(id);

    //1. 获取SqlSessionFactory
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    //2. 获取SqlSession对象
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //SqlSession sqlSession = sqlSessionFactory.openSession(true);
    //3. 获取Mapper接口的代理对象
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);
    //4. 执行方法
    brandMapper.update(brand);
    //提交事务
    sqlSession.commit();
    //5. 释放资源
    sqlSession.close();
}
```

执行测试方法结果如下：

![image-20210729224205522](assets/image-20210729224205522.png)

从结果中SQL语句可以看出，只修改了 `status`  字段值，因为我们给的数据中只给Brand实体对象的 `status` 属性设置值了。这就是 `set` 标签的作用。

As you can see from the SQL statement in the result, only the value of the `status` field was changed, because we only set the value for the` status` property of the Brand entity object in the data we gave. That's what the `set` tag is for.

### 2.8  delete one

![image-20210729224549305](assets/image-20210729224549305.png)

如上图所示，每行数据后面都有一个 `删除` 按钮，当用户点击了该按钮，就会将该行数据删除掉。那我们就需要思考，这种删除是根据什么进行删除呢？是通过主键id删除，因为id是表中数据的唯一标识。

As shown in the figure above, there is a `delete` button at the end of each row of data. When the user clicks the button, the row of data will be deleted. Then we need to think, what is this deletion based on? Is deleted by the primary key id, because the id is a unique identifier for the data in the table.

#### 2.8.1  writing interface method

在 `BrandMapper` 接口中定义根据id删除方法。

define delete method by id in `BrandMapper` interface

```java
void deleteById(int id);
```

#### 2.8.2  writing sql statement

在 `BrandMapper.xml` 映射配置文件中编写删除一行数据的 `statement`

writing sql `statement` in `BrandMapper.xml` configuration file

```xml
<delete id="deleteById">
    delete from tb_brand where id = #{id}
</delete>
```

#### 2.8.3  writing test method

在 `test/java` 下的 `com.huat.mapper`  包下的 `MybatisTest类中` 定义测试方法

define test method in `MyBatisTest` class of `com.huat.mapper` package

```java
 @Test
public void testDeleteById() throws IOException {
    //接收参数
    int id = 6;

    //1. 获取SqlSessionFactory
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    //2. 获取SqlSession对象
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //SqlSession sqlSession = sqlSessionFactory.openSession(true);
    //3. 获取Mapper接口的代理对象
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);
    //4. 执行方法
    brandMapper.deleteById(id);
    //提交事务
    sqlSession.commit();
    //5. 释放资源
    sqlSession.close();
}
```

运行过程只要没报错，直接到数据库查询数据是否还存在。

### 2.9  batch delete

<img src="assets/image-20210729225713894.png" alt="image-20210729225713894" style="zoom:70%;" />



如上图所示，用户可以选择多条数据，然后点击上面的 `删除` 按钮，就会删除数据库中对应的多行数据。

As shown in the figure above, user can select multiple data, and then click the `Delete` button above, will delete the corresponding multiple rows of data in the database.

#### 2.9.1  writing interface method

在 `BrandMapper` 接口中定义删除多行数据的方法。

define a method to delete multiple lines of data in the `BrandMapper` interface.

```java
/**
  * batch delete
  */
void deleteByIds(int[] ids);
or
void deleteByIds(@Param("ids") int[] ids);    
```

> 参数是一个数组，数组中存储的是多条数据的id
>
> the parameter is an array that stores the ids of multiple pieces of data

#### 2.9.2  writing sql statment

在 `BrandMapper.xml` 映射配置文件中编写删除多条数据的 `statement`。

writing batch delete sql `statement` in `BrandMapper.xml` configuration file

编写SQL时需要遍历数组来拼接SQL语句。Mybatis 提供了 `foreach` 标签供我们使用

When you write SQL, you need iterate over the array to concatenate SQL statements. Mybatis provides the `foreach` tag for us to use

**foreach tag**

用来迭代任何可迭代的对象（如数组，集合）。

Iterate over any iterable object (e.g., array, collection).

* collection attribute：
  * mybatis会将数组参数，封装为一个Map集合。
       mybatis encapsulates the array arguments as a collection of maps.
    * default：array
    * 使用@Param注解改变map集合默认key的名称
      Use @Param to change the name of the default key in the map collection
* item attribute：The elements retrieved in this iteration。
* separator attribute：集合项迭代之间的分隔符。`foreach` 标签不会错误地添加多余的分隔符。也就是最后一次迭代不会加分隔符。
  A separator between iterations of a collection item. The `foreach` tag doesn't add extra separators by mistake. That is, the last iteration does not add a separator.
* open attribute：This value is concatenated only once before the SQL statement is concatenated
* close attribute：The value is concatenated only once after the SQL statement is concatenated

```xml
<delete id="deleteByIds">
    delete from tb_brand where id
    in
    <foreach collection="array" item="id" separator="," open="(" close=")">
        #{id}
    </foreach>
</delete>
or
<delete id="deleteByIds">
    delete from tb_brand where id
    in
    <foreach collection="ids" item="id" separator="," open="(" close=")">
        #{id}
    </foreach>
</delete>
```

> 假如数组中的id数据是{1,2,3}，那么拼接后的sql语句就是：
>
> If the ids in the array are {1,2,3}, the concatenation produces the following sql statement:
>
> ```sql
> delete from tb_brand where id in (1,2,3);
> ```

#### 2.9.3  writing test method

在 `test/java` 下的 `com.huat.mapper`  包下的 `MybatisTest类中` 定义测试方法

define test method in `MyBatisTest` class of `com.huat.mapper` package

```java
@Test
public void testDeleteByIds() throws IOException {
    //接收参数
    int[] ids = {5,7,8};

    //1. 获取SqlSessionFactory
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    //2. 获取SqlSession对象
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //SqlSession sqlSession = sqlSessionFactory.openSession(true);
    //3. 获取Mapper接口的代理对象
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);
    //4. 执行方法
    brandMapper.deleteByIds(ids);
    //提交事务
    sqlSession.commit();
    //5. 释放资源
    sqlSession.close();
}
```

### 2.10  Mybatis参数传递 Mybatis parameters passed

Mybatis 接口方法中可以接收各种各样的参数，如下：

The Mybatis interface methods accept a variety of parameters, as follows:

* 多个参数 multiple parameters
* 单个参数：单个参数又可以是如下类型
  single parameter: A single parameter can be of the following types
  * POJO type
  * Map type
  * Collection type
  * List type
  * Array type
  * other type

#### 2.10.1  多个参数 multiple parameters

接收多个参数需要使用 `@Param` 注解，那么为什么要加该注解呢？这个问题要弄明白就必须来研究Mybatis 底层对于这些参数是如何处理的。

Accepting multiple arguments requires the '@Param' annotation, so why use it? To understand this, we'll have to look at how Mybatis handles these parameters under the surface.

```java
User select(@Param("username") String username,@Param("password") String password);
```

```xml
<select id="select" resultType="user">
	select *
    from tb_user
    where 
    	username=#{username}
    	and password=#{password}
</select>
```

我们在接口方法中定义多个参数，Mybatis 会将这些参数封装成 Map 集合对象，值就是参数值，而键在没有使用 `@Param` 注解时有以下命名规则：

We define multiple parameters in the interface method, and Mybatis will wrap these parameters into a Map collection object, where the value is the parameter value, and the key has the following naming convention when it is not annotated with `@Param` :

* 以 arg 开头  ：第一个参数就叫 arg0，第二个参数就叫 arg1，以此类推。如：
  Start with arg: the first argument is called arg0, the second is called arg1, and so on. Such as:

  > map.put("arg0"，parameter value 1);
  >
  > map.put("arg1"，parameter value 2);

* 以 param 开头 ： 第一个参数就叫 param1，第二个参数就叫 param2，依次类推。如：
  Start with param: the first argument is called param1, the second is called param2, and so on. Such as:
  
  > map.put("param1"，parameter value 1);
  >
  > map.put("param2"，parameter value 2);

**Code validation：**

* define method in `UserMapper` interface

  ```java
  User select(String username,String password);
  ```

* define sql statment in `UserMapper.xml` configuration file

  ```xml
  <select id="select" resultType="user">
  	select *
      from tb_user
      where 
      	username=#{arg0}
      	and password=#{arg1}
  </select>
  ```

  or

  ```xml
  <select id="select" resultType="user">
  	select *
      from tb_user
      where 
      	username=#{param1}
      	and password=#{param2}
  </select>
  ```

* The result of running the code is as follows

  <img src="assets/image-20210805230303461.png" alt="image-20210805230303461" style="zoom:80%;" />

  在映射配置文件的SQL语句中使用 `arg` 和 `param` 书写，代码的可读性会变的特别差，此时可以使用 `@Param` 注解。
  
  If you use `arg` and `param` in the SQL statement of the mapping configuration file, the readability of the code will become particularly poor. In this case, you can use the `@Param` annotation.

在接口方法参数上使用 `@Param` 注解，Mybatis 会将 `arg` 开头的键名替换为对应注解的属性值。

Using the `@Param` annotation on an interface method parameter, Mybatis replaces the key starting with `arg` with the attribute value of the corresponding annotation.

**Code validation：**

* 在 `UserMapper` 接口中定义如下方法，在 `username` 参数前加上 `@Param` 注解
  define method in `UserMapper` interface, add `@Param` annotation before the `username` parameter

  ```java
  User select(@Param("username") String username, String password);
  ```

  Mybatis 在封装 Map 集合时，键名就会变成如下：

  When Mybatis encapsulates the Map collection, the key name becomes as follows:

  > map.put("username"，parameter value 1);
  >
  > map.put("arg1"，parameter value 2);
  >
  > map.put("param1"，parameter value 1);
  >
  > map.put("param2"，parameter value 2);

* 在 `UserMapper.xml` 映射配置文件中定义SQL
  define sql statement in `UserMapper.xml` configuration file

  ```xml
  <select id="select" resultType="user">
  	select *
      from tb_user
      where 
      	username=#{username}
      	and password=#{param2}
  </select>
  ```

* 运行程序结果没有报错。而如果将 `#{}` 中的 `username` 还是写成  `arg0` 
  No error was reported after running the program results. If the `username` in `#{}` is still written as `arg0`

  ```xml
  <select id="select" resultType="user">
  	select *
      from tb_user
      where 
      	username=#{arg0}
      	and password=#{param2}
  </select>
  ```

* 运行程序则可以看到错误
  Run the program and you'll see the error
  
  ![image-20210805231727206](assets/image-20210805231727206.png)

==结论：以后接口参数是多个时，在每个参数上都使用 `@Param` 注解。这样代码的可读性更高。==
Conclusion: In the future, when there are multiple interface parameters, use `@Param` annotation on each parameter. This makes the code more readable

#### 2.10.2  单个参数 single paramter

* POJO 类型

  直接使用。要求 `属性名` 和 `参数占位符名称` 一致
  Use it directly. The `attribute name` and the `parameter placeholder name` are required to be the same

* Map 集合类型

  直接使用。要求 `map集合的键名` 和 `参数占位符名称` 一致

  Use it directly. The `The key name of the map collection` and the `parameter placeholder name` are required to be the same

* Collection 集合类型

  Mybatis 会将集合封装到 map 中，如下：
  Mybatis encapsulates the collection into the map as follows:

  > map.put("arg0"，collection);
  >
  > map.put("collection"，collection);

  ==可以使用 `@Param` 注解替换map集合中默认的 arg 键名。==

  The default arg key name in the map collection can be replaced with the `@Param` annotation

* List 集合类型

  Mybatis 会将集合封装到 map 中，如下：

  Mybatis encapsulates the List collection into the map as follows:

  > map.put("arg0"，list);
  >
  > map.put("collection"，list);
  >
  > map.put("list"，list);

  ==可以使用 `@Param` 注解替换map集合中默认的 arg 键名。==

  The default arg key name in the map collection can be replaced with the `@Param` annotation

* Array 类型

  Mybatis 会将集合封装到 map 集合中，如下：
  Mybatis encapsulates the Array into the map as follows:

  > map.put("arg0"，array);
  >
  > map.put("array"，array);

  ==可以使用 `@Param` 注解替换map集合中默认的 arg 键名。==

  The default arg key name in the map collection can be replaced with the `@Param` annotation

* 其他类型 other type

  比如int类型，`参数占位符名称` 叫什么都可以。尽量做到见名知意
  
  For int type, the parameter placeholder name can be anything. Try to know the name by name

### 注解实现CRUD Annotations implement CRUD

使用注解开发会比配置文件开发更加方便。如下就是使用注解进行开发

Developing with annotations is easier than developing with configuration files. Here's how to develop with annotations

```java
@Select(value = "select * from tb_user where id = #{id}")
public User select(int id);
```

> ==Notice：==
>
> * 注解是用来替换映射配置文件方式配置的，所以使用了注解，就不需要再映射配置文件中书写对应的 `statement`
>   Annotations are used to replace the mapping configuration file configuration, so there is no need to write the corresponding statement in the mapping configuration file

Mybatis 针对 CRUD 操作都提供了对应的注解，已经做到见名知意。如下：

Mybatis provides corresponding annotations for CRUD operations, which have been done by name. As follows:

* @Select
* @Insert
* @Update
* @Delete

接下来我们做一个案例来使用 Mybatis 的注解开发

Next let's do an example using Mybatis annotation development

**code implementation：**

* 将之前案例中 `UserMapper.xml` 中的 根据id查询数据 的 `statement` 注释掉
  Comment out the statement in the previous case `UserMapper.xml` that query the data by id

  <img src="assets/image-20210805235229938.png" alt="image-20210805235229938" style="zoom:70%;" />

* 在 `UserMapper` 接口的 `selectById` 方法上添加注解
  add an annotation to the `selectById` method of the `UserMapper` interface 

  <img src="assets/image-20210805235405070.png" alt="image-20210805235405070" style="zoom:70%;" />

* 运行测试程序也能正常查询到数据
  Running the test program can also query the data normally

我们课程上只演示这一个查询的注解开发，其他的同学们下来可以自己实现，都是比较简单。

Our course only demonstrates the annotation development of this query, other functions can come down to achieve by yourself, which are relatively simple.

==Notice：==在官方文档中 `入门` 中有这样的一段话：In the official document 'Getting Started' there is this passage:

The annotations are a lot cleaner for simple statements, however, Java Annotations are both limited and messier for more complicated statements. Therefore, if you have to do anything complicated, you're better off with XML mapped statements.

It will be up to you and your project team to determine which is right for you, and how important it is to you that your mapped statements be defined in a consistent way. That said, you're never locked into a single approach. You can very easily migrate Annotation based Mapped Statements to XML and vice versa.

![image-20210805234302849](assets/image-20210805234302849.png)

所以，==注解完成简单功能，配置文件完成复杂功能。==

So, the annotation does the simple function, and the configuration file does the complex function. 

而我们之前写的动态 SQL 就是复杂的功能，如果用注解使用的话，就需要使用到 Mybatis 提供的SQL构建器来完成，而对应的代码如下：

The dynamic SQL we wrote before is a complex function, if using annotations, it needs to use the SQL builder provided by Mybatis to complete, and the corresponding code is as follows:

<img src="assets/image-20210805234842497.png" alt="image-20210805234842497" style="zoom:70%;" />

上述代码将java代码和SQL语句融到了一块，使得代码的可读性大幅度降低。

This code merges java code and SQL statements together, making the code much less readable.

## 3 SSM整合案例

### 3.1 技术点

基础框架 - SSM（SpringMVC+Spring+Mybatis）

数据库 - MySQL

前端框架 - Pintuer 快速搭建简洁美观的页面

项目的依赖管理 - Maven

分页 -  PageHelper

逆向工程 - Mybatis Generator

### 3.2 基础环境搭建

#### 3.2.1 创建maven工程 ssm-crud

![image-20221110085048829](assets/image-20221110084937936.png)

Click the plus sign button to create web.xml file. Modify the web.xml path and click ok button.

G:\teach\ssmcrud\src\main\webapp\WEB-INF\web.xml

![image-20221110085355868](assets/image-20221110085355868.png)

#### 3.2.2 import project dependencies

https://mvnrepository.com/

```xml
    <dependencies>
        <!--SpringMVC-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.3.19</version>
        </dependency>
        <!--SpringJDBC-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.3.19</version>
        </dependency>
        <!--Spring aspects-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>5.3.19</version>
        </dependency>
        <!--Mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.9</version>
        </dependency>
        <!--spring integrates with Mybatis packages-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.7</version>
        </dependency>
        <!--logback-->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.2.3</version>
        </dependency>
        <!--Druid datasource-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.2.9</version>
        </dependency>
        <!--Mysql-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.28</version>
        </dependency>
        <!--jstl-->
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
        <!--servlet api-->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
            <!--scope means after the project is published to the server tomcat, it provided by tomcat-->
            <scope>provided</scope>
        </dependency>
        <!-- jsp-api -->
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>javax.servlet.jsp-api</artifactId>
            <version>2.3.3</version>
            <scope>provided</scope>
        </dependency>
        <!--junit-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>
```

#### 3.2.3 Deploy the tomcat server

![image-20221110095202364](assets/image-20221110095202364.png)

![image-20221110095315984](assets/image-20221110095315984.png)

![image-20221110095510315](assets/image-20221110095510315.png)

![image-20221110095549157](assets/image-20221110095549157.png)

![image-20221110095621180](assets/image-20221110095621180.png)

![image-20221110095654480](assets/image-20221110095654480.png)

### 3.3 Configuration file

#### 3.3.1 project configuration file (web.xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">

    <!--1.start spring container-->
    <!-- needed for ContextLoaderListener -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    <!-- Bootstraps the root web application context before servlet initialization -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!--2. Spring mvc front-end controller, which intercepts all requests-->
    <servlet>
        <servlet-name>DispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>DispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!--3. Character code filter-->
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
            <param-name>forceResponseEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!--4. Use RESTful URIs-->
    <filter>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```

#### 3.3.2 spring configuration file (spring-mvc.xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--configure component scan, only scan controller component-->
    <context:component-scan base-package="com.huat.crud.controller"/>

    <!--configure view resolver -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!--default servlet handler-->
    <mvc:default-servlet-handler/>
    <!--spring mvc annotation driven,support for some more advanced features-->
    <mvc:annotation-driven/>
</beans>
```

#### 3.3.3 spring configuration file (applicationContext.xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!--Spring configuration files, which are mostly configured for business logic-->

    <!--configure component scan, Scan all package but except the controller package-->
    <context:component-scan base-package="com.huat.crud">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

        <!--1.将mybatis配置环境集成到spring中，交由Spring托管
              The mybatis configuration environment is integrated into spring and hosted by Spring
                configure datasource-->
    <context:property-placeholder location="classpath:dbconfig.properties"/>
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driverClassName}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

    <!--configure integration with mybatis
       2. 将SqlSessionFactory交给Spring托管
          Let Spring host SqlSessionFactory-->
    <bean class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--specifies the location of the mybatis global configuration file-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <!--specifies the mybatis datasource-->
        <property name="dataSource" ref="dataSource"/>
        <!--specifies the location of the mybatis mapper file-->
        <property name="mapperLocations" value="classpath:mapper/*.xml"></property>
    </bean>

    <!--configure scanner, put mybatis implementation class of interface into ioc container-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.huat.crud.dao"/>
    </bean>

    <!--configure transaction-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!--datasource-->
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <aop:config>
        <!--pointcut expression-->
        <aop:pointcut id="txPoint" expression="execution(* com.huat.crud.service.*.*(..))"></aop:pointcut>
        <!--configure transaction enhancements-->
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPoint"/>
    </aop:config>

    <!--configure transaction enhancements-->
    <tx:advice id="txAdvice">
        <tx:attributes>
            <!--all the methods are transaciton method-->
            <tx:method name="*"/>
            <!--The methods that start with get are read-only-->
            <tx:method name="get*" read-only="true"/>
        </tx:attributes>
    </tx:advice>

    <!--The core of Spring configuration files (data sources, integration with mybatis, transaction management)-->
</beans>
```

- dbconfig.properties

```xml
jdbc.url=jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=utf8&useSSL=true&serverTimezone=Asia/Shanghai
jdbc.driverClassName=com.mysql.cj.jdbc.Driver
jdbc.username=root
jdbc.password=1234
or
jdbc.url=jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf8&serverTimezone=Asia/Shanghai
jdbc.driverClassName=com.mysql.cj.jdbc.Driver
jdbc.username=root
jdbc.password=1234
```

- logback.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!--console:表示当前的日志信息是可以输出到控制台的
    console: Indicates that the current log message is available for output to the console
    -->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>[%level] %blue(%d{HH:mm:ss.SSS}) %cyan([%thread]) %boldGreen(%logger{15}) - %msg %n</pattern>
        </encoder>
    </appender>

    <!--
        日志输出级别(优先级高到低): Log output level (high to low priority)
        error: 错误 - 系统的故障日志 Error - The failure log of the system
        warn: 警告 - 存在风险或使用不当的日志 Warning - There are risks or improperly used logs
        info: 一般性消息 General message
        debug: 程序内部用于调试信息 Used internally in a program to debug information
        trace: 程序运行的跟踪信息 Information tracing the execution of the program
     -->
    <root level="debug">
        <appender-ref ref="console"/>
    </root>
</configuration>
```

#### 3.3.4 mybatis configuration file (mybatis-config.xml)

https://mybatis.org/mybatis-3/

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <!--enable UserscoreCmelCase-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
    <typeAliases>
        <package name="com.huat.crud.bean"/>
    </typeAliases>
</configuration>
```

### 3.4 Create database and tables

![image-20221110145759307](assets/image-20221110145759307.png)

![image-20221110150218183](assets/image-20221110150218183.png)

![image-20221110145900296](assets/image-20221110145900296.png)

```sql
create table tbl_emp
(
	emp_id int not null auto_increment primary key,
	emp_name varchar(255) not null,
	gender char(1) not null,
	email varchar(255),
	d_id int not null
)

create table tbl_dept
(
  dept_id int not null auto_increment primary key,
	dept_name varchar(255) not null
)

alter table tbl_emp add constraint tbl_emp_dept foreign key (d_id) references tbl_dept(dept_id);
```

### 3.5 Mybatis Generater

use mybatis reverse engineering to generate the beans and mappers

http://mybatis.org/generator/

#### 3.5.1 import dependencies

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis.generator/mybatis-generator-core -->
<dependency>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-core</artifactId>
    <version>1.4.1</version>
</dependency>
```

#### 3.5.2 MyBatis GeneratorXML Configuration File Reference

![image-20221110152712210](assets/image-20221110152712210.png)

![image-20221126092831618](assets/image-20221126092831618.png)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>

    <context id="DB2Tables" targetRuntime="MyBatis3">
        <!--When the property value is true, no comments will be added to any generated element-->
        <commentGenerator>
            <property name="suppressAllComments" value="true" />
        </commentGenerator>
        <!-- configure database connection -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf8&amp;serverTimezone=Asia/Shanghai"
                        userId="root"
                        password="1234">
        </jdbcConnection>

        <javaTypeResolver>
            <property name="forceBigDecimals" value="false" />
        </javaTypeResolver>

        <!-- Specifies where the javaBean is generated -->
        <javaModelGenerator targetPackage="com.huat.crud.bean" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
            <property name="trimStrings" value="true" />
        </javaModelGenerator>

        <!--Specifies the location where the sql mapping file is generated -->
        <sqlMapGenerator targetPackage="mapper" targetProject=".\src\main\resources">
            <property name="enableSubPackages" value="true" />
        </sqlMapGenerator>

        <!-- Specifies where the mapper interface is generated -->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.huat.crud.dao" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>

        <!-- Specify the generation strategy for each table -->
        <table tableName="tbl_emp" domainObjectName="Employee"></table>
        <table tableName="tbl_dept" domainObjectName="Department"></table>
    </context>
</generatorConfiguration>
```

#### 3.5.3 Running MBG from Java with an XML Configuration File

![image-20221110155456142](assets/image-20221110155456142.png)

```java
package com.huat.crud.utils;

import org.mybatis.generator.api.MyBatisGenerator;
import org.mybatis.generator.config.Configuration;
import org.mybatis.generator.config.xml.ConfigurationParser;
import org.mybatis.generator.internal.DefaultShellCallback;

import java.io.File;
import java.util.ArrayList;
import java.util.List;

public class MBGTest {
    public static void main(String[] args) throws Exception {
        List<String> warnings = new ArrayList<>();
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

It is time for a miracle. when we running the above code, mybatis generator help me to generate all the code. 

<img src="assets/image-20221126094226965.png" alt="image-20221126094226965" style="zoom:80%;" />

### 3.6 Spring Unit Testing

import spring test dependencies

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.3.19</version>
    <scope>test</scope>
</dependency>
```

write testing code

The example class specifies how to build a dynamic where clause. Each non-BLOB column in the table can optionally be included in the where clause. Examples are the best way to demonstrate the usage of this class.

http://mybatis.org/generator/generatedobjects/exampleClassUsage.html

```java
package com.huat.crud.test;

import com.huat.crud.bean.Department;
import com.huat.crud.bean.Employee;
import com.huat.crud.bean.EmployeeExample;
import com.huat.crud.dao.DepartmentMapper;
import com.huat.crud.dao.EmployeeMapper;
import org.apache.ibatis.session.ExecutorType;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import java.util.List;
import java.util.UUID;

@ContextConfiguration(locations = {"classpath:applicationContext.xml"})
@RunWith(SpringJUnit4ClassRunner.class)
public class MapperTest {

    @Autowired
    private DepartmentMapper departmentMapper;

    @Autowired
    private EmployeeMapper employeeMapper;

    @Autowired
    private SqlSessionFactory sqlSessionFactory;

    @Test
    public void testCRUD() {
        System.out.println(departmentMapper);
    }

    @Test
    public void testDepartmentInsert() {
        departmentMapper.insertSelective(new Department(null, "product"));
        departmentMapper.insertSelective(new Department(null, "developer"));
        departmentMapper.insertSelective(new Department(null, "testing"));
    }

    @Test
    public void testEmployeeBatchInsert() {
//        for(int i = 0;i<1000;i++){
//			employeeMapper.insertSelective(new Employee(null, "Jerry", "M", "Jerry@atguigu.com", 1));
//		}
        SqlSession sqlSession = sqlSessionFactory.openSession(ExecutorType.BATCH);
        //注意：这里不能使用注入的EmployeeMapper，因为要使用sqlSession的批量提交，应从具有批量提交功能的sqlSession获取mapper
        //Note: Injected EmployeeMapper cannot be used here because to use batch commit of SQLSessions, 
        // the mapper should be obtained from the sqlSession with batch commit capability
        EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
        for (int i = 0; i < 1000; i++) {
            String uid = UUID.randomUUID().toString().substring(0, 5) + i;
            if (i % 2 == 0) {
                mapper.insertSelective(new Employee(null, uid, "M", uid + "@163.com", 2));
            } else if (i % 3 == 0) {
                mapper.insertSelective(new Employee(null, uid, "F", uid + "@163.com", 3));
            } else {
                mapper.insertSelective(new Employee(null, uid, "M", uid + "@163.com", 1));
            }
        }
        sqlSession.clearCache();
        sqlSession.commit();
        sqlSession.close();
        System.out.println("batch finished");
    }

    @Test
    public void testGetEmployeeCounts() {
        long count = employeeMapper.countByExample(null);
        System.out.println(count);
    }

    @Test
    public void testGetEmployeeByExample() {
        EmployeeExample employeeExample = new EmployeeExample();
        employeeExample.or().andGenderEqualTo("F");

        List<Employee> employees = employeeMapper.selectByExample(employeeExample);
        for (Employee employee : employees) {
            System.out.println(employee);
        }
    }
}
```

### 3.7 Paging plug-in PageHelper

#### 3.7.1 Information needed for pagination

Current page data query – `select * from table limit 0,10`

Total record count query – `select count(*) from table`

Count the total number of pages、Previous page number、The next page number

So, pagination is a very messy business.

![image-20221126125947955](assets/image-20221126125947955.png)

#### 3.7.2 The process used by PageHelper

https://pagehelper.github.io/docs/howtouse/

- maven import PageHelper

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.3.2</version>
</dependency>
```

- mybatis-config.xml增加Plugin配置 
  add plugin configuration in mybaitis-config.xml

```xml
<configuration>
    <settings>
        <!--开启驼峰命名转换-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
    <typeAliases>
        <package name="com.huat.crud.bean"/>
    </typeAliases>
    <!--enable PageHelper pagination plugin, put this code after settings and before environments tags-->
    <plugins>
        <plugin interceptor="com.github.pagehelper.PageInterceptor">
            <!--The paging plugin will automatically detect the current database link and automatically select the appropriate paging method. You can specify which dialect the paging plugin uses by configuring the helperDialect property-->
            <property name="helperDialect" value="mysql"/>
            <!-- reasonable property is paging rationalization parameter, which defaults to false. When this parameter is set to true, pageNum<=0 will query the first page, and pageNum>pages (when the total is exceeded) will query the last page. When this parameter is false, the query is based directly on the parameters.-->
            <property name="reasonable" value="true"/>
        </plugin>
    </plugins>
</configuration>
```

- 代码中使用Pagehelper.startPage()自动分页
  The code uses Pagehelper.startPage() for automatic pagination

`PageHelper.startPage(2, 10);`

- 测试方法

```java
    @Test
    public void testGetEmployeeByPagination() {
        PageHelper.startPage(1, 5);
        List<Employee> emps = employeeMapper.selectByExample(null);
        for (Employee emp : emps) {
            System.out.println(emp);
        }
    }

    @Test
    public void testGetEmployeeByPageInfo() {
        PageHelper.startPage(10, 5);
        List<Employee> emps = employeeMapper.selectByExample(null);
        //使用PageInfo对象包装查询到的数据,将pageInfo对象转发给页面
        //navigatePages参数表示连续显示的页数
        PageInfo pageInfo = new PageInfo(emps, 5);
        List<Employee> list = pageInfo.getList();
        for (Employee employee : list) {
            System.out.println(employee);
        }

        System.out.println("Current pageInfo: " + pageInfo.getPageNum());
        System.out.println("Total pages: " + pageInfo.getPages());
        System.out.println("Total records: " + pageInfo.getTotal());
        System.out.println("navigation pageInfo numbers: ");
        int[] nums = pageInfo.getNavigatepageNums();
        for (int i : nums) {
            System.out.print(" " + i);
        }
        System.out.println();
    }
```

![image-20221126115357048](assets/image-20221126115357048.png)

### 3.8 modify dao layer's code

![image-20221110162808214](assets/image-20221110162808214.png)

<img src="assets/image-20221110162950848.png" alt="image-20221110162950848" style="zoom:80%;" />

EmployeeMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.huat.crud.dao.EmployeeMapper">
  <resultMap id="BaseResultMap" type="com.huat.crud.bean.Employee">
    <id column="emp_id" jdbcType="INTEGER" property="empId" />
    <result column="emp_name" jdbcType="VARCHAR" property="empName" />
    <result column="gender" jdbcType="CHAR" property="gender" />
    <result column="email" jdbcType="VARCHAR" property="email" />
    <result column="d_id" jdbcType="INTEGER" property="dId" />
  </resultMap>
  <resultMap id="BaseResultMapWithDept" type="com.huat.crud.bean.Employee">
    <id column="emp_id" jdbcType="INTEGER" property="empId" />
    <result column="emp_name" jdbcType="VARCHAR" property="empName" />
    <result column="gender" jdbcType="CHAR" property="gender" />
    <result column="email" jdbcType="VARCHAR" property="email" />
    <result column="d_id" jdbcType="INTEGER" property="dId" />
    <association property="department" javaType="com.huat.crud.bean.Department">
      <id column="dept_id" property="deptId"/>
      <result column="dept_name" property="deptName"/>
    </association>
  </resultMap>
  ......
  --------------------------------------------------------------------------------------
  <sql id="Base_Column_List">
    emp_id, emp_name, gender, email, d_id
  </sql>
  <sql id="Base_Column_List_With_Dept">
    emp_id, emp_name, gender, email, d_id, dept_id, dept_name
  </sql>
  -------------------------------------------------------------------------------------  
  <select id="selectByExample" parameterType="com.huat.crud.bean.EmployeeExample" resultMap="BaseResultMap">
    select
    <if test="distinct">
      distinct
    </if>
    <include refid="Base_Column_List" />
    from tbl_emp
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
    <if test="orderByClause != null">
      order by ${orderByClause}
    </if>
  </select>
  <select id="selectByExampleWithDept" parameterType="com.huat.crud.bean.EmployeeExample" resultMap="BaseResultMapWithDept">
    select
    <if test="distinct">
      distinct
    </if>
    <include refid="Base_Column_List_With_Dept" />
    from tbl_emp left join tbl_dept ON tbl_emp.d_id = tbl_dept.dept_id
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
    <if test="orderByClause != null">
      order by ${orderByClause}
    </if>
  </select>
  --------------------------------------------------------------------------------------
  <select id="selectByPrimaryKey" parameterType="java.lang.Integer" resultMap="BaseResultMap">
    select 
    <include refid="Base_Column_List" />
    from tbl_emp
    where emp_id = #{empId,jdbcType=INTEGER}
  </select>
  <select id="selectByPrimaryKeyWithDept" parameterType="java.lang.Integer" resultMap="BaseResultMapWithDept">
    select
    <include refid="Base_Column_List_With_Dept" />
    from tbl_emp left join tbl_dept ON tbl_emp.d_id = tbl_dept.dept_id
    where emp_id = #{empId,jdbcType=INTEGER}
  </select>
  ......
</mapper>
```

修改Employee.java中的toString()方法，仍然编写测试类中的测试方法

```java
	@Test
    public void testGetEmployeeByPaginationWithDept() {
        PageHelper.startPage(1, 5);
        List<Employee> emps = employeeMapper.selectByExampleWithDept(null);
        for (Employee emp : emps) {
            System.out.println(emp);
        }
    }

    @Test
    public void testGetEmployeeByPageInfoWithDept() {
        PageHelper.startPage(10, 5);
        List<Employee> emps = employeeMapper.selectByExampleWithDept(null);
        //使用PageInfo对象包装查询到的数据,将pageInfo对象转发给页面
        //navigatePages参数表示连续显示的页数
        PageInfo pageInfo = new PageInfo(emps, 5);
        List<Employee> list = pageInfo.getList();
        for (Employee employee : list) {
            System.out.println(employee);
        }

        System.out.println("Current pageInfo: " + pageInfo.getPageNum());
        System.out.println("Total pages: " + pageInfo.getPages());
        System.out.println("Total records: " + pageInfo.getTotal());
        System.out.println("navigation pageInfo numbers: ");
        int[] nums = pageInfo.getNavigatepageNums();
        for (int i : nums) {
            System.out.print(" " + i);
        }
        System.out.println();
    }
```

### 3.9 Employee CRUD Implemention

#### 3.9.1 Home page Design

create index.jsp in webapp folder

Import the js and css files from the Pinture framework

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html lang="zh-cn">
<head>
    <title>SSM-CRUD</title>
    <%
        pageContext.setAttribute("APP_PATH", request.getContextPath());
    %>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="${APP_PATH}/css/pintuer.css">
    <script src="${APP_PATH}/js/jquery.js"></script>
    <script src="${APP_PATH}/js/pintuer.js"></script>

</head>
<body>
<!--home page header start-->
<div class="container padding-big-top padding-big-bottom border-bottom">
    <div class="line">
        <div class="xl12 xs5 xm6 xb7">
            <button class="button icon-navicon float-right" data-target="#header-demo">
            </button>
            <a href="index.jsp">
                <h2><span class="icon-paper-plane"></span> SSM-CRUD</h2>
            </a>
        </div>
        <div class="xl12 xs7 xm6 xb5 padding-small-top">
            <ul class="nav nav-menu nav-inline nav-navicon nav-split" id="header-demo">
                <li class="active"><a href="${APP_PATH}/">Index</a> </li>
                <li><a href="${APP_PATH}/emp">Employee</a></li>
                <li><a href="#">Department</a> </li>
            </ul>
        </div>
    </div>
</div>
<!--home page header start-->
<!--home page content start-->
<div class="container padding-big-top padding-big-bottom">
    <div class="line-big">
        <div class="xl12 xs3 xm3 xb3">
            <div class="panel">
                <div class="panel-head">友情链接</div>
                <ul class="list-group">
                    <li>电信学院</li>
                    <li>机械学院</li>
                    <li>经管学院</li>
                    <li>人文社科学院</li>
                    <li>汽车学院</li>
                </ul>
            </div>
        </div>
        <div class="xl12 xs9 xm9 xb9">
            <div class="banner">
                <div class="carousel">
                    <div class="item">
                        <img src="${APP_PATH}/images/1.jpg" style="width: 100%" alt="1"/>
                    </div>
                    <div class="item">
                        <img src="${APP_PATH}/images/2.jpg" style="width: 100%" alt="2"/>
                    </div>
                    <div class="item">
                        <img src="${APP_PATH}/images/3.jpg" style="width: 100%" alt="3"/>
                    </div>
                </div>
            </div>
        </div>
    </div>

</div>
<!--home page content end-->
<!--home page footer start-->
<div class="container margin-big-top">
    <div class="border-top padding-top">
        <div class="text-center">
            <ul class="nav nav-inline">
                <li class="active"><a href="#">网站首页</a> </li>
                <li><a href="#">新闻资讯</a> </li>
                <li><a href="#">产品中心</a> </li>
                <li><a href="#">技术反馈</a> </li>
                <li><a href="#">留言反馈</a> </li>
                <li><a href="#">联系方式</a> </li>
            </ul>
        </div>
        <div class="text-center height-big">
            Copy Right © Pintuer.com All Rights Reserved</div>
    </div>
</div>
<!--home page footer end-->
</body>
</html>
```

![image-20221126154534332](assets/image-20221126154534332.png)

#### 3.9.2 Empoyee List

- Writing EmployeeService

```java
@Service
public class EmployeeService {

    @Autowired
    private EmployeeMapper employeeMapper;

    public List<Employee> getAll() {
        return employeeMapper.selectByExampleWithDept(null);
    }
}

```

- Writing DepartmentService

```java
@Service
public class DepartmentService {
    @Autowired
    private DepartmentMapper departmentMapper;

    public List<Department> getAll() {
        return departmentMapper.selectByExample(null);
    }
}
```

- Writing EmployeeController

  It is used to receive requests, query the employee data, and forward the data to the `Employee.jsp` page for display

```java
package com.huat.crud.controller;

import com.github.pagehelper.PageHelper;
import com.github.pagehelper.PageInfo;
import com.huat.crud.bean.Department;
import com.huat.crud.bean.Employee;
import com.huat.crud.dao.DepartmentMapper;
import com.huat.crud.service.DepartmentService;
import com.huat.crud.service.EmployeeService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;

import java.util.List;

@Controller
@RequestMapping("/emp")
public class EmployeeController {

    @Autowired
    private EmployeeService employeeService;
    @Autowired
    private DepartmentService departmentService;

    @RequestMapping(value = "", method = RequestMethod.GET)
    public String getAllEmps(@RequestParam(value = "pn", defaultValue = "1") Integer pn, Model model) {
        PageHelper.startPage(pn, 7);
        List<Employee> employeeList = employeeService.getAll();
        List<Department> departments = departmentService.getAll();
        //使用PageInfo对象包装查询到的数据,将pageInfo对象转发给页面
        //navigatePages参数表示连续显示的页数
        PageInfo pageInfo = new PageInfo(employeeList, 5);
        model.addAttribute("pageInfo", pageInfo);
        model.addAttribute("departments", departments);
        return "employee";
    }
}
```

- SpringMVC 单元测试

```java
package com.huat.crud.test;

import com.github.pagehelper.PageInfo;
import com.huat.crud.bean.Employee;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.mock.web.MockHttpServletRequest;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.test.context.web.WebAppConfiguration;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.MvcResult;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

import java.util.List;

@ContextConfiguration(locations = {"classpath:applicationContext.xml","classpath:spring-mvc.xml"})
@WebAppConfiguration
@RunWith(SpringJUnit4ClassRunner.class)
public class MvcTest {

    @Autowired
    WebApplicationContext context;

    //模拟mvc请求，获取处理结果结果
    MockMvc mockMvc;

    @Before
    public void initMockMvc() {
        mockMvc = MockMvcBuilders.webAppContextSetup(context).build();
    }

    @Test
    public void testPage() throws Exception {
        //模拟请求拿到返回值
        MvcResult result = mockMvc.perform(MockMvcRequestBuilders.get("/emp").param("pn", "1")).andReturn();
        //请求成功后，请求域中会有pageInfo
        MockHttpServletRequest request = result.getRequest();
        PageInfo pageInfo = (PageInfo) request.getAttribute("pageInfo");
        System.out.println("Current page: " + pageInfo.getPageNum());
        System.out.println("Total pages: " + pageInfo.getPages());
        System.out.println("Total records: " + pageInfo.getTotal());
        System.out.println("navigation page numbers: ");
        int[] nums = pageInfo.getNavigatepageNums();
        for (int i : nums) {
            System.out.print(" " + i);
        }
        System.out.println();

        //获取员工数据
        List<Employee> list = pageInfo.getList();
        for (Employee employee : list) {
            System.out.println(employee);
        }
    }
}
```

- 搭建pintuer分页页面

在WEB-INF/views目录下创建employee.jsp页面，代码如下所示：

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html lang="zh-cn">
<head>
  <title>SSM-CRUD</title>
  <%
    pageContext.setAttribute("APP_PATH", request.getContextPath());
  %>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" href="${APP_PATH}/css/pintuer.css">
  <script src="${APP_PATH}/js/jquery.js"></script>
  <script src="${APP_PATH}/js/pintuer.js"></script>
</head>
<body>
<!--home page header start-->
<div class="container padding-big-top padding-big-bottom border-bottom">
  <div class="line">
    <div class="xl12 xs5 xm6 xb7">
      <button class="button icon-navicon float-right" data-target="#header-demo">
      </button>
      <a href="${APP_PATH}/">
        <h2><span class="icon-paper-plane"></span> SSM-CRUD</h2>
      </a>
    </div>
    <div class="xl12 xs7 xm6 xb5 padding-small-top">
      <ul class="nav nav-menu nav-inline nav-navicon nav-split" id="header-demo">
        <li><a href="${APP_PATH}/">Index</a> </li>
        <li class="active"><a href="${APP_PATH}/emp">Employee</a></li>
        <li><a href="#">Department</a> </li>
      </ul>
    </div>
  </div>
</div>
<!--home page header start-->
<!--home page content start-->
<div class="container padding-big-top padding-big-bottom">
  <div class="line-big">
    <div class="line margin-big-bottom">
      <form method="post" action="${APP_PATH}/emp/search">
        <div class="form-group">
          <div class="field">
            <div class="input-group">
                            <span class="addbtn">
                                <button type="button" class="button icon-search"></button>
                            </span>
              <input type="text" class="input" name="empName" size="50" placeholder="Last Name"/>
              <span class="addbtn">
                                <button type="submit" class="button">Search</button>
                            </span>
            </div>
          </div>
        </div>
      </form>
    </div>
    <div class="line">
      <table class="table table-hover table-bordered">
        <thead>
        <tr>
          <th>
            <input type="checkbox" id="check_all">
          </th>
          <th>ID</th>
          <th>Last Name</th>
          <th>gender</th>
          <th>email</th>
          <th>deptName</th>
          <th>operation</th>
        </tr>
        </thead>
        <tbody>
        <c:forEach items="${pageInfo.list}" var="emp">
          <tr class="height-big">
            <td>
              <input type="checkbox" class="check_item">
            </td>
            <td class="text-center">${emp.empId}</td>
            <td>${emp.empName}</td>
            <td>${emp.gender=='M'?'Male':'Female'}</td>
            <td>${emp.email}</td>
            <td>${emp.department.deptName}</td>
            <td class="text-center">
              <a class="button bg-sub button-small" href="${APP_PATH}/emp/${emp.empId}"><span class="icon-edit"></span> Edit</a>
              <a class="button bg-dot button-small" href="${APP_PATH}/emp/${emp.empId}" id="deleteEmp"><span class="icon-trash-o"></span> Delete</a>
            </td>
          </tr>
        </c:forEach>
        <tr class="height-large text-center">
          <td colspan="6">
            <!--pagination start-->
            Page Info: <span class="badge">${pageInfo.pageNum} / ${pageInfo.pages}</span>
            Total Records: <span class="badge">${pageInfo.total}</span>&nbsp;&nbsp;
            <ul class="pagination pagination-group">
              <c:if test="${!pageInfo.isFirstPage}">
                <li><a href="${APP_PATH}/emp?pn=1">First</a></li>
              </c:if>
              <c:if test="${pageInfo.hasPreviousPage}">
                <li>
                  <a href="${APP_PATH}/emp?pn=${pageInfo.pageNum-1}">
                    <span aria-hidden="true">&laquo;</span>
                  </a>
                </li>
              </c:if>
              <c:forEach items="${pageInfo.navigatepageNums}" var="i">
                <c:if test="${pageInfo.pageNum == i}">
                  <li class="active"><a href="#">${i}</a></li>
                </c:if>
                <c:if test="${pageInfo.pageNum != i}">
                  <li><a href="${APP_PATH}/emp?pn=${i}">${i}</a></li>
                </c:if>
              </c:forEach>
              <c:if test="${pageInfo.hasNextPage}">
                <li>
                  <a href="${APP_PATH}/emp?pn=${pageInfo.pageNum+1}">
                    <span aria-hidden="true">&raquo;</span>
                  </a>
                </li>
              </c:if>
              <c:if test="${!pageInfo.isLastPage}">
                <li><a href="${APP_PATH}/emp?pn=${pageInfo.pages}">Last</a></li>
              </c:if>
            </ul>
            <!--pagination end-->
          </td>
          <td>
            <a class="button bg-main button-small" href="${APP_PATH}/emp/add"><span class="icon-plus"></span> Add Employee</a>
            <a class="button bg-dot button-small" id="deleteSelected"><span class="icon-trash-o"></span> Delete Selected</a>
          </td>
        </tr>
        </tbody>
      </table>
      <form id="delete_form" method="post">
        <!-- HiddenHttpMethodFilter要求：必须传输_method请求参数，并且值为最终的请求方式 -->
        <input type="hidden" name="_method" value="delete"/>
      </form>
    </div>
  </div>
</div>
<!--home page content end-->
<!--home page footer start-->
<div class="container margin-big-top">
  <div class="border-top padding-top">
    <div class="text-center">
      <ul class="nav nav-inline">
        <li class="active"><a href="#">网站首页</a> </li>
        <li><a href="#">新闻资讯</a> </li>
        <li><a href="#">产品中心</a> </li>
        <li><a href="#">技术反馈</a> </li>
        <li><a href="#">留言反馈</a> </li>
        <li><a href="#">联系方式</a> </li>
      </ul>
    </div>
    <div class="text-center height-big">
      Copy Right © Pintuer.com All Rights Reserved</div>
  </div>
</div>
<!--home page footer end-->
</body>
</html>
```

![image-20221126162637863](assets/image-20221126162637863.png)

#### 3.9.3 Employee Add

Before jump to the employee add page, the department information data should be queried and forwarded to the add page.

- Writing EmployeeService

```java
@Service
public class EmployeeService {

    @Autowired
    private EmployeeMapper employeeMapper;

    public List<Employee> getAll() {
        return employeeMapper.selectByExampleWithDept(null);
    }

    public void addEmployee(Employee employee) {
        employeeMapper.insertSelective(employee);
    }

    public long checkname(String empName) {
        EmployeeExample example = new EmployeeExample();
        example.or().andEmpNameEqualTo(empName);
        return employeeMapper.countByExample(example);
    }
}
```

- 编写EmployeeController控制器方法

```java
@Controller
@RequestMapping("/emp")
public class EmployeeController {

    @Autowired
    private EmployeeService employeeService;
    @Autowired
    private DepartmentService departmentService;

    @RequestMapping(value = "", method = RequestMethod.GET)
    public String getAllEmps(@RequestParam(value = "pn", defaultValue = "1") Integer pn, Model model) {
        PageHelper.startPage(pn, 7);
        List<Employee> employeeList = employeeService.getAll();
        List<Department> departments = departmentService.getAll();
        //使用PageInfo对象包装查询到的数据,将pageInfo对象转发给页面
        //navigatePages参数表示连续显示的页数
        PageInfo pageInfo = new PageInfo(employeeList, 5);
        model.addAttribute("pageInfo", pageInfo);
        model.addAttribute("departments", departments);
        return "employee";
    }

    @RequestMapping(value = "add", method = RequestMethod.GET)
    public String toAddEmp(Model model) {
        List<Department> departments = departmentService.getAll();
        model.addAttribute("departments", departments);
        return "employee_add";
    }

    @RequestMapping(value = "", method = RequestMethod.POST)
    public String addEmp(Employee employee) {
        System.out.println(employee);
        employeeService.addEmployee(employee);
        return "redirect:/emp";
    }

    @RequestMapping(value = "checkname", method = RequestMethod.GET)
    @ResponseBody
    public String checkname(String empName) {
        long n = employeeService.checkname(empName);
        return n > 0 ? "{\"getdata\":\"false\"}" : "{\"getdata\":\"true\"}";
    }
}
```

- 创建employee_add.jsp页面

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html lang="zh-cn">
<head>
    <title>Employee Add</title>
    <%
        pageContext.setAttribute("APP_PATH", request.getContextPath());
    %>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="${APP_PATH}/css/pintuer.css">
    <script src="${APP_PATH}/js/jquery.js"></script>
    <script src="${APP_PATH}/js/pintuer.js"></script>
</head>
<body>
<!--home page header start-->
<div class="container padding-big-top padding-big-bottom border-bottom">
    <div class="line">
        <div class="xl12 xs5 xm6 xb7">
            <button class="button icon-navicon float-right" data-target="#header-demo">
            </button>
            <a href="index.jsp">
                <h2><span class="icon-paper-plane"></span> SSM-CRUD</h2>
            </a>
        </div>
        <div class="xl12 xs7 xm6 xb5 padding-small-top">
            <ul class="nav nav-menu nav-inline nav-navicon nav-split" id="header-demo">
                <li><a href="${APP_PATH}/">Index</a> </li>
                <li class="active"><a href="${APP_PATH}/emp">Employee</a></li>
                <li><a href="#">Department</a> </li>
            </ul>
        </div>
    </div>
</div>
<!--home page header start-->
<!--home page content start-->
<div class="container padding-big-top padding-big-bottom">
    <div class="line-big">
        <div class="xl12 xs3 xm3 xb3">
            <div class="panel">
                <div class="panel-head">友情链接</div>
                <ul class="list-group">
                    <li>电信学院</li>
                    <li>机械学院</li>
                    <li>经管学院</li>
                    <li>人文社科学院</li>
                    <li>汽车学院</li>
                </ul>
            </div>
        </div>
        <div class="xl12 xs9 xm9 xb9">
            <form method="post" action="${APP_PATH}/emp">
                <div class="form-group">
                    <div class="label">
                        <label for="empName">Last Name</label>
                    </div>
                    <div class="field">
                        <input type="text" class="input" id="empName" name="empName" size="50"
                               placeholder="empName" data-validate="required: must input,ajax#${APP_PATH}/emp/checkname?empName=:name already exists"/>
                        <%--,ajax#${APP_PATH}/emp/checkname?empName=:name already exists--%>
                    </div>
                </div>
                <div class="form-group">
                    <div class="label">
                        <label for="gender">Gender</label>
                    </div>
                    <div class="field">
                        <div class="button-group radio">
                            <label class="button active">
                                <input name="gender" value="M" checked="checked" type="radio"><span class="icon icon-male"></span> male
                            </label>
                            <label class="button">
                                <input name="gender" value="F" type="radio"><span class="icon icon-female"></span> female
                            </label>
                        </div>
                    </div>
                </div>
                <div class="form-group">
                    <div class="label">
                        <label for="email">Email</label>
                    </div>
                    <div class="field">
                        <input type="text" class="input" id="email" name="email" size="50" placeholder="email"/>
                    </div>
                </div>
                <div class="form-group">
                    <div class="label">
                        <label for="dId">DeptName</label>
                    </div>
                    <div class="field">
                        <select class="input" name="dId" id="dId">
                            <c:forEach items="${departments}" var="dept">
                                <option value="${dept.deptId}">${dept.deptName}</option>
                            </c:forEach>
                        </select>
                    </div>
                </div>
                <div class="form-button">
                    <button class="button" type="submit">提交</button>
                </div>
            </form>
        </div>
    </div>
</div>
<!--home page content end-->
<!--home page footer start-->
<div class="container margin-big-top">
    <div class="border-top padding-top">
        <div class="text-center">
            <ul class="nav nav-inline">
                <li class="active"><a href="#">网站首页</a> </li>
                <li><a href="#">新闻资讯</a> </li>
                <li><a href="#">产品中心</a> </li>
                <li><a href="#">技术反馈</a> </li>
                <li><a href="#">留言反馈</a> </li>
                <li><a href="#">联系方式</a> </li>
            </ul>
        </div>
        <div class="text-center height-big">
            Copy Right © Pintuer.com All Rights Reserved</div>
    </div>
</div>
<!--home page footer end-->
</body>
</html>
```

#### 3.9.4 Employee Update

- 编写service层代码

```
@Service
public class EmployeeService {

    @Autowired
    private EmployeeMapper employeeMapper;
    ......
    public Employee getEmp(Integer id) {
        Employee employee = employeeMapper.selectByPrimaryKeyWithDept(id);
        return employee;
    }
}
```

- 编写controller代码

```java
@Controller
@RequestMapping("/emp")
public class EmployeeController {
    ......
    @RequestMapping(value = "/{id}", method = RequestMethod.GET)
    public String getEmp(@PathVariable("id") Integer id, Model model) {
        Employee employee = employeeService.getEmp(id);
        List<Department> departments = departmentService.getAll();
        model.addAttribute("employee", employee);
        model.addAttribute("departments", departments);
        return "employee_update";
    }
}
```

- 编写employee_update.jsp页面

满足发送put请求的两个条件：

a> 当前请求的请求方式必须为post

`<form method="put" action="${APP_PATH}/emp" class="form-x">`

b> 当前请求必须传输请求参数_method
`<input type="hidden" name="_method" value="put">`

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html lang="zh-cn">
<head>
  <title>Employee Update</title>
  <%
    pageContext.setAttribute("APP_PATH", request.getContextPath());
  %>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" href="${APP_PATH}/css/pintuer.css">
  <script src="${APP_PATH}/js/jquery.js"></script>
  <script src="${APP_PATH}/js/pintuer.js"></script>
</head>
<body>
<!--home page header start-->
<div class="container padding-big-top padding-big-bottom border-bottom">
  <div class="line">
    <div class="xl12 xs5 xm6 xb7">
      <button class="button icon-navicon float-right" data-target="#header-demo">
      </button>
      <a href="${APP_PATH}/">
        <h2><span class="icon-paper-plane"></span> SSM-CRUD</h2>
      </a>
    </div>
    <div class="xl12 xs7 xm6 xb5 padding-small-top">
      <ul class="nav nav-menu nav-inline nav-navicon nav-split" id="header-demo">
        <li><a href="${APP_PATH}/">Index</a> </li>
        <li class="active"><a href="${APP_PATH}/emp">Employee</a></li>
        <li><a href="#">Department</a> </li>
      </ul>
    </div>
  </div>
</div>
<!--home page header start-->
<!--home page content start-->
<div class="container padding-big-top padding-big-bottom">
  <div class="line-big">
    <div class="xl12 xs3 xm3 xb3">
      <div class="panel">
        <div class="panel-head">友情链接</div>
        <ul class="list-group">
          <li>电信学院</li>
          <li>机械学院</li>
          <li>经管学院</li>
          <li>人文社科学院</li>
          <li>汽车学院</li>
        </ul>
      </div>
    </div>
    <div class="xl12 xs9 xm9 xb9">
      <h2>Update Employee Data</h2>
      <hr class="bg-green margin-large-bottom" />
      <form method="post" action="${APP_PATH}/emp" class="form-x">
        <input type="hidden" name="_method" value="put">
        <input type="hidden" name="empId" value="${employee.empId}"/>
        <div class="form-group">
          <div class="label">
            <label for="empName">Last Name</label>
          </div>
          <div class="field">
            <input type="text" class="input" id="empName" name="empName" size="50" value="${employee.empName}"
                   placeholder="empName" data-validate="required: must input"/>
          </div>
        </div>
        <div class="form-group">
          <div class="label">
            <label>Gender</label>
          </div>
          <div class="field">
            <div class="button-group radio">
              <label class="button <c:if test="${employee.gender=='M'}">active</c:if>">
                <%--<input name="gender" value="M" <c:if test="${employee.gender=='M'}">checked</c:if> type="radio"><span class="icon icon-male"></span> male--%>
                <input name="gender" value="M" ${employee.gender=='M'?'checked':""} type="radio"><span class="icon icon-male"></span> male
              </label>
              <label class="button <c:if test="${employee.gender=='F'}">active</c:if>">
                <%--<input name="gender" value="F" <c:if test="${employee.gender=='F'}">checked</c:if> type="radio"><span class="icon icon-female"></span> female--%>
                <input name="gender" value="F" ${employee.gender=='F'?'checked':""} type="radio"><span class="icon icon-female"></span> female
              </label>
            </div>
          </div>
        </div>
        <div class="form-group">
          <div class="label">
            <label for="email">Email</label>
          </div>
          <div class="field">
            <input type="text" class="input" id="email" name="email" size="50" placeholder="email" value="${employee.email}"/>
          </div>
        </div>
        <div class="form-group">
          <div class="label">
            <label for="dId">DeptName</label>
          </div>
          <div class="field">
            <select class="input" name="dId" id="dId">
              <c:forEach items="${departments}" var="dept">
                <option value="${dept.deptId}" ${dept.deptId == employee.dId?"selected":""}>${dept.deptName}</option>
              </c:forEach>
            </select>
          </div>
        </div>
        <div class="form-button">
          <button class="button" type="submit">提交</button>
        </div>
      </form>
    </div>
  </div>
</div>
<!--home page content end-->
<!--home page footer start-->
<div class="container margin-big-top">
  <div class="border-top padding-top">
    <div class="text-center">
      <ul class="nav nav-inline">
        <li class="active"><a href="#">网站首页</a> </li>
        <li><a href="#">新闻资讯</a> </li>
        <li><a href="#">产品中心</a> </li>
        <li><a href="#">技术反馈</a> </li>
        <li><a href="#">留言反馈</a> </li>
        <li><a href="#">联系方式</a> </li>
      </ul>
    </div>
    <div class="text-center height-big">
      Copy Right © Pintuer.com All Rights Reserved</div>
  </div>
</div>
<!--home page footer end-->
</body>
</html>
```

- 编写service层代码

```java
@Service
public class EmployeeService {

    @Autowired
    private EmployeeMapper employeeMapper;
    ......
    public void updateEmp(Employee employee) {
        employeeMapper.updateByPrimaryKeySelective(employee);
    }
}
```

- 编写controller代码

```java
@Controller
@RequestMapping("/emp")
public class EmployeeController {
    ......
    @RequestMapping(value = "", method = RequestMethod.PUT)
    public String saveEmp(Employee employee) {
        employeeService.updateEmp(employee);
        return "redirect:/emp";
    }
}
```

#### 3.9.5 Employee Delete

- Writing EmployeeService

```java
@Service
public class EmployeeService {

    @Autowired
    private EmployeeMapper employeeMapper;
    ......
    public void deleteEmp(Integer id) {
        employeeMapper.deleteByPrimaryKey(id);
    }
}
```

- Writing EmployeeController

```java
@Controller
@RequestMapping("/emp")
public class EmployeeController {
    ......
    @RequestMapping(value = "/{id}", method = RequestMethod.DELETE)
    public String deleteEmp(@PathVariable("id") Integer id) {
        employeeService.deleteEmp(id);
        return "redirect:/emp";
    }
}
```

- Modify employee.jsp

```jsp
<form id="delete_form" method="post">
    <!-- HiddenHttpMethodFilter要求：必须传输_method请求参数，并且值为最终的请求方式 -->
    <input type="hidden" name="_method" value="delete"/>
</form>
------------------------------------------------------------------------------------
<script type="text/javascript">
    $(document).on("click","#deleteEmp",function(){
        var tip = "Are you confirm want to delete the current employee's data?";
        if (confirm(tip)) {
            var href = $(this).attr("href");
            $("#delete_form").attr("action", href).submit();
        }
        //阻止超链接的默认行为
        return false;
    });
</script>
```

#### 3.9.6 Employee Batch Delete

- the js code for select/unselect function 

```javascript
<script type="text/javascript">
    ......
    //全选/全不选
    $("#check_all").click(function () {
        //attr方法获取checked是undefined
        //attr方法用于获取自定义属性的值
        //prop方法修改和读取dom原生属性的值
        $(".check_item").prop("checked",$(this).prop("checked"));
    });
    $(document).on("click",".check_item",function () {
        //判断当前选中的元素个数是否为所有复选框个数
        var flag = $(".check_item:checked").length==$(".check_item").length;
        $("#check_all").prop("checked", flag);
    });
    //批量删除
    $("#deleteSelected").click(function () {
        if($(".check_item:checked").length>0) {
            var empNames = "";
            var del_ids = "";
            $.each($(".check_item:checked"), function () {
                empNames += $(this).parents("tr").find("td:eq(2)").text() + ",";
                del_ids += $(this).parents("tr").find("td:eq(1)").text() + ",";
            });
            //去除末尾多余的逗号
            empNames = empNames.substring(0, empNames.length - 1);
            del_ids = del_ids.substring(0, del_ids.length - 1);
            var tip = "Are you confirm want to delete [" + empNames + "] employee's data?";
            if (confirm(tip)) {
                var href = "${APP_PATH}/emp/" + del_ids;
                $("#delete_form").attr("action", href).submit();
            }
        } else {
            alert("Please select the data you want to delete!")
        }
        //阻止超链接的默认行为
        return false;
    });
</script>
```

- 编写service层代码

```java
@Service
public class EmployeeService {

    @Autowired
    private EmployeeMapper employeeMapper;
    ......
    public void deleteBatchEmp(List<Integer> ids) {
        EmployeeExample example = new EmployeeExample();
        example.or().andEmpIdIn(ids);
        employeeMapper.deleteByExample(example);
    }
}
```

- 改造EmployeeController删除功能代码

```java
@Controller
@RequestMapping("/emp")
public class EmployeeController {

    ......

    @RequestMapping(value = "/{ids}", method = RequestMethod.DELETE)
    public String deleteEmp(@PathVariable("ids") String ids) {
        if (ids.contains(",")) {
            //批量删除
            String[] str_ids = ids.split(",");
            List<Integer> int_ids = new ArrayList<>();
            for (String id : str_ids) {
                int_ids.add(Integer.parseInt(id));
            }
            employeeService.deleteBatchEmp(int_ids);
        } else {
            //单个删除
            employeeService.deleteEmp(Integer.parseInt(ids));

        }
        return "redirect:/emp";
    }
}
```

#### 3.9.7 搜索功能

- 搜索表单设计

```jsp
	<div class="line margin-big-bottom">
      <form method="post" action="${APP_PATH}/emp/search">
        <div class="form-group">
          <div class="field">
            <div class="input-group">
              <span class="addbtn">
                <button type="button" class="button icon-search"></button>
              </span>
              <input type="text" class="input" name="empName" size="50" placeholder="Last Name"/>
              <span class="addbtn">
                <button type="submit" class="button">Search</button>
              </span>
            </div>
          </div>
        </div>
      </form>
    </div>
```

- 编写service层代码

```java
import org.springframework.util.StringUtils;

@Service
public class EmployeeService {

    @Autowired
    private EmployeeMapper employeeMapper;
    
    ......

    public List<Employee> searchEmp(String empName) {
        EmployeeExample example = new EmployeeExample();
        if (!StringUtils.isEmpty(empName)) {
            example.or().andEmpNameLike("%" + empName + "%");
        }
        return employeeMapper.selectByExampleWithDept(example);
    }
}
```

- 编写controller层代码

```java
@Controller
@RequestMapping("/emp")
public class EmployeeController {

    @Autowired
    private EmployeeService employeeService;

    @Autowired
    private DepartmentService departmentService;
    
    ......

    @RequestMapping(value = "search", method = RequestMethod.POST)
    public String searchEmp(String empName, Model model) {
        PageHelper.startPage(1, 7);
        List<Employee> emps = employeeService.searchEmp(empName);
        PageInfo pageInfo = new PageInfo(emps);
        model.addAttribute("pageInfo", pageInfo);
        return "employee";
    }
}
```

