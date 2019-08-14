# 常用注解

applicationContext.xml 书写

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
                           http://www.springframework.org/schema/context
                           http://www.springframework.org/schema/context/spring-context-3.2.xsd
                           http://www.springframework.org/schema/aop
                           http://www.springframework.org/schema/aop/spring-aop-3.2.xsd
                           ">
</beans>
```

### 获取配置文件

```java
@Test
public void test3() {
     ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
     Person person1 = (Person) context.getBean("person1");
     System.out.println(person1);
}
```

### 获取配置类

```java
@Test
public void test1() {
     AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(MyConfig1.class);
     Car car = context.getBean(Car.class);
     System.out.println(car);
}
```

### @Configuration

>   ApplicationContext context = new AnnotationConfigApplicationContext(MainConfig.class);
>
> // 配置类 MainConfig.class

```java
@Configuration
public class MainConfig {
     @Bean(name = "person")
     public Person getPerson() {	// 方法名作为 默认 名称， 
          return new Person("CaoBourne", 20);
     }
}
```

```java
public class App 
{
     public static void main( String[] args )
     {
          ApplicationContext context = new AnnotationConfigApplicationContext(MainConfig.class);
          // Person person = (Person) context.getBean(Person.class); // 根据类名
          Person person = (Person) context.getBean("person"); // 根据 name
          // Person person = (Person) context.getBean("person"); // 根据 id , id 就是 方法名称
          System.out.println(person);
     }
}
```

### @ComponentScan

> 包扫描， 只要是标注了 @Repository, @Component, @Controller, @Service， 都会被扫描到。
>
> == 记住， 一定要放在 主配置类中， 而不是其他类。
>
> ==这里也可以没有 ComponentScan(value = "com.bourne")==

```java
@Configuration
@ComponentScan(value = "com.bourne")// 这里也可以没有 ComponentScan(value = "com.bourne")
public class MainConfig {
     @Bean("person")
     public Person getPerson() {
          return new Person("CaoBourne", 20);
     }
}
```

==context.getBeanDefinitionNames() 获取注解类==

```java
ApplicationContext context = new AnnotationConfigApplicationContext(MainConfig.class);
        String[] beanNamesForType = context.getBeanDefinitionNames();	// 
        for(String name: beanNamesForType) {
            System.out.printf("name = %s\n", name);
        }
        System.out.println();
```

##### @excludeFilters

> 过滤掉一些注解 FilterType.ANNOTATION -- FilterType类型
>
> classes = {Controller.class, Service.class}  // 表示要过滤掉的类型

```java
@ComponentScan(value = "com.bourne", excludeFilters = {
        @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = {Controller.class, Service.class})
})
```

##### @includeFilters

> 包含进来一些 组件， 和过滤用法相似， 但是不同的必须添加  useDefaultFilters = false 属性，表示禁用掉默认的fileters规则，

```java
@ComponentScan(value = "com.bourne", includeFilters = {
     @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = {Controller.class, Service.class})
}, useDefaultFilters = false)
```

```java
boolean useDefaultFilters() default true;
```

### @ComponentScans

> 表示要配置多个组建扫描配置。

```java
@ComponentScans(value = {
     @ComponentScan(value = "com.bourne", includeFilters = {
          @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = {Controller.class, Service.class})
     }, useDefaultFilters = false)
})
```

### @FilterType

- ANNOTATION 注解形式

  ```java
  @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = {Controller.class}),
  ```

- ASSIGNABLE_TYPE   // 给定类的类型

  ```java
  @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE, classes = {PersonServiceImp.class})
  ```

- CUSTOM//自定义类型

### @Scope

> 对象在spring容器（IOC容器）中的生命周期，也可以理解为对象在spring容器中的创建方式。

> - singleton:一个Spring容器中只有一个Bean的实例，此为Spring的默认配置，整个IOC容器共享一个实例, IOC容器启动， 就会创建该对象实例。
> - prototype:每次调用新建一个Bean的实例，只有在使用对象时才会创建对象实例。

```java
Person person1 = (Person) context.getBean("person");
Person person2 = (Person) context.getBean("person");
System.out.println(person1 == person2);
//  Scope = singleton  -- 结果为 true
//  Scope = prototype  -- 结果为 false
```

### @Lazy 

懒加载， 只针对 单实例 有用， 也就是默认状态的singleton， 即使有 @Scope("prototype") 也只会是单实例， 懒加载。就是在只有在调用时，才会创建对象。

```java
Person person1 = (Person) context.getBean("person");
Person person2 = (Person) context.getBean("person");
System.out.println(person1 == person2);	== true
```

### @Conditional （可以为 bean 添加创建条件）

> 表示只有满足 WindowCondition.class中的match 方法时， 才会创建 name = bills 的 Person实例。

```java
@Bean("bills")
@Conditional(value = {WindowCondition.class})
public Person getBills() {
     System.out.println("创建了新的实例");
     return new Person("Bills", 60);
}
```

> 如果要设置条件，首先要创建相应实现 Condition 接口的类， 并覆盖match 方法。
>
> 下面的例子表示 如果操作系统为 window ， 则会返回ture, 而上面的@Conditional(value = {WindowCondition.class})， 表示如果满足上面的条件， 则会创建 bills 实例。

```java
public class WindowCondition implements Condition {

     @Override
     public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
          Environment environment = conditionContext.getEnvironment();
          String property = environment.getProperty("os.name");
          if(property.contains("Window")) {
               return true;
          }
          return false;
     }
}
```

### @Import(给容器中注册组件)

- 包扫描 + 组件标注注解 （@Controller/@Service/@Repository/@Component）这一般是针对我们自己所写的类，可以自行添加一些注解。（不适用于第三方包）

- 对于第三方包，或者是没有添加上面注解的类， 都可以利用 @Bean 注解在配置类中创建该类的实例。

- 如果要注册的实例比较简单， 也可以通过 @Import 导入组件。容器中就会自动注册这个组件。id 默认是全类名。

  

```java
@Configuration
@Import(value = {Student.class})	//  只需要写在 配置类上面， 记住是一个数组。
```

### @ImportSelector（接口）

>  可以自行定义 导入的组件，首先要实现 ImportSelector 接口

```java
@Configuration
@Import(value = {Student.class, MyImportSelector.class})
```

>  返回全类名组成的数组

```java
public class MyImportSelector implements ImportSelector {
     @Override
     public String[] selectImports(AnnotationMetadata annotationMetadata) {
          return new String[]{"com.bourne.pojo.Police", "com.bourne.pojo.Student", "com.bourne.pojo.Teacher"};
     }
}
/*
beanName = com.bourne.pojo.Student
beanName = com.bourne.pojo.Police
beanName = com.bourne.pojo.Teacher
*/
```

### @FactoryBean



### bean实例的生命周期

**bean 的生命周期分三步， 1、 bean 创建  2、bean 初始化  3、bean 销毁**

- **bean 创建：**
  - 单实例， 容器启动时创建对象。
  - 多实例， 每次获取的时候创建对象。	

- **bean 初始化**
  - 对象创建完成， 并完成实例的一些属性的赋值操作。

- **bean 销毁**
  -  ==单实例： 容器关闭时销毁所有bean 实例。==
  - 多实例： 容器关闭时并不会销毁bean实例。并且不会调用销毁方法。

#### 通过@bean 注解添加初始化和销毁方法**

```java
@Bean(initMethod = "initMethod", destroyMethod = "destroyMethod")
public Police getPolice() {
     return new Police();
}
```

>  记住， init_method 和 destroyMethod  都是无参方法。

```java
public void initMethod() {
     System.out.println("Police init_method....");
}

public void destroyMethod() {
     System.out.println("Police destroy_method....");
}
```

- 如果bean 改成多实例， 只有在获取实例的时候才会创建， 所以初始化也要在创建之后。

```java
@Scope("prototype")
@Bean(initMethod = "initMethod", destroyMethod = "destroyMethod")
public Police getPolice() {
     return new Police();
}
```

- 多实例： 容器关闭时并不会销毁bean实例。并且不会调用销毁方法。==

```java
@Scope("prototype")
@Bean(initMethod = "initMethod", destroyMethod = "destroyMethod")
public Police getPolice() {
     return new Police();
}
```

```java
@Test
public void test01() {
     AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(MainIOCLifeCycle.class);
     Police police = context.getBean(Police.class);
     context.close();
}
```

```java
Police constructor...
Police init_method....    
```

#### 通过实现InitializingBean, DisposableBean 实现 初始化和销毁方法

》 单实例 可以在容器关闭时就可以调用销毁方法。

》 多实例在容器关闭时并不会调用销毁方法。

```java
public class Student implements InitializingBean, DisposableBean {

     public Student() {
          System.out.println("Student ....");
     }

     @Override
     public void destroy() throws Exception {
          // 销毁方法
          System.out.println("Student destroy.. ");
     }

     @Override
     public void afterPropertiesSet() throws Exception {
          // 初始化方法
          System.out.println("Student afterPropertiesSet.. ");
     }
}
```

#### 通过实现@PostConstruct, @PreDestroy实现 初始化和销毁方法

》PostConstruct 在bean 创建完成，并且赋值成功， 才会被初始化

》PreDestroy 在容器销毁bean 之前

```java
public class Passager {
     public Passager() {
          System.out.println("Passager Constructor");
     }
     @PostConstruct
     public void init() {
          System.out.println("Passager ... init");
     }
     @PreDestroy
     public void destory() {
          System.out.println("Passager ... destory");
     }
}
@Scope("prototype")
@Bean
public Passager getPassager() {
     return new Passager();
}
// 如果是单实例 
Passager Constructor
Passager ... init
容器初始化完成
Passager ... destory
// 如果是多实例， 并不会调用 销毁方法。
```

#### BeanPostProcessor（接口）

> 就是可以定义在 bean 初始化之前和之后，
>
> bean 表示当前bean 实例。
>
> beanName 表示当前bean 实例的名称。
>
> 》 并且该接口是对所有的实例初始化进行操作， 包括配置类。

```java
@Component
public class Processor implements BeanPostProcessor {
     @Override
     public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
          System.out.println("初始化 + postProcessBeforeInitialization =>" + beanName + "=>" + beanName);
          return bean;
     }

     @Override
     public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
          System.out.println("初始化 + postProcessAfterInitialization +>" + beanName + "+>" + beanName);
          return bean;
     }
}
```

```java
@Configuration	
/*
表示 Passager 实例
另外还有 MainIOCLifeCycle 配置类
*/
@ComponentScan("com.bourne.processor")
public class MainIOCLifeCycle {
     @Scope("prototype")
     @Bean
     public Passager getPassager() {
          return new Passager();
     }
}
```

```java
@Test	

public void test02() {
     AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(MainIOCLifeCycle.class);
     System.out.println("容器初始化完成");
     Passager passager = context.getBean(Passager.class);
     context.close();
}
```

```java
初始化 + postProcessBeforeInitialization =>mainIOCLifeCycle=>mainIOCLifeCycle	// 
初始化 + postProcessAfterInitialization +>mainIOCLifeCycle+>mainIOCLifeCycle
容器初始化完成
Passager Constructor
初始化 + postProcessBeforeInitialization =>getPassager=>getPassager		// passager 实例。
Passager ... init
初始化 + postProcessAfterInitialization +>getPassager+>getPassager
```

### 属性赋值 

### @Value

> 可以在一些类的属性上添加默认值， 可以通过三种方式，
>
> - key -value
> - sPEL 表达式
> - 外部文件 

#### key -value

```java
@Value(value = "CaoBourne")
private String name;
```

#### 外部文件@PropertySource

> ==@PropertySource(value = {"classpath:person.properties"})==

```properties
person.name=CaoBourne
person.age=26
```

**配置类书写**

==@PropertySource(value = {"classpath:person.properties"})==

```java
@Configuration
@PropertySource(value = {"classpath:person.properties"})
public class MyConfig1 {
     @Bean
     public Person getPerson() {
          return new Person();
     }
}
```

```java
@Value(value = "${person.age}")
private int age;
```

### 自动装配 @AutoWeird

> 由于PersonServiceImpl 已添加 @Service 注解， 这样利用报扫描， 就可以获取bean 实例， 而bean 实例的id 就是 bean 的名称。 如果再次利用配置类添加 实例bean （同样默认为方法名称）, 

````java
@Repository
public class PersonDao {

     String label = "1";

     public String getLabel() {
          return label;
     }

     public void setLabel(String label) {
          this.label = label;
     }

     @Override
     public String toString() {
          return "PersonDao{" +
               "label='" + label + '\'' +
               '}';
     }
}
````

```java
@Configuration
@ComponentScan(value = {"com.bourne.service", "com.bourne.dao"})
public class AutoWeirdConfig {
     @Bean("personDao2")
     public PersonDao personDao() {
          return new PersonDao();
     }
}
```

> @Autowired
> private PersonDao personDao2;   // 这里自动装配的bean 实例一定要以 已经存在的bean 实例为根据。	

```java
@Service("personServiceImpl")
public class PersonServiceImpl {

    @Autowired
    private PersonDao personDao2;	 // 例如上面已经存在 id = personDao2 和 personDao

    @Override
    public String toString() {
        return "PersonServiceImpl{" +
                "personDao=" + personDao2 +
                '}';
    }
}
```

测试

```java
@Test
public void test01() {
     AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(AutoWeirdConfig.class);
     PersonServiceImpl personService = (PersonServiceImpl) context.getBean("personServiceImpl");
     System.out.println(personService);
     PersonDao personDao = (PersonDao)context.getBean("personDao");
     System.out.println(personDao);
}
/*
PersonServiceImpl{personDao=com.bourne.dao.PersonDao@14d3bc22}
com.bourne.dao.PersonDao@14d3bc22
*/
```

==但又一点需要区分的是，如果在测试类中 直接获取这些实例，需要利用配置文件,因为这样 实例 == null==

```java
@Autowired
private PersonDao personDao;
```

### @RunWith 和 @ContextConfiguration

==因为用到SpringJUnit4ClassRunner， 所以要求 junit 4.12以上==

> @RunWith就是一个运行器
>
> @RunWith(JUnit4.class)就是指用JUnit4来运行
>
> @RunWith(SpringJUnit4ClassRunner.class),让测试运行于Spring测试环境
>
> @RunWith(Suite.class)的话就是一套测试集合，
>
>
> @ContextConfiguration Spring整合JUnit4测试时，使用注解引入多个配置文件
>
>
> 单个文件 
>
> @ContextConfiguration(Locations="classpath：applicationContext.xml")  
> @ContextConfiguration(classes = SimpleConfiguration.class)
>
>
> 多个文件时，可用{}
>
> @ContextConfiguration(locations = { "classpath:spring1.xml", "classpath:spring2.xml" }) 

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = AutoWeirdConfig.class)
public class AutoWeirdTest {

     @Autowired
     private PersonDao personDao1;	// 这儿需要的是 上述配置类中 id = personDao1 的bean 实例

     @Test
     public void test01() {
          System.out.println(personDao1); // 2
     }
}
```

### @Qualifier

> 使用 Qualifier 的 装配该 id 的bean 实例 , 一般在测试类中都会使用接口， 而每个接口可能有多个实现类， 这个时候我们除了 primary 标签之外， 我们可以使用  Qualifier注解， 

>  如果没有 // @Qualifier("impl1")， 就会出错
>
> could not autowire. There is more than one bean of 'PersonService' type.
> Beans:
> personServiceImpl   (PersonServiceImpl.java) personServiceImpl2   (PersonServiceImpl2.java) ,
>
> 所以我们需要添加  Qualifier

```java
@Autowired
// @Qualifier("impl1")
private PersonService personService;

@Test
public void test01() {
     System.out.println(personService);
}
```

```java
@Qualifier("personDao")	// personDao 是另外一个bean 实例。
@Autowired
private PersonDao personDao1; // personDao1 是一个 bean 实例

@Test
public void test01() {
     System.out.println(personDao1); // 1 
}
```

### @Bean(required = false)

> 如果一个bean 实例不存在， 或者是没有赋值。也不会出现错误。

### @Primary

> 如果一个工程里包含多个相同类型的bean 实例， 在获取时就会报错， 所以我们可以设置哪一个类是首选类。单可以结合 @Qualifier 注解。

```java
@Service
@Primary
public class PersonServiceImpl2 implements PersonService {
     private String label = "2";
```

```java
@Service
public class PersonServiceImpl implements PersonService {
```

```java
@Test
public void test01() {

     AnnotationConfigApplicationContext context = new ...(AutoWeirdConfig.class);
     PersonService personService = context.getBean(PersonService.class);
     System.out.println(personService);
     // 这样只会输出PersonServiceImpl2 的相关内容。
}
```

