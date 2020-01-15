# final 

### 成员：

- final 成员一经初始化， 就不可以在改变值。

- final 成员必须初始化。

- final 可以修饰 成员变量和局部变量

- 值类型不能被修改， 但是引用类型的属性可以被修改。

- 形参也可以被 final 修饰， 并且引用不能被修改。

  ```java
  public static void changeObject2(final Person per) {
  	// per = new Person(); error
       // 但是不影响 其属性改变
       per.setName("CaoBourne")  ;
  }
  ```

##### 初始化

1- 静态成员： 声明时初始化、静态代码块初始化。

```java
private final static int age = 20; // 
private final static int height;
static {
     height = 175;
}
```

2-非静态成员： 声明时初始化、非静态代码块以及构造函数

```java
private final String sex;  // 初始化
private final int height = 20;
private final String name;
{
     sex = "Male";
}
public Cat(String name) {
     this.name = name;
}
```

### 方法：

final 修饰的方法类似于普通方法， 但是有意思的是， final 方法不满足多态， 也就是说， 子类不能重写该方法， 因为认为该方法已经满足要求

```java
public final void printInfo5() {// 可以修饰 方法， 可以被子类调用。
     System.out.println("printInfo5");
}
```

也可以final static 方法（其实没有必要， 因为static 方法就不会多态。）

```java
public static final void printInfo() {
     System.out.println("也可以有静态方法");
}
```

### 类：

**不能修饰接口和抽象类。**

final 类不能被继承。

# finally

finally 关键字表示 无论是否出现错误，总是被执行, 除了exit(0) 和系统崩溃;

# finalize

释放内存