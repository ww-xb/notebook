# IDEA创建mybatis入门项目（XML版）

## 1.新建maven工程

所需依赖如下：

```xml
<dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.6</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.6</version>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
```


## 2.创建数据库表

### 创建一个如下结构的数据库表

![image-20200306224213000](C:\Users\horan\AppData\Roaming\Typora\typora-user-images\image-20200306224213000.png)

```sql
create table user
(
	id int auto_increment,
	username varchar(32) not null comment '用户名',
	birthday datetime default null null comment '生日',
	sex char(1) default null null comment '性别',
	address varchar(255) default null null comment '地址',
	constraint user_pk
		primary key (id)
)engine = InnoDB Default char set = UTF8;
```


## 3.创建如下结构目录：

![image-20200306220032955](C:\Users\horan\AppData\Roaming\Typora\typora-user-images\image-20200306220032955.png)

### com.company.entity包下为实体类，定义了如下属性以及相应的setter和getter方法并重写了toString()方法(方法生成和重写均可通过idea自动生成)，其属性应与数据库对应

```java
public class User implements Serializable {
    private Integer id;
    private String username;
    private Date birthday;
    private String sex;
    private String address;
}
```

### com.company.dao包下为与上述实体类对应的接口，其中定义了一个方法：

```java
public interface IUserDao {
    List<User> findAll();
}
```

### SQLMapConfig.xml为mybatis全局配置文件（名称随意，无特殊含义）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--mybatis的主配置文件-->
<configuration>
    <!--配置环境-->
    <environments default="mysql">
        <!--配置mysql环境-->
        <environment id="mysql">
            <!--配置事务类型-->
            <transactionManager type="jdbc"></transactionManager>
            <!--配置数据源(连接池)-->
            <dataSource type="POOLED">
                <!--配置连接数据的四个基本信息-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://xuebing.online:3306/mybatis?useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="[用户名]"/>
                <property name="password" value="[密码]"/>
            </dataSource>
        </environment>
    </environments>
    <!--指定映射配置文件的位置映射配置文件指的是每个DAO独立的配置文件-->
    <mappers>
        <mapper resource="com/company/dao/IUserDao.xml"/>
    </mappers>
</configuration>
```

### IUserDao.xml为对应实体类中接口的映射文件，里面保存了IUserDao接口中定义的方法的具体实现(即sql语句)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace中填写此映射文件对应的接口的完整类名-->
<mapper namespace="com.company.dao.IUserDao">
    <!--id为findAll，即IUserDao接口中需要实现的方法名-->
    <!--resultType指定当前sql语句执行完的结果集需要被封装成那种类型-->
    <select id="findAll" resultType="com.company.entity.User">
        select * from user
    </select>
</mapper>
```



## 4.创建测试实例

### 可以通过创建如下测试类进行测试

![image-20200306230722969](C:\Users\horan\AppData\Roaming\Typora\typora-user-images\image-20200306230722969.png)

### log4j.properties配置如下：

```properties
log4j.rootCatalog=debug,CONSOLE,LOGFILE
log4j.logger.org.apache.axis.enterprise=FATAL,CONSOLE
# console log
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} [%t] %-5p %c - %m%n

# 日志文件
log4j.appender.LOGFILE=org.apache.log4j.FileAppender
log4j.appender.LOGFILE.File=D:/axis.log
log4j.appender.LOGFILE.append=true
log4j.appender.LOGFILE.layout=org.apache.log4j.PatternLayout
log4j.appender.LOGFILE.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} [%t] %-5p %c - %m%n
```



#### Test代码如下：

```java
public class MybatisTest {
    public static void main(String[] args) throws Exception {
        //1.创建输入流读取全库配置文件
        InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
        //2.创建SqlSessionFactory工厂
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(in);
        //3.使用工厂生成SqlSession对象
        SqlSession session = factory.openSession();
        //4.使用SqlSession创建Dao接口的代理对象
        IUserDao userDao = session.getMapper(IUserDao.class);
        //5.使用代理对象执行方法
        List<User> users = userDao.findAll();
        for (User user : users)
            System.out.printf(user.toString());
        //6.释放资源
        session.close();
        in.close();

    }
}
```




