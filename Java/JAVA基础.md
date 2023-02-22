### JAVA基础



[TOC]



#### 基本数据类型

​	java默认的浮点类型为`double`。



#### 面向对象

##### 类和实例

###### 类的属性

​	类属性即成员变量，直接定义在类的代码块`{}`中，而局部变量则是声明在方法内、方法形参、构造器形参、构造器内部的变量。

###### 局部变量和成员变量

* 相同点

  * 都是先声明后使用

  * 变量都有其对应的作用域

    

* 不同点

  * 声明位置不同

  * 访问修饰符的使用，局部变量不可以使用访问修饰符。

  * 成员变量有默认初始化值，局部变量在调用前必须显示赋值

  * 在内存中加载的位置不同：

    成员变量：加载到堆空间中

    局部变量：加载到栈空间中

###### 类的方法

###### 重载（overloading）

​	在**同一个类**中允许存在一个以上的同名方法。参数个数或类型不同即为重载方法。

* 权限修饰符(public private 默认)无关，与返回值类型无关。
* 方法名必须相同，[形参类型的顺序不同、形参的个数不同、形参的类型不同]三者至少满足一个。

```java
1.判断：与void show(int a,char b,double c){}构成重载的有：
    
a)void show(int x,char y,double z){} // no
b)int show(int a,double c,char b){} // yes
c) void show(int a,double c,char b){} // yes
d) boolean show(int c,char b){} // yes
e) void show(double c){} // yes 
f) double show(int x,char y,double z){} // no
g) void shows(){double c} // no

```

###### 重写（overwrite）

```java
权限修饰符 返回值类型 方法名(形参列表){
  						//方法体
 				 }
```

​	是不同类中的方法。子类继承父类后可以对父类中的方法进行覆盖操作，即通过子类去调用重写过的父类中**同名同参数**的方法时实际执行的是子类的方法，子类的方法覆盖了父类的方法。

* 子类的方法名、形参列表必须和父类一致，返回值类型是非基本数据类型时可以是父类的子类。(返回值和声明异常的类型，小于等于父类)
* 权限修饰符必须大于等于父类的权限

###### 重写和重载的区别

* 重写和重载是Java**多态性**的不同表现。重载是展现一个类中的多态性，重写是父类和子类间的多态。

* 重写可以理解为外表不变，核心重写（方法名和参数不变），而重载是参数列表不同的同名函数构成重载。


###### 构造器（构造方法）

​	构造器主要有以下两个作用：

1. 创建对象
2. 初始化对象的属性

​	Constructor有以下性质

* 如果没有显示的定义类的构造器的话，则系统默认提供一个**空参**的构造器。

 * 定义构造器的格式: `权限修饰符  类名(形参列表) { }`，

   没**有返回类型**(因为返回的类型肯定是这个对象本身的类型)

 * 一个类中定义的多个构造器，彼此构成重载。

 * 一旦显示的定义了类的构造器之后，系统不再提供默认的空参构造器。

* 一个类中，至少会有一个构造器(空参构造西)。

* 可以由`private`修饰，表示其他程序无法创建该类的对象。

###### JavaBean

​	JavaBean是一种可重用组件，是满足以下性质的java类：

1. 类是公共的	
2. 有一个无参的公共的构造器
3. 有属性且有对应的get、set方法

```java
public class Customer {
	private int id;
	private String name;

	public Customer(){
		
	}
	
	public void setId(int i){
		id = i;
	}
	
	public int getId(){
		return id;
	}
	
	public void setName(String n){
		name = n;
	}
	
	public String getName(){
		return name;
	}
}
```



##### 三大基本特征

###### 1. 封装

​	Encapsulation是指一种将抽象性函式接口的实现细节部分包装、隐藏起来的方法。

​	体现：*我们将类的属性私有化(private),同时,提供公共的(public)方法来获取(getXxx)和设置(setXxx)*

​	好的程序设计追求“高内聚（类的内部数据操作细节与外界无关），低耦合（仅对外部暴露少量的方法用于使用）”，从而提高系统的可扩展性、可维护性。

​	封装的好处：

* 良好的封装能够减少耦合
* 类内部结构可以自由修改
* 对成员变量更好地控制
* 隐藏信息，实现细节

###### 2. 继承

​	从已有的类派生出新的类称为继承。

​	问题场景：不同的类中也可能有共同的特征和动作，将这些定义为一个通用类，使其他类共享可以提高代码的可维护性（不用一修改全部都需要修改），但同时也增加了代码的耦合度。

- 子类拥有父类**非 private** 的属性、方法
- 子类可以拥有自己的属性和方法，即子类可以对父类进行扩展
- 子类可以用自己的方式实现父类的方法（重写）
- Java 的继承是单继承，但是可以多重继承
- 提高了类之间的耦合性（继承的缺点，耦合度高就会造成代码之间的联系越紧密，代码独立性越差）

​	**继承关键字**

1. extends

   java的类继承是单一继承因此`extends`后面只能跟一个类

   ```java
   public class B extends  A{ 
   }
   ```

2. implements

   使用 implements 关键字可以变相的使java具有多继承的特性，使用范围为类继承接口的情况，可以同时继承多个接口（接口跟接口之间采用逗号分隔）。

   ```java
   public class C implements A,B {
   }
   ```

3. super、this

   通过super关键字来实现对父类成员的访问，用来引用当前对象的父类，this则是指向自己的引用。

   **注：对于构造器而言，子类是不继承父类的构造器的**，因此它必须显式或隐式地调用父类的构造器，遵循以下原则：

   * 若父类构造器含参，必须在子类构造器中通过同样带参的`super`关键字调用父类构造器
   * 若父类构造器无参，子类不用显示调用，系统会**自动调用**父类的无参构造器。

**为什么类只支持单继承**

​	在Java中一个类只能有一个直接父类，不允许多重继承的原因在于：假设A同时继承B和C，而B和C中都提供了D方法，则此时A将无法决定继承哪一个D方法了。

###### 3. 多态

​	多态是同一个行为具有多个不同表现形式或形态的能力。同一个接口使用不同的实例而执行不同的操作。具体表现为父类的引用指向子类的对象实例。

​	多态的实现方式：

1. 重写
2. 接口
3. 抽象类和抽象方法

​	多态的优点如下：

* 消除类型之间的耦合关系

* 可替换性
* 可扩充性
* 接口性
* 灵活性
* 简化性

​	多态存在的三个必要条件：**继承、重写以及父类引用指向子类对象**。

​	eg. `Parent p = new Child();`

​	<img src="https://s2.loli.net/2022/04/25/ve5MNquJcUl1Xn6.png" alt="image-20220425095233909" style="zoom:50%;" />

注： **当使用多态方式调用方法时，首先检查父类中是否有该方法，如果没有，则编译错误；如果有，再去调用子类的同名方法**。

​	**如果没有多态需要写很多重载的方法。有了之后就可以通过不同的子类对象实现不同的行为方式。**

###### 向上和向下转型

<img src="https://s2.loli.net/2022/05/15/oAhx89m2BHRvqwi.png" alt="image-20220515101924033" style="zoom: 67%;" />

**基本数据类型**

1. 向上转换

```java
public static void main(String[] args) {
    int a = 1;
    byte b = 2;
  	// byte x = a + b; ---> 报错
    //int类型和byte类型运算，结果是int类型
    int y = a + b;
    System.out.println(y);
}
```

* 转换规则：byte,short,char → int → long → float → double
* byte，short，char之间不会相互转换，在计算时首先会转换为**int**类型
* boolean 类型是**不可以**转换为其他基本数据类型

2. 向下转换

```java
int i = (int) l;//必须强转，也叫显示转换

double d = 3.14;

float f = (float) d;
```

###### instanceof关键字

​	为了避免在向下转型时出现ClassCastException异常，需要先进行`instanceof`的判断，只有返回true后才进行向下转型。

```java
// 向下转型过程中，可能会发生类型转换的错误，即ClassCastException异常
// 需要做健壮性判断 
if( !a instanceof Dog){ // 判断当前对象是否是Dog类型
    System.out.println("类型不匹配，不能转换"); 
    return; 
} 
Dog d = (Dog) a; //向下转型
```

​	注：如果`a instanceof Dog`返回true，则`a instanceof Animal`也返回true。( 其中Animal是Dog的父类)

###### 动态绑定(dynamic binding)

##### 五大基本原则	

1. 单一职责原则(SRP)

​	就一个类而言，应该仅有一个引起它变化的原因。

2. 开放-封闭原则(OCP)

​	一个软件实体应当对扩展开放，对修改关闭

3. 里氏代换原则(LSP)

​	子类型(subtype)必须能够替换它们的基（父）类型

4. 依赖倒转（置）原则(DIP)

​	要依赖于抽象，不要依赖于具体

5. 接口隔离原则(ISP)

​	使用多个专门的接口比使用单一的总接口总要好



#### 类加载过程

​	类从被加载到虚拟机的内存中到卸载出内存的周期包括以下7个阶段：

* 加载（Loading）
* 验证（Verification）
* 准备（Preparation）
* 解析（Resolution）
* 初始化（Initialization）
* 使用（Using）
* 卸载（Unloading）

##### 类变量和成员变量

​	在类加载的时候会赋予初值的是类变量，而非对象成员（在实例化对象后才有）。

#### 注解(Annotation)

JAVA中的类、方法、变量、参数和包都可以被标注。



#### 接口（Interface）

​	Java不支持多重继承，但可以通过接口实现同样的效果。接口可以理解成统一的**协议**, 而接口中的属性也属于协议中的内容。但是接口的属性都是公共的，静态的，最终的。

​	**接口中没有构造器(意味着接口不可以实例化)，是抽象方法和常量值定义的结合。**

​	接口成员的特点：

* **成员变量只能是常量**，默认修饰符(即可省略不写)为`public static final`(关键字final也意味着变量必须初始化赋值)。
* **成员方法只能是抽象方法，**默认修饰符为`public abstract`

```java
[修饰符] interface 接口名称 [extends 其他的接口名] {
        // 声明变量
        // 抽象方法
    //全局变量
	public static final int MAX_SPEED = 7900;	
	int MIN_SPEED = 1;//省略了 public static final 
	
	//抽象方法
	public abstract void fly();
	
	void stop();//省略了 public abstract 
	//Interfaces cannot have constructors
	//	public 接口名称(){
	//		
	//	}	
}        
}
```

*JDK8提出:除了全局常量和抽象方法之外，还可以定义静态方法、默认方法*。

##### 接口和抽象类

​	**abstract class** 和 **interface** 所反映出的设计理念不同。其中 **abstract class** 表示的是 **"is-a"** 关系，**interface** 表示的是 **"has-a"** 关系。

1. 抽象类中可以有方法的具体实现，但接口中所有的方法必须是**抽象方法**，**Java 8** 之后 接口中可以使用 **default、static** 关键字修饰的非抽象方法。
2. 抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是 **public static final** 类型的。
3.  接口中不能含有静态代码块以及静态方法(用 static 修饰的方法)，而抽象类可以。
4. 一个类只能继承一个抽象类，而一个类却可以实现多个接口。

​	**注：** *JDK 1.8 以后，接口里可以有静态方法和方法体了。*

​	*JDK 1.8 以后，接口允许包含具体实现的方法，该方法称为"默认方法"，默认方法使用 **default** 关键字修饰。更多内容可参考 [Java 8 默认方法](https://www.runoob.com/java/java8-default-methods.html)。*

​	*JDK 1.9 以后，允许将方法定义为 private，使得某些复用的代码不会把方法暴露出去。更多内容可参考 [Java 9 私有接口方法](https://www.runoob.com/java/java9-private-interface-methods.html)。*

​	**接口可以有default、static方法。**

```
Interface A {
    abstract void a();
    static void s() {
    }
    default void d(){
       
    }
    void b();//默认用abstract修饰
    int a = 0;//默认用static final 修饰
}
```



**什么时候使用接口或抽象类？**

- 需要有一些默认实现的方法，使用抽象类。
- 为了实现多重继承，必须使用接口。由于**Java不支持多继承**，子类不能够继承多个类，但可以实现多个接口。
- 如果基本功能在不断改变，那么就需要使用抽象类。因为如果不断改变基本功能并且使用接口，那么就需要改变所有实现了该接口的类。

##### 接口的应用

###### 代理模式(proxy pattern)

<img src="https://s2.loli.net/2022/05/20/pgrmJNQICYf1uV6.png" alt="image-20220520095337448" style="zoom: 50%;" />

​	在代理模式中，一个类代表另一个类的功能，我们创建现有对象的对象一遍向外界提供功能接口。为其他对象提供一种代理来控制对这个对象的访问。

应用场景

* Windows的快捷方式
* spring aop
* 延迟加载：先加载轻量级的代理对象，真正需要时再加载真实对象。比如你要开发一个大文档查看软件，大文档中有大的图片，有可能一个图片有 100MB，在打开文件时，不可能将所有的图片都显示出来，这样就可以使用代理模式，当需要查看图片时，用 proxy 来进行大图片的打开。

优点：

* 职责清晰
* 高拓展性
* 智能化

缺点：

* 由于在客户端和真实主体之间增加了代理对象，因此可能会造成请求的处理速度变慢
* 实现代理模式需要额外的开销，有些实现十分复杂

分类：

* 远程代理(remote proxy)

* 虚拟代理(virtual proxy)

  这些代理启动对真实对象的操作并向应用程序提供默认结果，一旦真实的对象完成，这些代理就会将实际数据推送到客户端(客户端之前已经提供了虚拟数据)。

* 保护代理(protection proxy)

  如果应用程序无权访问某些资源，则此类代理将与有权访问该资源的应用程序中的对象通信，然后获取结果。

  例如：连接校园网之前，代理会先检查是否是校园网用户再提供连接。

* 智能代理(smart proxy)

  智能代理通过在访问对象时插入特定操作来提供额外的安全层。例如，在访问真实对象之前检查它是否被锁定，以确保没有其他对象可以更改它。

###### 工厂模式

#### 异常

<img src="https://s2.loli.net/2022/05/16/sfnOKHa6DMr9AEm.png" alt="image-20220516120905837" style="zoom:80%;" />

​	位于`java.lang`包中的Throwable表示作为任何异常抛出的类，分为Error和Exception两种。

* **Error**( JVM无法处理的错误，只能尽量避免)：无法通过`catch`捕获的异常，例如，Java 虚拟机运行错误（`Virtual MachineError`）、虚拟机内存不够错误(`OutOfMemoryError`)、类定义（`NoClassDefFoundError`）等。这些异常发生时，JVM一般会选择**线程终止**。

* **Exception**：程序本身可以处理的异常，可以通过 `catch` 来进行捕获。`Exception` 又可以分为 受检查异常(必须处理) 和 不受检查异常(可以不处理)。
  * **受检异常**：在编译过程中若没有被`catch/throw`处理的话就无法通过编译。
    * 除了`RuntimeException`及其子类以外其他的`Exception`类及其子类都属于受检查异常。常见的有IO相关的异常`IOException`、`ClassNotFoundException、SQLException`等。
    
  * **不受检查异常**：在编译过程中即使不处理也可以正常编译。
    * 常见的有：`NullPointerException`、`NumberFormatException`（字符串转换为数字）、`ArrayIndexOutOfBoundsException`、`ClassCastException`、`ArithmeticException`等 
    

<img src="https://s2.loli.net/2022/06/22/jlwTiVZxHYy25kg.png" alt="image-20220622134332145" style="zoom:67%;" />

##### Throwable常用方法

* **`public String getMessage()`**:返回异常发生时的简要描述

* **`public String toString()`**:返回异常发生时的详细信息

* **`public String getLocalizedMessage()`**:返回异常对象的本地化信息。

  使用 `Throwable` 的子类覆盖这个方法，可以生成本地化信息。如果子类没有覆盖该方法，则该方法返回的信息与 `getMessage()`返回的结果相同

* **`public void printStackTrace()`**:在控制台上打印 `Throwable` 对象封装的异常信息

#####  try-catch-finally

* try：用于捕获异常，后面可跟0个或多个catch，若没有catch则必须接上finally。
* catch：用于处理try捕获到的异常。
* finally：一定会被执行的语句。当在try或catch块中遇到`return`语句时，finally的语句块会在**方法返回前**被执行。
  * 以下几种情况finally将**不会**被执行
    1. 在try/catch中执行了`System.exit(int)`退出程序。但若`System.exit(int)`在异常语句后，finally还是会被执行。
    2. 程序所在的线程死亡
    3. 关闭CPU

​	**总结：**

* 用try-catch 捕获异常

* 用try-finally 清除异常 (finally块用作回收无法被JVM回收的物理资源，例如数据库链接、 网络链接等。

  注： Jdk1.7中虽增强了try()的功能，使得其中的代码在程序执行完毕后自动关闭代码，相当与隐含finally块。

* 用try-catch-finally 处理所有的异常。三者选一种即可

##### try-with-resources

​	当我们有必须要关闭的资源时，建议优先使用 `try-with-resources`，这样写出的代码更加简短清晰，产生的异常信息也更有用。

​	**适用范围：** 任何实现 `java.lang.AutoCloseable`或者 `java.io.Closeable` 的对象。

​	**关闭资源和finally的执行顺序：**在 `try-with-resources` 语句中，任何 catch 或 finally 块在声明的资源关闭后运行。

> ​		Java 中类似于`InputStream`、`OutputStream` 、`Scanner` 、`PrintWriter`等的资源都需要我们调用`close()`方法来**手动关闭**，一般情况下我们都是通过`try-catch-finally`语句来实现这个需求，如下 ：
>
> ```java
> //读取文本文件的内容
> Scanner scanner = null;
> try {
>     scanner = new Scanner(new File("D://read.txt"));
>     while (scanner.hasNext()) {
>         System.out.println(scanner.nextLine());
>     }
> } catch (FileNotFoundException e) {
>     e.printStackTrace();
> } finally {
>     if (scanner != null) {
>         scanner.close();
>     }
> }
> ```
>
> 使用 Java 7 之后的 `try-with-resources` 语句改造上面的代码:
>
> ```java
> try (Scanner scanner = new Scanner(new File("test.txt"))) {
>     while (scanner.hasNext()) {
>         System.out.println(scanner.nextLine());
>     }
> } catch (FileNotFoundException fnfe) {
>     fnfe.printStackTrace();
> }
> ```
>
> 注：在try-with-resources中多个资源间可以使用分号分隔

#### 泛型

​	Java 泛型（generics）是 JDK 5 中引入的一个新特性, 泛型提供了**编译时**类型安全检测机制，该机制允许程序员在编译时检测到非法的类型。

​	**泛型不会影响java虚拟机生成的汇编代码，在编译阶段虚拟机就会把泛型的类型擦除，还原成没有泛型的代码。**

​	**因此，泛型只在编译的时候保证数据类型的正确性(可能编译速度稍慢)，和运行期间的性能无关，执行速度是完全没有什么区别的。**

#### Java修饰符

##### 访问修饰符

​	对于 class 的权限修饰只可以用 public 和 default(缺省)。

- public 类可以在任意地方被访问。
- default 类只可以被同一个包内部的类访问。

<img src="https://s2.loli.net/2022/07/21/umy5Cn1AaGjibZh.png" alt="image-20220721102250211" style="zoom:50%;" />

​	**总结：**

1. default拒绝一切包外访问而protected接受包外的子类访问

2. 对于外部类来说，只有两种修饰，public和默认（default），因为对于包中的外部类，只有两种可能，包可见和包不可见。

   对于内部类来说，可以有所有的修饰，因为内部类放在外部类中，与成员变量的地位一致，所以有四种修饰符。

###### public

​	对**所有类**可见。修饰：类、接口、变量、方法。

###### protected

​	对**同一包内的类和所有子类**可见。修饰：变量、方法，**不能修饰类（外部类）**。

###### default

​	默认不用使用修饰符，**同一包内**可见。用于类、接口、变量、方法。

###### private

​	**在同一类内可见**。不能修饰类（外部类）。

###### 访问控制和继承

- 父类中声明为 public 的方法在子类中也必须为 public。
- 父类中声明为 protected 的方法在子类中要么声明为 protected，要么声明为 public，不能声明为 private。
- 父类中声明为 private 的方法，不能够被子类继承。

​	即：子类继承而来的方法的访问控制权限**不能低于**父类。



##### 非访问修饰符

###### final

​	final用来修饰类、方法、变量。意味着已经确定，不能再修改了。

​	即：final修饰的类不能被继承，修饰的方法不能被子类重新定义（因为声明final方法的目的就是防止方法内容被修改），修饰的变量为不可修改的常量。

​	在修饰**成员变量**时有以下几种方式：

1. 定义变量时直接赋值

   `private final int i = 22; `

2. 在类的构造函数中初始化，且每个构造方法中都要有该变量的赋值语句。

   ```java
    private final int j;  
       public FinalTest() {  
           j = 3;  
       }  
   	 //下面的代码同样会报错，因为对j重复赋值  
       /*public FinalTest1(String str1, String str2) { 
           this(); 
           j = 3; 
       }*/  
   ```

3. 声明变量后在代码块中赋值

   ```java
    	//如果采用此方式，就不能在构造方法中再次为其赋值  
       //构造代码块中的代码会在构造函数之前执行，如果在构造函数中再次赋值，  
       //就会造成final变量的重复赋值    
   private final int k;  
     
       {  
           k = 4;  
       }  
   ```

​	在修饰**类变量**时有以下几种方式：

1. 定义变量时直接赋值
2. 在静态代码块中赋值

```java
  	//初始化方式一，在定义类变量时直接赋值  
    public final static int p = 3;  
  
    //初始化方式二，在静态代码块中赋值  
    //成员变量可以在构造函数中赋值，但是类变量却不可以。  
    //因为成员变量属于对象独有，每个对象创建时只会调用一次构造函数，  
    //因此可以保证该成员变量只被初始化一次；  
    //而类变量是该类的所有对象共有，每个对象创建时都会对该变量赋值  
    //这样就会造成变量的重复赋值。  
    public final static int q;  
  
    static {  
        q = 3;  
    }
```

###### static

​	用来修饰类方法和类变量。

​	可直接使用`classname.variablename `和` classname.methodname` 的方式访问静态变量和静态方法。

* 静态变量：也称作类变量，指独立于对象的静态变量，静态变量只有一份拷贝。（因此局部变量不能被声明为static变量）。
* 静态方法：用来声明独立于对象的静态方法，在静态方法中不能使用类的**非静态变量**，而是从参数列表中得到数据进行操作。

​	final和static通常一起使用来创造类常量：

​	`public static final int BOXWIDTH = 6;`

###### abstract	

* 抽象类：声明抽象类的目的是为了将来对该类进行扩充，因此抽象类不能被实例化。

  * 若一个类包含抽象方法则必须声明为abstract，否则将出现编译错误。
  * 抽象类可以包含抽象和非抽象方法。

* 抽象方法：抽象方法没有具体实现，由子类提供（除非子类也是抽象类）。因此抽象方法不能被声明为static、final。

  抽象方法的声明以分号结尾，例如：**`public abstract sample();`**

​	注：1. abstract不能用来修饰变量、代码块、构造器

​			2. abstract不能用来修饰`private`私有方法(私有的方法子类是无法继承的，所以子类无法重写)、`static`静态方法、`final`的方法和类

###### synchronized

​	synchronized 关键字声明的**方法或代码块**同一时间只能被一个线程访问，解决的是多个进程间访问资源的同步性。可以应用于四个访问修饰符。

**synchronized最主要的三种使用方式**

1. 修饰实例方法：给当前对象实例加锁，线程进入同步代码前要获得当前对象实例的锁

   ```java
   synchronized void method() {
       //业务代码
   }
   ```

   

2. 修饰静态方法：给当前类加锁，作用于类的所有对象实例。进入同步代码前要获得 **当前 class 的锁**。

   ```java
   synchronized static void method() {
       //业务代码
   }
   ```



3. 修饰代码块：指定加锁对象，给定对象/类加锁。

   ```java
   synchronized(this) {
       //业务代码
   }
   ```

   **注：**尽量不要使用 `synchronized(String a)` 因为 JVM 中，**字符串常量池具有缓存功能！**

###### transient

​	序列化的对象包含被 transient 修饰的实例变量时，java 虚拟机(JVM)跳过该特定的变量。该修饰符包含在定义变量的语句中，用来预处理类和变量的数据类型。

###### volatile

​	一旦一个共享变量（类的成员变量、类的静态成员变量）被`volatile`修饰之后，那么就具备了两层语义：

1. 保证了不同线程对这个变量进行操作时的可见性，即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的。
   * `volatile` 修饰的成员变量在每次被线程访问时，都**强制**从共享内存中重新读取该成员变量的值，当成员变量发生变化时，会强制线程将变化值回写到共享内存。
   * 因此，在任何时刻，两个不同的线程总是看到某个成员变量的同一个值。

2. 禁止进行指令重排序。

​	`volatile`只提供了保证访问该变量时，每次都是从内存中读取最新值，并不会使用寄存器缓存该值，而是每次都从内存中读取。

​	而对该变量的修改，volatile并不提供原子性的保证。由于及时更新，很可能导致另一线程访问最新变量值，无法跳出循环的情况

​	因此，多线程下计数器必须使用锁保护。

#### 关键字

##### super

​	super可以用来调用父类的属性、方法以及构造器。

#### Obeject

##### 常用方法

```java
public final native Class<?> getClass()
    //native方法，用于返回当前运行时对象的Class对象，使用了final关键字修饰，故不允许	 子类重写。

public native int hashCode() 
    //native方法，用于返回对象的哈希码，主要使用在哈希表中，比如JDK中的HashMap。
    
public boolean equals(Object obj)
    //用于比较2个对象的内存地址是否相等，String类对该方法进行了重写用户比较字符串的值	 是否相等。

protected native Object clone() throws CloneNotSupportedException
    //naitive方法，用于创建并返回当前对象的一份拷贝。
    //一般情况下，对于任何对象 x，表达式 x.clone() != x 为true，			 x.clone().getClass() == x.getClass() 为true。
    //Object本身没有实现Cloneable接口，所以不重写clone方法并且进行调用的话会发生CloneNotSupportedException异常。

public String toString()
    //返回类的名字@实例的哈希码的16进制的字符串。建议Object所有的子类都重写这个方法。

public final native void notify()
    //native方法，并且不能重写。唤醒一个在此对象监视器上等待的线程(监视器相当于就是锁的概念)。如果有多个线程在等待只会任意唤醒一个。

public final native void notifyAll()
    //native方法，并且不能重写。跟notify一样，唯一的区别就是会唤醒在此对象监视器上等待的所有线程，而不是一个线程。

public final native void wait(long timeout) throws InterruptedException
    //native方法，并且不能重写。暂停线程的执行。注意：sleep方法没有释放锁，而wait方法释放了锁 。timeout是等待时间。

public final void wait(long timeout, int nanos) throws InterruptedException
    //多了nanos参数，这个参数表示额外时间（以毫微秒为单位，范围是 0-999999）。 所以超时的时间还需要加上nanos毫秒。

public final void wait() throws InterruptedException
    //跟之前的2个wait方法一样，只不过该方法一直等待，没有超时时间这个概念

protected void finalize() throws Throwable { }
	//实例被垃圾回收器回收的时候触发的操作

```

##### 重要方法

###### equals()

**equals()和"=="**

> Object 内部使用的就是`==`比较运算符。
>
> ​	`==`在基本数据类型比较的是值，引用数据类型比较的是地址值。所以 Object类中就是使用两个对象的内存地址在比较。在开发经常需要子类重写equals方法根据对象的属性值进行比较。 

​	String、Date、包装类等，重写了 Object 的 equals 方法。

###### hashCode()



###### toString()

​	toString()方法返回该对象的字符串的表现形式，该字符串内容为：

​	`对象的类型名+@+16进制hashCode值`

​	getClass().getName() + "@" + Integer.toHexString(hashCode())。

###### clone()

​	clone() 是 Object 的 protected 方法因此如果一个类不显式去重写 clone()，其它类就不能直接去调用该类实例的 clone() 方法。

​	**注：** clone() 方法并不是 Cloneable 接口的方法而是 Object 的一个 protected 方法。

​	Cloneable 接口只是规定，如果一个类没有实现 Cloneable 接口又调用了 clone() 方法，就会抛出 CloneNotSupportedException。

##### Objects类

​	在**JDK7**添加了一个`java.util.Objects`工具类，它提供了一些方法来操作对象，它由一些静态的实用方法组成，这些方法是 null-save（**空指针安全**的）或null-tolerant（**容忍空指针**的），用于计算对象的hashcode、返回对象的字符串表示形式、比较两个对象。

​	在比较两个对象的时候，Object的equals方法容易抛出空指针异常，而Objects类中的equals方法就优化了这个问题。方法如下：

```java
// 判断两个对象是否相等
public static boolean equals(Object a, Object b) {  
    return (a == b) || (a != null && a.equals(b));  
}
```

> [Object类概述]: https://www.yuque.com/deltaqin/klzn0g/ybpfy0



#### 浅拷贝和深拷贝

​	在java中对利用`=`做赋值操作时有两种情形：

* 对基本数据类型的赋值操作相当于直接拷贝赋值
* 对引用类型而言，赋值操作则是引用的赋值，使得两者指向同一个对象	

​	深拷贝浅拷贝在此基础上区分，如果在拷贝对象的过程中只是进行了引用的传递，没有创建新的对象则为**浅拷贝**，反之若**创建了新的对象并复制其成员变量**，则为**深拷贝**。

​	浅拷贝：

<img src="https://s2.loli.net/2022/06/23/6qEG8J4dPZIgf9T.png" alt="image-20220623172306654" style="zoom: 50%;" />

​	深拷贝：

​	<img src="https://s2.loli.net/2022/06/23/khAoyTx4CLVINiJ.png" alt="image-20220623172346675" style="zoom: 50%;" />



##### Object的clone()方法

<img src="C:\Users\Tangerine\AppData\Roaming\Typora\typora-user-images\image-20220623172711896.png" alt="image-20220623172711896" style="zoom: 33%;" />

​	在 Object 上存在一个 clone() 方法，它被声明为了 `protected `，所以我们可以在其子类中使用它。无论是浅拷贝还是深拷贝，都需要实现 clone() 方法，来完成拷贝操作。



<img src="https://segmentfault.com/img/remote/1460000010648522" alt="/clone-cloneable.png" style="zoom:33%;" />

​	而对于 **Cloneable** 接口其实什么方法都不需要实现。可以简单地理解只是一个标记，标记开发者允许这个对象被拷贝。



> [细说 Java 的深拷贝和浅拷贝]: https://segmentfault.com/a/1190000010648514



#### 反射

##### Reflection概念

> Reflection enables Java code to discover information about the **fields, methods and constructors** of loaded classes, and to use reflected fields, methods, and constructors to operate on their underlying counterparts, within security restrictions.
> The API accommodates applications that need access to either the public members of a target object (based on its runtime class) or the members declared by a given class. It also allows programs to suppress default reflective access control.

​	反射是动态语言的关键，反射机制允许程序在**执行期**借助于Reflection API取得任何类的内部信息，并能直接操作任意对象的**内部属性及方法**。

​	每个类在**类加载**完后会在堆内存的方法区生成**仅且一个**Class类型的对象，这个Class对象包含了类完整的结构信息，通过该对象看到类的结构，称之为反射。

<img src="https://s2.loli.net/2022/05/14/zLI4EUDHvgjlwXi.png" alt="image-20220514103244909" style="zoom: 80%;" />

###### Class类的加载过程

1. 执行javac.exe命令后，生成一个或多个字节码文件(.class文件)
2. 接着执行java.exe命令，对某个字节码文件进行解释运行，即将字节码文件加载到内存中，该过程称为类的加载
3. 加载到内存中的类被称为运行时类，该类就作为Class的一个实例

###### 获取Class对象的四种方式

​	我们需要依靠Class对象来动态获取类的方法、变量等信息(先获得Class对象才能获取Mthod、Constructor、Field)，Java提供了以下四种方式获取Class对象：

1. 知道具体类的情况下：

​	java中任何一种数据类型，包括基本数据类型，都有.class属性。

```java
	Class clazz = TargetObject.class;
	Class s = String.class;
```

2. 通过`Class.forName()`传入类的路径

​	这个方法的执行会导致类加载，若类包含静态代码块则静态代码块也会被执行。

```java
	Class clazz1 = Class.forName("com.javatest.TargertObject");
```

3. 通过对象实例instance.getClass()

```java
	TargetObject o = new TargetObject;
	Class clazz2 = o.getClass();
```

4. 通过类加载器`xxxClassLoader.loadClass()`传入类路径

```java
	Class clazz = ClassLoader.loadClass("com.javatest.TargertObject");
```

​	通过类加载器获取 Class 对象**不会进行初始化**，意味着不进行包括初始化等一些列步骤，**静态块和静态对象**不会得到执行。

##### 动态语言

​	是一类在运行时可以根据某些条件改变自身结构的语言。例如：新的函数、对象、代码被引进后，已有的函数被删除或是发生其他结构上的变化。

​	常见的动态语言有：Object-C、C#、JavaScript、PHP、Python、Erlang。

##### 静态语言

​	与动态语言相比，运行时结构不可改变的称为静态语言。

​	常见的有：Java、C、C++。

​	虽然Java不是动态语言，但Java可以利用反射机制、字节码操作获得类似动态语言的特性。

##### 反射机制提供的功能

* 在运行时判断一个对象所属的类
* 在运行时构造任意一个类的对象
* 在运行时判断任意一个类所具有的成员变量和方法
* 在运行时获取泛型信息
* 在运行时调用任意一个对象的成员变量和方法
* 在运行时处理注解
* 生成动态代理

​	因此，反射的重点在于**运行时**。

反射相关的主要API有：

* `java.lang.Class`：代表一个类
* `java.lang.reflect.Method`：代表类的方法
* `java.lang.reflect.Field`：代表类的成员变量
* `java.lang.reflect.Constructor`:代表类的构造器

##### 反射机制有什么用？

* 可以操作字节码文件
* 使程序更灵活(只需要修改相关配置文件，不需要修改java程序就可以得到不同类的实例)

#### I/O流

##### 序列化和反序列化

* 序列化：将数据结构或对象转换为二进制字节流

* 反序列化：将序列化产生的二进制字节流转换成数据结构或对象

​	序列化的目的：**通过网络传输对象或为了将对象存储到文件系统、数据库、内存中。**

<img src="https://s2.loli.net/2022/06/22/yFjaLQs5HDUGgWe.png" alt="image-20220622140345386" style="zoom:50%;" />

##### transient关键字

​	实现了实现了`Serilizable`接口的对象就可以被序列化，不想序列化的变量可以使用`transient`关键字修饰。eg.用户的一些敏感信息(密码等)不希望在网络中操作、本地序列化缓存，可以将这些信息加上`transient`修饰，这个字段的生命周期仅存于调用者的内存中而不会写到磁盘里持久化。

**作用**：当对象被序列化后，被`transient`修饰的变量值不会恢复和被持久化。

##### IO流分类

* InputStream/Reader: 所有的输入流的基类，前者是字节输入流，后者是字符输入流。
* OutputStream/Writer: 所有输出流的基类，前者是字节输出流，后者是字符输出流。

**为什么 I/O 流操作要分为字节流操作和字符流操作？**

​	字符流是由 Java 虚拟机将字节转换得到的并且这个过程是非常耗时的，而且如果我们不知道编码类型就很容易出现乱码问题。所以 I/O 流提供了一个直接操作字符的接口，方便我们平时对字符进行流操作。

​	因此，音频文件、图片等媒体文件用字节流比较好，涉及到字符的话使用字符流比较好。

#### 并发编程

##### 进程

​	进程是系统分配资源的基本单位，是程序的一次执行过程，包含了进程的创建、运行到消亡的过程。

##### 线程

​	线程是系统执行的基本单位，一个进程可以包含多个线程，线程也被称为轻量级进程。

* 同类的多个线程共享进程的**堆和方法区**(**JDK1.8 后的元空间**)资源
* 每个线程有自己的**程序计数器、虚拟机栈和本地方法栈**

###### Thread类

​	JVM通过`java.lang.Threand`类支持多线程，该类有以下特点：

* 每个线程都是通过某个特定`Thread`对象的`run()`方法来完成操作的，`run()`方法的主体也被称为线程体
* 通过该`Thread`对象的`start()方`法来启动这个线程，而非直接调用`run()`

###### 线程优先级

​	Java线程的优先级是一个整数，取值范围由1-10（Thread.MIN_PRIORITY - Thread.MAX_PRIORITY ）。默认情况下每个线程被分配的优先级为 NORM_PRIORITY（5）。数值**越大**意味着**优先级越高**。

​	注：并不是所有的操作系统都⽀持10 级优先级的划分（⽐如有些操作系统只⽀持3级划分：低，中，高），Java只是给 操作系统⼀个优先级的参考值，线程最终在操作系统的优先级是多少还是由**操作系统**决定。

###### 通过实现`Runnable`接口

1. 创建实现`Runnable`接口的类
2. 实现类实现`Runnable`中的抽象方法`run()`
3. 创建实现类对象
4. 将此对象作为参数传递到`Thread`类的构造器中，创建`Thread`类的对象
5. 通过`Thread`类的对象调用`start()`

```java
	 	MThread m1 = new MThread();
        //4.将此对象作为参数传递到Thread类的构造器中，创建Thread类的对象
        Thread t1 = new Thread(m1);
        //5.通过Thread类的对象调用start():①启动线程 ②调用当前线程的run() --> 调用了Runnable类型的target的run()
        t1.start();
```

###### 通过继承`Thread`类

```java
public class CreateThread {
	public static void main(String[] args) {
		Thread myThread = new MyThread();
        // 启动线程
		myThread.start();
	}
}

class MyThread extends Thread {
	@Override
	public void run() {
		System.out.println("my thread running...");
	}
}
```

​	过程：

1. 创建一个继承于`Thread`类的子类
2. 重写Thread的run()方法(将此线程的方法声明在run()中)
3. 创建`Thread`类的子对象
4. 通过此对象调用`start()`

​	使用继承方式的好处是在run（）方法内获取当前线程直接使用this就可以了，无须使用Thread.currentThread（）方法；

​	不好的地方是Java不支持多继承，如果继承了Thread类，那么就不能再继承其他类。另外任务与代码没有分离，当多个线程执行一样的任务时需要多份任务代码。

###### 使用Runnable配合Thread(推荐)

```java
public class Test2 {
	public static void main(String[] args) {
		//创建线程任务
		Runnable r = new Runnable() {
			@Override
			public void run() {
				System.out.println("Runnable running");
			}
		};
		//将Runnable对象传给Thread
		Thread t = new Thread(r);
		//启动线程
		t.start();
	}
}
```

**Thread 与 Runnable 的关系**

- 继承Thread 是把线程和任务合并在了一起
- Runnable 是把线程和任务分开了
- 用 Runnable 更容易与线程池等高级 API 配合 用 Runnable 让任务类脱离了 Thread 继承体系，更灵活

###### 通过`Callable`((JDK5.0新增))和`Future`创建线程

1. 创建一个实现Callable的实现类
2. 实现call方法，将此线程需要执行的操作声明在call()中
3. 创建Callable接口实现类的对象
4. 将此Callable接口实现类的对象作为参数传递到FutureTask构造器中，创建FutureTask的对象
5. 将FutureTask的对象作为参数传递到Thread类的构造器中，创建Thread对象，并调用start()
6. 获取Callable中call方法的返回值

```java
  		//3.创建Callable接口实现类的对象
        NumThread numThread = new NumThread();

        //4.将此Callable接口实现类的对象作为传递到FutureTask构造器中，创建FutureTask的对象
        FutureTask futureTask = new FutureTask(numThread);

        //5.将FutureTask的对象作为参数传递到Thread类的构造器中，创建Thread对象，并调用start()
        new Thread(futureTask).start();

        try {
            //6.获取Callable中call方法的返回值
            //get()返回值即为FutureTask构造器参数Callable实现类重写的call()的返回值。
            Object sum = futureTask.get();
            System.out.println("总和为:" + sum);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
```

###### 使用线程池创建

常见API

* JDK 5.0起提供了线程池相关API：**`ExecutorService`和`Executors`**
  * `ExecutorService`：真正的线程池接口。常见子类`ThreadPoolExecutor`
    * `void execute(Runnable command)` ：执行任务/命令，没有返回值，一般用来执行`Runnable`
    * `Future submit(Callable task)`：执行任务，有返回值，一般又来执行`Callable`
    * `void shutdown()`：关闭连接池
  * `Executors`：工具类、线程池的工厂类，用于创建并返回不同类型的线程池
    * `Executors.newCachedThreadPool()` `Executors.newSingleThreadExecutor()`...

###### 继承和实现方式的对比

> 比较创建线程的两种方式。
>
>  *  开发中：优先选择实现**Runnable**接口的方式
> *  原因：
>  *  1. 实现的方式没有**类的单继承性的局限性**
>  *  2. 实现的方式更适合来处理**多个线程有共享数据**的情况。
> 
> 联系：public class Thread implements Runnable
>
>  *  相同点：两种方式都需要重写run(),将线程要执行的逻辑声明在run()中。
>     



###### 使用线程池的好处

​	**问题场景：**经常创建和销毁、使用量特别大的资源，比如并发情况下的线程，对性能影响很大。

​	**解决思路：**提前创建好多个线程，放入线程池中，使用时直接获取，使用完放回池中。可以避免频繁创建销毁、实现重复利用。类似生活中的公共交通工具。

​	**好处：**

* 提高了响应速度(减少了创建新线程的时间)
* 降低资源消耗(重复利用线程池中线程，不需要每次创建)
* 便于线程管理
  * `corePoolSize`：核心池的大小
  * `maximumPoolSize`：最大线程数
  * `keepAliveTime`：线程没有任务时最多保持多长时间后会终止

###### 线程的生命周期

<img src="https://s2.loli.net/2022/05/24/6f8Jxh4aFYuA7OQ.png" alt="image-20220524111012078" style="zoom: 33%;" />

1. 新建：当一个`Thread`类或其子类的对象被声明并创建时，新生的线程对象处于新建状态。
2. 就绪：处于新建状态的线程被`start()`后，将进入线程队列等待CPU时间片，此时它已具备了运行的条件，只是没分配到CPU资源。
3. 运行：当就绪的线程被调度并获得CPU资源时,便进入运行状态，`run()`方法定义了线程的操作和功能。
4. 阻塞：在某种特殊情况下，被人为挂起或IO中断时，让出CPU并临时中止自己的执行，进入阻塞状态。
5. 死亡：程完成了它的全部工作或线程被提前强制性地中止或出现异常导致结束。

##### 线程的同步

​	多个线程操作同一资源时会导致线程安全的问题，在java中我们通过同步机制来解决。

1. 同步代码块：

   ```java
   	synchronized(同步监视器) {
   		//需要被同步的代码
   	}
    *  说明：1.操作共享数据的代码，即为需要被同步的代码 ---> 不能包含代码多了，也不能包含代码少了。
    *       2.共享数据：多个线程共同操作的变量。比如：售票厅的票就是共享数据
    *       3.同步监视器，俗称：锁。任何一个类的对象，都可以来充当锁。
    *          要求：多个线程必须要共用同一把锁。
    *
    *       补充：在实现Runnable接口创建多线程的方式中，我们可以考虑使用this充当同步监视器。
   ```

   

2. 同步方法：如果操作共享数据的代码完整的声明在一个方法中，我们不妨将此方法声明同步

###### 同步的好处和局限

* 优点：解决了线程的安全问题
* 缺点：操作同步代码时只能有一个线程参与其他线程等待，此时相当于是单线程，效率降低

##### 线程死锁

​	不同的线程分别持续占用对方需要的同步资源，双方都在等待对方放弃自身所需的同步资源，就形成了线程的死锁。出现死锁后，不会出现异常，不会出现提示，只是所有的线程都处于阻塞状态，无法继续。

###### 产生死锁的四个必要条件

1. 互斥条件：该资源任意一个时刻只由一个线程占用
2. 请求与保持条件：一个线程因请求资源而阻塞时不会释放已经获得的资源。
3. 不剥夺条件：线程已获得的资源在未使用完之前不能被其他线程强行剥夺，只能自己释放。
4. 循环等待条件：若干线程之间形形成一种头尾相接的循环等待资源关系。

###### 如何避免

##### 常用方法

###### start()和run()

​	**调用 Thread.start 方法会开一个新的线程，run 方法不会。**

​	被创建的Thread对象直接调用重写的run方法时， run方法是在**主线程**中被执行的，而不是在我们所创建的线程中执行。如果想要在所创建的线程中执行run方法，**需要使用Thread对象的start方法。**

###### sleep()和yield()

**sleep()**

* 调用 sleep( )会让当前线程从` Running` 进入 `Timed Waiting` 状态（阻塞），可通过state()方法查看。
* 其它线程可以使用 **interrupt** 方法打断正在睡眠的线程，这时 sleep 方法会抛出 `InterruptedException`。
* 睡眠结束后的线程未必会立刻得到执行
* 建议用 **TimeUnit 的 sleep** 代替 Thread 的 sleep 来获得更好的可读性。

```java
//休眠一秒
TimeUnit.SECONDS.sleep(1);
//休眠一分钟
TimeUnit.MINUTES.sleep(1);
```

**yield()**

* 调用 yield 会让出当前线程，**yield 方法表示当前线程放弃 cpu，重新参与到 cpu 的竞争中去**，再次竞争时，自己有可能得到 cpu 资源，也有可能得不到，这样做的好处是防止当前线程一直霸占 cpu。内部调⽤的是 `Object`类的`wait`⽅法实现的.
* 当前线程从 `Running` 进入 `Runnable` 就绪状态（仍然有可能被执行），然后调度执行其它线程。
* 具体的实现依赖于操作系统的任务调度器。

###### join()

​	用于等待某个线程结束。哪个线程内调用join()方法，就等待哪个线程结束，然后再去执行其他线程。

```java
Thread thread = new Thread();
//等待thread线程执行结束
thread.join();
//最多等待1000ms,如果1000ms内线程执行完毕，则会直接执行下面的语句，不会等够1000ms
thread.join(1000);
```

###### interrupt()

​	用于打断**阻塞**状态(sleep, wait, join…)的线程。 处于阻塞状态的线程，CPU不会给其分配时间片。

* 如果一个线程在在运行中被打断，打断标记会被置为true。
* 如果是打断因sleep wait join方法而被阻塞的线程，会将打断标记置为false。

```java
//用于查看打断标记，返回值被boolean类型
t1.isInterrupted();
```

##### 线程的状态

###### 五种状态

​	从操作系统的层面来描述有五种状态。

