# spring中的依赖注入

## 依赖注入(Dependency Injection)：
- IOC的作用：
    - 降低程序间的耦合（依赖关系）
    - 依赖关系的管理：
    - 以后都交给spring来维护
    - 在当前类需要用到其他类的对象，由spring为我们提供，我们只需要在配置文件中说明
    
- 依赖关系的维护：就称之为依赖注入。
	
- 依赖注入能注入的数据有三类：
    - 基本类型和String
    - 其他bean类型（在配置文件中或者注解配置过的bean）
    - 复杂类型/集合类型
    
## 依赖注入的三种方式


### 第一种：使用构造函数提供
- 构造函数注入：
    - 使用的标签:constructor-arg
    - 标签出现的位置：bean标签的内部
    - 标签中的属性

        - type：用于指定要注入的数据的数据类型，该数据类型也是构造函数中某个或某些参数的类型
        - index：用于指定要注入的数据给构造函数中指定索引位置的参数赋值。索引的位置是从0开始
        - name：用于指定给构造函数中指定名称的参数赋值(常用的)

以上三个用于指定给构造函数中哪个参数赋值
        - value：用于提供基本类型和String类型的数据
        - ref：用于指定其他的bean类型数据。它指的就是在spring的Ioc核心容器中出现过的bean对象
        - 优势：在获取bean对象时，注入数据是必须的操作，否则对象无法创建成功。
        - 弊端：改变了bean对象的实例化方式，使我们在创建对象时，如果用不到这些数据，也必须提供。


### 第二种：使用set方法提供

- set方法注入 (更常用的方式)
    - 涉及的标签：property
    - 出现的位置：bean标签的内部
    - 标签的属性
  -  name：用于指定注入时所调用的set方法名称
	  -  value：用于提供基本类型和String类型的数据
	  - ref：用于指定其他的bean类型数据。它指的就是在spring的Ioc核心容器中出现过的bean对象
	    -  优势：创建对象时没有明确的限制，可以直接使用默认构造函数
	    -  弊端：如果有某个成员必须有值，则获取对象时有可能set方法没有执行。
- 复杂类型的注入/集合类型的注入
    - 用于给List结构集合注入的标签：
        - list
        - array
        - set
    - 用于个Map结构集合注入的标签:
          - map
          - props
结构相同，标签可以互换




### 第三种：使用注解提供

#### 曾经的XML配置

```xml
<bean id="" class="" scope=""  init-method="" destroy-method="">
     <property name=""  value="" / ref=""></property>
</bean>
```

#### 现在的注解方式

**用于创建对象的**
      他们的作用就和在XML配置文件中编写一个<bean>标签实现的功能是一样的

- Component:
    - 作用：用于把当前类对象存入spring容器中
    - 属性：
        - value：用于指定bean的id。当我们不写时，它的默认值是当前类名，且首字母改小写。
    
    - 注意：使用这个注释需要在bean配置文件添加对应配置
	    告知spring在创建容器时要扫描的包，配置所需要的标签不是在beans的约束中，而是一个名称为context名称空间和约束中
   
    
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd">
        <context:component-scan base-package=""></context:component-scan>
    </beans>
    ```
- Controller：一般用在表现层

- Service：一般用在业务层

- Repository：一般用在持久层
以上三个注解他们的作用和属性与Component是一模一样。他们三个是spring框架为我们提供明确的三层使用的注解，使我们的三层对象更加清晰

 **用于注入数据的**
      他们的作用就和在xml配置文件中的bean标签中写一个<property>标签的作用是一样的
- Autowired:
    - 作用：自动按照类型注入。只要容器中有唯一的一个bean对象类型和要注入的变量类型匹配，就可以注入成功
                如果ioc容器中没有任何bean的类型和要注入的变量类型匹配，则报错。
                如果Ioc容器中有多个类型匹配时：
    - 出现位置：
              可以是变量上，也可以是方法上
    - 细节：
              在使用注解注入时，set方法就不是必须的了。
- Qualifier:
    - 作用：在按照类中注入的基础之上再按照名称注入。它在给类成员注入时不能单独使用。但是在给方法参数注入时可以
    - 属性：
        - value：用于指定注入bean的id。
- Resource
    - 作用：直接按照bean的id注入。它可以独立使用
    - 属性：
        - name：用于指定bean的id。

以上三个注入都只能注入其他bean类型的数据，而基本类型和String类型无法使用上述注解实现。
          另外，集合类型的注入只能通过XML来实现。
    

- Value
    - 作用：用于注入基本类型和String类型的数据
    - 属性：
        - value：
			用于指定数据的值。它可以使用spring中SpEL(也就是spring的el表达式）
			SpEL的写法：${表达式}

 **用于改变作用范围的**
      他们的作用就和在bean标签中使用scope属性实现的功能是一样的
- Scope
    - 作用：用于指定bean的作用范围
    - 属性：
        - value：指定范围的取值。常用取值：singleton prototype

 **和生命周期相关（了解）**
      他们的作用就和在bean标签中使用init-method和destroy-methode的作用是一样的
- PreDestroy
    - 作用：用于指定销毁方法
- PostConstruct
    - 作用：用于指定初始化方法