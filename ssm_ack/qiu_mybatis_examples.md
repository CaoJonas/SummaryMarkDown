### mybatis 实例

### when

##### where 和  choose  when 的结合

> 该查询语句的含义为 查询该账号的 消费记录， 会根据提供的时间查询该时间段的消费记录。
>
> transactionType 表示 交易类型， 如果  transactionType  为全部， 则表示查询所有的记录， 否则，根据交易类型返回所有的交易记录， 也就是用到choose 的原因， 
>
>  when test="transactionType != null and transactionType != '全部' " 表示如果不为 全部， 则会进行比较， 否则什么也不做。

```xml
<!--根据卡号查询交易记录-->
<select id="selectRecordsMapper" resultType="com.bourne.springmvc.pojo.Record">
    select card_num, related_card, transaction_amount, transaction_type, balance, transaction_date, remark from
    transactionrecord
    <where>
        <if test="cardNum != null and cardNum != ''">
            and card_num = #{cardNum}
        </if>
        <if test="startTime != null">
            and transaction_date &gt;= #{startTime} and transaction_date &lt;= #{endTime}
        </if>
        <choose>
            <when test="transactionType != null and transactionType != '全部' ">
                and transaction_type = #{transactionType}
            </when>
        </choose>
    </where>
</select>
```



### trim

>插入语句

```xml
<insert id="establishRecord">
     insert into transactionrecord
     <trim prefix="(" suffix=")" prefixOverrides=",">
          <if test="record.cardNum != null and record.cardNum != ''">
               ,card_num
          </if>
          <if test="record.relatedCard != null and record.relatedCard != ''">
               , related_card
          </if>
          <if test="record.transactionAmount != null">
               , transaction_amount
          </if>
          <if test="record.transactionType != null">
               , transaction_type
          </if>
          <if test="record.transactionDate != null">
               , transaction_date
          </if>
          <if test="record.balance != null">
               , balance
          </if>
     </trim>
     <trim prefix="value(" suffix=")" prefixOverrides=",">
          <if test="record.cardNum != null and record.cardNum != ''">
               , #{record.cardNum}
          </if>
          <if test="record.relatedCard != null and record.relatedCard != ''">
               ,#{record.relatedCard}
          </if>
          <if test="record.transactionAmount != null">
               , #{record.transactionAmount}
          </if>
          <if test="record.transactionType != null">
               ,#{record.transactionType}
          </if>
          <if test="record.transactionDate != null">
               , #{record.transactionDate}
          </if>
          <if test="record.balance != null">
               , #{record.balance}
          </if>
     </trim>
</insert>
```

### 时间格式

```java
@DateTimeFormat(pattern = "yyyy-MM-dd")
private Date startDate; // 开始日期

@DateTimeFormat(pattern = "yyyy-MM-dd")
private Date endDate;// 截至日期
```

时间格式 就可以 用 between a and b

```xml
<where>
    <if test="cardNum != null and cardNum != ''">
        and card_num = #{cardNum}
    </if>
    <if test="recordDataEntity.startDate != null">
        and transaction_date between #{recordDataEntity.startDate} and #{recordDataEntity.endDate}
    </if>
    <choose>
        <when test="recordDataEntity.transactionType != null and recordDataEntity.transactionType != '全部' ">
            and transaction_type = #{recordDataEntity.transactionType}
        </when>
    </choose>
</where>
```

### mysql 查询区分大小写

> **第一种方法：**
> 要让mysql查询区分大小写，可以：
>
> ```mysql
> `select` `* ``from` `some_table ``where` `binary` `str=``'abc'``select` `* ``from` `some_table ``where` `binary` `str=``'ABC'`
> ```
>
> **第二方法：**
>
> 在建表时时候加以标识
>
> ```mysql
> `create table some_table(``   ``str ``char``(20) binary ``)`
> ```

### Mybatis中多表联查，查询出来的字段出现重名，造成数据异常的解决方法！

```xml
comment 类中包含 issue字段，表示是对哪一个问题的评论，但是关系到多表查询， 可能有多个字段名称重复， 这时候可能就需要另起别名，避免错误，因为 resultMap 中有 column 和 property, 只要别名 和 column 相同即可
<resultMap id="commentUserMap" type="com.bourne.springmvc.pojo.Comment">
    <id column="comment_id" property="commentId"/>
    <result column="content" property="content"/>
    <result column="comment_time" property="commentTime"/>
    <association property="user" javaType="com.bourne.springmvc.pojo.User">
        <result column="user_name" property="userName"/>
    </association>
    <association property="issue" javaType="com.bourne.springmvc.pojo.Issue">
        <result column="issueContent" property="content"/>
    </association>
</resultMap>

<select id="commentsOfUserMapper" resultMap="commentUserMap">
    select comment_id, comment.content, user1.user_name, issues.content as issueContent, comment.comment_time from user as user1, comment, issues
    where comment.issue_id = issues.issue_id
    and issues.user_id = user1.user_id
    and comment.user_id=(select user2.user_id from user as user2 where user2.user_name = #{user.userName})
</select>
```

```xml
如果数据库名称为 user_name ，在实体类中名字为 userName, 即可相互转换
<property name="configuration">
    <bean class="org.apache.ibatis.session.Configuration">
        <!--下划线驼峰命名-->
        <property name="mapUnderscoreToCamelCase" value="true"></property>
    </bean>
</property>
```

所以单表查询时，可以直接利用别名完成转换， 即 select comment_num as commentNum from user;

```xml
<select id="showUserIssueMapper" resultType="com.bourne.springmvc.pojo.Issue">
     select issue_id, title, content, comment_num as commentNum, create_time from issues
        where issue_id = #{issueId}
</select>
```





------

### mybatis 常见错误

- Mybatis中多表联查，查询出来的字段出现重名，造成数据异常的解决方法！

  ****[Mybatis中多表联查，查询出来的字段出现重名，造成数据异常的解决方法！](https://blog.csdn.net/qq_36791569/article/details/81513091)

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

- **Caused by: org.apache.ibatis.binding.BindingException: Parameter 'userName' not found. Available parameters are [arg1, arg0, param1, param2]**

  参数不匹配， 

  1. 可以用 arg0 arg1

  2. param1 param2

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

  - 写错了函数名称， 应该为 concat

  

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

  