# Spring相关

## Spring的优势

#### 1 方便解耦，简化开发

通过spring提供的ioc容器，可以将对象间的依赖关系交给spring管理,避免硬编码造成的程序过渡耦合

#### 2 AOP编程的支持

通过spring的aop功能可以实现面向切面编程

#### 3 声明式的事务支持

#### 4 方便集成其他第三方框架

## IOC的概念和作用

IOC指的是**控制反转**,指的就是以前我们获取一个对象时采用的是自己创建一个的方式，这是一个主动的过程；而控制反转后，当我们需要对象时就跟工厂要，而工厂来帮我们创建或者查找对象，这是一个被动的过程。

这种被动接收对象的方式就是控制反转。

它的作用是削减计算机程序的耦合(解除代码中的依赖关系)

**（只能降低，并不能完全解除依赖关系）**

## Spring的环境搭建（IDEA）

### **普通方式**

![](https://ae01.alicdn.com/kf/U95b126ba06dd4eeaa7b33f67c976341fh.png)

![](https://ae01.alicdn.com/kf/U0b90a7c93ecc43c1a3ed6ecdbb31d20bY.png)

注意：如果没有Spring选项，请进入IDEA的Plugins进行开启

![img](https://ae01.alicdn.com/kf/Hb0bd203977044f0581a374d0d48ac55fx.png)

![img](https://ae01.alicdn.com/kf/Hb5bcb35a622e46ec90e97c483f73a14aO.png)

### **Maven方式**

![img](https://ae01.alicdn.com/kf/H43e3afc6873741cbbce72fbeabfeaca6z.png)

![img](https://ae01.alicdn.com/kf/H92eb7375c7c14daaa4c69575a0af1f99D.png)

**在pom.xml中写入以下内容**：

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.2.3.RELEASE</version>
        </dependency>
</dependencies>
```

**或者自己添加Spring必要的环境包**

按住快捷键：**Ctrl+Alt+Shift+S** 打开项目设置界面

![img](https://ae01.alicdn.com/kf/Hd44669b03d154ca7a96fc6cfcb795fbdV.png)

![img](https://ae01.alicdn.com/kf/H5390f5dddc7344d48ab7a358156f7283Y.png)

**在resources文件夹中新建applicationConfig.xml文件，添加以下内容：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans-4.3.xsd">

</beans>
```

## 基于Xml方式的IOC

* **创建service、dao、entity（实体类）、service中的impl（实现接口）包**
* **在entity包中创建User实体类**

* **在service包中创建AccountService接口**
* **在service中的impl包中创建AccountService实现类**

* **配置applicationConfig.xml文件，添加以下内容：**

  > **bean相当于一个变量，把所有的类交给spring管理**
  >
  > **id为名字，class为类的全限类型**

```xml
<bean id="accountService" class="service.impl.AccountServiceImpl"></bean>
```

* **新建Main测试类**

```java
public class Main {
    public static void main(String[] args) {
        //1.获取核心容器对象
        ClassPathXmlApplicationContext ac = new ClassPathXmlApplicationContext("applicationConfig.xml");
        //2.根据id获取Bean对象
        AccountService accountService = (AccountService) ac.getBean("accountService");
        //3.调用accountService中的acc方法
        accountService.acc();
    }
}
```

* **ApplicationContext和BeanFactory两个核心容器接口的区别**

  * ApplicationContext（单例对象用，实际开发基本上都是用此接口）

    它在构建核心容器时，创建对象采取的策略是采用**立即加载**的方式。也就是说，**只要一读取配置文件马上就创建配置文件中配置的对象。**

  * BeanFactory（多例对象用）

    它在构建核心容器时，创建对象采取的策略是采用**延迟加载**的方式。也就是说，**什么时候根据id获取对象了，什么时候才真正的创建对象。**

## Bean对象相关

* **创建bean的三种方式**

```xml
<!--第一种方式：使用默认构造函数创建。
            在spring的配置文件中使用bean标签，配以id和class属性之后，且没有其他属性和标签时。
            采用的就是默认构造函数创建bean对象，此时如果类中没有默认构造函数，则对象无法创建。
    -->
    <bean id="accountService" class="service.impl.AccountServiceImpl"></bean>

    <!--第二种方式：使用普通工厂中的方法创建对象（使用某个类中的方法创建对象，并存入spring容器中）
            首先我们先创建类的bean对象，然后再通过类中的方法创建bean对象
    -->
    <bean id="instanceFactory" class="factory.InstanceFactory"></bean>
    <bean id="accountService" factory-bean="instanceFactory" factory-method="getAccountService"></bean>

    <!--第二种方式：使用静态工厂中的静态方法创建对象（使用某个类中的静态方法创建对象，并存入spring容器中）
    -->
    <bean id="accountService" class="factory.StaticFactory" factory-method="getAccountService"></bean>
```

* **bean对象的作用范围**

|       作用域       |                             描述                             |
| :----------------: | :----------------------------------------------------------: |
|   **singleton**    | （**单例**的 **默认值**）在spring IoC容器仅存在一个Bean实例，Bean以单例方式存在，bean作用域范围的默认值。**（常用）** |
|   **prototype**    | **（多例的）**每次从容器中调用Bean时，都返回一个新的实例，即每次调用getBean()时，相当于执行newXxxBean()。**（常用）** |
|    **request**     | 每次HTTP请求都会创建一个新的Bean，该作用域仅适用于web的Spring WebApplicationContext环境。**（web应用的请求范围）** |
|    **session**     | 同一个HTTP Session共享一个Bean，不同Session使用不同的Bean。该作用域仅适用于web的Spring WebApplicationContext环境。**（web应用的会话范围）** |
| **global-session** | 该作用域将 bean 的定义限制为全局 HTTP 会话。只在 web-aware Spring ApplicationContext 的上下文中有效。**（集群环境的会话范围，全局会话返回，当不是集群环境时，它就是session）** |

```xml
 <bean id="accountService" class="service.impl.AccountServiceImpl" scope="singleton"></bean>
```

* **bean对象的生命周期**

|              |                初始化                |               存活               |                             销毁                             |              总结              |
| :----------: | :----------------------------------: | :------------------------------: | :----------------------------------------------------------: | :----------------------------: |
| **单例对象** |        当容器创建时对象初始化        |    只要容器还在，对象一直存活    |                      容器销毁，对象销毁                      |  单例对象的生命周期与容器相同  |
| **多例对象** | 当我们使用对象时spring容器为我们创建 | 对象只要在使用的过程中就一直存活 | 当对象长时间不用，且没有别的对象引用时，由java的垃圾回收机器回收 | 多例对象的生命周期不和容器相同 |

```xml
<!--
	init-method中的init对应AccountServiceImpl中的init方法
	destroy-method中的destroy对应AccountServiceImpl中的destroy方法
-->
<bean id="accountService" class="service.impl.AccountServiceImpl" scope="prototype" init-method="init" destroy-method="destroy"></bean>
```

## Spring的依赖注入

> **依赖关系的管理：以后都交给spring来维护**
>
> **在当前类需要用到其他类的对象，由spring为我们提供，我们只需要在配置文件中说明**
>
> **依赖关系的维护就称之为依赖注入**
>
> **依赖注入能注入的数据有三类：基本类型和String、其他bean类型（在配置文件中或者注解配置过的bean）**
>
> **注入的方式有三种：使用构造函数提供、使用set方法方法提供、使用注解提供**

* **构造函数注入**

  **constructor-arg标签属性**：

  **type**：用于指定要注入的数据的数据类型，该数据类型页视构造函数中某个或某些参数的类型

  **index**:用于指定要注入的数据给构造函数中指定索引位置的参数赋值。索引的位置是从0开始
  **name**：用于指定给构造函数中指定名称的参数赋值  (常用的)
  **value**：用于指定的值，类型为基本类型和String类型的数据
  **ref**：用于指定其他的bean类型数据，在spring IOC容器中出现过的bean对象

```xml
<bean id="accountService" class="service.impl.AccountServiceImpl">
	<constructor-arg name="name" value="zhou"></constructor-arg>
	<constructor-arg name="age" value="18"></constructor-arg>
	<constructor-arg name="birthday" ref="now"></constructor-arg>
</bean>

<!-- 配置一个日期对象-->
<bean id="now" class="java.util.Date"></bean>
```

* **set方法注入**

  **property标签属性：**

  **name：**用于指定注入时所调用的set方法名称

  **value**：用于指定的值，类型为基本类型和String类型的数据
  **ref**：用于指定其他的bean类型数据，在spring IOC容器中出现过的bean对象

```xml
<bean id="accountService" class="service.impl.AccountServiceImpl">
	<property name="name" value="zhou"></property>
	<property name="age" value="18"></property>
	<property name="birthday" ref="now"></property>
</bean>

<!-- 配置一个日期对象-->
<bean id="now" class="java.util.Date"></bean>
```

* **[注解注入](#zhujie)**

## 基于注解方式的IOC

> 使用注解的前提:必须要在配置文件中配置扫描的包
>
> 且需要aop jar包

* **配置applicationConfig.xml文件，添加以下内容：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans-4.3.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
	<!-- 设置要扫描的包-->
    <context:component-scan base-package="com.project"></context:component-scan>
</beans>
```

* **@Component**

  使用**@Component**注解进行设置bean对象，如果后面没有填值，则默认是类名且首字母小写，后面如果有value值，则value值是bean对象的名字

```java
@Component(value = "accountService")
public class AccountServiceImpl implements AccountService {
    public void acc() {
        System.out.println("此方法是AccountService接口中的方法");
    }
}
```

* **@Controller（表现层）、@Service（业务层）、@Repository（持久层）对应三层架构**

  在对应的类中添加对应的注解，例如service层

```java
@Service(value = "accountService")
public class AccountServiceImpl implements AccountService {
    public void acc() {
        System.out.println("此方法是AccountService接口中的方法");
    }
}
```

#### **<span id="zhujie">注解方式的依赖注入</span>**

* **@Autowired自动按照类型注入**

  可以用在**变量**上也可以用在**方法**上

  假设有个变量是Service接口，那么有两个实现service的类，**@Autowired**就会先通过设置过的注解，自动找到两个类，然后通过变量名来识别要运行哪一个类

* **@Qualifier**

  * **作用**

    在按照类中的注入的基础之上再按照名称注入。它在给类成员注入时不能单独使用（必须和**@Autowired**进行配合）。但是在方法参数输入时可以 

```java
@Service(value = "accountService")
public class AccountServiceImpl implements AccountService {
    @Autowired
    @Qualifier("userDao")
    private UserDao userDao;
    public void acc() {
        System.out.println("此方法是AccountService接口中的方法");
    }
}
```

* **@Resource**
  * 可以直接指定Bean的id，但是属性值为name

```java
@Service(value = "accountService")
public class AccountServiceImpl implements AccountService {
    @Resource(name="userDao")
    private UserDao userDao;
    public void acc() {
        System.out.println("此方法是AccountService接口中的方法");
    }
}
```

> **以上三种都只能注入其他bean类型的数据**

* **@Value**
  * 用于注入基本类型和String数据
  * 属性为value，可以用于指定数据的值。它可以使用spring中SpEL（也就是spring的el表达式）SpEL的写法：${表达式}

* **@Scope**
  * 用于指定Bean的作用范围
  * 属性为value，默认为单例

## 配置类注解的一些方法

* **@Configuration**
  * 指定当前类是一个配置类
* **@ComponentScan(basePackages="com.test")**
  * 用于指定扫描的包

* **@Bean**

  * 用于把当前方法的返回值作为bean对象对如spring的ioc容器中

  * 属性：name指定bean的id，当不写时，默认值为当前方法的名称

```java
@Configuration
@ComponentScan(basePackages="com.test")
public class SpringConfiguration(){
	@Bean(name="runner")
	public QueryRunner createQueryRunner(DataSource dataSource){
		return new QueryRunner(dataSource);
	}
	
	@Bean(name="dataSource")
	public DataSource createDataSource(){
		//此方法为配置数据库连接信息
	}
	
}
```

> 如果使用配置类，获取容器的方法要用AnnotationConfigApplicationContext

```java
ApplicationContext as = new AnnotationConfigApplicationContext("SpringConfiguration.class")
```

## AOP的概念

* **AOP（Aspect Oriented Programming）称为面向切面编程，在程序开发中主要用来解决一些系统层面上的问题，比如日志，事务，权限等待，Struts2的拦截器设计就是基于AOP的思想，是个比较经典的例子。**

* **在不改变原有的逻辑的基础上，增加一些额外的功能。代理也是这个功能，读写分离也能用aop来做。**

> **简单来说，它就是把我们程序可重复性的代码抽取出来，在需要执行的时候，使用动态代理的技术，在不修改源码的基础上，对我们已有的方法进行加强**

## 基于XML方式的AOP

* **在pom.xml加入以下内容**

```xml
<dependencies>
	<dependency>
	    <groupId>org.springframework</groupId>
	    <artifactId>spring-context</artifactId>
	    <version>5.2.3.RELEASE</version>
	</dependency>
	<dependency>
	    <groupId>org.aspectj</groupId>
	    <artifactId>aspectjweaver</artifactId>
	    <version>1.9.5</version>
	</dependency>
</dependencies>
```

* **配置applicationConfig.xml（spring配置文件）文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans-4.3.xsd 
        http://www.springframework.org/schema/aop 
        http://www.springframework.org/schema/aop/spring-aop.xsd ">
 </beans>
```

* **使用aop:config标签表明开始AOP的配置**

* **使用aop:aspect标签表明配置切面**

* **在aop:aspect中使用内部标签配置通知的类型**

  * **aop:before配置前置通知**

    method属性:指定aop:aspect指定的logger类中的对应方法

    pointcut属性:用于指定切入点表达式，指的是对业务层的哪些方法进行加强

    切入点表达式：

    execution(public void com.test.service.AccountServiceImpl.saveAccount())

    execution(*  *  \*.\*.\*.AccountServiceImpl.saveAccount())

    execution(*  *  \*..AccountServiceImpl.saveAccount())

    execution(*  *  \*..\*.\*())   有参数的话，写类型就行

    execution(*  *  \*..\*.\*(..))  全通配写法 

    **pointcut-ref属性：用来指定切面表达式的id**

  * **aop:after-returning配置后置通知类型**

  * **aop:after-throwing配置异常通知类型**

  * **aop:after配置最终通知**

  * **aop:around配置环绕通知**

  > **异常和后置只能执行一个**

```xml
<bean id="logger" class="com.test.service.AccountServiceImpl.Logger"></bean>
<aop:config>
	<!-- 用于配置切面表达式-->
	<aop:pointcut id="pt1" expression="execution(public void com.test.service.AccountServiceImpl.after())"/>
	
    <!-- 配置切面-->
	<aop:aspect id="logAdvice" ref="logger">
		<!-- 配置通知类型，并且进行关联-->
		<aop:before method="before" pointcut-ref="pt1"></aop:before>
		
        <aop:after-returning method="afterretturn" pointcut-ref="pt1"></aop:after-returning>
		
        <aop:after-throwing method="error" pointcut-ref="pt1"></aop:after-throwing>
		
        <aop:after method="after" pointcut-ref="pt1"></aop:after>
	</aop:aspect>
</aop:config>
```

* **aop:around配置环绕通知**

```xml
<!-- 单独使用，不和其他四个通知一起用-->
<aop:around method="aroundLogger" pointcut-ref="pt1"></aop:around>
```

* **编写AccountServiceImpl中的aroundLogger方法(环绕通知)**

```java
 public Object aroundLogger(MethodInvocationProceedingJoinPoint pjp){
        Object rtValue = null;
        try{
            Object[] args = pjp.getArgs();
            System.out.println("前置通知");
            rtValue = pjp.proceed(args);
            System.out.println("后置通知");
            return rtValue;
        }catch(Throwable e){
            System.out.println("异常通知");
            throw new RuntimeException(e);
        }finally {
            System.out.println("最终通知");
        }
}
```

## 基于注解的AOP

* **配置spring配置文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
		http://www.springframework.org/schema/context
		https://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd ">
    <context:component-scan base-package="com.project"></context:component-scan>
    
    <!-- 配置spring中开启aop的注解-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
```

* **设置切面类Logger**
  * @Aspect //表示当前类是一个切面类

```java
@Component("logger")
@Aspect("execution(public void com.test.service.AccountServiceImpl.after())")
public class Logger{
    @Pointcut
    private void pt1(){}
	
    //前置通知
	@Before()
    private void before(){}
    
    //后置通知
    @AfterReturning("pt1()")
    private void afterReturning(){}
    
    //异常通知
    @AfterThrowing("pt1()")
    private void afterThrowing(){}
   
    //最终通知
    @After()
    private void after("pt1()"){}
    
    //环绕通知
    @Around()
    private void around("pt1()"{}
}
```

> **前四个通知顺序会不一样，最终通知始终会在第三个**
>
> **而Around环绕通知是按照顺序来的，按照你的顺序来写的**

## **基于XML方式的声明式事务控制（推荐使用）**

> ##### 事务管理是企业级应用程序开发中必备技术，用来确保数据的完整性和一致性！
>
> **防止插入数据和查询数据发生异常**
>
> **避免了每次对数据操作都要现获得Session实例来启动事务/提交/回滚事务还有繁琐的Try /Catch操作**
>
> **防止出现脏数据的一种方法，同时成功，或者同时失败**

* **环境搭建**

  **需要的Jar包：spring-jdbc,spring-tx,mysql-connector-java、aspectjweaver**

  **在pom.xml中添加以下坐标：**

```xml
<!--Spring事务-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>5.2.3.RELEASE</version>
</dependency>

<!--Spring jdbc模板-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.3.RELEASE</version>
</dependency>

<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
	<version>5.2.3.RELEASE}</version>
</dependency>

<dependency>
	<groupId>org.aspectj</groupId>
	<artifactId>aspectjweaver</artifactId>
	<version>1.9.5</version>
</dependency>
```

* **在Spring配置文件中添加以下内容：**

```xml
<!--配置连接池-->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
	<property name="driverClass" value="com.mysql.cj.jdbc.Driver"/>
	<property name="jdbcUrl" value="jdbc:mysql://localhost:3306/ssm?serverTimezone=Asia/Shanghai"/>
	<property name="user" value="root"/>
	<property name="password" value="root"/>
</bean>
<!--配置Spring框架声明式事务管理-->
<!--配置事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<!--数据库连接池信息-->
	<property name="dataSource" ref="dataSource"/>
</bean>

<!--配置事务通知-->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
	<tx:attributes>
        <!--Service接口中的所有查询方法设置成只读模式-->
	    <tx:method name="find*" propagation="SUPPOETS" read-only="true"/>
	    <tx:method name="*" isolation="DEFAULT"/>
	</tx:attributes>
</tx:advice>

<!--配置AOP增强-->
<aop:config>
	<aop:advisor advice-ref="txAdvice" pointcut="execution(* com.memorygzs.service.impl.*ServiceImpl.*(..))"/>
</aop:config>
```

* **配置事务的属性**

  * **isolation**

    用于指定事务的隔离级别，默认值是DEFAULT，表示使用数据库的默认隔离级别

  * **propagation**

    用于指定事务的传播行为。默认值是REQUIRED,表示一定会有事务、增删改的选择，查询方法可以选择SUPPOETS

  * **read-only**

    用于指定事务是否只读。只有查询方法才能设置为true。默认值是false，表示读写

  * **timeout**

    用于指定事务的超时时间，默认值是-1，表示永不超时。如果指定了数值，以秒为单位。

  * **rollback-for**

    用于指定一个异常，当产生该异常时，事务回滚，产生其他异常时，事务不回滚。没有默认值。表示任何异常都回滚

  * **no-rollback-for**

    用于指定一个异常，当产生该异常时，事务不回滚，产生其他异常时，事务回滚。没有默认值。表示任何异常都回滚

## 基于注解的声明式事务控制（麻烦）

> **如果一个类中有很多个增删改查方法，则需要配很多个事务注解**

* **配置Spring配置文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	https://www.springframework.org/schema/context/spring-context.xsd
    http://www.springframework.org/schema/aop
    http://www.springframework.org/schema/aop/spring-aop.xsd
    http://www.springframework.org/schema/tx
    http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!--配置连接池-->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
	<property name="driverClass" value="com.mysql.cj.jdbc.Driver"/>
	<property name="jdbcUrl" value="jdbc:mysql://localhost:3306/ssm?serverTimezone=Asia/Shanghai"/>
	<property name="user" value="root"/>
	<property name="password" value="root"/>
</bean>
<!--配置Spring框架声明式事务管理-->
<!--配置事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<!--数据库连接池信息-->
	<property name="dataSource" ref="dataSource"/>
</bean>
    
    <!--开启注解扫描-->
    <context:component-scan base-package="com.memorygzs"/>
    
    <!--开启Spring对注解事务的支持，->
    <tx:annotation-driven transaction-manager="transactionManager">
</beans>
```

> **在需要事务支持的地方使用@Transactional注解**
>
> **在类的上面或者方法的上面使用注解**



