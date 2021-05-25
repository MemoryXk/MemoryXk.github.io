# RestFul风格

REST是一种软件架构风格，或者说是一种规范，其强调HTTP应当以资源为中心，并且规范了URI的风格；规范了HTTP请求动作（GET/PUT/POST/DELETE/HEAD/OPTIONS）的使用，具有对应的语义。
 核心概念包括：

##### 资源（Resource）：

 在REST中，资源可以简单的理解为URI，表示一个网络实体。比如，/users/1/name，对应id=1的用户的属性name。既然资源是URI，就会具有以下特征：名词，代表一个资源；它对应唯一的一个资源，是资源的地址。

##### 表现（Representation）：

 是资源呈现出来的形式，比如上述URI返回的HTML或JSON，包括HTTP Header等；
  REST是一个无状态的架构模式，因为在任何时候都可以由客户端发出请求到服务端，最终返回自己想要的数据，当前请求不会受到上次请求的影响。也就是说，服务端将内部资源发布REST服务，客户端通过URL来定位这些资源并通过HTTP协议来访问它们。
 例如：

![img](https://ae01.alicdn.com/kf/Hacc5b89c6c044da1955175f961087290S.jpg)

 从上图可以看出请求路径相同但请求方式不同，所代表的业务操作也不同，例如，/advertiser/1这个请求，带有GET、PUT、DELETE三种不同的请求方式，对应三种不同的业务操作。
  虽然REST看起来还是很简单的，实际上我们往往需要提供一个REST框架，让其实现前后端分离架构，让开发人员将精力集中在业务上，而并非那些具体的技术细节。实际工作中，一般采用基于SSM（Spring+SpringMVC+Mybatis）框架实现REST风格的软件架构。

https://www.runoob.com/w3cnote/restful-architecture.html