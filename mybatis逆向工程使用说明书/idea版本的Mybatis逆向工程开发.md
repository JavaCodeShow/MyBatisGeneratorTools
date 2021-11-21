# idea版本的Mybatis逆向工程开发（自动生成实体类层,mapper文件,dao层）

# 一、使用逆向工程开发概述

今天早上打算做一个spring+springmvc+mybatis的项目，然后感觉这个mapper文件太难写了，最后就想在网上找一个方法能解决不写mapper文件的方法，最后就发现了这个懒人必背法宝：“myabtis逆向工程”的技术，但是全网几乎都是“`eclipse 版本生成 MyBatis 逆向工程`”,然后自己就搞了一个idea+maven版本的逆向工程，并且全部在gitee开源了的哟，如果你也想做懒人，就使用mybatis的逆向工程吧！（**开源地址在文末！**）

###  1、什么是Mybatis逆向工程开发

MyBatis逆向工程，就是通过相应插件，自动生成MyBatis数据库连接的一些文件。

```java
　表（数据库）→实体类、*Dao.java、*Mapper.xml
```

` 以Spring整合MyBatis环境下,MyBatis逆向工程自动生成pojo，mapper,dao的Java类。MyBatis的一个主要的特点就是需要程序员自己编写sql，那么如果表太多的话，难免会很麻烦，所以mybatis官方提供了一个逆向工程，可以针对单表自动生成mybatis执行所需要的代码，一般在开发中，常用的逆向工程方式是通过数据库的表生成代码。可以针对单表自动生成mybatis执行所需要的代码（mapper.java、mapper.xml、pojo…），提高工作效率。`

![](https://i.loli.net/2021/11/21/YtRqCxVLuIU2Ogi.jpg)

### 2、Pojo(plian ordinary java object)解释

```java
"Plain Old Java Object"就是简单java对象。
 Pojo的内在含义是指那些没有从任何类继承、也没有实现任何接口，
 更没有被其它框架侵入的java对象。
 即有无参构造函数，每个字段都有getter和setter的java类。
 其实就是传统开发中的**entity** 
```


- `按我的理解pojo就是entity`

### 3、mybatis提供的逆向工程核心插件

#### （1）mybatis提供的逆向工程核心插件依赖

**mybatis-generator-core** ** ** :进入[https://mvnrepository.com/](https://mvnrepository.com/)搜索 MyBatis ,找到 MyBatis Generator Core

       选择版本，其中Maven选择版本最好的原则，就是那个版本用得多久选择那个版本！

![](https://i.loli.net/2021/11/21/cTNhyK7bOPLvMxJ.png)

我选择的是  MyBatis Generator Core1.3.7这个版本

![](https://i.loli.net/2021/11/21/Z4ivlp36jcGo9k8.png)

```xml
<dependency>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-core</artifactId>
    <version>1.3.7</version>
</dependency>
```


#### （2）mybatis及数据库连接所需依赖

```xml
 <groupId>com.tjcu</groupId>
  <artifactId>MyBatisGeneratorTools</artifactId>
  <version>1.0-SNAPSHOT</version>


  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
    <!--日志文件-->
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
    </dependency>
  <!--mybatis-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.5</version>
    </dependency>
    <!--mybatis逆向工程-->
    <dependency>
      <groupId>org.mybatis.generator</groupId>
      <artifactId>mybatis-generator-core</artifactId>
      <version>1.3.7</version>
    </dependency>
   <!--数据库驱动链接-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.27</version>
    </dependency>
  </dependencies>
<!--在pom中添加如下工具：运行逆向功能 -->
<build>
  <plugins>
    <plugin>
      <groupId>org.mybatis.generator</groupId>
      <artifactId>mybatis-generator-maven-plugin</artifactId>
      <version>1.3.7</version>
      <!--需要在逆向工程中引入数据库驱动，不然不能创建-->
      <dependencies>
        <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
          <version>8.0.27</version>
        </dependency>
      </dependencies>
      <configuration>
        <verbose>true</verbose>
        <overwrite>true</overwrite>
      </configuration>
    </plugin>
  </plugins>

</build>
```


4、mysql数据库表的创建

- `这是一个《数码商城后台管理系统》的数据库表`，三张表

```sql
-- 1、管理员表
CREATE TABLE admin(
a_id INT AUTO_INCREMENT PRIMARY KEY,
a_username VARCHAR(20),
a_password VARCHAR(50)
);
-- 查询管理员表
select  * from admin;
-- 添加管理员
INSERT INTO admin(a_id,a_username,a_password) VALUES(1,'王恒杰','123456');

-- 2、商品类型表
CREATE TABLE product_type
(
type_id int auto_increment PRIMARY KEY,
type_name varchar(20)
);
-- 查询商品类型
select * from product_type;
-- 添加数据
insert into product_type(type_name) values('手机');
insert into product_type(type_name) values('电脑');
insert into product_type(type_name) values('电视');

-- 3、商品表
create table product_info
(
p_id int auto_increment primary key,
p_name varchar(20),
-- 商品规格/简介
p_content varchar(200),
-- 价格
p_price int, 
-- 图片
p_image varchar(200), 
-- 数量
p_number int, 
type_id int,
p_date date,
FOREIGN KEY(type_id) REFERENCES product_type(type_id)
);
-- 添加商品
insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('小米Note2','双曲面 黑色 6GB内存 64GB闪存',2899,'xmNote2.jpg',500,1,NOW());
insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('红米Note5A','5.5英寸 粉色 2GB内存 16GB闪存',699,'hmNote5A.jpg',500,1,NOW());
insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('红米Note4X','5.5英寸 绿色 4GB内存 64GB闪存',1299,'hmNote4X.jpg',500,1,NOW());
insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('红米4','5英寸 金色 3GB内存 32GB闪存',999,'hm4.jpg',500,1,NOW());
insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('红米4X','5英寸 黑色 3GB内存 32GB闪存',899,'hm4X.jpg',500,1,NOW());

insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('小米平板3','7.9英寸 金色 4GB内存 64GB闪存',1499,'xmPad3.jpg',500,2,NOW());
insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('小米Air12','12.5英寸 银色 4GB内存 128GB闪存',3599,'xmAir12.jpg',500,2,NOW());
insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('小米Air13','13.3英寸 银色 8GB内存 256GB闪存',4999,'xmAir13.jpg',500,2,NOW());
insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('小米Pro','15.6英寸 灰色 16GB内存 256GB闪存',6999,'xmPro.jpg',500,2,NOW());

insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('小米电视4','49英寸 原装LG屏 3840×2160 真4K',3299,'xmTV4-49.jpg',500,3,NOW());
insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('小米电视4','55英寸 原装三星屏 3840×2160 真4K',3999,'xmTV4-55.jpg',500,3,NOW());
insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('小米电视4','65英寸 原装三星屏 3840×2160 真4K',8999,'xmTV4-65.jpg',500,3,NOW());
insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('小米电视4A','43英寸 FHD全高清屏 1920*1080',1999,'xmTV4A-43.jpg',500,3,NOW());
insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('小米电视4A','49英寸 FHD全高清屏 1920*1080',2299,'xmTV4A-49.jpg',500,3,NOW());
insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('小米MIX2','全陶瓷 黑色 8GB内存 128GB闪存',4699,'xmMIX2.jpg',500,1,NOW());
insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('小米Note3','全网通 蓝色 6GB内存 64GB闪存',2499,'xmNote3.jpg',500,1,NOW());
insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('小米6','玻璃金属 白色 6GB内存 128GB闪存',2899,'xm6.jpg',500,1,NOW());
insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('小米MAX2','全金属 金色 4GB内存 64GB闪存',1599,'xmMAX2.jpg',500,1,NOW());
insert into product_info(p_name,p_content,p_price,p_image,p_number,type_id,p_date) values('小米5X','全金属 金色 4GB内存 64GB闪存',1499,'xm5X.jpg',500,1,NOW());
-- 查询商品
select * from product_info;
-- 删除原始的商品数据
delete from product_info;


```


**数据库名：**  digitalmall

**文档描述：**  该项目总共有三张表分别是管理员，商品信息，商品类别

|**表名** |**说明** |
|---|---|
|**admin** |**管理员表** |
|**product_info** |**商品信息** |
|**product_type** |**商品类别** |



- `admin``表结构`

![](https://i.loli.net/2021/11/21/12vNepu6MJkKTt8.png)

- **product_info** `表结构`

![](https://i.loli.net/2021/11/21/JWuULKOjohHdxXn.png)

- **product_type** `表结构`

![](https://i.loli.net/2021/11/21/KyFEXc3s8g1iPe5.png)

# 二、Mybatis逆向工程开发

### 1、　逆向工程配置文件的开发（generatorConfig.xml）

`用来配置指定数据库和表`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <context id="testTables" targetRuntime="MyBatis3">
        <commentGenerator>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
        <!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/digitalmall?useSSL=false&amp;serverTimezone=UTC&amp;allowPublicKeyRetrieval=true"
                        userId="root"
                        password="root">
        </jdbcConnection>
        <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和
            NUMERIC 类型解析为java.math.BigDecimal -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

        <!-- targetProject:生成PO类的位置 我写的是entity实体类-->
        <javaModelGenerator targetPackage="com.tjcu.entity"
                            targetProject="src/main/java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false"/>
            <!-- 从数据库返回的值被清理前后的空格 -->
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!-- targetProject:mapper映射文件生成的位置 -->
        <sqlMapGenerator  targetPackage="mapper" targetProject="./src/main/resources">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false"/>
        </sqlMapGenerator>
        <!-- targetPackage：mapper接口生成的位置 -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.tjcu.dao"
                             targetProject="src/main/java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false"/>
        </javaClientGenerator>
        <!-- 指定数据库表 -->
        <table schema="" tableName="admin"></table>
        <table schema="" tableName="product_info"></table>
        <table schema="" tableName="product_type"></table>
    </context>
</generatorConfiguration>


```


### 2、日志文件（log4j.properties）

- `没有什么作用，就是为了控制台打印日志，复制进项目就可以了`

```.properties
log4j.rootLogger=DEBUG, stdout

# SqlMap logging configuration...
log4j.logger.com.ibatis=DEBUG
log4j.logger.com.ibatis.common.jdbc.SimpleDataSource=DEBUG
log4j.logger.com.ibatis.sqlmap.engine.cache.CacheModel=DEBUG
log4j.logger.com.ibatis.sqlmap.engine.impl.SqlMapClientImpl=DEBUG
log4j.logger.com.ibatis.sqlmap.engine.builder.xml.SqlMapParser=DEBUG
log4j.logger.com.ibatis.common.util.StopWatch=DEBUG
log4j.logger.java.sql.Connection=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG

# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```


### 3、添加主程序运行项目：

- `使用主程序运行来生成逆向工程：`

```java
package com.tjcu.Tools;
import org.mybatis.generator.api.MyBatisGenerator;
import org.mybatis.generator.config.Configuration;
import org.mybatis.generator.config.xml.ConfigurationParser;
import org.mybatis.generator.internal.DefaultShellCallback;

import java.io.File;
import java.util.ArrayList;
import java.util.List;

/**
 * @author 王恒杰
 * @version 1.0
 * @date 2021/10/26 19:46
 * @email 1078993387@qq.com
 * @Address 天津
 * @Description: Mybatis的逆向工程
 */
public class GeneratorSqlmap {
    public void generator() throws Exception {

        List<String> warnings = new ArrayList<String>();
        boolean overwrite = true;
        //指定 逆向工程配置文件
        File configFile=new File(GeneratorSqlmap.class.getResource("/generatorConfig.xml").getFile());
        ConfigurationParser cp = new ConfigurationParser(warnings);
        Configuration config = cp.parseConfiguration(configFile);
        DefaultShellCallback callback = new DefaultShellCallback(overwrite);
        MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config,
                callback, warnings);
        myBatisGenerator.generate(null);

    }

    public static void main(String[] args) throws Exception {
        try {
            GeneratorSqlmap generatorSqlmap = new GeneratorSqlmap();
            generatorSqlmap.generator();
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
}



```


# 三、如何使用Maven运行逆向工程

### 1、打开运行的配置

![](https://i.loli.net/2021/11/21/YKar4VA15RJjPts.png)

### 2、选择maven

![](https://i.loli.net/2021/11/21/VnY9EzJowGWTLcv.png)

### 3、填入命令内容

命令是：`mybatis-generator:generate -e`

![](https://i.loli.net/2021/11/21/k6ExCgGAjXhwNBd.png)

### 4、点击如下运行：

![](https://i.loli.net/2021/11/21/aCw8Q6dHnUArlD4.png)

### 5、异常报错及原因

我才点击mybatis-generator:gengerate时他报异常

```java
Exception getting JDBC Driver: com.mysql.cj.jdbc.Driver
```


我检查了无数遍，代码都没有写错，最后才发现在插件里面**没有引入驱动** ，修改后：

![](https://i.loli.net/2021/11/21/8R3G4Jvz5xasfXu.png)

然后刷新一下 Maven，再次启动试一试：

### 6、逆向工程，运行结果如下

![](https://i.loli.net/2021/11/21/KNsBOHu9nXzocvj.png)

这样我们就自动成功了dao层，mapper文件和实体类

# 四、idea版本的《mybatis逆向工程》开源说明

### 1、开源位置在gittee

该idea+Maven实现的Mybatis逆向工程我已经开源在gitee上面了，

需要的可以自取：[https://gitee.com/wanghengjie563135/MyBatisGeneratorTools.git](https://gitee.com/wanghengjie563135/MyBatisGeneratorTools.git)

![](https://i.loli.net/2021/11/21/5uaU8OowfnZQ6Ng.png)

### 2、下载使用说明书

#### （1）在gitee点击下载

![](https://i.loli.net/2021/11/21/PGQlV6Ir9bMs1za.png)

#### （2）解压项目文件

![](https://i.loli.net/2021/11/21/29d3v4BkRHmT58x.png)

#### （3）mybatisn逆向工程项目目录结构

![](https://i.loli.net/2021/11/21/Mv4sL2gbz3TEcld.png)

#### （4）在idea直接引入项目

![](https://i.loli.net/2021/11/21/47nUI1altFbORDs.png)

![](https://i.loli.net/2021/11/21/txj38WoPv5L9J6D.png)

### （5）修改数据库名，和相关的表名就可以完成逆向工程了！

![](https://i.loli.net/2021/11/21/YyaHqA3fk5Z8ej6.png)

![](https://i.loli.net/2021/11/21/IVzaKu5f7w93SUp.png)

