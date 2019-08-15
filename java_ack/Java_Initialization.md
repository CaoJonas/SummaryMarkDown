# Java Initialization

## Kid

```java
public class Kid {
     public Kid(String kidName) {
          System.out.println(kidName + "Kid 类");
     }
}
```

## Animal

```java
public class Animal {
     Kid kid = new Kid("Kid1");
     static Kid kid2 = new Kid("Kid2");
     {
          System.out.println("Animal 非晶态代码块");
     }
     static {
          System.out.println("Animal 静态代码块");
     }
     public Animal(String name) {
          System.out.println("Animal 构造函数" + name);
     }
}
```

## Dog

```java
public class Dog {
     Animal animal2 = new Animal("Animal2");
     static{
          System.out.println("Dog静态代码块");
     }
     static Animal animal = new Animal("Animal1");
     {
          System.out.println("Dog 非静态代码块");
     }
     public Dog(String name) {
          super();
          System.out.println("dog 初始化" + name);
     }
}
```

## Person

```java
public class Person {
     static{
          System.out.println("Person 静态代码块");
     }
     {
          System.out.println("Person 非静态代码块");
     }
     Kid kid = new Kid("Person Kid1");
     static Kid kid2 = new Kid("Person Kid2");
     public Person(String name) {
          System.out.println(name + "person父类构造函数");
     }
}
```

## User

```java
public class User extends Person{
     static{
          System.out.println("User 静态代码块");
     }
     {
          System.out.println("user 非静态代码块");
     }
     Kid kid = new Kid("User Kid1");
     static Kid kid2 = new Kid("User Kid2");
     public User() {
          super("");
          System.out.println("子类构造函数");
     }
}
```

## User_1（继承）

```java
public static void main(String[] args) {
     new User();         
}
/*
Person 静态代码块
Person Kid2Kid 类
User 静态代码块
User Kid2Kid 类
Person 非静态代码块
Person Kid1Kid 类
person父类构造函数
user 非静态代码块
User Kid1Kid 类
子类构造函数
*/
```

> 继承类：User extends Person
>
> 父类静态代码块和父类静态成员（谁前谁先）
>
> 子类静态代码块和父类静态成员（谁前谁先）
>
> 父类非静态代码块和父类非静态成员（谁前谁先）
>
> 父类构造函数
>
> 子类非静态代码块和子类非静态成员（谁前谁先）
>
> 子类构造函数

## Animal_1(单类)

```java
public static void main(String[] args) {
     new Animal("Animal 1");
}
/*
Kid2Kid 类
Animal 静态代码块
Kid1Kid 类
Animal 非晶态代码块
Animal 构造函数Animal 1
*/
```

> Animal_1(单类)初始化顺序
>
> 类静态代码块和类静态成员（谁前谁先）
>
> 类非静态代码块和类非静态成员（谁前谁先）
>
> 类构造函数

## Dog_1 （组合）

```java
public static void main(String[] args) {
     new Dog("旺财");
}
/*
Dog静态代码块
Kid2Kid 类
Animal 静态代码块
Kid1Kid 类
Animal 非晶态代码块
Animal 构造函数Animal1
Kid1Kid 类
Animal 非晶态代码块
Animal 构造函数Animal2
Dog 非静态代码块
dog 初始化旺财
*/
```

> Dog_1 （组合）初始化顺序
>
> 当前类静态代码块和当前类静态成员（谁前谁先）==如果类成员为引用类型， 则利用单类初始化顺序==
>
> 当前类非静态代码块和当前类非静态成员（谁前谁先）==如果类成员为引用类型， 则利用单类初始化顺序==
>
> 构造函数

## Animal_2(初始化次数)

```java
public static void main(String[] args) {
     new Animal("Animal1");
     new Animal("Animal2");
}
/*
Kid2Kid 类
Animal 静态代码块
Kid1Kid 类
Animal 非晶态代码块
Animal 构造函数Animal1
Kid1Kid 类
Animal 非晶态代码块
Animal 构造函数Animal2
*/
```

> 可以看出静态成员和静态代码块只初始化一次（java 虚拟机加载类时，就会执行该代码），非静态可以初始化多次（每次 new 才会执行代码）