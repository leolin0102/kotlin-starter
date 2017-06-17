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

所有的次要构造函数都必须继承主构造函数，也可以继承其它已经继承自主构造函数的次要构造函数。使用 : 后跟this(&lt;parameters&gt;)的形式。

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

类中包含以下部分：
- 构造函数和初始化代码块
- 函数定义
- 属性
- 匿名类和内部类
- 对象声明

### 继承

继承是面向对象思想中的概念，一个类可以且只可以继承自另一个类。被继承的类称为父类（基类），继承自父类的所有类都称为它的子类。例如：



在面向对象设计中，我们通过继承方式将逻辑分层，通用的共同的逻辑不断向上曾迁移，而更及具体的需求，向下迁移由子类来承担。（同样的代码永远只存在一份，每当我们拷贝一处代码到另外一个地方的时候，一定要想以下，是否可以避免。）

后面我们还会介绍，通过组合和聚合的方式，同样可以实现代码的高度重用，同时，组合和聚合比继承增加了在runtime时的灵活性，也就是通过组合和聚合，我们可以在运行时调整一个宿主对象的行为和特性。

Kotlin的所有的类都继承自Any类，不需要声明。因此任何类如果不声明继承关系，则默认继承Any。

Any类与java的java.lang.Object不同,Any类只实现equals(), hashCode() 和 toString()。

定义类继承关系,我们在类的头部使用: 后跟父类的名字。

<pre><code>
open class Base(p: Int)

class Derived(p: Int) : Base(p)
</code></pre>

如果子类有主构造函数，则可以像想面的代码例子中，将主构造函数的参数来初始化基类的构造函数。（参数 p 作为基类构造函数的如参）如果子类没有主构造函数，则次要构造函数必须使用super关键字初始化父类，或者作为其它已经初始化主构造函数的次要构造函数的代理。这里注意的是，子类可以通过调用父类的主要和次要构造函数来完成父类的初始化，同时，子类的不同次要构造函数可以分别调用父类的不同构造函数。

<pre><code>
class MyView : View {
    constructor(ctx: Context) : super(ctx)

    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs)
}
</code></pre>

上面出现的open关键字相当于java中的final的反作用。因为在Kotlin语言中，所有的类都是final类型的，只有open类型的类才可以被继承。这样设计的原因在于，往往开发过程中，我们很难将我们开发的每一个类都设计的适合被继承，在java中，我们经常是又很多函数，一旦被子类覆盖实现，就会彻底崩溃掉。因此Kotlin则采用严谨的态度，只有我们设计中允许被重载的函数或者基类，才可以通过我们使用open关键字来开放，否则，就允许继承和重载。

### 方法重载

Kotlin需要明确的使用override来声明对父类的open类型的函数进行重载。

<pre><code>
open class Base {
    open fun v() {}
    fun nv() {}
}

class Derived() : Base() {
    override fun v() {} //我们必须使用 override 关键字，否则编译器会警告
}
</code></pre>

没有 open 修饰的函数，子类即使使用了 override 也是不合法的，编译会失败。如果类头部未使用 open 修饰，则函数也不允许为 open 类型。

添加了 override 的方法，本身是可以被它的子类重载的，如果想禁止，则需要增加 final关键字。

<pre><code>
open class AnotherDerived() : Base() {
    final override fun v() {}
}
</code></pre>

### 属性重载

属性重载的方式和方法重载类似，也是通过 override 声明，且只能重载父类中open类型的属性, 但是我们可以将不可变变量重载为可变变量，反过来也可以。重载不能改变属性的数据类型，只能抽载属性的初始化和 get 方法。

注意：override 关键字可以出现在主构造函数的参数列表中。

<pre><code>
interface Foo {
    val count: Int
}

class Bar1(override val count: Int) : Foo

class Bar2 : Foo {
    override var count: Int = 0 //在此增加属性默认值，以及将不可变属性重载为可变属性。
}
</code></pre>

### 重载规则

Kotlin中重载有一个需要遵守的规则，如果一个类的上层存在同一个方法的多个实现的话（例如，父类和接口扩展都实现了同一个方法），则子类必须重载此方法并提供自己的实现。这时java面向对象中不可能出现的情况。一种简单的方式是，在子类的实现中，通过super&lt;type&gt;这个关键字来

<pre><code>
open class A {
    open fun f() { print("A") }
    fun a() { print("a") }
}
interface B {
    //后面我们会讲到，Kotlin中接口可以提供默认的实现，着类似swift中的扩展，因此我们可以不通过集成，直接通过扩展的方式，为任何类增加新的方法。即能力。
    fun f() { print("B") } // 接口中的方法默认是open类型的
    fun b() { print("b") }
}
class C() : A(), B {
    // 因为这个类的父类和接口中都实现了这个方法，因此，即使我们没打算在子类从载，编译器也需要我们提供一个明确的实现来避免歧义。
    // 这里，我们可以简单的通过
    override fun f() {
        super&lt;A&gt;.f() // call to A.f()
        super&lt;B&gt;.f() // call to B.f()
    }
}
</code></pre>

## 抽象类

同java一样，我们可以通过 abstract 来定义抽象类和抽象方法。抽象方法没有具体的实现。且因为抽象函数就是希望被子类实现的，因此，不需要使用open修复，就可以被子类重载。

<pre><code>

open class Base {
    open fun f() {}
}
abstract class Derived : Base() {
    override abstract fun f()
}

</code></pre>