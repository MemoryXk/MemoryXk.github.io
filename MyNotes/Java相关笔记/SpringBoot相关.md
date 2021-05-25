# SpringBoot相关

## SpringBoot简介

> 简化Spring应用开发的框架；
>
> 整个Spring技术栈的一个大整合;
>
> J2EE开发的一站式解决方案;

## 微服务

2014年**Martin Fowler**提出

[Martin Fowler个人博客](https://martinfowler.com/)

微服务是一种**架构风格**

一个应用应该是一组**小型服务**;可以通过HTTP的方式进行互通;

每一个功能元素(服务)最终都是一个可**独立替换**和**独立升级**的软件单元

[详细请参考Martin Fowler微服务文档](https://martinfowler.com/microservices/)

## 环境搭建(IDEA)

### 通过官网创建

> **如果选择Maven，则相当于在IDEA中创建Maven项目**

**打开官方网址**:https://start.spring.io/

![img](https://ae01.alicdn.com/kf/Hb09d439d807c449d8ebe4069af43db1cD.png)

* **将下载的项目导入到IDEA中**

![img](https://ae01.alicdn.com/kf/Hbb9aa242a65e40798479b6ca14a1405eI.png)

<img src="https://ae01.alicdn.com/kf/Hbf25c7f5f4f1468083ff9c16769a61f56.png" alt="img" style="zoom:80%;" />

<img src="https://ae01.alicdn.com/kf/Hec446b97a45b42b291c026e27373acf2J.png" alt="img" style="zoom:80%;" />

<img src="https://ae01.alicdn.com/kf/H234985d01ca54bafa7fb2547203b0af3Z.png" alt="img" style="zoom: 80%;" />

<img src="https://ae01.alicdn.com/kf/H6363c353bd284b21a63d90c3759580aaK.png" alt="img" style="zoom:80%;" />

<img src="https://ae01.alicdn.com/kf/H4ea3f154555845ac8ec947b8ea9226237.png" alt="img" style="zoom:80%;" />

<img src="https://ae01.alicdn.com/kf/Hf28f4982fd0d4883a7317e684c5f7d36E.png" alt="img" style="zoom:80%;" />

* **打开pom.xml文件，然后IDEA自动加载坐标**

### 通过IDEA的脚手架工具创建(Spring Initializr) 推荐

> **从spring官网创建，相当于方法一的官网创建移到IDEA中来创建**

![img](https://ae01.alicdn.com/kf/H27759f233302435d886bede0febd9130V.png)

<img src="https://ae01.alicdn.com/kf/Hb25791b7eaeb4fb19b49c158e0ca1a08p.png" alt="img" style="zoom: 67%;" />

* **剩下的操作和上面一样的**

<img src="https://ae01.alicdn.com/kf/H15e6870d31b74a90a759bc11144b1c53r.png" alt="img" style="zoom: 67%;" />

### Maven方式 

<img src="https://ae01.alicdn.com/kf/H43e3afc6873741cbbce72fbeabfeaca6z.png" alt="img" style="zoom:80%;" />

<img src="https://ae01.alicdn.com/kf/H92eb7375c7c14daaa4c69575a0af1f99D.png" alt="img" style="zoom:80%;" />

* **在pom.xml添加以下内容:**

```xml
<!--spring-boot的父工程，此依赖必须要有-->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.4.RELEASE</version>
</parent>

<dependencies>
    <!--Spring-Boot web启动器坐标-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <!--配置spring-boot打包插件，
                如果没有配置此插件，则不会打包
                springboot中的jar包,此插件可以将
                应用打包成一个可执行jar包-->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

### 编写SpringBoot主程序

> **方法①和方法②已经创建好主程序类**

* **在main/java下创建包并且创建SpringBoot主程序类**

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @SpringBootApplication 来标注一个主程序类，说明这是一个Spring Boot应用
 **/
@SpringBootApplication
public class SpringBootMainApplication {
    public static void main(String[] args) {
        //使SpringBoot应用启动起来,下面第一个参数必须是一个SpringBoot的主程序类
        SpringApplication.run(SpringBootMainApplication.class,args);
    }
}
```

### **编写Controller**

* **创建并且编写Controller类**

```java
package com.memorygzs.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

//@ResponseBody
//@Controller
@RestController
public class HelloController {

    
    @RequestMapping("/hello")
    public String hello(){
        return "Hello World";
    }
}
```

**@RestController可以代替@ResponseBody和@Controller,因为@RestController包含这两个注解**

### 启动SpringBoot程序

* **在刚刚写的SpringBoot主程序类中启动Main方法就可以启动SpringBoot应用了**

<img src="https://ae01.alicdn.com/kf/Hfa9dd251e25044d491805111e746a5a8L.png" alt="img"  />

![img](https://ae01.alicdn.com/kf/Hd2bffd3eaaaf49f4854f15ff85c1199cA.png)

* **从上图可以看出，在8080端口启动，访问的时候要带8080进行测试**

**为什么是8080端口？因为SpringBoot中内置tomcat，所以我们并不需要配置tomcat**

## 部署到服务器中

**如果pom.xml中没有配置以下插件，则不会打包springboot中的jar包**

![img](https://ae01.alicdn.com/kf/Hca2297b8cffb4d819d1157722e872834Z.png)

* **在idea最右边找到Mavem选项卡，进行下面操作**

![img](https://ae01.alicdn.com/kf/H3e322ccf7f3d43b888836d0496de8cc3w.png)

* **打包位置放到以下目录**

![img](https://ae01.alicdn.com/kf/Hc61ef61fbe3f42d0828823dee0005f53E.png)

* **将此jar包放到服务器中，使用以下命令启动**

> **我是用cmd进行操作**

输入命令:

```bash
java -jar SpringBoot_01-1.0-SNAPSHOT.jar
```

![img](https://ae01.alicdn.com/kf/H9ea87c78088d40e682f58812626cc7e2b.png)

## SpringBoot配置

### 配置文件

SpringBoot使用一个全局的配置文件，配置文件名是固定的；以下两种格式任选一种

* application.properties 优先级高
* application.yml

配置文件的作用:修改SpringBoot自动配置的默认值

### YAML配置文件

> **以数据为中心，比json、xml等更适合做配置文件**

​	写法为下面内容:

```yaml
server:
	port: 8081
```

* **语法**
  * k:(空格)v:表示一对键值对(空格必须有)  
  * 以空格的缩进来控制层级关系，只要是左对齐的一列数据，都是同一级的
  * 属性和值也是大小写敏感

```yaml
server:
	port: 801
	path: /hello
```

* **值的写法**

  * 字面量:普通的值(数字，字符串，布尔)

    * 字符串默认不用家伙是那个单引号或者双引号，如果用了，双引号不会转义字符、单引号会转义字符
    * name: "zhangsan \n lisi" 输出：zhangsan 回车 lisi   双引号
    * name: 'zhangsan \n lisi' 输出：zhangsan \n lisi   单引号

  * 对象、Map（属性和值）(键值对)

    k:v

  ```yaml
  friends:
  	lastName: zhangsan
  	age: 20
  ```

  ​	行内写法:

  ```yaml
  friends:{lastName: zhangsan,age: 10}
  ```

  * 数组（List、set）

    用(空格)-(空格)值表示数组中的一个元素

  ```yaml
  pets:
   - cat
   - dog
   - pig
  ```

  ​		行内写法

  ```yaml
  pets:[cat,dog,pig]
  ```

* **配置文件注入值**

**yml和properties文件任选一个，如果两个同时存在，会选properties**

**application.properties**

```properties
person.lastName=张三
person.age=18
person.boss=false
person.birth=2020/1/3
person.maps.k1=v1
person.maps.k2=v2
person.lists=a,b,c
person.dog.name=小狗
person.dog.age=2
```

乱码请设置properties默认编码为UTF-8

<img src="https://ae01.alicdn.com/kf/Hd67d8615e3ba4d47961cb5b87d7c82c9A.png" alt="img" style="zoom: 67%;" />

**application.yml文件**

> **这个对应着Person实体类中的属性及值**

```yaml
person:
  lastName: zhangsan
  age: 18
  boss: false
  birth: 2020/1/3
  maps: {k1: v1,k2: v2}
  lists: [a,b,c]
  dog:
    name: 小狗
    age: 2
```

**Person实体类**

> **要在实体类中用@Component,才能提供@ConfigurationProperties功能**
>
> **要使用@ConfigurationProperties注解，需要在pom.xml中配置以下坐标:**
>
> ```xml
> <!--导入配置文件处理器,配置文件进行绑定，以后在yml文件中编写就会有提示-->
> <dependency>
>  <groupId>org.springframework.boot</groupId>
>  <artifactId>spring-boot-configuration-processor</artifactId>
>  <optional>true</optional>
> </dependency>
> ```

```java
package com.memorygzs.bean;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.List;
import java.util.Map;

/**
 * 将配置文件中配置的每一个属性的值，映射到这个组件中
 * @ConfigurationProperties 告诉SpringBoot将本类中所有的属性和配置文件中相关的配置进行绑定
 *  prefix:"person" 配置文件中哪个下面的所有属性和本类进行一一映射
 * 这个类必须是Spring容器中一个组件，才能提供@ConfigurationProperties功能
 * 所以要在下面添加@Component
 */
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
    private String lastName;
    private Integer age;
    private Boolean boss;
    private Date birth;

    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;

	//getter、setter、toString方法这里省略
}

```

> **@ConfigurationProperties 告诉SpringBoot将本类中所有的属性和配置文件中相关的配置进行绑定**
>
> **prefix:"person" 配置文件中哪个下面的所有属性和本类进行一一映射**

### @PropertySource和@ImportResource注解(用于加载指定的配置文件)

* **@PropertySource:加载指定的配置文件**

```java
@PropertySource(value = {"classpath:person.properties"})
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
    private String lastName;
    private Integer age;
    private Boolean boss;
    private Date birth;
}
```

* **@ImportResource:导入Srping的配置文件，让配置文件里面的内容生效**

  SpringBoot里面没有Spring的配置文件，我们自己编写的配置文件也不能识别

  想让Spring的配置文件生效，加载进来，需要将@ImportResource标注在SpringBoot主配置类上

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.PropertySource;

@PropertySource(value = {"classpath:person.properties"})
@SpringBootApplication
public class SpringBootMainApplication {
    public static void main(String[] args) {
        //使SpringBoot应用启动起来
        SpringApplication.run(SpringBootMainApplication.class,args);
    }
}
```

**但是SpringBoot推荐的是用Spring配置类代替Spring xml,所以上方@ImportResource注解可以不用**

所以要写一个Spring配置类，然后不需要用@ImportResource注解了

新建config包且在里面创建springConfig类

```java
package com.memorygzs.config;

import com.memorygzs.service.HelloService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @Configuration:指明当前是一个Spring配置类
 **/
@Configuration
public class MySpringConfig {

    //此方法的返回值添加到容器中;容器中这个组件默认的id就是方法名
    @Bean
    public HelloService helloService(){
        System.out.println("配置类@Bean给容器中添加组件了");
        return new HelloService();
    }
}
```

> **Bean下方的方法返回的值会添加到IOC容器中**

### 配置文件占位符

> **利用${}来获取相应的内容**

* **可以获取随机数或者之前配置的值**

```properties
person.lastName=张三${random.long}
person.age=19
person.boss=false
person.birth=2020/1/3
person.maps.k1=v1
person.maps.k2=v2
person.lists=${person.lastName}_${random.uuid},b,c
person.dog.name=dog_${random.int}
person.dog.age=2
```

## SpringBoot对静态资源的映射规则

* **1）、所有/webjars/,都去classpath:/META-INF/resources/webjars/找资源**

  * webjars：以jar包的方式引入静态资源 [webjars官网](https://www.webjars.org/)
    * 以maven坐标方式引入静态资源（例如：jquery）下载到本地通过上面映射路径找资源

* **2）、“/”访问当前项目的任何资源（静态资源的文件夹）**

  在以下路径找（可以把静态资源放到以下路径）

  ```java
  "classpath:/META-INF/resources/",
  "classpath:/resources/",
  "classpath:/static/"：一般放css、js等静态资源,
  "classpath:/public/",
  "/":当前项目的根路径
  ```

  **访问例子：在上面任意路径下有/js/jquery.js 是这样访问的：localhost:8080/js/jquery.js(不带静态资源文件夹名)**

## Thymeleaf模板引擎

* **引入Thymeleaf**

  在pom.xml添加以下坐标

```xml
 <!--引入Thymeleaf模板引擎-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

* **一般html文件都放在“classpath:/templates/”下**

### 使用

* **导入thymeleaf的名称空间**

```html
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```

* **thymeleaf语法**

```html
 <h1 th:text="${hello}">成功</h1>
th:text="${hello}" 渲染文本
```

​	**th:任意html属性; 来替换原生属性的值**

![thymeleaf语法规则](https://ae01.alicdn.com/kf/Uc121bbfd78bc4a6a9ef9437a99d6a6a60.jpg)

​	注意：上面的th:text是显示原有字符 th:utext 会解析html标签

* **表达式**

```properties
Simple expressions:（表达式语法）
    表达式1: ${...}：获取变量值；OGNL；
    		1）、获取对象的属性、调用方法
    		2）、使用内置的基本对象： 
                ${session.foo}
            3）、内置的一些工具对象：
    表达式2: *{...}：选择表达式：和${}在功能上是一样；
    	补充：配合 th:object="${session.user}" 
   <div th:object="${session.user}">
    <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
    <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p>
    <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
   </div>
   *{...}中的*代表${session.user}这个对象
    
   表达式3: #{...}：获取国际化内容
   表达式4: @{...}：定义URL；
    		@{/order/process(execId=${execId},execType='FAST')}
   表达式5: ~{...}：片段引用表达式
    		<div th:insert="~{commons :: main}">...</div>
    		
Literals（字面量）
      Text literals: 'one text' , 'Another one!' ,…
      Number literals: 0 , 34 , 3.0 , 12.3 ,…
      Boolean literals: true , false
      Null literal: null
      Literal tokens: one , sometext , main ,…
Text operations:（文本操作）
    String concatenation: +
    Literal substitutions: |The name is ${name}|
Arithmetic operations:（数学运算）
    Binary operators: + , - , * , / , %
    Minus sign (unary operator): -
Boolean operations:（布尔运算）
    Binary operators: and , or
    Boolean negation (unary operator): ! , not
Comparisons and equality:（比较运算）
    Comparators: > , < , >= , <= ( gt , lt , ge , le )
    Equality operators: == , != ( eq , ne )
Conditional operators:条件运算（三元运算符）
    If-then: (if) ? (then)
    If-then-else: (if) ? (then) : (else)
    Default: (value) ?: (defaultvalue)
Special tokens:
    No-Operation: _ 
```

* **th:each的使用**

```html
<!-- th:each每次遍历都会生成当前这个标签 也就是遍历多少次就会有多少个标签-->
<tr th:each="good:${goods}"> <!--good是变量名 ${goods}要遍历的对象 -->
	<td th:text="${good.nane}">香蕉</td>
    <td th:text="${good.price}">10</td>
    <td th:text="${good.type}">水果</td>
</tr>

```

* [[]] 和 [()]

```html
<!-- [[]]代表th:text [()]代表th:utext 会解析html标签-->
<span th:each="user:${users}">[[${user}]]</span>
```

## 扩展SpringMVC

* **使用自己的配置类**

  **在config包中创建MyMvcConfig类（springmvc配置类）**

```java
package com.memorygzs.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyMvcConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        //将访问路径 /memorygzs 映射到success.html页面
        registry.addViewController("/memorygzs").setViewName("success");
    }
    
    //配置拦截器
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginHandlerInterceptor()).addPathPatterns("/**").excludePathPatterns("/", "/login.html");
        //addInterceptor添加配置类，addPathPatterns添加要拦截的路径 /**表示拦截所有，excludePathPatterns添加不要拦截的路径
    }
}
```

* **@EnableWebMvc注解全面接管Springmvc（在配置类上添加注解）**

  **相当于重写一个SpringMvc的配置类 springboot的一些Springmvc自动配置失效**

  **这个注解一般不用 因为springboot自动帮你配的一些配置都会失效 比如：静态文件的位置**

## SpringBoot定制错误的页面

### **定制错误的页面**

* **将自己制作的html错误页面放到templates下的error文件夹，并且html文件名为http错误状态码**
* **比如：404错误，则html文件名为404.html**
* **可以使用4xx和5xx作为错误页面的文件名来匹配这种类型的所有错误，精确优先**

## SpringBoot与数据访问

### 整合JDBC

* **在pom.xml添加以下坐标**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jdbc</artifactId>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

* **在SpringBoot配置文件中添加以下内容（我这里用yml文件）**

```properties
spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://192.168.42.251:3306/db_xkblog?serverTimezone=Asia/Shanghai
    driver-class-name: com.mysql.cj.jdbc.Driver
```

### 整合Druid数据源

* **在pom.xml添加以下坐标**

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.23</version>
</dependency>
```

* **在SpringBoot配置文件中添加以下内容**

```properties
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
```

**和上面一样 更换数据源在spring下datasource的type指定数据源**

#### Druid属性配置

* **在SpringBoot配置文件中的spring下DataSource下添加以下内容：**

```properties
initialSize: 5
minIdle: 5
maxActive: 20
maxWait: 60000
timeBetweenEvictionRunsMillis: 60000
minEvictableIdleTimeMillis: 300000
validationQuery: SELECT 1 FROM DUAL
testWhileIdle: true
testOnBorrow: false
testOnReturn: false
poolPreparedStatements: true
#配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙  
filters: stat,wall,log4j
maxPoolPreparedStatementPerConnectionSize: 20
useGlobalDataSourceStat: true  
connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
```

**添加以上内容配置未生效 ,要执行以下操作才会生效（配置Druid类）**

* **在config包下新建DruidConfig类**

```java
@Configuration
public class DruidConfig {

    @ConfigurationProperties(prefix = "spring.datasource")
    @Bean
    public DataSource druid(){
        return new DruidDataSource();
    }

    //配置Druid的监控
    //1、配置一个管理后台的Servlet
    @Bean
    public ServletRegistrationBean statViewServlet(){
        ServletRegistrationBean bean = new ServletRegistrationBean(new StatViewServlet(), "/druid/*");
        Map<String,String> initParams = new HashMap<>();

        initParams.put("loginUsername","admin");
        initParams.put("loginPassword","123456");
        initParams.put("allow","");//默认就是允许所有访问

        bean.setInitParameters(initParams);
        return bean;
    }


    //2、配置一个web监控的filter
    @Bean
    public FilterRegistrationBean webStatFilter(){
        FilterRegistrationBean bean = new FilterRegistrationBean();
        bean.setFilter(new WebStatFilter());

        Map<String,String> initParams = new HashMap<>();
        initParams.put("exclusions","*.js,*.css,/druid/*");

        bean.setInitParameters(initParams);

        bean.setUrlPatterns(Arrays.asList("/*"));

        return  bean;
    }
}
```

**浏览器访问：http://localhost:8080/druid 可进入Druid后台监控 账号密码在上面配置类设置**

### 整合Mybatis

* **在pom.xml添加以下坐标**

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.2</version>
</dependency>
```

#### **xml配置版**

* **在SpringBoot主配置文件中配置mybatis相关内容以及数据库相关信息**

  * **此处用properties和yml都可以，当前用properties,mysql是8.0**

  ```properties
  #数据库连接信息
  spring.datasource.username=root
  spring.datasource.password=root
  spring.datasource.url=jdbc:mysql://192.168.1.105:3306/mybatis?serverTimezone=Asia/Shanghai
  spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
  
  #mybatis相关信息
  #config-location指的是全局配置文件在哪个位置
  mybatis.config-location=classpath:mybatis/mybatis-config.xml
  #mapper-locations指的是mapper映射文件在哪个位置
  mybatis.mapper-locations=classpath:mybatis/mapper/*.xml
  ```

* **在dao包中新建IStudentDao接口**

```java
//此处可以用@Mapper将接口扫描到spring IOC容器中
//或者在SpringBoot主配置类中添加注解@MapperScan来指定dao包路径将接口扫描到spring IOC容器中
@Repository
public interface IStudentDao {

    List<Student> findAll();

}
```

* **在resources下mybatis文件夹中的mapper新建StudentDaoMapper.xml映射文件**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.memorygzs.dao.IStudentDao">
    <select id="findAll" resultType="com.memorygzs.bean.Student">
        select * from student
    </select>
</mapper>
```

* **在resources下mybatis文件夹中新建mybatis全局配置文件mybatis-config.xml**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!-- Mybatis的主配置文件-->
<configuration>

</configuration>
```

**最后可以通过test测试类来测试mybatis是否整合成功**

**注意：注解版不需要配置任何内容 按照原来的注解方式来做**

## SpringBoot缓存

### 注解版缓存

* **在SpringBoot主配置文件中添加以下注解**
  * **@EnableCaching**

* **缓存注解介绍**
  |       Cache        | 缓存接口，定义缓存操作。实现有：RedisCache、EhCacheCache、ConcurrentMapCache等 |
  | :----------------: | :----------------------------------------------------------- |
  |  **CacheManager**  | **缓存管理器，管理各种缓存（Cache）组件**                    |
  |   **@Cacheable**   | **主要针对方法配置，能够根据方法的请求参数对其结果进行缓存（一般用于查询数据时使用）**<br />**属性：<br />cacheNames/value:指定缓存组件的名字** |
  |  **@CacheEvict**   | **主要针对方法配置,清空缓存（一般用于删除数据时使用）**      |
  |   **@CachePut**    | **主要针对方法配置,保证方法被调用（一定会执行方法），又希望结果被缓存。(修改数据库内容，同时更新缓存，一般用于更新数据时使用)** |
  |    **@Caching**    | **主要针对方法配置，用于指定多个缓存规则，以上三个注解的结合。例如:<br />@Caching(cacheable={@Cacheable=()},put={@CachePut=()}）** |
  | **@EnableCaching** | **开启基于注解的缓存,用于Config文件**                        |
  |  **keyGenerator**  | **缓存数据时key生成策略**                                    |
  |   **serialize**    | **缓存数据时value序列化策略**                                |

* **spEL表达式**

| **名字**          | **位置**               | **描述**                                                     | **示例**                  |
| ----------------- | ---------------------- | ------------------------------------------------------------ | ------------------------- |
| **methodName**    | **root object**        | **当前被调用的方法名**                                       | **#root.methodName**      |
| **method**        | **root object**        | **当前被调用的方法**                                         | **#root.method.name**     |
| **target**        | **root object**        | **当前被调用的目标对象**                                     | **#root.target**          |
| **targetClass**   | **root object**        | **当前被调用的目标对象类**                                   | **#root.targetClass**     |
| **args**          | **root object**        | **当前被调用的方法的参数列表**                               | **#root.args[0]**         |
| **caches**        | **root object**        | **当前方法调用使用的缓存列表如@Cacheable(value={"cache1", "cache2"})），则有两个cache** | **\#root.caches[0].name** |
| **argument name** | **evaluation context** | **方法参数的名字. 可以直接 #参数名 ，也可以使用 #p0或#a0 的形式，0代表参数的索引；** | **#id 、 #a0 、 #p0**     |
| **result**        | **evaluation context** | **方法执行后的返回值（仅当方法执行之后的判断有效，如‘unless’，’cache put’的表达式 ’cache evict’的表达式,'cacheable'中不可用’'beforeInvocation=false）** | **\#result**              |

* **@CacheEvict、@Cacheable、@CachePut如果要对同一个实体类对象进行缓存操作，则缓存组件的名字（cacheNames/value）一致，key根据实际情况来定（一般一个实体对象Service中，缓存组件的名字基本上都是相同的）**

  * **缓存名字一个一个写太麻烦了，用@CacheConfig注解可以指定这个实体对象Service缓存组件的名字，[见下面操作](#cacheNames)**

* **@CachePut更新缓存流程**

  * **首先我们先根据id查询一条数据，此时key为id的值，value为返回的实体类对象，并且存到缓存中**
  * **然后再次访问时从缓存中取出数据**
  * **再执行更新方法，修改数据库数据，此时更新的数据已经存到了缓存中，但是，它的key和value默认都是返回的实体类对象**
  * **更新的数据已经存到了缓存中，但是key的值不一样，所以我们再次查询时是为更新的数据**
  * **所以最后我们要在@CachePut属性中指定key的值，例如：#employee.id或者#result.id，根据哪个key来更新缓存**

  **注意：你查询时的缓存中的key要和更新时缓存中的key一致，不然会导致已更新缓存，但是key不一样，查询的数据还是未更新时的数据**

* <span id="cacheNames">**@CacheConfig注解介绍**</span>
  * **使用了这个注解，下面方法上用其他缓存注解时就不需要指定value或者cacheNames了**
  * **在要指定的实体对象Service实现类中加入@CacheConfig注解**

```java
@CacheConfig(cacheNames="emp")/*默认是value，value和cacheNames都是指缓存组件名字*/
@Service
public class EmployeeServiceImpl implements IEmployeeService {
    @Autowired
    IEmployeeDao iEmployeeDao;
}
```

