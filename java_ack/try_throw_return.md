# Try && Return

## ==不建议在finally中使用return语句==

**1-至少有两种情况下finally语句是不会被执行的：**

**（1）try语句没有被执行到，如在try语句之前就返回了，这样finally语句就不会执行，这也说明了finally语句被执行的必要而非充分条件是：相应的try语句一定被执行到。**

```java
// 在执行 try 语句之前 抛出异常，跳出当前执行方法
public static void returnTry() throws Exception {
     int i = 0;
     if( i == 0) {
          throw new Exception("出错了");
     }
     try {
          System.out.println("hehe");
     } catch (Exception e) {
          e.printStackTrace();
     } finally {
     }
}
```

**（2）在try块中有System.exit(0);这样的语句，System.exit(0);是终止Java虚拟机JVM的，连JVM都停止了，所有都结束了，当然finally语句也不会被执行到。**

**2- finally语句在return语句执行之后return返回之前执行的。**

> 如果执行了 try 语句，一定会执行 finally 语句， 但是如果 try 中有return 语句， 会先执行 return 中的语句，然后再执行 finally 中语句， 然后再返回。

```java
public static int printNum(int number ) {
     System.out.println("printNum" + number);
     return number * 2;
}
public static int getNum() {
     int number = 10;
     try {
          number += 10;
          return printNum(number);
     } catch (Exception e) {
          e.printStackTrace();
     } finally {
          System.out.println("finally block");
     }
     return number;
}

public static void main(String[] args) {
     System.out.println(getNum());
}
/*
printNum20
finally block
40
// 说明return语句已经执行了再去执行finally语句，不过并没有直接返回，而是等finally语句执行完了再返回结果。
*/
```

**2. finally块中的return语句会覆盖try块中的return返回。**

> 和上面的结论相同，会率先执行try 中的 return 语句， 然后执行 finally 中的语句，但是由于 finally 中有return 语句， 会覆盖掉 try 中的return 语句，返回结果是finally 中的语句。

```java
public static int printNum(int number ) {
     System.out.println("printNum" + number);
     return number * 2;
}
public static int getNum() {
     int number = 10;
     try {
          number += 10;
          return printNum(number);
     } catch (Exception e) {
          e.printStackTrace();
     } finally {
          System.out.println("finally block");
          return 100;
     }
}
public static void main(String[] args) {
     System.out.println(getNum());
}
/*
printNum20
finally block
100
*/
```

**catch 同 try 的作用相同**

```java
public static int printNum(int number ) {
     System.out.println("printNum" + number);
     return number * 2;
}
public static int getNum() {
     int number = 10;
     try {
          throw new Exception();
     } catch (Exception e) {
          System.out.println("catch block");
          return printNum(number);
     } finally {
          System.out.println("finally block");
          return 100;
     }
}

public static void main(String[] args) {
     System.out.println(getNum());
}
/*
catch block
printNum10
finally block
100
*/
```

**3. 如果finally语句中没有return语句覆盖返回值，那么原来的返回值可能因为finally里的修改而改变也可能不变。**

> 有点类似于值传递和引用传递，如果只是简单的修改值， 不会影响 原先返回的值，如果是引用类型， 修改引用类型中成员的值，会影响到返回的值。

```java
// 例如，try 中返回的应该是 20， 但是要执行finally ，finally 中改变了 number ,但不影响 number 的值。
public static int printNum(int number ) {
     System.out.println("printNum" + number);
     return number * 2;
}
public static int getNum() {
     int number = 10;
     try {
          return number + 10;
     } catch (Exception e) {
          System.out.println("catch block");
     } finally {
          System.out.println("finally block");
          number = 0;
     }
     return number;
}
public static void main(String[] args) {
     System.out.println(getNum());
}
/*
finally block
20
*/
```

**引用类型**

> 例如，再 finally 语句中并没有返回类型，但是却修改了 map 中 name 对应的value 和添加了 age 的属性，查看输出，显示返回的 map 都被修改了。
>
> ==但是如果改变 map的指向的对象， 却不能影响到 try中的 map 实例。==

```java
public static Map<String, Object> getMap() {
     Map<String, Object> map = new HashMap<>();
     map.put("name", "CaoBourne");
     try {
          map.put("name", "CaoBourne1");
          return map;
     } catch (Exception e) {
          System.out.println("catch block");
     } finally {
          System.out.println("finally block");
          map.put("name", "CaoBourne2");
          map.put("age", 25);
     }
     return map;
}

public static void main(String[] args) {
     // System.out.println(getNum());
     System.out.println(getMap());
}
/*
finally block
{name=CaoBourne2, age=25}
*/
```

**finally 语句 只是改变了 map的指向，是不会影响到 try 中的返回值。**

> 因为 map 指向了新的内存，但是java 是值传递， 不会影响到 map 的指向。

```java
public static Map<String, Object> getMap() {
     Map<String, Object> map = new HashMap<>();
     map.put("name", "CaoBourne");
     try {
          map.put("name", "CaoBourne1");
          return map;
     } catch (Exception e) {
          System.out.println("catch block");
     } finally {
          System.out.println("finally block");
          map = new HashMap<>();
          map.put("age", 24);
     }
     return map;
}
public static void main(String[] args) {
     System.out.println(getMap());
}
/*
finally block
{name=CaoBourne1}
*/
```

