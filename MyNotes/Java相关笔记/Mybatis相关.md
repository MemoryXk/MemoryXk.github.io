# Mybatis相关

## Mybatis环境搭建(IDEA)

### **<span id="AddideaProject">新建IDEA项目</span>**

![img](https://ae01.alicdn.com/kf/H7302f0d106064a41a07a1790cfe5b90fI.png)

![img](https://ae01.alicdn.com/kf/Ha3f8c6b7902f40dcb1bd68c0483362dd0.png)

![img](https://ae01.alicdn.com/kf/Hde1471ea4b6a49dbb16d16bd14570bcfH.png)

### **<span id="pom">添加以下内容到pom.xml</span>**

```xml
<dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.4</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.19</version>
        </dependency>
</dependencies>
```

### **<span id="entity">创建User实体类</span>**

> **创建entity包,在entity里面创建User实体类**

```java
package entity;

import java.util.Date;

public class User {
    private Integer id;
    private String username;
    private Date birthday;
    private String address;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", birthday=" + birthday +
                ", address='" + address + '\'' +
                '}';
    }
}
```

### **创建IUserDao接口**

> **创建dao包，在dao里面创建IUserDao接口**

```java
package dao;

import entity.User;

import java.util.List;

/**
 * 用户的持久层接口
 **/
public interface IUserDao {

    /**
     * 查询所有操作
     * @return
     */
    List<User> findAll();
}

```

### **在resources文件夹中创建SqlMapConfig.xml文件（Mybatis的主配置文件）**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!-- Mybatis的主配置文件-->
<configuration>
    <!-- 配置环境-->
    <environments default="mysql">
        <!-- 配置mysql的环境-->
        <environment id="mysql">
            <!-- 配置事务的类型-->
            <transactionManager type="jdbc"></transactionManager>
            <!-- 配置数据源（连接池）-->
            <dataSource type="POOLED">
                <!--
                    配置连接数据库的4个基本信息
                    Mysql8.0 Driver要使用com.mysql.cj.jdbc.Driver
                -->
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <!-- Mysql8.0后面要加?serverTimezone=Asia/Shanghai-->
                <property name="url" value="jdbc:mysql://localhost:3306/db_mybatis?serverTimezone=Asia/Shanghai"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>

    <!-- 指定映射配置文件的位置，映射配置文件指的是每个dao独立的配置文件-->
    <mappers>
            <mapper resource="mapper/IUserDao.xml"></mapper>
    </mappers>
</configuration>
```

### **创建Mybatis的映射配置文件**

* 在resources文件夹中创建mapper文件夹再创建IUserDao.xml添加以下内容:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="dao.IUserDao">

    <!-- 配置查询所有 id对应上方接口中的方法-->
    <select id="findAll" resultType="entity.User">
        select * from user
    </select>
</mapper>
```

**注意：完成以上操作，无需创建接口的实现类**

### **<span id="test">创建Main类（用来测试mybatis）</span>**

```java
import dao.IUserDao;
import entity.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.InputStream;
import java.util.List;

public class Main {
    public static void main(String[] args) throws Exception {
        //1.读取配置文件
        InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
        //2.创建SqlSessionFactory工厂
        /**
         * 因SqlSessionFactory是一个接口，所以要用
         * SqlSessionFactoryBuilder来创建一个SqlSessionFactory对象
         */
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(in);
        //3.创建工厂生产SqlSession对象
        SqlSession sqlSession = factory.openSession();
        //4.使用SqlSession创建Dao接口的代理对象
        IUserDao mapper = sqlSession.getMapper(IUserDao.class);
        //5.使用代理对象执行方法
        List<User> users = mapper.findAll();
        for (User user:users){
            System.out.println(user);
        }
        //6.释放资源
        sqlSession.close();
        in.close();
    }
}
```

## Mybatis的XML增删改查

> **增删改测试时都需要提交并且要实例化User实体类**
>
> **还要给User中的属性设置值**
>
> **注解的方式是用@+要执行的增删改查方法，后面再加上下方的sql语句**
>
> **例：@Update("update user set username=#{username} where id=#{id}")**

* **增加**

```xml
 <insert id="saveUser" parameterType="entity.User">
	insert into user(username,address,sex,birthday) values(#{username},#{address},#{sex},#{birthday})
</insert>
```

* **删除**

```xml
<delete id="delUser" parameterType="entity.User">
	delete from user where address=#{address}
</delete>
```

* **更新**

```xml
<update id="updateUser" parameterType="entity.User">
	update user set username=#{username} where id=#{id}
</update>
```

* **查询**

```xml
<select id="findUserName" resultType="String">
 	select username from user
</select>

<!-- 根据ID查询信息-->
<select id="findByIdSelect" parameterType="Integer" resultType="entity.User">
	select * from user where id=#{id}
</select>

<!-- 根据名字模糊查询信息在测试时需要带模糊查询的表达式-->
<select id="findByUserNameSelect" parameterType="String" resultType="entity.User">
	select * from user where username like #{username}
</select>
```

## Mybatis中的XML多表查询

### 一对一

> **人和身份证号**
>
> ​	**一个人只能有一个身份证号**
>
> ​	**一个身份证号对应一个人**

* **新建Person实体类**

  > **一对一：随便在哪个实体类中添加另一个实体类和id，这里用Person类中包含Card类和id**

  字段:

  pid、username(姓名)、age(年龄)、sex(性别)、cid(身份证id)

```java
private Integer pid;
private String username;
private Integer age;
private Integer sex;
private Integer cid;
private Card card;
```

* **新建Card实体类**

  字段：

  cid、card(身份证号)

```java
private Integer cid;
private String card;
```

* **新建IPersonDao接口**

```java
package dao;

import entity.Person;

import java.util.List;

public interface IPersonDao {
	/**
 	* 通过pid查询出身份证号
 	**/
    List<Person> findAll();

}
```

* **新建IPersonDao.xml映射文件**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
        <!DOCTYPE mapper
                PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
                "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="dao.IPersonDao">
    <!--定义封装account和user的restultMap-->
    <resultMap id="personCard" type="entity.Person">
        <id property="pid" column="pid"></id>
        <result property="username" column="username"></result>
        <result property="age" column="age"></result>
        <result property="sex" column="sex"></result>
        <result property="cid" column="cid"></result>
        <!--一对一的关系映射：配置封装user的内容-->
        <association property="card" column="cid" javaType="entity.Card">
            <id property="cid" column="cid"></id>
            <result column="card" property="card"></result>
        </association>
    </resultMap>
    <select id="findAll" resultMap="accountUserMap">
        SELECT * from person p,card c where p.cid=c.cid
    </select>
</mapper>
```

* **在Mybatis主配置文件中添加对应的映射文件**
* **[测试](#test)**

### 一对多

> **用户和账户**
>
> ​	**一个用户可以有多个账户**

* **新建User实体类**

```java
//一对多关系映射:主表实体（User）应该包含从表实体（Account）的集合引用
private Integer id;
private String username;
private String password;
private Date birthday;
private String sex;
private String address;
private List<Account> accounts;
```

* **新建Account实体类**

  > 在多的一方添加User表中的id

```java
private Integer id;  //账户id
private Double money;
private Integer uid; //用户id
```

* **新建IUserDao接口**

```java
package dao;

import entity.User;

import java.util.List;

public interface IUserDao {

    /**
 	* 通过用户id去查询该用户有多少个账户
 	**/
    List<User> findAll();

}
```

* **新建IUserDao.xml映射文件**

```xml
<resultMap id="userAccount" type="entity.User">
    <id property="id" column="id"></id>
    <result property="username" column="username"></result>
    <result property="password" column="password"></result>
    <result property="address" column="address"></result>
    <result property="sex" column="sex"></result>
    <result property="birthday" column="birthday"></result>
    <!--配置user对象中accounts集合的映射-->
    <collection property="accounts" ofType="entity.Account">
        <id property="id" column="aid"></id>
        <result property="uid" column="uid"></result>
        <result property="money" column="money"></result>
    </collection>
</resultMap>

<!-- 一对多：通过用户查询该用户有多少账户 -->
<select id="findAllAccount" resultMap="userAccount">
	SELECT u.*,a.id AS aid,a.`money` FROM USER u LEFT JOIN account a ON u.`id` = a.`uid`
</select>
```

* **在主配置文件中添加对应的映射文件**
* **[测试](#test)**

### 多对多 

* **和一对多的配置是一样的，只是在两个配置文件中都要配**

> **学生和老师**
>
> ​	**一个学生可以被多名老师教**
>
> ​	**一个老师可以教多名学生**
>
> **多对多关系：在双方实体类中添加对方的list集合**
>
> **需要在数据库中创建3张表:student表、teacher表、student_teacher表（中间表）**

* **新建Student实体类**

  * 字段

    sid、sname(学生姓名)、s_sex(学生性别)

```java
private Integer studentId;
private String studentName;
private String studentSex;
//多对多关系映射:一个学生被多名老师教
private List<Teacher> teachers;
```

* **新建Teacher实体类**

  * 字段

    tid、tnamet(老师姓名)、t_sex(老师性别)

```java
private Integer teacherId;
private String teacherName;
private String teacherSex;
//多对多关系映射:一个老师有多名学生
private List<Student> students;
```

* **新建IStudentDao接口**

```java
package dao;

import entity.Student;

import java.util.List;

public interface IStudentDao {

    /**
 	* 查询学生，以及学生有多少个老师
 	**/
    List<Student> findAll();

}
```

* **新建ITeacherDao接口**

```java
package dao;

import entity.Teacher;

import java.util.List;

public interface ITeacherDao {

    /**
 	* 查询老师，以及老师有多少个学生
 	**/
    List<Teacher> findAll();

}
```

* **新建IStudentDao.xml映射文件**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
        <!DOCTYPE mapper
                PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
                "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="dao.IStudentDao">
    <resultMap id="studentMap" type="entity.Student">
        <id property="studentId" column="sid"></id>
        <result property="studentName" column="sname"></result>
        <result property="studentSex" column="s_sex"></result>
         <collection property="teachers" ofType="entity.Teacher">
            <id property="teacherId" column="tid"></id>
            <result property="teacherName" column="tname"></result>
            <result property="teacherSex" column="t_sex"></result>
        </collection>
    </resultMap>
    <select id="findAll" resultMap="studentMap">
        select s.*,t.* from student s left 
		 join student_teacher st on s.sid = st.sid 
		 left join teacher t on t.tid = st.tid
    </select>
</mapper>
```

* **新建ITeacherDao.xml映射文件**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
        <!DOCTYPE mapper
                PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
                "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="dao.ITeacherDao">
    <resultMap id="studentMap" type="entity.Teacher">
        <id property="teacherId" column="tid"></id>
        <result property="teacherName" column="tname"></result>
        <result property="teacherSex" column="t_sex"></result>
         <collection property="students" ofType="entity.Student">
            <id property="studentId" column="sid"></id>
        	<result property="studentName" column="sname"></result>
        	<result property="studentSex" column="s_sex"></result>
        </collection>
    </resultMap>
    <select id="findAll" resultMap="studentMap">
        select t.*,s.* from teacher t left 
		 join student_teacher st on t.tid = st.tid 
		 left join student s on s.sid = st.sid
    </select>
</mapper>
```

* **在主配置文件中添加对应的映射文件**
* **[测试](#test)**

## Mybatis注解的使用

* **[新建IDEA项目](#AddideaProject)**

* **[添加内容到pom.xml](#pom)**

* **修改SqlMapConfig文件**

> **把上面SqlMapConfig文件内容复制一下，修改以下内容：**

```xml
<mappers>
            <mapper class="dao.IUserDao"></mapper>
</mappers>
```

> **注意：修改完之后Resources文件夹里面的mapper文件夹（映射配置文件）可以**
>
> **删除，在使用注解方式的时候映射文件是没有用的**

* **[创建User实体类](#entity)**
* **创建IUserDao接口**

```java
public interface IUserDao {
    @Select("select * from user")
    List<User> findAll();
}
```

#### Mybatis注解建立实体类数据库表对应关系

> id="userMap"可以让别的方法引用：
>
> 例：
>
> @Select("select * from user")
>
> @Results(value={"userMap"})
>
> 或者
>
> @Results("userMap")

```java
public interface IUserDao {
    @Select("select * from user")
    @Results(id="userMap",value={
            @Result(id=true,column = "id",property = "userid"),
            @Result(column = "username",property = "userName"),
            @Result(column = "sex",property = "sex")
    })
    List<User> findAll();
}
```

#### Mybatis注解多表查询

* **一对一**

```java
public interface IUserDao {
    @Select("select * from user")
    @Results(id="userMap",value={
            @Result(id=true,column = "id",property = "userid"),
            @Result(column = "username",property = "userName"),
            @Result(column = "sex",property = "sex")，
            @Result(property = "user",column = "uid",one=@one(select="dao.IUserDao.findByid",fetchType = FetchType.EAGER))
    })
    List<User> findAll();
}
```

* **一对多**

```java
@Result(property = "accounts",column = "id",many=@many(select="dao.IAccountDao.findAccountByid",fetchType = FetchType.LAZY))
})
```



## Mybatis主配置文件标签信息

#### **Properties标签的使用及细节**

* **将数据库连接信息写入到resources文件夹中的jdbcConfig.properties文件中**

```properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/db_mybatis?serverTimezone=Asia/Shanghai
user=root
password=root
```

* **修改Mybatis主配置文件中的连接池信息**

```xml
#在configuration标签下添加以下标签（引入jdbcConfig.properties）：
<properties resource="jdbcConfig.properties"/>

#修改连接池信息（value和jdbcConfig.properties文件中的内容相对应):
<dataSource type="POOLED">
<property name="driver" value="${driver}"/>
<property name="url" value="${url}"/>
<property name="username" value="${user}"/>
<property name="password" value="${password}"/>
</dataSource>
```

#### TypeAliases标签和Package标签

* **TypeAliases标签（双标签）**
  * **放在environments标签之上**
  * **标签内可以放typeAlias标签，用于配置别名，type属性指定的是实体类全限定类名。alias属性指定别名，当指定了别名不区分大小写**
* **Package标签**
  * **放在typeAliases标签中是用于配置别名的包，name属性为指定的包路径。该包下的实体类都会注册别名，并且类名就是别名，不区分大小写**
  * **放在mappers标签中是用于指定dao接口所在的包，不需要写class或者resource**

## Mybatis中动态SQL语句

* **If标签**

> **where 1=1 表示无条件的，当用户查询信息的时候，如果没有选择任何选项也不会出错，当你选择了哪些信息，就要在where 1=1 后面加上 and + 你选择的字段信息**

```xml
select * from user where 1=1
<if test="username != null">
	and username = #{username}
</if>
<if test="sex != null">
	and sex = #{sex}
</if>
<if test="address != null">
	and sex = #{address}
</if>
```

* **where 标签**

> **使用了where标签就不需要使用where 1=1**

```xml
select * from user
<where>
	<if test="username != null">
		and username = #{username}
	</if>
	<if test="sex != null">
		and sex = #{sex}
	</if>
	<if test="address != null">
		and sex = #{address}
	</if>
</where>
```

* **bind标签**

> **bind 标签可以使用 OGNL 表达式创建一个变量井将其绑定到上下文中。用于模糊查询**
>
> **bind 标签的两个属性都是必选项， name 为绑定到上下文的变量名， va l ue 为 OGNL 表**
> **达式。**

```xml
<bind name="usernmae" value="'%'+name+'%'"/>
select * from user where name like #{username}
```

**value中的name为传入的字段属性名，也可以设置为 _parameter.getName()**

**#{username} 必须为bind中的name**

* **foreach标签**

> **foreach的主要用在构建in条件中，它可以在SQL语句中进行迭代一个集合。**

​		**属性：**

​	**item：**集合中元素迭代时的别名，该参数为必选。

​	**index**：在list和数组中,index是元素的序号，在map中，index是元素的key，该参数可选

​	**open**：foreach代码的开始符号，一般是(和close=")"合用。常用在in(),values()时。该参数可选

​	**separator**：元素之间的分隔符，例如在in()的时候，separator=","会自动在元素中间用“,“隔开，避免手动输入	逗号导致sql错误，如in(1,2,)这样。该参数可选。

​	**close:** foreach代码的关闭符号，一般是)和open="("合用。常用在in(),values()时。该参数可选。

​	**collection:** 要做foreach的对象，作为入参时，List对象默认用"list"代替作为键，数组对象有"array"代替作为		键，Map对象没有默认的键。当然在作为入参时可以使用@Param("keyName")来设置键，设置keyName		后，list,array将会失效。 除了入参这种情况外，还有一种作为参数对象的某个字段的时候。举个例子：如果		User有属性List ids。入参是User对象，那么这个collection = "ids".***如果User有属性Ids ids;其中Ids是个			对象，Ids有个属性List id;入参是User对象，那么collection = "ids.id"***

**单参数List类型：**

```xml
<select id="countByUserList" resultType="_int" parameterType="list">
select count(*) from users
  <where>
    id in
    <foreach item="item" collection="list" separator="," open="(" close=")">
      #{item.id}
    </foreach>
  </where>
</select>
```

**单参数Arrays数组类型：**

```xml
<select id="dynamicForeach2Test" resultType="Blog">
	select * from t_blog where id in
	<foreach collection="array" item="item" open="(" separator="," close=")">
			#{item}
	</foreach>
</select> 
```

**Map类型：**

> List ids = new ArrayList()
>
> ids.add(1);
>
> ids.add(2);
>
> ids.add(3);
>
> Map params = new HashMap();
>
> params.put("ids",ids)
>
> 下面的ids是传入的参数map的key值：

```xml
<select id="dynamicForeach3Test" resultType="Blog">
	select * from t_blog where title like "%"#{title}"%" and id in
	<foreach collection="ids" item="item" open="(" separator="," close=")">
                #{item}
	</foreach>
</select>
```

## PageHelper分页插件

### 配置

* **在pom.xml中添加pagehelper依赖坐标**

```xml
<!--Mybatis PageHelper插件-->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.1.11</version>
</dependency>
```

* **在SqlSessionFactoryBean配置MyBatis插件(Spring.xml)**

  **在相应的位置添加以下内容:**

```xml
 <!--配置SqlSessionFactory工厂-->
 <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
     <property name="dataSource" ref="dataSource"/>
     <!--指定要扫描的mapper映射文件路径-->
     <property name="mapperLocations" value="classpath:mapper/*"/>
     <!-- 配置插件-->
     <property name="plugins">
         <array>
             <!-- 配置PageHelper插件-->
             <bean class="com.github.pagehelper.PageInterceptor">
                 <property name="properties">
                     <props>
                         <!-- 配置页面的合理化修正,在1-总页数 之间修正页码-->
                         <prop key="reasonable">true</prop>
                     </props>
                 </property>
             </bean>
         </array>
     </property>
 </bean>
```

> **PageHelper插件4.0.0以后的版本支持自动识别使用的数据库**
>
> **所以不需要配置  \<prop key="dialect">mysql\</prop>**

### 使用

* **dao层写分页sql语句时不需要添加limit**

```mysql
SELECT * FROM goods WHERE gname like concat("%",#{gname},"%")
```

​	**dao接口中返回List集合**

```java
List<Goods> queryByGName(String gname);
```

* **在service层使用PageHelper**

  > **要传入以下参数:**
  >
  > **1.要查询的字符串-gname**
  >
  > **2.要查询的分页页码-pageNum**
  >
  > **3.设置每一页有多少条数据-pageSize**

  **service接口中添加以下方法，返回PageInfo类型的集合**

```java
PageInfo<Goods> getPageInfo(String gname,Integer pageNum,Integer pageSize);
```

​		**service实现类**

```java
@Override
public PageInfo<Goods> getPageInfo(String gname, Integer pageNum, Integer pageSize) {
    //1.调用PageHelper的静态方法开启分页功能
    //这里充分体现了PageHelper的“非侵入式”设计:原本要做的查询不必有任何修改
    PageHelper.startPage(pageNum,pageSize);
    //2.执行查询
    List<Goods> list = iGoodsTypeDao.queryByGName(gname);
    //3.封装到PageInfo对象中
    return new PageInfo<>(list);
}
```

* **Controller层**

```java
PageInfo<Art> pageInfoByName = iArtService.getPageInfoByName(name, page, 10);
modelAndView.addObject("pageInfo",pageInfoByName.getList());
//pageInfoByName.getList() 获取list对象，也就是查询出来的List<Art>
```

