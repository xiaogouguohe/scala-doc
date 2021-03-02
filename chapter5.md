# 第五章 Scala基础——类和对象

## 5.1 类

- 一个例子

```scala
scala> class Students {
         |    var name = "None"
         |    def register(n: String) = name = n
         |  }
defined class Students

scala> val stu = new Students
stu: Students = Students@1a2e563e

scala> stu.name
res0: String = None

scala> stu.register("Bob")

scala> stu.name
res2: String = Bob
```

- 类成员默认都是共有的

- 如果不想某个成员被外部访问，可以在前面加上"private"修饰

## 5.2 类的构造方法

### 5.2.1 主构造方法

- 一个例子

  ```scala
  scala> class Students(n: String) {
           |    val name = n
           |    println("A student named " + n + " has been registered.")
           |  }
  defined class Students
  
  scala> val stu = new Students("Tom")
  A student named Tom has been registered.
  stu: Students = Students@5464eb28
  ```

- 不用额外定义构造函数

### 5.2.2 辅助构造方法

- 除了主构造方法，还可以定义若干个辅助构造方法

  - 辅助构造方法都是以"def this(...)"开头的
  - 辅助构造方法的第一条语句必须是"this(...)"，即要么是主构造方法，要么是另一个辅助构造方法
  - 因此，任何构造方法最后都会调用主构造方法

- 例子

  ```scala
  scala> class Students(n: String) {
           |    val name = n
           |    def this() = this("None")
           |    println("A student named " + n + " has been registered.")
           |  }
  defined class Students
  
  scala> val stu = new Students
  A student named None has been registered.
  stu: Students = Students@74309cd5
  ```

- 只允许主构造方法调用超类的构造方法（见后续章节）

### 5.2.3 析构函数

- Scala没有指针，垃圾自动回收，因此不需要析构函数

### 5.2.4 私有主构造方法

- 例子

  ```scala
  scala> class Students private (n: String, m: Int) {
           |    val name = n
           |    val score = m
           |    def this(n: String) = this(n, 100)
           |    println(n + "'s score is " + m)
           |  }
  defined class Students
  
  // 主构造方法是私有的，不能这样访问
  // scala> val stu = new Students("Bill", 90)
  
  // 这样是可以的，调用辅助构造方法
  scala> val stu = new Students("Bill")
  Bill's score is 100
  stu: Students = Students@7509b8e7
  ```

## 5.3 重写ToString方法

- 5.2.1中的例子，构造类的对象时，会打印"Students@xxx"，来着来自于Students类的toString方法
  - 该方法返回一个字符串，并在构造玩对象一个对象时被自动调用
  - 该方法是所有Scala类隐式继承来的，不重写就会用默认继承的版本
  - 重写必须加上关键字"override"（见后续章节）

- 例子

  ```scala
  scala> class Students(n: String) {
           |    val name = n
           |    override def toString = "A student named " + n + "."
           |  }
  defined class Students
  
  scala> val stu = new Students("Nancy")
  stu: Students = A student named Nancy.
  ```

## 5.4 方法重载

## 5.5 类参数

- 很多时候，类参数只是为了赋给类的某些字段

- 为了简化代码，允许在类参数前加上val或var来修饰，这样会在类的内部生成一个与参数同名的公有字段

- 也可以加上关键字private, protected, override来表明字段权限（权限见后续章节）

- 例子

  ```scala
  scala> class Students(val name: String, var score: Int) {
      	 // Students类会直接生成name和score成员
           |    def exam(s: Int) = score = s
           |    override def toString = name + "'s score is " + score + "."
           |  }
  defined class Students
  
  scala> val stu = new Students("Tim", 90)
  stu: Students = Tim's score is 90.
  ```

## 5.6 单例对象与伴生对象

### 5.6.1 单例对象

- 除了可以用new构造一个对象，也可以用"object"开头定义一个对象
- new构造的对象是某个类的实例，而且数量没限制，而object定义的对象只有这一个，因此称为“单例对象”

- 单例对象和new实例的对象一样， 可以在类内定义的代码，同样可以在单例对象内定义
  - 单例对象里可以定义字段和方法
  - 单例对象可以包含别的类和单例对象的定义
- 单例对象的常见用途
  - 用作伴生对象
  - 打包某方面功能的函数系列成为工具集
  - 包含主函数，成为程序入口

### 5.6.2 伴生对象

- 如果某个单例对象和某个类同名，那么互为“伴生对象”和“伴生类”
- 必须在同一个文件里，可以互相访问成员
- 静态变量
  - 在cpp, java等oop语言里， 类内部可以定义静态变量，这些静态变量不属于任何对象
  - Scala追求纯粹的面向对象，所有的事物都是类和对象，但是静态变量不属于类也不属于对象，因此把静态变量集中定义在伴生对象里

### 5.6.3 例子

```scala
scala> object B { val b = "a singleton object" }
defined object B

scala> B.b
res2: String = a singleton object

// 单例对象可以这样赋给一个变量
scala> val y = B
y: B.type = B$@4489b853

scala> y.b
res3: String = a singleton object
```

### 5.6.4 单例对象和类型

- 定义一个类，就定义了一个类型

- 定义一个伴生对象，并没有定义一个类型（单例对象有自己独特的类型，即使是其它单例对象，类型也是不同的， 不像类那样，很多个对象都是这个类的类型）

- 例子

  ```scala
  scala> object X
  defined object X
  
  scala> object Y
  defined object Y
  
  scala> var x = X
  x: X.type = X$@630bb67
  
  // X和Y类型不同，不能这样赋值
  scala> x = Y
  <console>:17: error: type mismatch;
   found   : Y.type
   required: X.type
         x = Y
             ^
  ```

## 5.7 工厂对象和工厂方法

- 如果定义一个方法专门用来构造某一个类的对象，那么这种方法就称为“工厂方法”

- 包含这些工厂方法集合的单例对象，称为“工厂对象”

- 这样的对象通常是伴生对象，尤其是当一系列类存在继承关系是，可以在基类的伴生对象里定义一系列对应的工厂方法

- 工厂方法的好处是，可以不用直接new来实例化对象，改用方法调用，方法名自定义，这样就对外隐藏了类的实现细节

- 例子

  ```scala
  class Students(val name: String, var score: Int) {
    def exam(s: Int) = score = s
    override def toString = name + "'s score is " + score + "."
  }
   
  object Students {
    def registerStu(name: String, score: Int) = new Students(name, score)
  }
  ```

## 5.8 apply方法

- 定义apply方法，可以显式调用，也可以隐式调用

- 通常，在伴生对象里定义的apply方法，会是工厂方法，来构造伴生类的对象

- 在伴生类里面也可以定义apply方法，来实现用户想要的一些行为

- 例子

  ```scala
  // students2.scala
  class Students2(val name: String, var score: Int) {
    def apply(s: Int) = score = s
    def display() = println("Current score is " + score + ".")
    override def toString = name + "'s score is " + score + "."
  }
   
  object Students2 {
      // 伴生对象里的apply方法通常是工厂方法，构造伴生类的对象
    def apply(name: String, score: Int) = new Students2(name, score)
  }
  
  // 编译上述文件后
  // 实际上是Students2.apply("Jack", 60)，调用伴生对象的apply方法
  scala> val stu2 = Students2("Jack", 60)
  stu2: Students2 = Jack's score is 60.
  
  // 实际上是Students2.apply(80)，调用伴生类的apply方法
  scala> stu2(80)
  
  scala> stu2.display
  Current score is 80.
  ```

## 5.9 主函数

- 主函数是Scala程序唯一入口

- 要提供这样的入口，必须在某个单例对象里定义一个"main"函数，且参数类型和返回类型固定，见例子

- 例子

  ```scala
  // students2.scala
  class Students2(val name: String, var score: Int) {
    def apply(s: Int) = score = s
    def display() = println("Current score is " + score + ".")
    override def toString = name + "'s score is " + score + "."
  }
   
  object Students2 {
    def apply(name: String, score: Int) = new Students2(name, score)
  }
   
  // main.scala
  object Start {
    def main(args: Array[String]) = { // 参数类型为Array[String]，返回类型为Unit
      try {
        val score = args(1).toInt
        val s = Students2(args(0), score)
        println(s.toString)
      } catch {
        case ex: ArrayIndexOutOfBoundsException => println("Arguments are deficient!")
        case ex: NumberFormatException => println("Second argument must be a Int!")
      }
    }
  }
  ```

  - 编译和使用

    ```bash
    scalac students2.scala main.scala
    scala Start Tom 100
    ```

- 简化写法

  - 让单例对象混入"App"特质（后续章节），这样就只需要在单例对象里编写主函数的函数体

  - 例子

    （先看App特质）###

