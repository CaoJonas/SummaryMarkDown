### ssm 开发 

##### 退出登陆

```java
@RequestMapping("/logout")
/**
 * 用户退出登陆
 * @param account
 * @param model
 * @return
 */
public String loginAccount(HttpSession httpSession) {
    httpSession.removeAttribute("accountInfo");
    httpSession.invalidate();
    return "redirect:/index.jsp";
}
```

##### 实现分页

```xml
<!-- https://mvnrepository.com/artifact/com.github.pagehelper/pagehelper -->
<!--mybatis 分页 依赖-->
<dependency>
  <groupId>com.github.pagehelper</groupId>
  <artifactId>pagehelper</artifactId>
  <version>5.1.2</version>
</dependency>
```

mybatis 插件

```xml
<bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"/>
    <property name="mapperLocations" value="classpath:com/bourne/springmvc/mapper/*.xml"/>
    <property name="configuration">
        <bean class="org.apache.ibatis.session.Configuration">
            <!--下划线驼峰命名-->
            <property name="mapUnderscoreToCamelCase" value="true"></property>
        </bean>
    </property>
    <!-- 配置分页插件 -->
    <property name="plugins">
        <array>
            <bean class="com.github.pagehelper.PageInterceptor">
                <property name="properties">
                    <value>
                        helperDialect=mysql
                        reasonable=true
                        supportMethodsArguments=true
                        params=count=countSql
                        autoRuntimeDialect=true
                    </value>
                </property>
            </bean>
        </array>
    </property>
</bean>
```

