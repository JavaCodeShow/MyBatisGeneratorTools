# idea版本mybatis逆向工程源码

#### MyBatis逆向工程介绍
MyBatis逆向工程，就是通过相应插件，自动生成MyBatis数据库连接的一些文件。

```java
　表（数据库）→实体类、*Dao.java、*Mapper.xml
```

以Spring整合MyBatis环境下,MyBatis逆向工程自动生成pojo，mapper,dao的Java类。 MyBatis的一个主要的特点就是需要程序员自己编写sql，那么如果表太多的话，难免会很麻烦，
所以mybatis官方提供了一个逆向工程，可以针对单表自动生成mybatis执行所需要的代码， 一般在开发中，常用的逆向工程方式是通过数据库的表生成代码。
可以针对单表自动生成mybatis执行所需要的代码（mapper.java、mapper.xml、pojo…），提高工作效率。

去除jdbcType=xxx 链接：https://blog.csdn.net/qielanyu_/article/details/111312141
ctrl+f使用正则表达式替换： "\,jdbcType\=[A-Z]*"






