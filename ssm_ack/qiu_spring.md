

## Spring

------

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

------



### 注解

------

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

