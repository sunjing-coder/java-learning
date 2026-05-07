# Day03

### 项目中创建项目

> [!WARNING]
>
> 1. new module
>
> 2. resources中创建beans.xml
>
> 3. beans.xml中写入
>
> ```xml
> <?xml version="1.0" encoding="UTF-8"?>
> <beans xmlns="http://www.springframework.org/schema/beans"
> 	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
> 	xsi:schemaLocation="http://www.springframework.org/schema/beans
> 		https://www.springframework.org/schema/beans/spring-beans.xsd">
> 
> 	<bean id="..." class="...">
> 		<!-- collaborators and configuration for this bean go here -->
> 	</bean>
> 
> 	<bean id="..." class="...">
> 		<!-- collaborators and configuration for this bean go here -->
> 	</bean>
> 
> 	<!-- more bean definitions go here -->
> 
> </beans>
> ```



什么是Spring的XML配置

Spring的XML配置是通过XML文件来定义Bean以及它们之间关系的一种方式。这种配置方式直观明了，所有的配置信息都集中在XML文件中，便于管理和维护。

基本环境搭建

首先我们需要在项目中引入Spring的相关依赖。

```xml
<dependency>
<groupId>org.springframework</groupId>
<artifactId>spring-context</artifactId>
<version>5.3.21</version>
</dependency>
```

XML配置文件的基本结构

一个典型的Spring XML配置文件结构如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
  http://www.springframework.org/schema/beans/spring-beans.xsd">
  <!-- Bean的定义 -->
  </beans>
```



### IOC理论推导

在我们之前的业务中,用户的需求可能会影响我们原来的代码,我们需要根据用户的需求去修改原代码。

我们使用了一个Set接口实现

```java
private UserDao userDao;

//l利用set进行动态实现值的注入
public void setUserDao(UserDao userDao){
    this.userDao = userDao;
}
```

之前程序是主动创建对象,控制权在程序员手上

现在使用set注入后,程序变成了被动的接受对象

### bean的作用域

| Scope                                                        | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [singleton](https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html#beans-factory-scopes-singleton) | (Default) Scopes a single bean definition to a single object instance for each Spring IoC container. |
| [prototype](https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html#beans-factory-scopes-prototype) | Scopes a single bean definition to any number of object instances. |
| [request](https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html#beans-factory-scopes-request) | Scopes a single bean definition to the lifecycle of a single HTTP request. That is, each HTTP request has its own instance of a bean created off the back of a single bean definition. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [session](https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html#beans-factory-scopes-session) | Scopes a single bean definition to the lifecycle of an HTTP `Session`. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [application](https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html#beans-factory-scopes-application) | Scopes a single bean definition to the lifecycle of a `ServletContext`. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [websocket](https://docs.spring.io/spring-framework/reference/web/websocket/stomp/scope.html) | Scopes a single bean definition to the lifecycle of a `WebSocket`. Only valid in the context of a web-aware Spring `ApplicationContext`. |

1. 单例模式(Spring默认机制):

```
<bean id="accountService" class="com.something.DefaultAccountService"/>

<!-- the following is equivalent, though redundant (singleton scope is the default) -->
<bean id="accountService" class="com.something.DefaultAccountService" scope="singleton"/>
```

![singleton](D:/images/singleton.png)

2. 原型模式:每次从容器中get的时候都会产生新对象!

```
<bean id="accountService" class="com.something.DefaultAccountService" scope="prototype"/>
```

![prototype](D:/images/prototype.png)

### bean的自动装配

自动装配是Spring满足bean 依赖的一种方式!

Spring会在上下文中自动寻找,并自动给bean装配属性

在Spring中有三种装配的方式:

1. 在xml中显示的装配
2. 在java中显示配置
3. 隐式的自动装配bean

##### 举例:

一个人有两个宠物,一只猫,一只狗.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="cat" class="com.jing.pojo.Cat"/>
	<bean id="dog" class="com.jing.pojo.Dog"/>
    <bean id="people" class="com.jing.pojo.People">
        <property name="name" value="sj"/>
        <property name="dog" ref="dog"/>
        <property name="cat" ref="cat"/>
    </bean>
</beans>
```



##### ByName自动装配

原理:会自动在容器上下文查找,和自己对象set方法后面的值对应的beanid

如果<bean id="dog" class="com.jing.pojo.Dog"/>

中的id="dog111"则会找不到,并报错

> [!CAUTION]
>
> 保证所有的bean的id唯一,并且这个beanid需要和自动注入的set方法的值一致



```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="cat" class="com.jing.pojo.Cat"/>
	<bean id="dog" class="com.jing.pojo.Dog"/>
    <bean id="people" class="com.jing.pojo.People" autowire="ByName">
        <property name="name" value="sj"/>
    </bean>
</beans>
```

##### ByType自动装配

原理:会自动在容器上下文查找,和自己对象属性相同的对应的bean

如果<bean id="dog222" class="com.jing.pojo.Dog"/>

也存在于配置文件中,编译不过去,原因是Dog类型的有多个

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="cat" class="com.jing.pojo.Cat"/>
	<bean id="dog111" class="com.jing.pojo.Dog"/>
    <bean id="people" class="com.jing.pojo.People" autowire="ByType">
        <property name="name" value="sj"/>
    </bean>
</beans>
```

> [!CAUTION]
>
> 保证所有的bean的class唯一,并且这个bean需要和自动注入的属性类型一致

##### 使用注解实现自动装配

[Annotation-based Container Configuration :: Spring Framework](https://docs.spring.io/spring-framework/reference/core/beans/annotation-config.html)

1.导入约束:context约束

2.配置注解的支持:context:annotation-config/

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		https://www.springframework.org/schema/context/spring-context.xsd">

	<context:ggannotation-config/>

</beans>
```

###### @**Autowired**（@Qualifier（value=””））

直接在属性上使用，也可以在set方法上使用

可以不用在编写set方法了，前提是自动装配的属性在IOC容器，且符合符合名字：byname

**@Resourse**

两者都是先看类型再看名字，且需加其他东西配合使用

@Nullable：字段标记了这个注解则可以为null



### 使用注解开发

**可用conponent-scan扫描一个包含所有类的包（配置注解支持））**

@Conponent 组件写在类上，这样就不用写bean（需配置注解支持）

@Value（值）给属性赋值

@Repository用在dao层

@Service用在service层

@Controller用在controller层

@Configuration 代表这是一个配置类,和beans.xml作用同

@Bean 注册这个,就相当于我们的bean标签,方法的名字相当于bean标签中id,方法的返回值相当于bean标签中的class属性

