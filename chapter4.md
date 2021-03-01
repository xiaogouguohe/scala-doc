# 第四章 Scala基础——函数及其几种形式

## 4.1 定义一个函数

- 一个简单的例子

```scala
def max(x: Int, y: Int): Int = {
    if (x > y)
    	x
    else 
    	y
}
```

### 4.1.1 分号推断

- 语句末尾的分号可选，因为编译器会自动推断分号
- 如果一行有多条语句，必须用分号隔开
- 不会推断出分号的情况
  - 句末是非法结尾字符，如'.'或者中缀操作副
  - 下一行句首是非法起始字符，如'.'
  - 跨行出现的"()"或"[]"里面
- "{}"里面可以进行分号的自动推断
  - 为了整洁，一般一行只写一条完整的语句，句末不懈分号

### 4.1.2 函数的返回结果

- return可选

  - 默认情况下，编译器会自动为函数体里的最后一个表达式加上"return"
  - 不建议显式声明"return"，会引发warning，且看起来像指令式而非函数式

- 返回结果的类型也是可以根据参数类型和返回的表达式自动推断，也就是说例子中的: Int 可以省略

- 返回结果的特殊类型Uint，表示没有值返回

  - 同样可以被推断出来

  - 如果显式声明，则即使函数体最后有一个可以返回具体值的表达时，也不会返回表达式的结果

  - ```scala
    scala> def nothing(x: Int, y: Int): Unit = { x + y }
    nothing: (x: Int, y: Int)Unit
    
    scala> nothing(1, 2)
    
    ```

### 4.1.3 等号与函数体

- 看起来类似于数学里的"f(x) = ..."
- 不建议省略

### 4.1.4 无参函数

- 如果一个函数没有参数，可以写一个空"()"作参数列表，也可以不写
- ###

## 4.2 方法

- 方法其实就是定义在class、object、trait里的函数，称为“成员函数”或“方法”

## 4.3 嵌套函数

###

## 4.4. 函数字面量

- 函数式编程的一个重要思想：函数是一等的值，也就是说和一个Int值，String值地位是一样的

- 因此，就像整数字面量"1"一样，也可以定义函数的字面量

  - 字面量的意思是，x = 1，则x是常量/变量（取决于val或var），1是字面量

  - 字面量是一种匿名函数的形式，如：

    ```scala
    // 可以省略{}，因为{}内只有一条语句
    // 还可以只保留函数体，那么就会写成：
    // val f = (_: Int) + (_: Int)
    // 这是用下划线作为占位符来代替参数
    scala> val f = (x: Int, y: Int) => {x+y}
    val f: (Int, Int) => Int = $Lambda$1120/0x0000000800fee728@22854f2b
    
    scala> f(1, 2)
    res0: Int = 3
    ```

    在这里，(x: Int, y: Int) = {x+y} 就是一个字面量

  - 无论是"def"定义的函数，还是函数字面量，它们的函数体都可以把一个函数字面量作为一个返回结果，如：

    ```scala
    // 变量add被赋予了一个返回函数的函数字面量
    scala> val add = (x: Int) => { (y: Int) => x + y }
    add: Int => (Int => Int) = $$Lambda$1192/1767705308@55456711
    
    // 调用时，括号里的"1"传给参数x，第二个括号里的"2"传给参数y
    scala> add(1)(10)
    res0: Int = 11
    ```

  - 函数的参数类型也可以是函数，这样调用时传入的参数就可以是一个函数字面量

    ```scala
    // 函数aFunc的参数f是函数，
    // 且这个函数类型是参数为Int类型，返回结果为Int类型
    scala> def aFunc(f: Int => Int) = f(1) + 1
    aFunc: (f: Int => Int)Int
    
    scala> aFunc(x => x + 1)
    res1: Int = 3
    ```

    f(1)是什么？？？

## 4.5 部分应用函数

- 函数字面量（4.4）实现了函数作为一等值的功能，而"def"定义的函数也具有同样的功能，不过需要借助部分应用函数的形式来实现，如

  ```scala
  scala> def sum(x: Int, y: Int, z: Int) = x + y + z
  sum: (x: Int, y: Int, z: Int)Int
  
  scala> val a = sum(1, 2, 3)
  a: Int = 6
  
  // b获得了部分应用函数打包的sum函数
  scala> val b = sum(1, _: Int, 3)
  b: Int => Int = $$Lambda$1204/1037479646@5b0bfe86
  
  scala> b(2)
  res0: Int = 6
  
  // c和b类似
  scala> val c = sum _
  c: (Int, Int, Int) => Int = $$Lambda$1208/1853277442@5e4c26a1
  
  scala> c(1, 2, 3)
  res1: Int = 6
  ```

## 4.6 闭包

- 一个函数除了可以使用它的参数外，还能使用定义在函数以外的其它变量

  - 绑定变量：函数的参数
  - 自由变量：函数以外的变量

- 例子

  ```scala
  var more = 1
  val addMore = (x: Int) => x + more  // addMore = x + 1
  more = 2                                           // addMore = x + 2
  // 同名的自由变量覆盖了前面的定义，但是函数闭包还是用之前的那个自由变量，因此addMore = x + 2
  var more = 10                                  
  more = -100                                      
  ```

## 4.7 函数的特殊调用形式

### 4.7.1 具名参数

- 普通函数调用的时候，参数是按先后顺序逐个传递的

- 但如果调用的时候显式声明参数名并且给其赋值，则可以无视参数顺序

- 例子

  ```scala
  scala> def max(x: Int, y: Int, z: Int) = {
    if(x > y && x > z) println("x is maximum")
    else if(y > x && y > z) println("y is maximum")
    else println("z is maximum")
  }
  max: (x: Int, y: Int, z: Int)Unit
  
  scala> max(1, z = 10, y = 100)
  y is maximum 
  ```

### 4.7.2 默认参数值

- 例子

  ```scala
  scala> def max(x: Int = 10, y: Int, z: Int) = {
    if(x > y && x > z) println("x is maximum")
    else if(y > x && y > z) println("y is maximum")
    else println("z is maximum")
  }
  max: (x: Int, y: Int, z: Int)Unit
  
  scala> max(y = 3, z = 5)
  x is maximum
  ```

### 4.7.3 重复参数

- 允许把函数的最后一个参数标记为重复参数

- 形式为在最后一个参数的类型后面加上星号"*"

- 重复参数的意思是，可以在运行时传入任意个相同类型的元素（也可以是零个）

- 例子

  ```scala
  scala> def addMany(msg: String, num: Int*) = {
    var sum = 0
    for(x <- num) sum += x
    println(msg + sum)
  }
  addMany: (msg: String, num: Int*)Unit
  
  scala> addMany("sum = ", 1, 2, 3)
  sum = 6
  
  scala> addMany("sum = ")
  sum = 0
  
  scala> addMany("sum = ", Array(1, 2, 3): _*)
  sum = 6
  ```

## 4.8 柯里化

###

## 4.9 传名参数

- 如果某个函数的参数类型是一个无参函数，那么通常的类型表示法是"() => 函数的返回类型"，如：

  ```scala
  var assertionEnabled = false
   
  // predicate是类型为无参函数的函数入参
  def myAssert(predicate: () => Boolean) =
    if(assertionEnabled && !predicate())
      throw new AssertionError
  // 常规版本的调用
  myAssert(() => 5 > 3)
  ```

- 为了让代码更简洁，提供了特殊语法——传名参数，当函数参数类型是无参函数的时候，传名参数的类型可以表示为"=> 函数的返回类型"，如：

  ```scala
  // 传名参数的用法，注意因为去掉了空括号，所以调用predicate时不能有括号
  def byNameAssert(predicate: => Boolean) =
    if(assertionEnabled && !predicate)
      throw new AssertionError
  // 传名参数版本的调用，看上去更自然
  byNameAssert(5 > 3)
  ```

###（未完）