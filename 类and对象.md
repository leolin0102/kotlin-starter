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
主构造函数内的变量既可以是var，也可以是val的。

如果构造函数包含注解或者作用域修饰则constructor关键字是必须的。作用域修饰要在最前面。

<pre><code>
class Customer public @Inject constructor(name: String) { ... }
</code></pre>

###  次要构造函数

一个类可以有多个次要构造函数，在类body中使用constructor来修饰。

<pre><code>
class Person {
    constructor(parent: Person) {
        parent.children.add(this)
    }
}
</code></pre>

所有的次要构造函数都必须继承主构造函数，也可以继承其它已经继承自主构造函数的次要构造函数。使用 : 后跟this(<parameters>)的形式。

<pre><code>
class Person(val name: String) {
    constructor(name: String, parent: Person) : this(name) { //继承主函数，或者称为做主构造函数的代理构造函数
        parent.children.add(this)
    }
}
</code></pre>

一个没有任何主次构造函数的非抽象类将默认生成一个没有如参的主构造函数，因此如果我们想让此类不可以被外部创建。（有类的共有函数来创建）则我们可以使用下面的方式。

<pre><code>
class DontCreateMe private constructor () {
}
</code></pre>

注意：如果我们为jvm环境编写程序，需要为主构造函数的参数指定默认值，编译器可以自动生成一个没有参数的构造函数，并为所有参数初始化默认值。这样有助于我们的kotlin类被用在例如JPA等一些容器通过无参构造函数，runtime创建实例的库中。

<pre><code>
class Customer(val customerName: String = "")
</code></pre>

##  创建对象实例

 我们可以通过直接调用一个类的构造函数来创建对象，因此，kotlin没有new关键字。看起来，我们就像是在调用一个与类同名的函数。

<pre><code>
val invoice = Invoice()
val customer = Customer("Joe Smith")
</code></pre>

### 类的组成

