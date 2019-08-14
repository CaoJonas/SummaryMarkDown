pom 常用 依赖

```xml
<dependency>
     <groupId>junit</groupId>
     <artifactId>junit</artifactId>
     <version>4.12</version>
     <scope>test</scope>
</dependency>
<dependency>
     <groupId>org.springframework</groupId>
     <artifactId>spring-webmvc</artifactId>
     <version>5.1.5.RELEASE</version>
</dependency>
<!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
<dependency>
     <groupId>javax.servlet</groupId>
     <artifactId>javax.servlet-api</artifactId>
     <version>3.1.0</version>
     <scope>provided</scope>
</dependency>
<!-- https://mvnrepository.com/artifact/javax.servlet/jstl -->
<dependency>
     <groupId>javax.servlet</groupId>
     <artifactId>jstl</artifactId>
     <version>1.2</version>
</dependency>
```



## 配置文件

### 1-连接数据库

mysqlConnect1 xml 连接数据库

mysqlConnect2 注解的方式

```xml
<configuration>
    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
            <!--配置数据源-->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <!--url=jdbc:mysql://127.0.0.1:3306/framework?characterEncoding=utf8&useSSL=true-->
                <property name="url" value="jdbc:mysql://localhost:3306/myproject?useSSL=false"/>
               
                <property name="username" value="root"/>
                <property name="password" value="CaoBourne"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/itheima/dao/IUserDao.xml"/>
    </mappers>

</configuration>
```

### 2-整合文件

可以把 一个配置文件作为 所有配置文件的整合文件。把其他文件 导入即可， 还可以使用通配符 表示导入所有的文件

```xml
<bean class="com.bourne.springpractice1.pojo.relative.Dog" name="dog1">
    <constructor-arg name="dName" value="旺财"/>
    <constructor-arg name="dAge" value="2"/>
</bean>
bean1.xml
```

```xml
<bean class="com.bourne.springpractice1.pojo.relative.Dog" name="dog2">
    <constructor-arg name="dName" value="旺财"/>
    <constructor-arg name="dAge" value="2"/>
</bean>
bean2.xml
```

```xml
<import resource="bean*.xml"/>
<!--也可以采用一条一条的书写-->
<import resource="bean1.xml"/>
<import resource="bean2.xml"/>
这个标签可以将其他的配置文件引入， 这样的话， 我们将来整体读取这一个核心配置文件即可
整合文件.xml
```

```java
@Test
public void autoTest5() {
    ApplicationContext context = new ClassPathXmlApplicationContext("autoWeird.xml");
    Dog dog1 = context.getBean("dog1", Dog.class);
    Dog dog2 = context.getBean("dog2", Dog.class);
    System.out.println(dog1);
    System.out.println(dog2);
}
/*
	dName='旺财', dAge=2}
	dName='旺财', dAge=2}
*/
```

### 3-application中所有的命名空间

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">    
</beans>

发现  每个 命名空间中 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 都会跟一个  http://www.springframework.org/schema/context
```

### 3.5-mapper.xml 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace：命名空间，用于隔离sql-->
<mapper namespace="com.bourne.springmvc.mapper.NewsCommentMapper">

    <!--为相关新闻添加一条评论-->
    <insert id="addOneCommentByNewsId">
        insert into news_comment(newid, content, author, commentdate) values
        (#{newId}, #{newsComment.content}, #{newsComment.author}, #{newsComment.createDate})
    </insert>

    <!--更新一条评论-->
    <update id="updateCommentById">
        update news_comment set content = #{newsComment.content}
        <where>
            id=#{newsComment.id}
        </where>
    </update>
    <select id="findCommentByCommentId" resultType="com.bourne.springmvc.pojo.NewsComment">
        select id, newid as newId, content, author from news_comment
        <where>
            id = #{id}
        </where>
    </select>
</mapper>
```

### 4-context命名空间导入其他文件

```properties
url=jdbc:mysql://localhost:3306/myproject?useSSL=false
driver=com.mysql.jdbc.Driver
username=root
password=CaoBourne
// 一些变量的值
```

```xml
<context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>
<bean class="com.bourne.springpractice1.dao.Dao" id="dao1">
    <property name="driver" value="${driver}"/>
    <property name="url" value="${url}"/>
    <property name="username" value="${username}"/>
    <property name="password" value="${password}"/>
</bean>
```

解决

## MyBatis

### mybatis 参数问题

###### 1. 多个参数

```java
/**
 *
 * @param userName
 * @param sex
 * @return
 */
List<User> findNameASex(@Param("userName") String userName, @Param("sex") String sex);
```

```xml
<select id="findNameASex" resultType="user">
   select * from user where user_name = #{userName} and sex= #{sex}
    <!--使用注解的方式  就可以这些名词了-->
</select>
```

###### 2. 单个java bean 

通过 get 方法 获取bean中 属性的值

```java
List<User> findByUser(User user);
```

```xml
<select id="findByUser" resultType="user">
   select * from user where user_name = #{userName} and sex= #{sex}
   <!--这里面的 userName 和 sex 都是user中的成员变量， 他会通过 get属性 。-->
</select>
```

###### 3. map类型

```java
Map<String, Object> map1 = new HashMap<String, Object>();
map1.put("userName", "汪洋");
map1.put("sex", "男");
List<User> users = mapper.findByMap(map1);
```

```xml
<select id="findByMap" resultType="user">
   select * from user where user_name = #{userName} and sex= #{sex}
   <!--这里面的 userName 和 sex 要对应 map中 的key-->
</select>
```

###### 4. 多个java bean

```java
User user1 = new User();
user1.setUserName("汪洋");
User user2 = new User();
user2.setSex("男");
List<User> users = mapper.findByMultiUser(user1, user2);
```

```xml
<select id="findByMultiUser" resultType="user"><!--第一种-->
   select * from user where user_name = #{param1.userName} and sex= #{param2.sex}
</select>
```

```java
/**
 *
 * @param user1
 * @param user2
 * @return
 */
// 利用注解的方式 ， 这些都写在了 接口中
List<User> findByMultiUser(@Param("user1")User user1, @Param("user2") User user2); // 单个bean
```

```xml
<select id="findByMultiUser" resultType="user"> <!--第二种方式-->
   select * from user where user_name = #{user1.userName} and sex= #{user2.sex}
</select>
```

### 动态查询

###### 1- if 标签 和 where 标签

```xml
<select id="findByTwoPro" resultType="com.bourne.ssmpractice1.pojo.Address">
    select * from address
     <where>
        <!--
			where 会自动过滤掉 多余的and字符，但仅限定于前置and
		-->
         <if test="country != null">
             and country=#{country}
         </if>
         <if test="city != null">
             and city=#{city}
         </if>
     </where>
</select>

```

```java
// List<Address> addresses = mapper.findByTwoPro("China", null);
// List<Address> addresses = mapper.findByTwoPro("China", "HangZhou");
List<Address> addresses = mapper.findByTwoPro("China", "");
// 分别表示 city = null 和 city = ""
```

###### 2- set 标签

> 即使是对象， 在 test 语句中 只需要 object.attr 即可， 不需要 #{object.attr}.  基本数据类型 也是如此。 

```xml
<update id="updateById" parameterType="com.bourne.ssmpractice1.pojo.Address">
    update address
    <set>
        /*
        可以省略掉末尾的 ，
        尽管 为 Address对象， 也要在查询条件时添加 address1.country
        
        */
        <if test="address1.country != null and address1.country != ''" >
            country = #{address1.country},
        </if>
        <if test="address1.city != null and address1.city != ''" >
            city = #{address1.city}
        </if>
    </set>
    <where>
        /*动态条件*/
        addrid=#{address1.addrId}
    </where>
</update>
```

```java
int number = mapper.updateById(address1);// 更新语句
```

###### ==3-choose_when_otherwise 标签==

> 牢记于心， 一定要在 when 之后添加 一条 otherwise 语句， 这样才能保证不会删除 所有的数据

```xml
<!--choose when otherwise-->
// 有时我们不想应用到所有的条件语句，而只想从中择其一项。针对这种情况，MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句。
<select id="findByMultiPro" resultType="com.bourne.ssmpractice1.pojo.Address">
    select * from address where addrid = 2
    <choose>
        <when test="address1.country != null and address1.country != ''" >
            and country = #{address1.country}
        </when>
        <when test="address1.city != null and address1.city != ''" >
            and city = #{address1.city}
        </when>
        <otherwise>
            and state = #{address1.state}
        </otherwise>
    </choose>
</select>

DEBUG [main] - ==>  Preparing: select * from address where addrid = 2 and state = ? 
DEBUG [main] - ==> Parameters: ChaoYang(String)
DEBUG [main] - <==      Total: 1
{com.bourne.ssmpractice1.pojo.AddressaddrId=2, country='China', city='BeiJing', state='ChaoYang', street='ChangAn', zip='100020'}
```

> 也可以 <where> 直接嵌套 <choose> 等标签， 加不加 and 取决于  是否像上面实例中 where 关键字 在 外面， 所以才会用到  and 
>
> 如同下面示例中， 如果commentId 为空时， 就会 根据 newid 删除评论， 如果 commentId 不为 空 时 ，就根据 commentId 删除评论 。 最好设置  otherwise ， 如果二者都不满足， 也就是说 会删除所有的 评论， 

```xml
<delete id="delCommentByID">
    delete from news_comment
    <where>
        <choose>
            <when test="commentId != null">
               and id = #{commentId}
            </when>
            <when test="newId != null">
               and newid = #{newId}
            </when>
        </choose>
    </where>
</delete>
16:14:57,432 DEBUG http-bio-8080-exec-5 NewsCommentMapper.delCommentByID:54 - ==>  Preparing: delete from news_comment WHERE id = ? 
16:14:57,433 DEBUG http-bio-8080-exec-5 NewsCommentMapper.delCommentByID:54 - ==> Parameters: 25(Integer)
16:15:21,959 DEBUG http-bio-8080-exec-6 NewsCommentMapper.delCommentByID:54 - ==>  Preparing: delete from news_comment WHERE newid = ? 
16:15:21,960 DEBUG http-bio-8080-exec-6 NewsCommentMapper.delCommentByID:54 - ==> Parameters: 1(Integer)
```

###### 4- trim 

```xml
<!--
where 元素只会在至少有一个子元素的条件返回 SQL 子句的情况下才去插入“WHERE”子句。而且，若语句的开头为“AND”或“OR”，where 元素也会将它们去除。       
如果 where 元素没有按正常套路出牌，我们可以通过自定义 trim 元素来定制 where 元素的功能。比如，和 where 元素等价的自定义 trim 元素为：
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ...
</trim>
prefixOverrides 属性会忽略通过管道分隔的文本序列（注意此例中的空格也是必要的）。它的作用是移除所有指定在 prefixOverrides 属性中的内容，并且插入 prefix 属性中指定的内容。
--> 
<!--Trim-->
<select id="findByTrim" resultType="com.bourne.ssmpractice1.pojo.Address">
    select * from address
    <trim prefix="where" prefixOverrides="and | or">
        <if test="address1.country != null and address1.country != ''">
            and country=#{address1.country}
        </if>
        <if test="address1.city != null and address1.city != ''">
           and city=#{address1.city}
        </if>
    </trim>
</select>
```

###### 5- Foreach	

​	**foreach** 元素的功能非常强大，它允许你指定一个集合，声明可以在元素体内使用的集合项（item）和索引（index）变量。它也允许你指定开头与结尾的字符串以及在迭代结果之间放置分隔符。这个元素是很智能的，因此它不会偶然地附加多余的分隔符。      

​	**注意** 你可以将任何可迭代对象（如 List、Set 等）、Map 对象或者数组对象传递给 *foreach* 作为集合参数。当使用可迭代对象或者数组时，index 是当前迭代的次数，item 的值是本次迭代获取的元素。当使用 Map 对象（或者 Map.Entry 对象的集合）时，index 是键，item 是值。

- collection in (list, array, map, set)

  ```java
  List<Address> findBySet(Set<Integer> set); // 根据国家和城市查询
  List<Address> findByList(List<Integer> list); // 根据国家和城市查询
  List<Address> findByArray(Integer[] arrays); // 根据国家和城市查询
  List<Address> findByMap(@Param("map")Map<Integer, Integer> map); // 根据国家和城市查询 这里面一定要加 注解
  ```

- List

  ```xml
  <select id="findByList" resultType="com.bourne.ssmpractice1.pojo.Address">
      select * from address
      <where>
          addrid in
          <foreach item="item" index="index" collection="list" open="(" separator="," close=")">
              #{item}
          </foreach>
      </where>
  </select>
  <!--其中 item 可以更换成其他的 有意义的属性， 例如， attrid -->
  <!--
  DEBUG [main] - ==>  Preparing: select * from address WHERE addrid in ( ? , ? , ? ) 
  DEBUG [main] - ==> Parameters: 1(Integer), 2(Integer), 3(Integer)
  DEBUG [main] - <==      Total: 3
  {com.bourne.ssmpractice1.pojo.AddressaddrId=1, country='China', city='HangZhou', state='JiangGan', street='XueLin', zip='310000'}
  {com.bourne.ssmpractice1.pojo.AddressaddrId=2, country='China', city='BeiJing', state='ChaoYang', street='ChangAn', zip='100020'}
  {com.bourne.ssmpractice1.pojo.AddressaddrId=3, country='America', city='Texas', state='Houston', street='Calhoun', zip='77004'}
  -->
  ```

- Array

  ```xml
  <!--find foreach by array-->
  <select id="findByArray" resultType="com.bourne.ssmpractice1.pojo.Address">
      select * from address
      <where>
          addrid in
          <foreach item="addrid" index="index" collection="array" open="(" separator="," close=")">
              #{addrid}
          </foreach>
      </where>
  </select>
  <!--
  其中 item 可以更换成其他的 有意义的属性， 例如， attrid
  collection = array
  -->
  <!--
  DEBUG [main] - ==>  Preparing: select * from address WHERE addrid in ( ? , ? , ? ) 
  DEBUG [main] - ==> Parameters: 1(Integer), 2(Integer), 3(Integer)
  DEBUG [main] - <==      Total: 3
  {com.bourne.ssmpractice1.pojo.AddressaddrId=1, country='China', city='HangZhou', state='JiangGan', street='XueLin', zip='310000'}
  {com.bourne.ssmpractice1.pojo.AddressaddrId=2, country='China', city='BeiJing', state='ChaoYang', street='ChangAn', zip='100020'}
  {com.bourne.ssmpractice1.pojo.AddressaddrId=3, country='America', city='Texas', state='Houston', street='Calhoun', zip='77004'}
  -->
  
  ```

  

- Set

  ```xml
  <!--find foreach by set-->
  <select id="findBySet" resultType="com.bourne.ssmpractice1.pojo.Address">
      select * from address
      <where>
          addrid in
          <foreach item="addrid" index="index" collection="collection" open="(" separator="," close=")">
              #{addrid}
          </foreach>
      </where>
  </select>
  <!--
  其中 item 可以更换成其他的 有意义的属性， 例如， attrid
  collection = collection
  -->
  <!--
  DEBUG [main] - ==>  Preparing: select * from address WHERE addrid in ( ? , ? , ? ) 
  DEBUG [main] - ==> Parameters: 1(Integer), 2(Integer), 3(Integer)
  DEBUG [main] - <==      Total: 3
  {com.bourne.ssmpractice1.pojo.AddressaddrId=1, country='China', city='HangZhou', state='JiangGan', street='XueLin', zip='310000'}
  {com.bourne.ssmpractice1.pojo.AddressaddrId=2, country='China', city='BeiJing', state='ChaoYang', street='ChangAn', zip='100020'}
  {com.bourne.ssmpractice1.pojo.AddressaddrId=3, country='America', city='Texas', state='Houston', street='Calhoun', zip='77004'}
  -->
  ```

- Map

  [map collection 设置详情链接](https://www.cnblogs.com/anruy/p/5942044.html)

  ```java
  // map 类型的参数要有注解， 其余三种都没有必要
  List<Address> findByMap(@Param("map")Map<Integer, Integer> map); // 根据国家和城市查询
  ```

  ```xml
  <select id="findByMap" resultType="com.bourne.ssmpractice1.pojo.Address">
      select * from address
      <where>
          addrid in
          <foreach item="map.addrid" index="map.index" collection="map" open="(" separator="," close=")">
              #{map.addrid}
          </foreach>
      </where>
  </select>
  <!--
  其中 item 可以更换成其他的 有意义的属性， 例如， attrid
  collection = map
  -->
  <!--
  DEBUG [main] - ==>  Preparing: select * from address WHERE addrid in ( ? , ? , ? ) 
  DEBUG [main] - ==> Parameters: 1(Integer), 2(Integer), 3(Integer)
  DEBUG [main] - <==      Total: 3
  {com.bourne.ssmpractice1.pojo.AddressaddrId=1, country='China', city='HangZhou', state='JiangGan', street='XueLin', zip='310000'}
  {com.bourne.ssmpractice1.pojo.AddressaddrId=2, country='China', city='BeiJing', state='ChaoYang', street='ChangAn', zip='100020'}
  {com.bourne.ssmpractice1.pojo.AddressaddrId=3, country='America', city='Texas', state='Houston', street='Calhoun', zip='77004'}
  -->
  ```

###### 6-bind 标签 动态绑定

**bind**暂时发现多用于 模糊查询

```java
// 第一种方式  直接写成 模糊查询的形式
// List<Address> addresses = mapper.findByLikeState("%an%");
// 第二种方式  也可以通过 写成  state like contact('%', #{state}, '%')
// List<Address> addresses = mapper.findByLikeState("an");
// 第三种方式  动态绑定  <bind name="_state" value=" '%' + state + '%'" />
List<Address> addresses = mapper.findByLikeState("an");
```

```xml
<!-- /*state like #{state}*/-->
<select id="findByLikeState" resultType="com.bourne.ssmpractice1.pojo.Address">
    <bind name="_state" value=" '%' + state + '%'" />
    select * from address
    <where>
        state like #{_state}
    </where>
</select>
```

###### 7-定义常用属性

```xml
<sql id="baseColumn">
    country, city, state
</sql>
<!--sql 定义常用属性-->
<select id="findByBaseColumn" resultType="com.bourne.ssmpractice1.pojo.Address">
    select
    <include refid="baseColumn"/>
    from address
</select>
<!--
DEBUG [main] - ==>  Preparing: select country, city, state from address 
DEBUG [main] - ==> Parameters: 
DEBUG [main] - <==      Total: 3
{com.bourne.ssmpractice1.pojo.AddressaddrId=null, country='China', city='HangZhou', state='JiangGan', street='null', zip='null'}
{com.bourne.ssmpractice1.pojo.AddressaddrId=null, country='China', city='BeiJing', state='ChaoYang', street='null', zip='null'}
{com.bourne.ssmpractice1.pojo.AddressaddrId=null, country='America', city='Texas', state='Houston', street='null', zip='null'}
-->
```



### 缓存

<font style="color:red">***mybatis 有二次缓存， 一次缓存是默认打开的（在同一个会话中才有效），***</font>

<font style="color:red">***如果二级缓存开启， 先去二级缓存中命中，***</font>

<font style="color:red">***如果无法命中， 尝试去以及缓存中 命中，*** </font>

<font style="color:red">***否则， 去数据库查询***</font>

###### **1-select**

- 默认情况下，只启用了本地的会话缓存，它仅仅对一个会话中的数据进行缓存。映射语句文件中的所有 select 语句的结果将会被缓存。(一次会话)

  ```java
  @Test
  public void findByOrderId() {
      /*
       * id 查询
       * */
      SqlSession sqlSession = MyBatisUtil.getSession();
      OrderMapper mapper = sqlSession.getMapper(OrderMapper.class);
      List<Order> orders1 = mapper.findByOrderId(2);
      for(Order order: orders1) {
          System.out.println(order);
      }
      List<Order> orders2 = mapper.findByOrderId(2);
      for(Order order: orders2) {
          System.out.println(order);
      }
      sqlSession.commit();
      sqlSession.close();
  }
  /*
  	虽然有两次查询， 但是只查询了一次。（因为而这也时一次 会话）
  */
  /*
  DEBUG [main] - ==>  Preparing: select * from orderdetail where order_id = ? 
  DEBUG [main] - ==> Parameters: 2(Integer)
  DEBUG [main] - <==      Total: 1
  {com.bourne.ssmpractice1.pojo.OrderorderId=2, orderAddress='济南市市中区王官庄', orderName='得力中性笔', orderPrice=9.6, orderTime=Tue Mar 20 00:00:00 CST 2018}
  {com.bourne.ssmpractice1.pojo.OrderorderId=2, orderAddress='济南市市中区王官庄', orderName='得力中性笔', orderPrice=9.6, orderTime=Tue Mar 20 00:00:00 CST 2018}
  */
  ```

- 二次会话

  ```java
  @Test
  public void findByOrderId() {
      /*
       * id 查询
       * */
      SqlSession sqlSession = MyBatisUtil.getSession();
      OrderMapper mapper = sqlSession.getMapper(OrderMapper.class);
      List<Order> orders1 = mapper.findByOrderId(2);
      for(Order order: orders1) {
          System.out.println(order);
      }
  
      SqlSession sqlSession2 = MyBatisUtil.getSession();
      OrderMapper mapper2 = sqlSession2.getMapper(OrderMapper.class);
      List<Order> orders2 = mapper2.findByOrderId(2);
      for(Order order: orders2) {
          System.out.println(order);
      }
      sqlSession.commit();
      sqlSession.close();
  }
  /*
  // 两次会话， 互补干扰	缓存会被视为读/写缓存，这意味着获取到的对象并不是共享的，可以安全地被调用者修改，而不干扰其他调用者或线程所做的潜在修改
  DEBUG [main] - ==>  Preparing: select * from orderdetail where order_id = ? 
  DEBUG [main] - ==> Parameters: 2(Integer)
  DEBUG [main] - <==      Total: 1
  {com.bourne.ssmpractice1.pojo.OrderorderId=2, orderAddress='济南市市中区王官庄', orderName='得力中性笔', orderPrice=9.6, orderTime=Tue Mar 20 00:00:00 CST 2018}
  DEBUG [main] - ==>  Preparing: select * from orderdetail where order_id = ? 
  DEBUG [main] - ==> Parameters: 2(Integer)
  DEBUG [main] - <==      Total: 1
  {com.bourne.ssmpractice1.pojo.OrderorderId=2, orderAddress='济南市市中区王官庄', orderName='得力中性笔', orderPrice=9.6, orderTime=Tue Mar 20 00:00:00 CST 2018}
  */
  ```

###### 2- insert, delete and update

- 一次会话， 发生 增、 删 和 改 (映射语句文件中的所有 insert、update 和 delete 语句会刷新缓存)。

  ```java
  @Test
  public void findOneSessionInesert() {
      /*一次会话， 但又插入操作
       * id 查询
       *
       * */
      SqlSession sqlSession = MyBatisUtil.getSession();
      OrderMapper mapper = sqlSession.getMapper(OrderMapper.class);
      List<Order> orders1 = mapper.findByOrderId(2);
      for (Order orderInner : orders1) {
          System.out.println(orderInner);
      }
  
      Order order = new Order();
      order.setOrderName("华为手机");
      order.setOrderPrice(500.0);
      order.setOrderAddress("杭州市江干区下沙街道");
      order.setOrderTime(new Date());
  
      int number = mapper.insertNewOrder(order);
      System.out.println("一共插入了" + number + "条数据");
  
      List<Order> orders2 = mapper.findByOrderId(2);
      for (Order orderInner : orders2) {
          System.out.println(orderInner);
      }
     /* SqlSession sqlSession2 = MyBatisUtil.getSession();
      OrderMapper mapper2 = sqlSession2.getMapper(OrderMapper.class);
      List<Order> orders2 = mapper2.findByOrderId(2);
      for (Order order : orders2) {
          System.out.println(order);
      }*/
      sqlSession.commit();
      sqlSession.close();
  }
  /*
  // 同一次会话， 但又插入操作， 刷新缓存，
  DEBUG [main] - ==>  Preparing: select * from orderdetail where order_id = ? 
  DEBUG [main] - ==> Parameters: 2(Integer)
  DEBUG [main] - <==      Total: 1
  {com.bourne.ssmpractice1.pojo.OrderorderId=2, orderAddress='济南市市中区王官庄', orderName='得力中性笔', orderPrice=9.6, orderTime=Tue Mar 20 00:00:00 CST 2018}
  DEBUG [main] - ==>  Preparing: insert into orderdetail(order_name, order_price, order_address, order_time) values(?, ?, ? ,? ) 
  DEBUG [main] - ==> Parameters: 华为手机(String), 500.0(Double), 杭州市江干区下沙街道(String), 2019-04-11 15:10:35.194(Timestamp)
  DEBUG [main] - <==    Updates: 1
  一共插入了1条数据
  DEBUG [main] - ==>  Preparing: select * from orderdetail where order_id = ? 
  DEBUG [main] - ==> Parameters: 2(Integer)
  DEBUG [main] - <==      Total: 1
  {com.bourne.ssmpractice1.pojo.OrderorderId=2, orderAddress='济南市市中区王官庄', orderName='得力中性笔', orderPrice=9.6, orderTime=Tue Mar 20 00:00:00 CST 2018}	
  */
  ```

###### 3-清空缓存

 - clearCache()  // 强制清空缓存

###### 4-开启二级缓存

```xml
<!--
   cacheEnabled 表示 缓存， 值 = true
-->
<setting name="cacheEnabled" value="true"/>
```

mapper 映射文件中

```xml
<cache
  eviction="FIFO"
  flushInterval="60000"
  size="512"
  readOnly="true"/>
```

实例

<font style="color: red;">[mybatis 缓存原理详情](https://www.cnblogs.com/yuluoxingkong/p/8205858.html)</font>

![](C:\Users\Bourne\Desktop\Snipaste_2019-04-11_15-37-27.jpg)

```java
@Test
public void findTwoSessionCache() {
    /*两次会话 开启二级缓存
     * id 查询
     * 缓存会被视为读/写缓存，这意味着获取到的对象并不是共享的，可以安全地被调用者修改，而不干扰其他调用者或线程所做的潜在修改
     * */
    SqlSession sqlSession = MyBatisUtil.getSession();
    OrderMapper mapper = sqlSession.getMapper(OrderMapper.class);
    List<Order> orders1 = mapper.findByOrderId(2);
    for (Order order : orders1) {
        System.out.println(order);
    }
    sqlSession.commit();
    sqlSession.close();
    /*
    必须关闭， 才能共享
    */

    SqlSession sqlSession2 = MyBatisUtil.getSession();
    OrderMapper mapper2 = sqlSession2.getMapper(OrderMapper.class);
    List<Order> orders2 = mapper2.findByOrderId(2);
    for (Order order : orders2) {
        System.out.println(order);
    }

    sqlSession2.commit();
    sqlSession2.close();
}
/*
DEBUG [main] - Cache Hit Ratio [com.bourne.ssmpractice1.mapper.OrderMapper]: 0.0
DEBUG [main] - ==>  Preparing: select * from orderdetail where order_id = ? 
DEBUG [main] - ==> Parameters: 2(Integer)
DEBUG [main] - <==      Total: 1
{com.bourne.ssmpractice1.pojo.OrderorderId=2, orderAddress='济南市市中区王官庄', orderName='得力中性笔', orderPrice=9.6, orderTime=Tue Mar 20 00:00:00 CST 2018}
DEBUG [main] - Cache Hit Ratio [com.bourne.ssmpractice1.mapper.OrderMapper]: 0.5
{com.bourne.ssmpractice1.pojo.OrderorderId=2, orderAddress='济南市市中区王官庄', orderName='得力中性笔', orderPrice=9.6, orderTime=Tue Mar 20 00:00:00 CST 2018}
*/
```

### 多表查询

###### [MyBatis学习总结—实现关联表查询](https://www.cnblogs.com/xdp-gacl/p/4264440.html)

###### 1-一对一

- ==Student==

  ```java
  public class Student {
      private Integer sno;
      private String sname;
      private Integer ssex;
      private Integer sage;
      private String sdepart;
      private Score score;		//  1:1 暂时只有一个分数
  }
  ```

- ==Score==

  ```java
  public class Score {
      private Integer itemId;
      private String course;
      private Float grade;
  }
  ```

- ==Hobby==

  ```java
  public class Hobby {
      private Integer hId;
      private String hName;
  }
  ```

- 写法一

  ```xml
  <resultMap id="studentInfo" type="com.bourne.ssmpractice1.pojo.Student">
      <id property="sno" column="sno"/>
      <result property="sname" column="sno"/>
      <result property="ssex" column="ssex"/>
      <result property="sage" column="sage"/>
      <result property="sdepart" column="sdepart"/>
      <association property="score" javaType="com.bourne.ssmpractice1.pojo.Score">
          <id property="itemId" column="item_id"/>
          <result property="course" column="course"/>
          <result property="grade" column="grade"/>
      </association>
  </resultMap>
  ```

- 写法二

  ```xml
  <resultMap id="studentInfo" type="com.bourne.ssmpractice1.pojo.Student">
      <id property="sno" column="sno"/>
      <result property="sname" column="sno"/>
      <result property="ssex" column="ssex"/>
      <result property="sage" column="sage"/>
      <result property="sdepart" column="sdepart"/>
      <result property="score.itemId" column="item_id"/> /*也可以采用这种 方式， 元素的属性*/
      <result property="score.course" column="course"/>
      <result property="score.grade" column="grade"/>
  </resultMap>
  ```

###### 2-一对多

 - Student

   ```java
   public class Student {
       private Integer sno;
       private String sname;
       private Integer ssex;
       private Integer sage;
       private String sdepart;
       private Score score;
       private List<Hobby> hobbyList;
   }
   ```

- 写法一

  ==collection, 集合表示数组， 集合类型的==

  ==ofType表示集合的类型==

  ```xml
  <resultMap id="studentInfo1" type="com.bourne.ssmpractice1.pojo.Student">
      <id property="sno" column="sno"/>
      <result property="sname" column="sno"/>
      <result property="ssex" column="ssex"/>
      <result property="sage" column="sage"/>
      <result property="sdepart" column="sdepart"/>
      <association property="score" javaType="com.bourne.ssmpractice1.pojo.Score">
          <id property="itemId" column="item_id"/>
          <result property="course" column="course"/>
          <result property="grade" column="grade"/>
      </association>
      <collection property="hobbyList" ofType="com.bourne.ssmpractice1.pojo.Hobby">
          <id property="hId" column="h_id"/>
          <result property="hName" column="h_name"/>
      </collection>
  </resultMap>
  ```





## Spring

---

### 值的注入

#### setter 注入（）

###### 注意事项

```javascript
/*
必须有字段对应相应的 get 和 set方法，并且 xml 中property 的name 必须 和 类中的 属性的get和 
set方法名称相同
*/
private String name;
public String getName1() {
    return name;
}
public void setName1(String name) {
    this.name = name;
}
<bean class="com.bourne.springpractice1.pojo.impl.UserCache" abstract="true" id="userCache">
     <property name="name1" value="CaoBourne"/>
</bean>
```

###### 多个xml文件同时注入

==new String[]{"bean1.xml", "bean2.xml"}==

```java
ApplicationContext context = new ClassPathXmlApplicationContext(new String[]{"bean1.xml", "bean2.xml"});
UserNormal userNormal = context.getBean("userNormal", UserNormal.class);
System.out.println(userNormal);

Dog dog = context.getBean("dog1", Dog.class);
System.out.println(dog);
```

###### 基础类型注入

```xml
<bean class="com.bourne.springpractice1.pojo.relative.Dog" id="dog">
    <property name="dName" value="豆豆"/>
    <property name="dAge" value="1"/>
</bean>
```

###### 引用类型注入

```xml
<bean class="com.bourne.springpractice1.pojo.userbag.UserNormal" id="userNormal">
    <property name="name" value="CaoBourne"/>
    <property name="age" value="25"/>
    <property name="dog" ref="mydog"/>
</bean>
```

###### 别名

```xml
<alias name="dog" alias="mydog"/>	
<!--别名-->
<bean class="com.bourne.springpractice1.pojo.relative.Dog" id="dog">
    <property name="dName" value="豆豆"/>
    <property name="dAge" value="1"/>
</bean>
<bean class="com.bourne.springpractice1.pojo.userbag.UserNormal" id="userNormal">
    <property name="name" value="CaoBourne"/>
    <property name="age" value="25"/>
    <property name="dog" ref="mydog"/>
</bean>
```

#### 构造方法注入

- 最好使用  <constructor-arg name="dName" value="旺财" /> 不要使用 index 这种 
- 必须要有相对应的有参构造方法- 

```xml
<bean class="com.bourne.springpractice1.pojo.relative.Dog" id="dog2">
    <constructor-arg name="dName" value="旺财"/>
    <constructor-arg name="dAge" value="1"/>
</bean>
```

#### 自动注入autowire

***优先按照类型 去匹配， 如果匹配到一个那么直接注入， 不止一个按照名字注入， 如果一个都找不到， 那就出问题了***

###### 1- byType

==byType 只是表明根据属性的类型在当前xml文件中查找 相应的 bean, 但是同一个xml文件中不能有多个同一类型的java bean==

```xml
<bean class="com.bourne.springpractice1.pojo.userbag.Person" id="person" autowire="byType">
    <constructor-arg name="name" value="CaoBourne"/>
    <constructor-arg name="age" value="25"/>
</bean>
<bean class="com.bourne.springpractice1.pojo.relative.Dog">
    <constructor-arg name="dName" value="旺财"/>
    <constructor-arg name="dAge" value="2"/>
</bean>
```

###### 2-byName

==无论java bean 中有 id属性还是name属性， 都会被引用，但是要注意的是， 这里的java bean 的名称 和 id 必须要同 Person中的dog属性具有相同的名称==

<font style="color: red;">private Dog dog 属性名称为 dog </font>

<font style="color: red;">那么被引用的java **bean** 也必有有一个和这个属性相同的名称</font>

```java
private String name;
private int age;
private Dog dog;
public Dog getDog() {
    return dog;
}
public void setDog(Dog dog) {
    this.dog = dog;
}
```

```xml
<bean class="com.bourne.springpractice1.pojo.userbag.Person" id="person" autowire="byName">
    <constructor-arg name="name" value="CaoBourne"/>
    <constructor-arg name="age" value="25"/>
</bean>
<bean class="com.bourne.springpractice1.pojo.relative.Dog" id="dog">
    <constructor-arg name="dName" value="旺财1"/>
    <constructor-arg name="dAge" value="1"/>
</bean>
<bean class="com.bourne.springpractice1.pojo.relative.Dog" id="dog2">
    <constructor-arg name="dName" value="旺财2"/>
    <constructor-arg name="dAge" value="2"/>
</bean>
```

```java
ApplicationContext context = new ClassPathXmlApplicationContext("autoWeird.xml");
Person person = context.getBean("person", Person.class);
System.out.println(person);
// 所以结果为
// Person{name='CaoBourne', age=25, dog=	dName='旺财1', dAge=1}} 只是引用了 id 或 name 为 dog的bean
```

###### 3-constructor

==根据构造器中 属性的名称，例如下列中构造器的参数为 prettyDog , xml文件中的person**bean** 自动获取name = prettyDog的dog bean==

<font style="color: red;">**记住一定是 构造器中的参数名称**</font>

```xml
<bean class="com.bourne.springpractice1.pojo.userbag.Person" id="person" autowire="constructor">
    <constructor-arg name="name" value="CaoBourne"/>
    <constructor-arg name="age" value="25"/>
</bean>
<bean class="com.bourne.springpractice1.pojo.relative.Dog" name="prettyDog">
    <constructor-arg name="dName" value="旺财1"/>
    <constructor-arg name="dAge" value="1"/>
</bean>
<bean class="com.bourne.springpractice1.pojo.relative.Dog" name="dog2">
    <constructor-arg name="dName" value="旺财2"/>
    <constructor-arg name="dAge" value="2"/>
</bean>
```

```java
public Person(String name, int age, Dog prettyDog) {
    this.name = name;
    this.age = age;
    this.dog = prettyDog;
}
// Person{name='CaoBourne', age=25, dog=	dName='旺财1', dAge=1}} // 输出的结果为 prettyDog bean
```

###### 4-default（没太大用）













#### xml配置项中常用的 属性

###### abstract 和 parent

可以把xml文件中的类 注册为 抽象类， 也就是不能创建实例，  可以有子类继承

```xml
<bean class="com.bourne.springpractice1.pojo.impl.UserCache" abstract="true" id="userCache">
    <property name="name1" value="CaoBourne"/>
</bean>
<bean class="com.bourne.springpractice1.pojo.impl.UserNormal" parent="userCache" id="userNormal">
    <property name="sex" value="25"/>
</bean>
```

###### id 和 name(都是单例)	

- 都是别名， 都可以获取实例

  ```java
  ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
  UserCache userCache = context.getBean("userNormal", UserCache.class);
  UserCache userCache1 = context.getBean("userNormal", UserCache.class);
  System.out.println(userCache.toString());
  System.out.println(userCache1.toString());
  /*
  	创建了新的 UserCache 对象 // 初始化 方法只调用了一次
  	UserCache{name='CaoBourne'}sex=25}
  	UserCache{name='CaoBourne'}sex=25}
  */
  ```

- 同一个实例可以有 多个name  name = "name1, name2, name3", 但同一个实例只有 一个id

###### destory-method

制定bean最后销毁时执行的方法， 适合于清理型工作， 触发条件必须时 被销毁时才会发生	

​	1 容器 close 会触发

​	2 refresh

​	3 destory (已过时)

init-method

初始化 时一定要执行的 方法 （在创建该对象时 就会执行该方法， 自动触发）

###### Scope 

singleton 单例， spring 上下文当中， 只有一个实例==默认为singleton==

prototype 原型， 每获取一次就会创建一个新实例

###### lazy-init

表示延迟加载， 只有被获取时， 才会创建实例， 默认情况下， 无论是否被获取， 都会创建实例

###### depend-on 

依赖的bean m 如果某一个bean 的使用严重依赖于另外一个bean

---



### 注解

---

#### 常用注解

***// 每个组件语义不同***

- Component（一般组件）	
- Service（业务层）
- Controller（SpringMVC)
- Reposity(dao层)

#### xml  注解扫描

@Component

```java
@Component()
public class Cat {
    public void test() {
        System.out.println("怎么这里面的可以啊");
    }
}
```

扫描java 包

```xml
<context:component-scan base-package="com.bourne.springpractice1.pojo.relative"/>
<!--扫描该包， 激活注解-->
```

```xml
// 排除具体包下的某种组件 
<context:component-scan base-package="com.bourne.springpractice1.pojo">
    <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Component"/>
</context:component-scan>
```

- 

  

  
  
  
  
  
  
  
  
  

  

  
  
  
  
  
  
  
  
  
  
  
  

  

  
  
  
  
  
  

  

  

  

  

  

  

  

  

  

  


​	