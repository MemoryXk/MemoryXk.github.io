# SpringMVC相关

## SpringMVC概述

* **是一种基于Java实现的MVC设计模型的请求驱动类型的轻量级WEB框架**

* **Spring MVC属于Spring框架的后续产品，已经融合在Spring Web Flow里面。**

* **SpringMVC在三层架构属于WEB表现层**

![img](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1584097481257&di=88067a7e5870cbe7c447ef98b3093899&imgtype=0&src=http%3A%2F%2Fblog.chinaunix.net%2Fattachment%2F201408%2F19%2F28999202_1408463022PB2B.jpg)

## 环境搭建（IDEA）

### 普通方式

![img](https://ae01.alicdn.com/kf/H12dbe4c00cf647a09c5e745f314defd6B.png)

### Maven方式

![img](https://static.oschina.net/uploads/space/2018/0225/203753_Znj2_3042999.png)

**在pom.xml中写入以下内容**：

```xml
<properties>
    <spring.version>5.2.3.RELEASE</spring.version>
</properties>

<dependencies>
	<dependency>
	    <groupId>org.springframework</groupId>
	    <artifactId>spring-context</artifactId>
	    <version>${spring.version}</version>
	</dependency>
	<dependency>
	    <groupId>org.springframework</groupId>
	    <artifactId>spring-web</artifactId>
	    <version>${spring.version}</version>
	</dependency>
	<dependency>
	    <groupId>org.springframework</groupId>
	    <artifactId>spring-webmvc</artifactId>
	    <version>${spring.version}</version>
	</dependency>
	<dependency>
	    <groupId>javax.servlet</groupId>
	    <artifactId>servlet-api</artifactId>
	    <version>2.5</version>
	    <scope>provided</scope>
	</dependency>
	<dependency>
	    <groupId>javax.servlet.jsp</groupId>
	    <artifactId>jsp-api</artifactId>
	    <version>2.2</version>
	    <scope>provided</scope>
	</dependency>
</dependencies>
```

> **以上为创建的方式**

### 配置前端控制器（web.xml）

* **在web.xml中配置以下内容（加载SpringMVC配置文件）**

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>

  <servlet>
    <servlet-name>DispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!-- 加载springMVC配置文件-->
    <init-param>
      <param-name>contextConfigLocation</param-name>
        <!-- classpath:指的是java项目下的文件-->
      <param-value>classpath:springMvc.xml</param-value>
    </init-param>
    <!-- 启动服务器时创建DispatcherServlet加载springmvc配置文件-->
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>DispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
</web-app>
```

> **普通方式创建项目时，IDEA会自动帮你创建相应的配置文件（WEB文件夹中的WEB-INF下），并且web.xml中会有相应的初始值，只要适当的修改文件即可**

### **创建SpringMVC配置文件（springmvc.xml）**

> **普通方式创建：默认自动创建在web程序目录下的WEB-INF**
>
> **（dispatcher-servlet.xml和applicationContext.xml，分别代表springMVC配置文件和Spring文件）**
>
> **（普通方式java的程序目录只要src目录，没有main及以下的目录，必要时可手动创建）**
>
> 
>
> **maven方式创建：在java程序根目录下的resource中创建配置文件**
>
> **（Maven方式默认没有src/main/java和resource，需要手动配置）**

* **添加以下内容：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 开启注解扫描-->
    <context:component-scan base-package="controller"></context:component-scan>

    <!-- 配置视图解析器对象-->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
    
    <!--配置前端控制器哪些静态资源不拦截，比如：css,js-->
    <mvc:resources mapping="/js/**" location="/js/"/>
    <mvc:resources mapping="/image/**" location="/image/"/>
    <mvc:resources mapping="/css/**" location="/css/"/>

    <!-- 开启SpringMVC框架注解的支持-->
    <mvc:annotation-driven/>
</beans>
```

> **视图解析器：配置指定的跳转页面的路径目录、文件后缀名**
>
> **mvc:annotation-driven：自动加载处理器映射器和处理器适配器**

#### 利用view-controller标签进行请求地址和视图名称(jsp页面)进行关联

> **如果只创建controller并且跳转到某个页面，则不用写controller方法(节省时间)，直接在springmv配置文件中加入view-controller标签将请求地址和jsp页面进行绑定，一般用于后台页面跳转**
>
> **path:controller方法名(请求名),例如：/admin/login、/admin/login.html **
>
> **view-name:视图名称**
>
> ```xml
> <mvc:view-controller path="/admin/login.html" view-name="admin-login"/>
> ```

### 测试

* **创建index.jsp页面**

  > **href对应的内容前面如果加了 / 则是访问的根目录，不是web项目名的目录**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>入门程序</title>
</head>
<body>
    <h3>入门程序</h3>
    <a href="hello">跳转</a>
</body>
</html>
```

* **创建success.jsp页面(webapp下的pages，pages需收到创建)**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>成功</title>
</head>
<body>
    <h3>成功。。。</h3>
</body>
</html>
```

* **创建Controller包（类路径下：java的程序目录下）**

```java
package controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

//控制器类
@Controller
public class HelloController {

    //映射的访问目录
    @RequestMapping("/hello")
    public String sayHello(){
        System.out.println("Hello SpringMVC");
        return "success";
    }
}

```

* **配置Tomcat服务器**

![img](https://ae01.alicdn.com/kf/H18beb456acac43a6bf2de942271082c4K.png)

**按照下面方式操作：**

![IntelliJ IDEA上创建Maven Spring MVC项目](https://www.linuxidc.com/upload/2016_08/160802204256078.gif)

### 原理

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190605170324545.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMzc4OTQ1,size_16,color_FFFFFF,t_70)

## RequestMapping注解介绍

* **作用**

  **用于建立请求URL和处理请求方法之间的对应关系**

* **可用在方法上面也可以用在类上面**

```java
@Controller
@RequestMapping("/welcome")
public class HelloController {

    //映射的访问目录
    @RequestMapping("/hello")
    public String sayHello(){
        System.out.println("Hello SpringMVC");
        return "success";
    }
}
```

> **用在类上面，则请求的url是/welcome/hello**

* **属性**

  * **value和path都是指定映射的路径（不写则默认为value，一个参数的情况下）**
  * **method用于指定请求类型（GET、POST）**

  ```java
  @RequestMapping("/hello",method={RequestMethod.POST})
  ```

  * **params用于指定限制请求参数的条件。它支持简单的表达式。要求请求的参数的key和value必须和配置的一模一样**

  ```java
  @RequestMapping("/hello",params="username")
  @RequestMapping("/hello",params="username=zh")
  //请求的username和值必须要和上面一样
  ```

  * **headers用于指定限制请求消息头的条件**

    **发送的请求中必须包含的请求头**

  ```
  @RequestMapping("/hello",params="username",headers={"Accept"})
  ```

## 请求参数绑定

* **请求参数绑定字符串**

```java
@RequestMapping("/testParam")
    public String testParam(String username,String password){
        System.out.println("执行了...");
        System.out.println("用户名："+username);
        return "success";
}
//如果请求的参数为username,password，则可以在方法后面添加相对应的参数名即可获取参数
```

* **请求参数绑定实体类**

```html
<form action="param/saveAccount" method="post">
            姓名：<input type="text" name="username"><br>
            密码：<input type="password" name="password"><br>
            金额：<input type="number" name="money"><br>
            用户的姓名：<input type="text" name="user.uname"><br>
            用户的年龄：<input type="text" name="user.age"><br>
            <input type="submit" value="提交">
</form>
```

> 如果Account实体类中含有User实体类，则name应写成：user.uname 

```java
@RequestMapping("/saveAccount")
    public String saveAccount(Account account){
        System.out.println(account);
}
```

* **配置解决中文乱码的过滤器**
  * **在web.xml添加过滤器**

```xml
<filter>
	<filter-name>charset</filter-name>
	<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
	<init-param>
	    <param-name>encoding</param-name>
	    <param-value>utf-8</param-value>
	</init-param>
</filter>
<filter-mapping>
	<filter-name>charset</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```

* **请求参数绑定集合类型**

```html
<form action="param/saveAccount" method="post">
           姓名：<input type="text" name="username"><br>
           密码：<input type="password" name="password"><br>
           金额：<input type="number" name="money"><br>
           <p>封装到List中</p>
           用户的姓名：<input type="text" name="list[0].uname"><br>
           用户的年龄：<input type="text" name="list[0].age"><br>
           <p>封装到map中</p>
           用户的姓名：<input type="text" name="map['one'].uname"><br>
           用户的年龄：<input type="text" name="map['one'].age"><br>
           <input type="submit" value="提交">
</form>
```

## 自定义类型转换器

* **写一个类实现Converter接口**

  > Converter<String, Date> 是你要从什么转成什么

```java
package utils;

import org.springframework.core.convert.converter.Converter;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class StringToDateConverter implements Converter<String, Date> {
    @Override
    public Date convert(String s) {
        if (s == null){
            throw new RuntimeException("请您传入数据");
        }
        SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd");
        try {
           return df.parse(s);
        } catch (ParseException e) {
           throw new RuntimeException("数据类型转换出现错误");
        }
    }
}
```

* **配置自定义类型转换器**

  > 在springmvc配置文件中配置

```xml
 <!-- 配置自定义类型转换器-->
<bean id="ConversionServiceFactoryBean" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <set>
                <bean class="utils.StringToDateConverter"></bean>
            </set>
        </property>
</bean>

<!-- 开启SpringMVC框架注解、类型转换器的支持，-->
<mvc:annotation-driven conversion-service="ConversionServiceFactoryBean"/>
```

## 获取Servlet原生的API

```java
@RequestMapping("/testServlet")
    public String testServlet(HttpServletRequest request, HttpServletResponse response){
        System.out.println(request);

        HttpSession session = request.getSession();
        System.out.println(session);

        ServletContext servletContext = session.getServletContext();
        System.out.println(servletContext);

        System.out.println(response);
        return "success";
}
```

## 常用注解

### RequestParam注解

* **作用**

  **把请求中指定名称的参数给控制器中的形参赋值**

  > ```html
  > <a href="anno/testRequestParam?name=哈哈&pass=123">RequestParam</a>
  > ```

```java
 @RequestMapping("/testRequestParam")
    private String testRequestParam(@RequestParam("name") String username,@RequestParam("pass") String password){
        System.out.println(username);
        System.out.println(password);
        return "success";
}
```

### RequestBody注解

* **作用**

  **用于获取请求体内容。直接使用得到是key=value&key=value...结构的数据。**

  **get请求方式不适用**

  > ```html
  > <a href="anno/testRequestParam?name=哈哈&pass=123">RequestBody</a>
  > ```

```java
@RequestMapping("/testRequestBody")
    private String testRequestBody(@RequestBody String body){
        System.out.println(body);
        return "success";
}

//得到结果name=哈哈&pass=123
```

### PathVariable注解

* **作用**

  **用于绑定url中的占位符。例如：请求url中/delete/{id},这个{id}就是url占位符。**

  **url支持占位符是spring3.0之后加入的。是springmvc支持[rest风格URL](RestFul风格.md)的一个重要标志。**

```java
@RequestMapping("/testPathVariable/{sid}")
    private String testPathVariable(@PathVariable(name="sid") String id){
        System.out.println(id);
        return "success";
}
```

### ModelAttribute注解

* **作用**

  **该注解是Spring'MVC4.3版本之后新加入的。它可以用于修饰方法和参数。**

  **出现在方法上，表示当前方法会在控制器的方法执行之前，先执行。它可以修饰没有返回值的方法。也可以修饰有具体返回值的方法**

  **出现在参数上，获取指定的数据给参数赋值**

```java
@RequestMapping("/testModelAttribute")
private String testModelAttribute(){
        System.out.println(id);
        return "success";
}

@ModelAttribute
public void showUser(){
    System.out.println("showUser执行了")
}
//先执行showUser再执行testModelAttribute
```

### SessionAttribute注解及存值到域对象中

* **作用**

  **用于多次执行控制器方法间的参数共享**

  > ```html
  > <a href="anno/testSessionAttribute">SessionAttribute</a>
  > ```

```java
@RequestMapping("/anno")
@SessionAttributes(value = "msg")  //把msg=王五存到session域中
public class AnnoCotroller {
	@RequestMapping("/testSessionAttribute")
	    private String testSessionAttribute(Model model){
	        System.out.println("testPathVariable执行了");
	        //底层会存储到request域对象中
	        model.addAttribute("msg","王五");
	        return "success";
	}
}
```

> 在success.jsp中取值

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
<html>
<head>
    <title>成功</title>
</head>
<body>
    <h3>成功。。。</h3>
    ${requestScope.msg}
    ${sessionScope.msg}
</body>
</html>
```

### 从JSP页面中的域对象取值

```java
@RequestMapping("/getSessionAttribute")
	private String getSessionAttribute(ModelMap modelMap){
	System.out.println("getSessionAttribute执行了");
	//底层会取出request域对象中的数据
	String msg = (String) modelMap.get("msg");
    System.out.println(msg);
	return "success";
}
```

## ModelAndView类型响应返回值（常用）

```java
@RequestMapping("/testModelAndView")
private ModelAndView testModelAndView(){
    ModelAndView mv = new ModelAndView();
    System.out.println("testModelAndView执行了");
    //把对象值存到mv中，也会存到request域中
    mv.addObject("msg","王五");
    
    //跳转到哪个页面,会去找视图解析器
    mv.setViewName("success");
    return mv;
}
```

## forward和redirect进行转发和重定向（很少用）

```java
@RequestMapping("/testForwardOrRedirect")
private String testForwardOrRedirect(){
    //这两种方法不会经过视图解析器
    //转发操作
    //return "forward:/WEB-INF/pages/success.jsp";
    
    //重定向操作
    return "redirect:/pages/success.jsp"
}
```

## ResponseBody响应json数据（常用）

```javascript
 $(function () {
            $("#btn").click(function () {
                $.ajax({
                    type:"post",
                    url:"user/testAjax",
                    contentType:"application/json;charset=UTF-8",
                    data:'{"username":"haha"}',
                    dataType:"json",
                    success:function (data) {
                        // data服务器响应的json数据，进行解析
                        alert("用户名:"+data.username+"密码："+data.password+"年龄："+data.age);
                    }
                })
            })
})
```

> **因需要用到json解析，所以需要jackson的jar包**
>
> **普通方式需手动下载：https://maven.aliyun.com/mvn/search**
>
> **Maven方式需要在pom.xml添加以下坐标：**

```xml
<dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-annotations</artifactId>
      <version>2.10.2</version>
</dependency>
<dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>2.10.2</version>
</dependency>
<dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.10.2</version>
</dependency>
```

```java
package controller;

import entity.User;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@RequestMapping("/user")
public class UserController {
    /**
     * 模拟异步请求响应
     */
    @RequestMapping("/testAjax")
    @ResponseBody
    public  User testAjax(@RequestBody User user){
        //客户端发送ajax的请求，传的是json字符串，后端把json字符串封装到user对象中
        System.out.println(user);
        //做响应,模拟查询数据库
        user.setUsername("haha");
        user.setPassword("1223");
        user.setAge(18);
        //做响应，返回User对象
        return user;
    }
}
```

### MappingJackson2JsonView方法返回json数据页面

> https://blog.csdn.net/hehyyoulan/article/details/84779402

```java
@RequestMapping(value = "/users")
    public ModelAndView users(){
        User user = new User();
        user.setUsername("haha");
        user.setPassword("1223");
        user.setAge(18);
        ModelAndView mav=new ModelAndView(new MappingJackson2JsonView());
        mav.addObject(user);
        return mav;
}
```

## SpringMVC下的ajax请求

> **当ajax里面没有定义Content-type的时候，jquery默认使用application/x-www-form-urlencoded类型。**

### **用@RequestParam接收前端的数据**

> **当我们在后端用@RequestParam来接收数据时，是支持接收application/x-www-form-urlencoded格式传输的JSON对象。意思就是说后端可以接收数据**

**js代码**

```javascript
$(function () {
    //此处的data为json对象，在js打印出来则返回[object Object],并不会返回对应的内容
    var data = {"username":"zhangsan","password":"123456"}   
            $("#btn").click(function () {
                $.ajax({
                    type:"post",
                    url:"user/testAjax1",  
                    contentType:"application/json;charset=UTF-8",
                    data:data,
                    dataType:"json",
                    success:function (data) {
                        // data服务器响应的json数据，进行解析
                        alert("用户名:"+data.username+"密码："+data.password+"年龄："+data.age);
                    }
                })
            })
})
```

​	**SpringMVC代码**

> **能接收到数据**

```java
 @RequestMapping("/testAjax1")
    private String testRequestParam(@RequestParam("username") String username,@RequestParam("password") String password){
        System.out.println(username);
        System.out.println(password);
        return "success";
}
```

### 用@RequestBody接收前端的数据

> **但是，当我们在后端用@RequestBody来接收数据时，Content-type的类型必须设置为application/json,不会解析JSON对象。而我们下方的data是JSON对象，所以我们要将JSON对象转成JSON字符串，得到JSON对象中的内容。并且发送给后端。**
>
> **利用JSON.stringify()来将JSON对象转成JSON字符串**

​	**js代码**

> **所以，如果我们使用上方ajax代码，则后端不会接收到数据，并且前端会报错误**

```javascript
$(function () {
    //此处的data为json对象，在js打印出来则返回[object Object],并不会返回对应的内容
    var data = {"username":"zhangsan","password":"123456"};
    
            $("#btn").click(function () {
                $.ajax({
                    type:"post",
                    url:"user/testAjax1",                
                    data:JSON.stringify(data),          //将JSON对象转成JSON字符串
                    dataType:"json",
                    success:function (data) {
                        // data服务器响应的json数据，进行解析
                        alert("用户名:"+data.username+"密码："+data.password+"年龄："+data.age);
                    }
                })
            })
})
```

​	**SpringMVC代码**

```java
 @RequestMapping("/testAjax")
 public User testAjax(@RequestBody User user){
        //客户端发送ajax的请求，传的是json字符串，后端把json字符串封装到user对象中
        System.out.println(user);
        return "success";
    }
}
```

### 编写统一JSON格式工具类

> **我们需要创建一个util包，这个包中来写工具来**

* **创建JsonMSG工具类**

```java
package com.memorygzs.util;

import java.util.HashMap;
import java.util.Map;

public class JsonMSG {

    //状态码 1成功 0失败
    private int code;

    //提示信息
    private String msg;
    //要返回的数据
    private Map<String,Object> data = new HashMap<String,Object>();

    /**
     * 请求成功,但是不返回数据
     * @return
     */
    public static JsonMSG success(){
        JsonMSG jsonMSG = new JsonMSG();
        jsonMSG.setCode(1);
        jsonMSG.setMsg("ok");
        return jsonMSG;
    }

    /**
     * 请求失败，不返回数据
     * @return
     */
    public static JsonMSG fail(){
        JsonMSG jsonMSG = new JsonMSG();
        jsonMSG.setCode(0);
        jsonMSG.setMsg("fail");
        return jsonMSG;
    }

    public JsonMSG addInfo(String key, Object value){
        this.getData().put(key,value);
        return this;
    }

    public int getCode() {
        return code;
    }

    public void setCode(int code) {
        this.code = code;
    }

    public String getMsg() {
        return msg;
    }

    public void setMsg(String msg) {
        this.msg = msg;
    }

    public Map<String, Object> getData() {
        return data;
    }

    public void setData(Map<String, Object> data) {
        this.data = data;
    }
}
```

* **在Controller中返回JsonMSG类型**

```java
@RequestMapping("/findAll.json")
@ResponseBody
public JsonMSG findAllJson(){
    List<String> list = new List<String>;
    //返回成功的状态码和信息以及data数据
    return JsonMSG.success().addInfo("listInfo",list);
}
```

> 最终的数据为:
>
> {"code":1,"msg":"ok","data":{"listInfo":["a","b","c"]}}

## 文件上传

> **SpringMVC文件上传需要以下依赖包**
>
> **maven方式在pom.xml中加入下面坐标**

```xml
<dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.4</version>
</dependency>
<dependency>
      <groupId>commons-io</groupId>
      <artifactId>commons-io</artifactId>
      <version>2.6</version>
</dependency>
```

### 传统方式

> **利用Servelt中request，文件上传工具中的DiskFileItemFactory（设置用于储存的临时路径）和ServletFileUpload（一个上传工具，用于上传的）**
>
> ```html
> //enctype必须设置为multipart/form-data，表示是一个文件上传的表单，而不是普通表单
> <form action="user/fileupload1" method="post" enctype="multipart/form-data">
>         选择文件：<input type="file" name="upload"/><br>
>         <input type="submit" value="上传">
> </form>
> ```

```java
private String fileupload1(HttpServletRequest request) throws Exception {
        System.out.println("文件上传....");
        //上传的位置
        String path = request.getSession().getServletContext().getRealPath("/uploads/");
        //判断该路径是否存在
        File file = new File(path);
        if (!file.exists()){
            //创建该文件夹
            file.mkdirs();
        }

        DiskFileItemFactory factory = new DiskFileItemFactory();
        //创建一个上传工具，指定使用缓存区与临时文件存储位置.
        ServletFileUpload upload = new ServletFileUpload(factory);

        //解析request,获取所有的上传文件项，FileItem就是一个上传项
        List<FileItem> items = upload.parseRequest(request);

        for (FileItem item:items) {
            if (item.isFormField()){
                //为普通表单
            }else {
                //为上传文件项
                //获取上传文件的名称
                String filename = item.getName();
                //把文件的名称设置唯一值，uuid
                String uuid = UUID.randomUUID().toString().replace("-","");
                filename = uuid+"_"+filename;
                //上传控件，将上传项保存到服务器中
                item.write(new File(path,filename));
                //删除临时文件
                item.delete();
            }
        }
        return "success";
}
```

### SpringMVC文件上传

> ```html
> //enctype必须设置为multipart/form-data，表示是一个文件上传的表单，而不是普通表单
> //input中name必须和controller中MultipartFile upload 参数一样的名字
> <form action="user/fileupload2" method="post" enctype="multipart/form-data">
>         选择文件：<input type="file" name="upload"/><br>
>         <input type="submit" value="上传">
> </form>
> ```

* **在springmvc配置文件中配置文件解析器**

> **如果加了以下解析器，则传统方式不可用，会被拦截**
>
> **原因：因为在springmvc配置文件中已经将request解析为MultipartHttpServletRequest，所以在传统方式下如果再次解析（二次解析）则返回的值会为空。**

```xml
<!--配置文件解析器对象,id必须为multipartResolver -->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
	<!--文件大小设置为10M-->
	<property name="maxUploadSize" value="10485760"/>
</bean>
```

* **写Controller方法**

```java
/**
* springmvc文件上传
* @param upload
* @return
*/
@RequestMapping("/fileupload2")
private String fileupload2(HttpServletRequest request,MultipartFile upload) throws IOException {
        System.out.println("SpringMVC文件上传....");
        //上传的位置
        String path = request.getSession().getServletContext().getRealPath("/uploads/");
        //判断该路径是否存在
        File file = new File(path);
        if (!file.exists()){
            //创建该文件夹
            file.mkdirs();
        }
        String filename = upload.getOriginalFilename();
        //把文件的名称设置唯一值，uuid
        String uuid = UUID.randomUUID().toString().replace("-","");
        filename = uuid+"_"+filename;
        //上传控件，将上传项保存到服务器中
        upload.transferTo(new File(path,filename));
        
        return "success";
}
```

### 跨服务器上传

> **跨服务器上传需要的依赖包：jersey-client、jersy-core**
>
> **maven方式在pom.xml添加以下坐标：**

```xml
<dependency>
	<groupId>com.sun.jersey</groupId>
	<artifactId>jersey-core</artifactId>
	<version>1.19.4</version>
</dependency>
<dependency>
	<groupId>com.sun.jersey</groupId>
	<artifactId>jersey-client</artifactId>
	<version>1.19.4</version>
</dependency>
```

* **写Controller方法**

```java
/**
 * 跨服务器文件上传
 * @param upload
 * @return
 */
@RequestMapping("/fileupload3")
private String fileupload3(MultipartFile upload) throws IOException {
        System.out.println("跨服务器文件上传....");

        //定义文件上传服务器的路径
        String path = "http://localhost:9090/uploads/";
        String filename = upload.getOriginalFilename();

        //把文件的名称设置唯一值，uuid
        String uuid = UUID.randomUUID().toString().replace("-","");
        filename = uuid+"_"+filename;

        //创建客户端对象
        Client client = Client.create();
        //和文件服务器进行连接
        WebResource webResource = client.resource(path + filename);
        //上传文件,通过字节的方式上传
        webResource.put(upload.getBytes());
        return "success";
}
```

> **报错：**com.sun.jersey.api.client.UniformInterfaceException: PUT http://localhost:9090/uploads/654a1661b41149cea4c920f8e5734970.jpg returned a response status of **405** Method Not Allowed
>
> **原因：**TOMCAT考虑到安全性，默认关闭了TOMCAT的PUT和DELETE请求(即readonly = true)
>
> **解决方法**：进入到文件服务器的**tomcat的config**目录下，修改web.xml,在default servlet下增加参数readonly=false
>
> **路径：E:\apache-tomcat-9.0.22-windows-x64\apache-tomcat-9.0.22\conf\web.xml**

```xml
<servlet>
	<servlet-name>default</servlet-name>
	<servlet-class>org.apache.catalina.servlets.DefaultServlet</servlet-class>
	<init-param>
	    <param-name>debug</param-name>
	    <param-value>0</param-value>
	</init-param>
	<init-param>
	    <param-name>listings</param-name>
	    <param-value>false</param-value>
	</init-param>
	<init-param>
	    <param-name>readonly</param-name>
	    <param-value>false</param-value>
	</init-param>
	<load-on-startup>1</load-on-startup>
</servlet>
```

## SpringMVC的异常处理

* **编写自定义异常类（做提示信息的）**

```java
package exception;

/**
 * 自定义异常类
 **/
public class SysException extends Exception {

    //存储提示信息的
    private String message;

    public SysException(String message) {
        this.message = message;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }


}

```

> **在controller中模拟异常发生**

```java
package controller;

import exception.SysException;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/user")
public class UserController {
    @RequestMapping("/testException")
    private String testException() throws SysException {
        System.out.println("testException执行了...");
        try {
            //模拟异常
            int a = 10/0;
        } catch (Exception e) {
            e.printStackTrace();
            //抛出自定义异常信息
            throw new SysException("查询所有用户出现错误了...");
        }
        return "success";
    }
}
```

* **编写异常处理器**

> **进行异常处理器的编写，处理异常业务逻辑**
>
> **要编写SpringMVC的异常处理器需要实现HandlerExceptionResolver接口里面的方法**
>
> **方法返回ModelAndView类型，在方法中可以将自定义异常信息传给前端**

```java
package exception;

import org.springframework.web.servlet.HandlerExceptionResolver;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 处理异常业务逻辑
 **/
public class SysExceptionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception ex) {
        //获取异常对象
        SysException e = null;
        if (ex instanceof SysException){
            e = (SysException)ex;
        }else {
            e  = new SysException("系统正在维护");
        }
        //创建ModelAndView对象
        ModelAndView mv = new ModelAndView();
        mv.addObject("errorMSG",e.getMessage());
        mv.setViewName("error");
        return mv;
    }
}
```

* **配置异常处理器（跳转到提示页面）**

> **在springmvc配置文件中配置自己写的异常处理器（可当做普通Bean来配）**

```xml
<!--配置异常处理器-->
<bean id="SysExceptionResolver" class="exception.SysExceptionResolver"/>
```

## SpringMVC的拦截器

* **拦截器和过滤器的区别**

  过滤器是servlet规范中的一部分，任何java web工程都可以使用

  拦截器是SpringMVC框架自己的，只有使用了SpringMVC框架的工程才能使用

  过滤器在url-pattern中配置了**/***之后，可以对所有要访问的资源拦截

  拦截器它只会拦截访问的控制器（**Controller**）方法，如果访问的是jsp，html，css，image或者js是不会拦截的

* **编写拦截器类，实现HandlerInterceptor接口**

> **重写方法**

```java
package interceptor;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 自定义拦截器
 **/
public class MyInterceptor1  implements HandlerInterceptor {
    /**
     * 预处理，controller方法前执行
     * return true放行，执行下一个拦截器，如果没有，执行controller中的方法
     * return false不放行
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("MyInterceptor1执行了...前");
        return true;
    }

    /**
     *后处理方法
     */
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("MyInterceptor1执行了...后");
    }

    /**
     *当后处理方法执行完返回到页面时，最后再执行方法
     */
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("MyInterceptor1执行了...最后");
    }
}
```

* **配置拦截器**

> **在springmvc配置文件配置拦截器**

```xml
<!--配置拦截器-->
    <mvc:interceptors>
        <mvc:interceptor>
            <!--要拦截的具体方法-->
            <mvc:mapping path="/user/*"/>
            <!--不要拦截的具体方法
            <mvc:exclude-mapping path=""/>
            -->
            <!--配置拦截器对象-->
            <bean class="interceptor.MyInterceptor1"/>
        </mvc:interceptor>
        
        <!--配置第二拦截器-->
        <mvc:interceptor>
            <!--要拦截的具体方法-->
            <mvc:mapping path="/user/*"/>
            <!--不要拦截的具体方法
            <mvc:exclude-mapping path=""/>
            -->
            <!--配置拦截器对象-->
            <bean class="interceptor.MyInterceptor2"/>
        </mvc:interceptor>
</mvc:interceptors>
```