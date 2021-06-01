## <font style="color: crimson;font-weight: bold">equals 和 "=='' 区别</font>

#### 1 基本数据类型(byte, char, boolean, short, int , long, float, double)

 - ###### "==" 的作用 （只是简单的比较值）

   ```java
   int num1 = 10;
   int num2 = 10;
   System.out.println(num1 == num2);
   
   double dou1 = 10.0;
   double dou2 = 10.0;
   System.out.println(dou1 == dou2);
   //true
   //true2 
   ```

#### 2 引用类型

- ###### "==" 的作用 (比较的对象引用地址（基本类型的封装类型 也是引用类型）)	

  ```java
  Doctor doctor1 = new Doctor("CaoBourne", "20141214002");
  Doctor doctor2 = new Doctor("CaoBourne", "20141214002");
  System.out.println(doctor1 == doctor2);
  // false
   Integer num1 = new Integer(10);
   Integer num2 = new Integer(10);
   System.out.println(num1 == num2); // false
  ```

- ###### equals 的作用

  - 未重写 equals()

    ```java
    public boolean equals(Object obj) {
        return (this == obj);
    } // Object 中的方法
    ```

    ```java
    Doctor doctor1 = new Doctor("CaoBourne", "20141214002");
    Doctor doctor2 = new Doctor("CaoBourne", "20141214002");
    System.out.println(doctor1.equals(doctor2));
    // false
    
    ```

  - 重写 equals 方法(String, Byte, Character, Short, Integer, Long, Float, Double 都重写了 equals 方法)

    ```java
    public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }
    
    String name1 = new String("CaoBourne");
    String name2 = new String("CaoBourne");
    sout(name1 == name2) // false
    sout(name1.equals(name2)) // true
    ```

#### 3  String 类型

 - ##### String 类型重写了equals方法， 但是如果使用 "==" 进行比较也是有两种情况

   - ###### String 变量的创建方式

     ```java
     /*
     先说明一下String类型的变量的创建方式
     在创建新的String类型的变量时,首先会在缓冲区查找是否有这个产量对应的对象,有就直接将找到的对象的地址赋给新创建的变量,没有就重新创建一个对象,然后赋给新创建的变量
     */
     String str = "str";//创建一个新的String类型的变量,在已有的缓冲区中没有就重新创建一个
     String str1 = "str";//创建时在缓冲区发现有和即将创建的值一样的对象,就将str的地址赋给str1
     str == str1;//true
     String str2 = new String("str");//强制创建一个对象str2,地址和str,str1不同
     str2 == str;//false因为==比较的是对象的地址,而二者地址不同,故为false
     str2 == str1;//false同上
      
     str2.equals(str1);//true  String继承Object方法中的equals方法进行重写,内部比较是两个String类型的字符串一个一个字符的比较(下边是源码)
     str2.eauals(str);//true同上  -- 应该发生了 装箱 
     ```

   - ######  "字符串" 和 new String()的区别：

     <font style="color: red">https://blog.csdn.net/fengwei4618/article/details/69551908</font>  // 存放位置 以及 二者的区别

     <font style="color: red">**堆， 栈， 常量池**</font>

     ```java
     String s1 = "ABC";
     String s2 = "ABC";
     String s3 = new String("ABC");
     String s4 = new String("ABC");
     System.out.println(s1 == s2); //true
     System.out.println(s2 == s3); //false
     System.out.println(s3 == s4); //false
     // 并不会发生 拆箱 和装箱， 因为对象引用 是存放在 堆中， 而 S1 的值是储存在 常量池中的， s2 和 s3 的存放位置不同。 所以 为 false
     ```

#### 4 基本类型的自动拆箱和 装箱

 - 类型转换：

   **boolean类型与其他基本类型不能进行类型的转换（既不能进行自动类型的提升，也不能强制类型转换）， 否则，将编译出错**。

   **byte型不能自动类型提升到char，char和short直接也不会发生自动类型提升（因为负数的问题）**，同时，byte当然可以直接提升到short型。 

   当对小于int的数据类型（byte, char, short）进行运算时，首先会把这些类型的变量值强制转为int类型进行计算，最后会得到int类型的值。因此，如果把2个short类型的值相加，最后得到的结果是int类型，如果需要得到short类型的结果，就必须显示地运算结果转为short类型。

   ![](C:\Users\Bourne\Desktop\1170065-20180104154110299-576214776.png)

 - Byte, Short, Long 以及 Integer 的 自动装箱 和拆箱

   ```java
   public static Byte valueOf(byte b) { // Byte
       final int offset = 128;   // -128 - 127
       return ByteCache.cache[(int)b + offset];
   }
   ```

   ```java
   public static Character valueOf(char c) { //Character
       if (c <= 127) { // must cache  // 0-127
           return CharacterCache.cache[(int)c];
       }
       return new Character(c);
   }
   ```

   ```java
   public static Short valueOf(short s) { // Short
       final int offset = 128; // -128 -127
       int sAsInt = s;
       if (sAsInt >= -128 && sAsInt <= 127) { // must cache
           return ShortCache.cache[sAsInt + offset];
       }
       return new Short(s);
   }
   ```

   ```java
   public static Integer valueOf(int i) { // Integer
       if (i >= IntegerCache.low && i <= IntegerCache.high)	 // -128 -127
           return IntegerCache.cache[i + (-IntegerCache.low)];
       return new Integer(i);
   }
   ```

   ```java
   public static Long valueOf(long l) {  // Long
       final int offset = 128;
       if (l >= -128 && l <= 127) { // will cache	 // -128 -127
           return LongCache.cache[(int)l + offset];
       }
       return new Long(l);
   }
   ```

   ```java
   // https://www.cnblogs.com/zf29506564/p/5626583.html 
   // Java中 int和Integer的区别+包装类
   // -------------------------------------------------------------------------------------------
           Integer num1 = new Integer(10);
           Integer num2 = new Integer(159);
           int num3 = 10;
           int num4 = 159;
           System.out.println(num1 == num3); // true
           System.out.println(num2 == num4);// true
           System.out.println(num1.equals(num3));  // true
           System.out.println(num2.equals(num4));  // true
   　/*很奇怪吧：这就归结于java对于Integer与int的自动装箱与拆箱的设计，是一种模式：享元模式（flyweight）
   　　为了加大对简单数字的重利用，java定义：在自动装箱时对于值从–128到127之间的值，它们被装箱为Integer对象后，会存在内存中被重用，始终只存在一个对象
   　　而如果超过了从–128到127之间的值，被装箱后的Integer对象并不会被重用，即相当于每次装箱时都新建一个 Integer对象；
   　　以上的现象是由于使用了自动装箱所引起的，如果你没有使用自动装箱，而是跟一般类一样，用new来进行实例化，就会每次new就都一个新的对象；*/
   ```

---

- Boolean 

  ```java
   /**
       * The {@code Boolean} object corresponding to the primitive
       * value {@code true}.
       */
  public static final Boolean TRUE = new Boolean(true);
  
      /**
       * The {@code Boolean} object corresponding to the primitive
       * value {@code false}.
       */
  public static final Boolean FALSE = new Boolean(false);
  public static Boolean valueOf(boolean b) { // Boolean
      return (b ? TRUE : FALSE); // 这里的 TRUE 和 FALSE 是已经静态类成员， 只会初始化一次， 所以对于 引用同一个对象
  }
  ```

  ```java
  Boolean flag1 = new Boolean(true);
  Boolean flag2 = new Boolean(true);
  boolean flag3 = true;
  boolean flag4 = true;
  Boolean flag5 = true;
  Boolean flag6 = true;
  
  System.out.println(flag1 == flag2); // false 引用 不同的对象
  System.out.println(flag3 == flag4); // true
  System.out.println(flag5 == flag6); ..
  System.out.println(flag1 == flag3); ..
  System.out.println(flag5 == flag3); ..
  ```

- Float 和 Double

  ```java
  public static Float valueOf(float f) {
      return new Float(f);
  } // 无论如何都会返回新对象
  ```

  ```java
  public static Double valueOf(double d) {
      return new Double(d);
  }// 无论如何 都会返回新对象
  ```

  ```java
  Double dou1 = new Double(10);
  Double dou2 = new Double(10);
  Double dou3 = 10D;
  Double dou4 = 10D;
  double dou5 = 10;
  double dou6 = 10;
  
  System.out.println(dou1 == dou2); //	false
  System.out.println(dou3 == dou4);	// false
  System.out.println(dou3 == dou1);	// false
  System.out.println(dou5 == dou6);   // true 拆箱
  System.out.println(dou5 == dou4);   // true 拆箱
  System.out.println(dou5 == dou1);   // true	拆箱
  System.out.println(dou1.equals(dou5)); // true  装箱， equals 方法 被重写
  
  ```

记住： 如果 是 类的操作， 一定会 装箱， 如果 有基本数据类型 例如 == 都会拆箱

#### 5 [String字符串的==、equals方法以及对象的==、equals方法的区别](https://blog.csdn.net/zm13007310400/article/details/78300547)

[来看看String类和常量池内存分析以及8种基本类型和常量池例子](https://blog.csdn.net/qq_34115899/article/details/86583262)