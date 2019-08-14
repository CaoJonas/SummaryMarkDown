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

###### 3-choose_when_otherwise 标签

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

> - prefix 前缀
> - suffix 后缀
> - prefixOverrides 表示移除所有指定在 prefixOverrides 属性中的内容，并且 插入 prefix 中指定的内容
> - suffixOverrides 表示移除所有指定在 suffixOverrides 属性中的内容，并且 插入 suffix 中指定的内容
> - 一般 prefixOverrides（针对第一个元素） 和 suffixOverrides（针对最有一个元素）
> - 下面的插入语句会被编译成
>   insert into edoc_entry ( category_id ,title ,summary ,upload_user ,create_date ) values( ? ,? ,? ,? ,? ) 
> - 但是同时不要忘了 添加 ==逗号== 或其他 将要被替代的单词

```xml
<insert id="addNewEntry">
    insert into edoc_entry
    <trim prefix="(" suffix=")" prefixOverrides=",">
        <if test="newEntry.categoryId != null">
            ,category_id
        </if>
        <if test="newEntry.title != null and newEntry.title != ''">
             ,title
        </if>
        <if test="newEntry.summary != null and newEntry.summary != ''">
             ,summary
        </if>
        <if test="newEntry.uploadUser != null and newEntry.uploadUser != ''">
             ,upload_user
        </if>
        <if test="newEntry.createDate != null">
            ,create_date
        </if>
    </trim>
    <trim prefix="values(" suffix=")" prefixOverrides="," >
        <if test="newEntry.categoryId != null">
            ,#{newEntry.categoryId}
        </if>
        <if test="newEntry.title != null and newEntry.title != ''">
            ,#{newEntry.title}
        </if>
        <if test="newEntry.summary != null and newEntry.summary != ''">
            ,#{newEntry.summary}
        </if>
        <if test="newEntry.uploadUser != null and newEntry.uploadUser != ''">
            ,#{newEntry.uploadUser}
        </if>
        <if test="newEntry.createDate != null">
            ,#{newEntry.createDate}
        </if>
    </trim>
</insert>
```

###### 5- Foreach

###### 	

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

----


## mybatis 常见错误

- **Type interface com.bourne.ssmpractice1.mapper.UserMapper is already known to the MapperRegistry.**

  原因是 在 重复引 用mapper 文件

- **Warning:java: 源值1.5已过时, 将在未来所有发行版中删除， Warning:java: 要隐藏有关已过时选项的警告, 请使用 -Xlint:-options。**

  ```xml
  <properties>
      <maven.compiler.source>1.8</maven.compiler.source>
      <maven.compiler.target>1.8</maven.compiler.target>
  </properties># 
  ```

- **org.springframework.beans.factory.xml.XmlBeanDefinitionStoreException: Line 10 in XML document from class path resource [hellocontroller.xml] is invalid; nested exception is org.xml.sax.SAXParseException; lineNumber: 10; columnNumber: 180; cvc-elt.1: 找不到元素 'beans' 的声明。**

  ```java
  没有找到该  bean， 有可能该 bean 已存在 ，但是 该 bean中的成员变量和接收的参数列表 名字不相同， 
  ```

- **Type interface XXXMapper is not known to the MapperRegistry**

  用Mybatis的时候爆出的一个错误，主要原因是Mappper.xml里面的namespace 和对应的接口地址不一样， 有可能 在 mybatis.cfg.xml 中 没有 引入对应的xml 文件

  ```xml
  <mapper resource="com/bourne/ssmpractice1/mapper/StudentMapper.xml" />
  ```

- **SQL: insert into edoc_entry ( category_id ,title,summary ,upload_user  ,create_date )  values( ?                                          ?                                           ?                                           ?                                           ? )**

  正常的 insert 语句： insert into tableName(col1, col2, col3, col4, col5) values(param1, param2, param3, param4, param5). 要千万记住是==有逗号的==

- **There is no getter for property named 'entryId' in 'class java.lang.Integer'] with root cause**
  **org.apache.ibatis.reflection.ReflectionException: There is no getter for property named 'entryId' in 'class java.lang.Integer'**

  如果出现 no getter 方法时， 多半是因为 没有设置 @Param("entryId") Integer enterId

  

- **Caused by: org.apache.ibatis.binding.BindingException: Parameter 'userName' not found. Available parameters are [arg1, arg0, param1, param2]**

  参数不匹配， 

  1. 可以用 arg0 arg1

  2.  param1 param2

  3. 采用注解的方式

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

- **No matching constructor found in class 'UserNormal**

  - 在添加新属性时一定要记得重新修改 构造函数

- **Caused by: com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: FUNCTION myproject.conact does not exist**

  -  写错了函数名称， 应该为 concat

  

- **Caused by: com.mysql.jdbc.exceptions.jdbc4.MySQLIntegrityConstraintViolationException: Column 'sno' in where clause is ambiguous**

  就是因为 score 和 Student中都包含 sno属性， 所以 需要添加 ==tableName.attribute==

  ```xml
  <select id="findByIdDoubleTab" resultMap="studentInfo">
      select student.sno, sname, sage, ssex, sdepart, item_id, course, grade from student, score
      <where>
          student.sno = score.sno and 
          student.sno = #{sno}
      </where>
  </select>
  ```

- Caused by: org.xml.sax.SAXParseException; lineNumber: 6; columnNumber: 77; cvc-complex-type.2.4.c: 通配符的匹配很全面, 但无法找到元素 'context:component-scan' 的声明。

  <font style="color: red;">**Error**</font>

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
      <context:component-scan base-package="com.bourne.springpractice1.pojo"/>
  </beans>
  ```

  <font style="color: red;">**Right**</font>

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          http://www.springframework.org/schema/context/spring-context.xsd">
  <context:component-scan base-package="com.bourne.springpractice1.pojo.relative" />
  </beans>
  ```

  

  
  
  
  
  
  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

​	