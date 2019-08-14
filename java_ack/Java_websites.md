


​	

## **Java 常用网站**



#### 1 设计模式 

 - 单例模式	https://www.cnblogs.com/garryfu/p/7976546.html 
 - java 23种设计模式  https://www.cnblogs.com/foryang/p/5849402.html

#### 2 try-catch-finally(return)

 - try-catch-finally (return 和 finally ) https://www.cnblogs.com/lanxuezaipiao/p/3440471.html

#### 3 基本类型 拆箱与装箱（int）

 - Integer 和 int 的区别 https://www.cnblogs.com/guodongdidi/p/6953217.html
 - 基本类型的自动装箱和拆箱:https://blog.csdn.net/shukebai/article/details/72627448 

#### 4 抽象类 和 接口的区别

 - 抽象类和接口的区别 https://www.cnblogs.com/dolphin0520/p/3811437.html
 - 抽象类 https://blog.csdn.net/strive_penniless/article/details/68583857

#### 5 String字符串的==、equals方法以及对象的==、equals方法的区别

[String字符串的==、equals方法以及对象的==、equals方法的区别](https://blog.csdn.net/zm13007310400/article/details/78300547)

==[来看看String类和常量池内存分析以及8种基本类型和常量池例子](https://blog.csdn.net/qq_34115899/article/details/86583262)==

- 字符串连接

> 字符串连接 ‘+’ 如果两个都只是指向常量池中string 变量
>
> String s2 = "a"; 
> String s3 = "bc";
>
> String s4 = s2 + s3; //	这里会自动调用 StringBuffer中的 append()方法 以及 toString() 方法， 所以s4 会在 堆上创建对象。==但是s4 并没有在常量池中创建 abc对象。（）==
>
> 这是因为二者都调用了 StringBuffer中的 append方法和toString方法， 非常有意思的是， 这两个方法都不会在常量池中创建新的对象。

```java
String s1 = "ab";
String s2 = "cd";
String s3 = s1 + s2;
String s4 = s3.intern();
System.out.println(s3 == s4);		 // true
String newS1 = new String("ef") + new String("gh");
System.out.println(newS1.intern() == newS1); // true
// 这是因为二者都调用了 StringBuffer中的 append方法和toString方法， 非常有意思的是， 这两个方法都不会在常量池中创建新的对象。
```

- toString()

  > 返回新的对象。

  ```java
  @Override
  public synchronized String toString() {
       if (toStringCache == null) {
            toStringCache = Arrays.copyOfRange(value, 0, count);
       }
       return new String(toStringCache, true);
  }
  ```

```java
String s1 = "abc"; 
String s2 = "a"; 
String s3 = "bc"; 
String s4 = s2 + s3; 
System.out.println(s1 == s4);//false,因为s2+s3实际上是使用StringBuilder.append来完成，会生成不同的对象。
// s1指向常量池"abc",s4指向堆中"abc"(append连接而来)
String S1 = "abc"; 
final String S2 = "a"; 
final String S3 = "bc"; 
String S4 = S2 + S3; 
System.out.println(S1 == S4);//true,因为final变量在编译后会直接替换成对应的值
// 所以实际上等于s4="a"+"bc"，而这种情况下，编译器会直接合并为s4="abc"，所以最终s1==s4。
```

字符串常量

> 因为是字符串常量， ss4 = ss1 + ss2; 就变成了 ss4 = "a" + "b";所以只会在常量池中添加 ab字符串。但有意思的是， 在进行字符串连接之后， 常量池中仍然包含 a、b 和 ab字符串。

```java
final String ss1 = "a";
final String ss2 = "b";
String ss3 = "ab";
System.out.println(ss3 == ss1 + ss2);
```

```java
String s = "ab";    // 常量池创建"ab"
String s1 = new String("ab"); // 堆里面创建"ab"，因为常量池已有"ab"，不在常量池再创建"ab"
String str3 = "ab" + "cd"; // 连接之后常量池是否还有"ab"??在常量池连接成"abcd"后"ab"和"cd"是否还存在？
String s2 = s1.intern(); // 如果常量池还有"ab"，s2指向常量池"ab"，如果没有，则放入s1地址，s2就指向s1，即s2指向堆里的"ab"
System.out.println(s2 == s1);
// 上面的结果 为 false;
// 结果运行出来是false，说明"ab"+"cd"连接之后，不仅存在"ab","cd"还存在"abcd"
```
##### ==String name = "ab" + "cd";==

```java
String name = "ab" + "cd";
String name2 = new String("a") + new String("b");
String name3 = name2.intern();
System.out.println(name2 == name3);
```

==这里面的 name = "ab" + "cd"; 在编译阶段直接替换成 "ab" + "cd";, 而不会在常量池中创建新的对象。==

#### 6 Java 浅拷贝和深拷贝的理解和实现方式

​	https://www.cnblogs.com/shakinghead/p/7651502.html

	>clone 与 同时引用一个对象的区别
	>
	>无论深copy 还是浅copy ,都会为 基本数据类型开辟新的内存空间， 也就是说 经过copy, 改变一个对象的 基本数据类型， 并不会影响到 原对象，但是引用类型就会有区别， 如果深copy ， 引用类型也会开辟新空间， 二者修改相不影响。 浅copy则相反。
	>
	>但是引用类型互相赋值， 例如 User user = user1; 二者指向同一个地址， 二者任意一个修改都会发生改变。	

#### 7 张图轻松理解String.intern()

	- [几张图轻松理解String.intern()](https://blog.csdn.net/soonfly/article/details/70147205)

-  [来看看String类和常量池内存分析以及8种基本类型和常量池例子](https://blog.csdn.net/qq_34115899/article/details/86583262)
- [String的Intern方法详解](https://www.cnblogs.com/wxgblogs/p/5635099.html)

#### 8 java 泛型详解-绝对是对泛型方法讲解最详细的，没有之一

泛型<https://blog.csdn.net/s10461/article/details/53941091>

#### 9 StringBuffer 和 StringBuilder 的区别

[StringBuffer 和 StringBuilder 的区别](https://yq.aliyun.com/articles/629416)

#### 10 实现关联表查询

**[MyBatis学习总结(五)——实现关联表查询](https://www.cnblogs.com/xdp-gacl/p/4264440.html)**

#### 11 Maven 中 json

[JSON字符串和java对象的互转【json-lib】](https://www.cnblogs.com/teach/p/5791029.html)

[maven下载json-lib依赖找不到的问题]()

#### 12 集合中常见的问题

- subList [java ArrayList中的subList方法]()

  > ### **小结**
  >
  > - ArrayList.subList方法返回的只是视图
  > - subList的所有操作都是在操作源列表ArrayList，所以增删改都会以影响源列表。
  > - 对ArrayList进行添加、删除的操作之后，在操作subList会抛出异常
  > - 仅仅subList操作并不会抛出异常

#### 13 请问boolean型占几个字节

> 《Java虚拟机规范》一书中的描述：“虽然定义了``boolean``这种数据类型，但是只对它提供了非常有限的支持。
> 在Java虚拟机中没有任何供``boolean``值专用的字节码指令，Java语言表达式所操作的``boolean``值，
> 在编译之后都使用Java虚拟机中的``int``数据类型来代替，而``boolean``数组将会被编码成Java虚拟机的``byte``数组，
> 每个元素``boolean``元素占``8``位”。这样我们可以得出``boolean``类型占了单独使用是``4``个字节，在数组中又是``1``个字节。

- [请问boolean类型占几个字节](http://www.imooc.com/wenda/detail/312480)

- [[Java基础]Java中boolean类型到底占用多少个字节？](http://www.imooc.com/wenda/detail/312480)

