# 类，对象

## 类和继承

### 类

Kotlin中使用class关键字来声明类：

<pre><code>
class Foo {
}
</code></pre>

一个完整的类的声明，包括类名称，类的头部（包括类的主构造函数，父类声明），紧跟打括号包括的body。头部和body都是可选的，如果不需要的话。

<pre><code>
class Empty
</code></pre>

### 构造函数

一个Kotlin类只能有一个主构造函数，和一个或者多个次要构造函数。主构造函数作为类头部的一部分出现。

<pre><code>
class Person constructor(firstName: String) {
}
</code></pre>

如果主构造函数不包括任何代码或者可见的修改器，constructor关键字可以省略。

<pre><code>
class Person(firstName: String) {
}
</code></pre>

主构造函数不能包含任何代码，初始化代码可以放在专有的初始化代码块中，初始化代码块紧跟在init关键字后：

<pre><code>
class Customer(name: String) {
    init {
        logger.info("Customer initialized with value ${name}")
    }
}
</code></pre>

主构造函数声明的参数，可以被init代码块和属性的初始化。

<pre><code>
class Customer(name: String) {
    val customerKey = name.toUpperCase()
}
</code></pre>

Kotlin类声明的一种最简洁的形式是仅仅只需要又类名和参数。

<pre><code>
class Person(val firstName: String, val lastName: String, var age: Int) {
    // ...
}
</code></pre>