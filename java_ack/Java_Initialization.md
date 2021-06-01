# Java Initialization

## [Java中实例初始化方法()原理解析](https://blog.csdn.net/TomAndersen/article/details/104349311)

## [JVM init和clinit的理解](https://blog.csdn.net/u012588160/article/details/100108895?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task) 

父类静态成员变量、静态代码块-》子类静态成员变量、静态代码块-》父类非静态成员变量、非静态代码块-》父类构造函数-》子类非静态成员变量、非静态代码块-》子类构造函数

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
Person Kid2Kid 类（静态成员变量）
User 静态代码块
User Kid2Kid 类（静态成员变量）
Person 非静态代码块
Person Kid1Kid 类（非静态成员变量）
person父类构造函数
user 非静态代码块
User Kid1Kid 类（非静态成员变量）
子类构造函数
*/
```

> 继承类：User extends Person
>
> 父类静态代码块和父类静态成员（谁前谁先）
>
> 子类静态代码块和子类静态成员（谁前谁先）
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

## ==自己类中定义自己的实例对象==

> 这个还是要注意的，首先，如果再当前类中只能定义静态对象，如果定义非静态则会造成 死循环；第二，初始化当前静态成员变量； 只会进行非静态成员变量和非静态代码块初始化。而不会进行当前类中静态成员变量和代码块初始化，原因如下：

```java
第一步：在类加载过程的准备阶段，先对b进行系统的赋值，b = 0。
第二步：在类加载过程的初始化阶段，执行<clinit>方法，那么先执行类变量的初始化（调用类成员变量和静态代码块）
第三步：在第二步的时候，<clinit>正在执行，当执行到 “static Practice practice = new Practice();”，会去调用<init>方法（而init只会执行非静态成员变量和代码块，然后构造函数）
第四步：<init>方法已经执行完了，那么就接下来执行<cinit>剩余的部分，先执行类的静态代码块：（此时的先后顺序依照代码编写的先后顺序）此时<cinit>方法就执行完成了。
第五步：<clinit>和<init>方法都已经执行完成了，类已经加载完成，此时就是函数的调用了，JavaTest的函数入口是main()方法，因此会调用静态方法f1()：System.out.println("4"); 到此，整个程序就执行完成了。
```

> 一般在自己类中定义的成员变量一般为 静态成员变量， 如果按照下面定义, 就会出现死循环， 栈内存溢出
>
> ```java
> SynChronizedClass synChronizedClass1 = new SynChronizedClass(1000, "CaoBourne1");
> public static void main(String[] args){
>      new SynChronizedClass();
> }
> ```

```java
class Animal1 {
    public Animal1(String name) {
        System.out.println(name);
    }
}
public class Practice {
    static Practice practice = new Practice();
    static {
        System.out.println("静态代码1");
    }
    {
        System.out.println("非静态代码2");
    }
    static Animal1 animal1 = new Animal1("静态成员变量");
    Animal1 animal2 = new Animal1("非静态成员变量");
    public Practice() {
        System.out.println("Practice 构造函数");
    }
    public static void main(String... args) {
        new Practice();
    }
}
/*
非静态代码2
非静态成员变量
Practice 构造函数
静态代码1
静态成员变量
非静态代码2
非静态成员变量
Practice 构造函数
*/
```

## 调用其他类的静态方法中间的初始化

》==只会初始化 其中的类变量 和 静态代码块==

```java
class PersonRe {
     public final static String name = "CaoBourne";
     public static String name2 = "CaoBourne2";
     Kid kid1 = new Kid("Bourne");
     static{
          System.out.println("这是 PersonRe 静态方法");
     }
     {
          System.out.println("这是 PersonRe 非静态方法");
     }
     static Kid kid2 = new Kid("Jonas");

     public static void printInfo() {
          System.out.println("PersonRe 类");
     }
}
public class Person {
     public static void main(String[] args) {
          PersonRe.printInfo();
     }
}
/*
只会初始化 其中的类变量 和 静态代码块
这是 PersonRe 静态方法
JonasKid 类
PersonRe 类
*/
```

## 调用其他类的静态变量

> ==只会调用 静态代码块 和 初始化静态成员变量==

```java
class PersonRe {
     public final static String name = "CaoBourne";
     public static String name2 = "CaoBourne2";
     Kid kid1 = new Kid("Bourne");
     static Kid kid2 = new Kid("Jonas");
     static Kid kid3 = new Kid("Jonas2");
     static{
          System.out.println("这是 PersonRe 静态代码块");
     }
     {
          System.out.println("这是 PersonRe 非静态方法");
     }
     public static void printInfo() {
          System.out.println("PersonRe 类");
     }
}
public class Person {
     public static void main(String[] args) {
          System.out.println(PersonRe.kid2);
     }
}
/*
JonasKid 类
Jonas2Kid 类
这是 PersonRe 静态代码块
com.bourne.practice.pojo.Kid@4554617c
*/
```

## 调用静态常量

> 调用静态常量 例如： name 只会初始化 name, 不会初始化静态代码块， 和静态成员变量。。

```java
class PersonRe {

     public final static String name = "CaoBourne";

     public static String name2 = "CaoBourne2";

     Kid kid1 = new Kid("Bourne");
     static Kid kid2 = new Kid("Jonas");
     static Kid kid3 = new Kid("Jonas2");

     static{
          System.out.println("这是 PersonRe 静态代码块");
     }

     {
          System.out.println("这是 PersonRe 非静态方法");
     }


     public static void printInfo() {
          System.out.println("PersonRe 类");
     }
}

public class Person {

     public static void main(String[] args) {
          System.out.println(PersonRe.name);
     }

}
```

## 为什么调用（1)静态成员变量和静态方法会初始化所有静态成员变量和静态代码块，(2)调用常量却不会初始化静态成员变量和静态代码块

(1). 调用静态成员和静态方法首先会调用 putField, setField, 和 invokestatic 字节码，就会发生类加载，在类加载初始化阶段，JVM会调用<cinit>方法，该方法就会初始化静态成员变量和执行静态代码块

(2) 静态常量会在编译阶段加载到类的运行时常量池，而成员变量初始化和代码块的执行都会在类加载的时候和类加载之后

## 经典 题目分析

```java
class SingleTon {
    private static SingleTon singleTon = new SingleTon();
    public static int count1;
    public static int count2 = 0;
    private SingleTon() {
        count1++;
        count2++;
    }
    public static SingleTon getInstance() {
        return singleTon;
    }
}
public class Test {
    public static void main(String[] args) {
        SingleTon singleTon = SingleTon.getInstance();
        System.out.println("count1=" + singleTon.count1);
        System.out.println("count2=" + singleTon.count2);
    }
    // count1=1
	// count2=0
}
// 调用静态方法，首先会进行类加载，在准备阶段，初始化为 零值，在初始化singleTon静态成员变量的时候，<init>方法会执行，（只会初始化非静态成员变量和非静态代码块），init 初始化完成之后，会接着执行 cinit 的其他成员， count2 = 0; 又再次被初始化
/*
1:SingleTon singleTon = SingleTon.getInstance();调用了类的SingleTon调用了类的静态方法，触发类的初始化
2:类加载的时候在准备过程中为类的静态变量分配内存并初始化默认值 singleton=null count1=0,count2=0
3:类初始化化，为类的静态变量赋值和执行静态代码快。singleton赋值为new SingleTon()调用类的构造方法
4:调用类的构造方法后count=1;count2=1
5:继续为count1与count2赋值,此时count1没有赋值操作,所有count1为1,但是count2执行赋值操作就变为0
*/
```

