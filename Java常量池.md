# java常量池的深入学习

----------

## 相关概念

**概念**：
* 常量：用final修饰的成员变量表示常量，值一旦确定，就无法改变。
> final修饰的三种变量：**静态变量、成员变量、局部变量** 

**认识**：
* Class文件中的常量池: **字面量、符号引用量**

* 符号引用量：**类和接口的全限定名、字段名称和描述符、方法名称和描述符**


* 方法区中的运行时常量池
  1. 运行时常量池是方法区的一部分。
  2. Class文件中的`常量池`，将在类加载后进入方法区的运行时常量池中存放。
  3. 具备动态性：运行期间也可能将新的常量放入池中，用例：`String`类的`intern()`方法。


* 常量池的好处：
  实现了对象的共享，避免频繁的创建和销毁对象而影响系统性能。
	* 节省内存空间：所有相同的字符串常量被合并，只占用一个空间
	* 节省运行时间：比较字符串时，==比equals()快。


* 双等号==的含义
  * 基本数据类型之间应用双等号，比较的是他们的数值。
  * 复合数据类型(类)之间应用双等号，比较的是他们在内存中的存放地址。


## 8种基本类型的包装类与常量池的故事

### 基本类型的包装类基本都实现了常量池技术
> Byte、Short、Integer、Long、Character、Boolean

这几种包装类默认创建的数值在[-128,127]的相应类型的缓存数据，但是超出这个范围，就会新建对象。

> Float,Double没有实现常量池技术

### 常量池之用武之地

#### 基本类型和常量池

1. `Integer a=100;`和`Integer a=Integer.valueOf(100);`都是一样的，都是取的*常量池*中的对象。

2. `Integer b = new Integer(100);`这个创建的可是一个新对象，而不是从*常量池*中拿的。

3. 来个复杂的

```java
Integer a1 = 100;
Integer a2 = 100;
Integer a3 = 0;
Integer a4 = new Integer(100);
Integer a5 = new Integer(100);
Integer a6 = new Integer(0);

System.out.println(a1==a2);// true
System.out.println(a1==a2+a3);// true
System.out.println(a1==a4);// false
System.out.println(a4==a5);// false
System.out.println(a4==a5+a6);// true
System.out.println(40==a5+a6);// true
```
上面的算数运算符`+`与逻辑运算符`==`利用了java自动拆箱的特性，所以`a4`与`a5+a6`计算与比较时，都自动拆箱了，最后都是int基本类型在计算与比较。

#### String类和常量池
* String类创建方式

```java
String name1 = "zhangsan";
String name2 = new String("zhangsan");

System.out.println(name1a4==name2);// false
```
`name1`指向的对象是从*常量池*中拿的，`name2`指向的对象是直接在堆内存空间中创建的一个新对象。***只要用new关键字创建的对象都是新的对象。***

* 连接表达式 +
只有使用引号包含文本的方式创建的String对象之间使用连接运算符`+`产生的新对象，才会被加入字符串*常量池*。需要注意的是，如果用new关键字创建的String对象引用通过`+`连接而产生的新字符串对象，不会被加入字符串*常量池*。

	* 公式： 既定常量 + 既定常量 = 常量池中驻留对象 

* `String s1 = new String("xyz");`**创建了几个对象？** 
两个对象：
（1）类加载对一个类只会进行一次"xyz"创建,并驻留在*常量池*中；
（2）运行期间，从*常量池*中把"xyz"复制到堆内存上，并且把堆内存上的对象引用用指向栈内存上的s1变量。

* java.lang.String.intern()
String的`intern()`方法会查找在常量池中是否存在一份equal相等的字符串,如果有则返回该字符串的引用,如果没有则添加自己的字符串进入常量池。

* 字符串比较更丰富的一个例子

```java
//链接：https://www.jianshu.com/p/c7f47de2ee80
public class Test { 
	public static void main(String[] args) { 
		String hello = "Hello", lo = "lo"; 
		System.out.println((hello == "Hello") + " "); 
		System.out.println((Other.hello == hello) + " "); 
		System.out.println((other.Other.hello == hello) + " "); System.out.println((hello == ("Hel"+"lo")) + " "); 
		System.out.println((hello == ("Hel"+lo)) + " "); 
		System.out.println(hello == ("Hel"+lo).intern()); 
	} 
}

class Other { 
	static String hello = "Hello"; 
}

package other;
public class Other { 
	public static String hello = "Hello"; 
}
```