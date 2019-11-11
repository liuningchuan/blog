---
title: "Java知识总结"
tags:
- Java
categories:
- Java
catalog: true
cover: "/images/header_img/header-8.jpg"
date: 2018-3-18 20:51:24
---

- **JVM中虚拟栈中存的是对象的地址（引用），创建的对象实质在堆中。**

- **java中的接口支持多继承，即一个子接口可以有多个父接口。**

- **Jsp是java开发的专门用于动态显示页面的技术。jsp编译时首先编译成servlet文件，然后编译成class文件。**
  包含9大内置对象：

  > response、request、exception、out、application、pagecontext、config、session、page。

  有四个域对象：

  > request、pagecontext、session、application

- **多态：父类的引用指向子类的实例。**

- **Comparable & Comparator 都是用来实现集合中元素的比较、排序**

  只是 Comparable 是在集合内部定义的方法实现的排序，Comparator 、是在集合外部实现的排序，

  所以，如想实现排序，就需要在集合外定义 Comparator 接口的方法或在集合内实现 Comparable 接口的方法。

  Comparator位于包java.util下，而Comparable位于包 java.lang下 Comparable 是一个对象本身就已经支持

  自比较所需要实现的接口（如 String、Integer 自己就可以完成比较大小操作，已经实现了Comparable接

  口） 自定义的类要在加入list容器中后能够排序，可以实现Comparable接口，

  在用Collections类的sort方法排序时，如果不指定Comparator，那么就以自然顺序排序， 这里的自然顺序就

  是实现Comparable接口设定的排序方式。

  而 Comparator 是一个专用的比较器，当这个对象不支持自比较或者自比较函数不能满足你的要求时，你可

  以写一个比较器来完成两个对象之间大小的比较。

  可以说一个是自已完成比较，一个是外部程序实现比较的差别而已。 

  用 Comparator 是策略模式（strategy design pattern），就是不改变对象自身，而用一个策略对象

  （strategy object）来改变它的行为。 比如：你想对整数采用绝对值大小来排序，Integer 是不符合要求的，

  你不需要去修改Integer 类（实际上你也不能这么做）去改变它的排序行为，只要使用一个实现了 

  Comparator 接口的对象来实现控制它的排序就行了。

- **Java中的length属性是针对数组**

  比如说你声明了一个数组,想知道这个数组的长度则用到了length这个属性.
     Java中的length()方法是针对字符串String说的,如果想看这个字符串的长度则用到length()这个方法.
     Java中的size()方法是针对泛型集合说的,如果想看这个泛型有多少个元素,就调用此方法来查看!

7、super和this的异同
	super（参数）：调用基类中的某一个构造函数（应该为构造函数中的第一条语句） 
	this（参数）：调用本类中另一种形成的构造函数（应该为构造函数中的第一条语句）
	super:　它引用当前对象的直接父类中的成员（用来访问直接父类中被隐藏的父类中成员数据或函数，基类与派生类中有相同成员定义时如：
            super.变量名    super.成员函数据名（实参）
	this：它代表当前对象名（在程序中易产生二义性之处，应使用this来指明当前对象；如果函数的形参与类中的成员数据同名，这时需用this来
            指明成员变量名）
	调用super()必须写在子类构造方法的第一行，否则编译不通过。每个子类构造方法的第一条语句，都是隐含地调用super()，如果父类没有这
            种形式的构造函数，那么在编译的时候就会报错。
	super()和this()类似,区别是，super()从子类中调用父类的构造方法，this()在同一类内调用其它方法。
	super()和this()均需放在构造方法内第一行。
	

    尽管可以用this调用一个构造器，但却不能调用两个。
    
    this和super不能同时出现在一个构造函数里面，因为this必然会调用其它的构造函数，其它的构造函数必然也会有super语句的存在，所以
    在同一个构造函数里面有相同的语句，就失去了语句的意义，编译器也不会通过。
    
    this()和super()都指的是对象，所以，均不可以在static环境中使用。包括：static变量,static方法，static语句块。
    从本质上讲，this是一个指向本对象的指针, 然而super是一个Java关键字
    1.由于构造器的名字必须与类名相同，而匿名类没有类名，所以匿名类不能有构造器。

8.匿名内部类的创建格式为： new 父类构造器（参数列表）|实现接口（）{
                                             //匿名内部类的类体实现
                                             }
    使用匿名内部类时，必须继承一个类或实现一个接口
    匿名内部类由于没有名字，因此不能定义构造函数
    匿名内部类中不能含有静态成员变量和静态方法 

    （1）对于外部类而言，它也可以使用访问控制符修饰，但外部类只能有两种访问控制级别： public 和默认。
     因为外部类没有处于任何类的内部，也就没有其所在类的内部、所在类的子类两个范围，因此 private 和 
    protected 访问控制符对外部类没有意义。
    （2）内部类的上一级程序单元是外部类，它具有4个作用域：同一个类（private）、同一个包（protected）
    和任何位置（ public ）。
    （3）因为局部成员的作用域是所在方法，其他程序单元永远不可能访问另一个方法中的局部变量，所以所有的
    局部成员都不能使用访问控制修饰符修饰

 9.可以把局部内部类当做一个局部变量，所以它是不需要加任何修饰符的局部内部类前不能用修饰符public和private,protected

 10.成员变量有初始值，而局部变量没有初始值得.