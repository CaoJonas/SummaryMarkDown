# 抽象类 和 接口

## 接口：

#### 域（默认public static final）

> 可以包含 基本数据类型 和 引用类型，都必须在声明时 初始化。（public static final 可以省略，一般用作常量（全部大写MAX_VALUE））

```java
int age = 0;
public static final String name = "CaoBourne";
public static final Person person1 = null;
```

#### 静态方法

```java
public static void printinfo() {
     System.out.println("这里允许有静态代码");
}
```

#### default 方法（必须为==public default==）

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

#### public abstract 普通方法（）

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

- 如果继承一个抽象类或接口，但是为实现其抽象方法， 也必须设置为抽象类

## 抽象类和接口

### 区别

- 抽象类可以包含实例变量， 静态变量，以及常量，普通方法、抽象方法、静态方法、代码块以及构造函数。但是接口只能包含 常量（必须在声明时初始化），抽象方法、default 方法 和 静态方法，不能包含代码块和构造方法。

- 只能继承一个抽象类， 而可以实现多个接口。

- 抽象类是对一种事物的抽象，即对类抽象，而接口是对行为的抽象。抽象类是对整个类整体进行抽象，包括属性、行为，但是接口却是对类局部（行为）进行抽象。（想想门、电子门、密码们和木门（抽象类和接口）

- 接口可以继承多个 接口和类（必须用关键字 extends ,不要用implement, 并且，类无论是抽象类还是具体实现类）

  ```java
  Interface: Interface1, Interface2;
  Class Building
  public interface Interface3 extends Building,Interface1,Interface2{
      void canDance();
  }
  //  接口可以继承多各类和多个接口
  ```

- 被实现，只能用extends继承， 用implements 实现接口。

### 相同点

- 都可以定义方法和属性。

- 都可以不含有抽象方法。不含有抽象方法的接口可以作为一个标志（比如可序列化的接口Serializable）。比如，接口可以有public static 和 public default(记住不能有 public static final)

- 都不能被实例化。

- 实现了接口的类或者继承了抽象类的子类，都必须实现定义的 **抽象方法**。如果存在没有实现的方法，那么该类必须声明为抽象类。

- 实现了多态。通过方法的动态解析特性，在运行时动态调用实现类的方法。

## Java9 的 private method改变了什么

在Java9中，接口再次增强，可以实现 **private method** 和 **private static method**。

其实，这样的设计思路很简单。
 既然可以写代码了，那么是不是有一些共用的代码需要封装、又不希望暴露给外部？那就加 private 吧。以前只有抽象方法的时候没有这个需求。而Java8增加了 static method 和 default method 之后，没有一起增加这个功能，简直是设计上的缺失！

在有了这两个以后，接口和抽象类的区别，只剩下的构造器，成员变量，以及单继承。