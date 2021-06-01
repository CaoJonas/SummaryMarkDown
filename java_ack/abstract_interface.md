# 抽象类 和 接口

## 接口：

==**接口中的域必须是public static final ,抽象方法必须为 public abstract 修饰**。**由于接口代表着一个对外行为的集合，所以任何方法在接口内都是public(公开的)**。==

#### 域（默认public static final）

> 可以包含 基本数据类型 和 引用类型，都必须在声明时 初始化。（public static final 可以省略，一般用作常量（全部大写MAX_VALUE））

```java
int age = 0;
public static final String NAME = "CaoBourne";
public static final Person PERSON1 = null;
```

#### 静态方法（必须为 public )

```java
public static void printinfo() {  // 
     System.out.println("这里允许有静态代码");
}
```

#### default 方法（必须为==public default==）(default 不可或缺) `支持重写，多态的`

> 该修饰符表示 默认， 而不是 包级别访问权限。
>
> 静态方法和默认方法都有自己的方法体，用于提供一套默认的实现，这样子类对于该方法就不需要强制来实现，可以选择使用默认的实现，也可以重写自己的实现。当为接口扩展方法时，只需要提供该方法的默认实现即可，

```java
default void printInfo4(String name) {// 必须为 public 
     System.out.println(name);
}
```

#### 接口中的泛型

```java
public interface Building<E> {
     public static void printinfo(int info) {	// 不可以使用泛型
          System.out.println("这里允许有静态代码");
     }
     public abstract void printInfo2(E age);	// 可以有泛型
     void printInfo3(String name);
}
在实现类中，方法可以被写成 Object age
	@Override
    public void printInfo2(Object age) {
        System.out.println("抽象方法 printInfo2");
    }
```

#### public abstract 普通方法（）(不能被 protected 修饰)

> 接口中的方法默认为 public abstract 修饰符的，下面两种书写方式都可以。只能是 public abstract

```java
public abstract void printInfo2(int age);
void printInfo3(String name);
```

#### 静态代码块(不能)

> 因为接口中不能包含代码块， 所以这也是为什么  接口中的域都必须在 声明时被赋值。

```java
static {
     System.out.println("但是不允许有静态代码块");
}
```

#### 非静态代码块（不能）

```java
{
     System.out.println("但是不允许有非静态代码块");
}
```

## 抽象类：

#### 域（不能被 abstract 修饰）

> 可以有 实例变量、类变量、以及final 修饰的变量。但是域不能被 abstract 修饰

```java
private String name;
private int age;
private final static String sex;
public final int height;
```

#### 代码块

> 抽象类中可以有静态和非静态代码块

```java
static {
     sex = "Female";
}
{
     height = 30;
}
```

#### 构造方法

```java
public Animal(int height1) {
}
public Animal(String name, int age, String personSex, int height) {
     this.age = age;
     this.name = name;
}
```

#### 静态方法

```java
public static void printInfo() {
     System.out.println("也可以有静态方法");
}
```

#### 非静态方法

```java
public void printInfo(String name){
     System.out.println(name);
}
```

#### 抽象方法

> ==抽象类中抽象方法修饰符可以 protected 和 public(缺省为 public)。static 和 final 都不能和 abstract 同时出现。==

```java
public abstract void printInfo(int number);
protected abstract void printInfo(int number);
```

#### static final 方法

```java
public  static final void printInfo() {
     System.out.println("也可以有静态方法");
}
```

#### 抽象类：

- 包含抽象方法的类叫做抽象类， 但是抽象类不一定包含抽象方法
- 如果继承一个抽象类或接口，但未实现其抽象方法， 也必须设置为抽象类

## 方法解析

> 方法解析分成 类方法和接口方法解析
>
> 因为，抽象方法只是提供了一种实现模板，并非由真实实现，因此，和字段解析有所不同：
>
> 1. 父类和接口中包含`不同返回类型`和`相同方法签名`的方法，冲突。
> 2. 两个父接口中包含`不同返回类型`和`相同方法签名`的方法，冲突。
> 3. ==不用担心 访问权限对继承的问题， 因为接口中的方法始终是 public, 所以，实现方法也必定是 public 权限，但是如果是 抛出异常，如果 两个父接口中的方法抛出了不同的异常，但是还是以实现类的抛出异常为准。==

==但是要知道， 继承默认方法是不需要实现的==

`超类优先1`如果超类提供一个具体方法，而实现的接口中 无论是默认方法还是普通声明都会被忽略（`记住是一个接口中的默认方法`）

> 尽管 TestClass2 中没有实现 GetNum 方法，但是从超类中继承该方法，也就不用实现 接口中的方法。
>
> // `因为 从父类中 继承了 getNum 方法，也就会在类加载的创建的方法表中包含 该方法，但是要注意，如果基类中的该方法 访问修饰符低于 public, 需要修改访问修饰符为 (public)`

```java
interface Face1 {
    void getNum () ;
}
class TestClass {
    public void getNum() {}
}
// 超类优先
class TestClass2  extends  TestClass implements Face1 {
}
/*
// 当然实现也是没有问题的
class TestClass2  extends  TestClass implements Face1 {
    @Override
    public void getNum() {
    }
}
*/
```

`超类优先2`如果超类提供一个具体方法，而另外两个接口中都实现了相同方法签名的默认方法，需要解决冲突

```java
interface Face1 {
    default void getNum() {
    }
}
interface Face2 {
   default void getNum() {
   }
}
class TestClass {
    protected void getNum() {
    }
}
// 超累优先
class TestClass2 extends TestClass implements Face1, Face2 {
    @Override
    public void getNum() {
        
    }
}
```

`接口冲突1`如果两个接口中只要`有一个定义了默认方法`，无论是接口继承还是类实现，都需要解决冲突。（因为他们不知道实现了那个接口中的方法，只能通过重写的方式解决冲突）

```java
interface Face1 {
    default void getNum() {
    }
}
interface Face2 {
   void getNum();
}
interface Face3 extends Face2, Face1{
    @Override
    default void getNum() {
    }
}
```

`接口冲突2`如果两个接口中只是声明了两个普通方法（无方法体），有两种方法， 

1. 如果是类， 实现
2. 如果是类，不实现，定义成抽象类
3. 如果是接口，继承，什么也不做

## 抽象类和接口

### 区别

- 抽象类可以包含实例变量， 静态变量，以及常量，普通方法、抽象方法、静态方法、代码块以及构造函数。但是接口只能包含 常量（必须在声明时初始化），抽象方法、default 方法 和 静态方法，不能包含代码块和构造方法。

- 只能继承一个抽象类， 而可以实现多个接口。

- 抽象类是对一种事物的抽象，即对类抽象，而接口是对行为的抽象。抽象类是对整个类整体进行抽象，包括属性、行为，但是接口却是对类局部（行为）进行抽象。（想想门、电子门、密码们和木门（抽象类和接口）

- `接口可以继承多个 接口（必须用关键字 extends ,不要用implement）`

- 抽象类中抽象方法修饰符可以 protected 和 public(缺省为 public), 但是接口中的抽象方法只能是 public abstract(已缺省， 所以无序添加)

- `抽象类抽象方法和接口中的抽象方法不同`

  ```java
  抽象类----[public/protected] abstract void printInfo throw Exception();
  接口------[public abstract]void printInfo throw Exception();
  ```

  ```java
  Interface: Interface1, Interface2;
  Class Building
  public interface Interface3 extends Building,Interface1,Interface2{
      （public abstract）void canDance();
  }
  //  接口可以继承多各类和多个接口
  ```

- 被实现，只能用extends继承， 用implements 实现接口。

### 相同点

- 都可以定义方法和属性。

- 都可以不含有抽象方法。不含有抽象方法的接口可以作为一个标志（比如可序列化的接口Serializable）。比如，接口可以有public static 和 public default(记住不能有 public static final)

- ==都不能被实例化。==

- 实现了接口的类或者继承了抽象类的子类，都必须实现定义的 **抽象方法**。如果存在没有实现的方法，那么该类必须声明为抽象类。

- 实现了多态。通过方法的动态解析特性，在运行时动态调用实现类的方法。

## Java9 的 private method改变了什么

在Java9中，接口再次增强，可以实现 **private method** 和 **private static method**。

其实，这样的设计思路很简单。
 既然可以写代码了，那么是不是有一些共用的代码需要封装、又不希望暴露给外部？那就加 private 吧。以前只有抽象方法的时候没有这个需求。而Java8增加了 static method 和 default method 之后，没有一起增加这个功能，简直是设计上的缺失！

在有了这两个以后，接口和抽象类的区别，只剩下的构造器，成员变量，以及单继承。