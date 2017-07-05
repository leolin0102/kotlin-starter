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

## 伴生对象

因为Kotlin是没有 static 关键字的，因此类没有静态方法。推荐直接使用 package 作用域下的函数来代替。

如果想定义一个方法，想不通过获得一个类的实例直接调用，但同时右想访问被调用对象的内部。（例如，工厂方法）我们可以在被调用类内用companion object 关键字声明一个内部类，我们在半生对象内，可以像在java中调用静态方法一样，来调用宿主对象内的方法，只是需要使用宿主的类名作为前缀

<pre><code>

class MyClass {

    fun hello() {}
    companion object CompanionObj {
        fun touchIt(): {
            MyClass.hello()
        }
    }
}

</code></pre>

### 属性和字段（property and fields）

在面向对象设计中，一个类可以具有多种属性，例如，我们创建一个类来代表员工（Staff），每个公司的员工，都有工龄，薪资，入职时间，职位，部门，这些都是一个员工的属性，这些都是 Staff 类的 properties。

属性为一个类的状态，外部只可以通过getter 和 setter 来访问一个类的属性。而字段则是一个类下的变量，外部可以直接访问，不需要通过 getter 和 setter函数，但是，Kotlin不支持字段。

类可以有多个属性。可以声明成可变属性 var 和不可变 val 两种。

<pre><code>
class Address {
    var name: String = ...
    var street: String = ...
    var city: String = ...
    var state: String? = ...
    var zip: String = ...
}
</code></pre>

我们可以通过属性名直接引用一个属性。

<pre><code>
fun copyAddress(address: Address): Address {
    val result = Address() // there's no 'new' keyword in Kotlin
    result.name = address.name // accessors are called
    result.street = address.street
    // ...
    return result
}
</code></pre>

### Getter 和 Setter

声明一个属性的完整语法

<pre><code>
var &lt;propertyName&gt;[: &lt;PropertyType&gt;] [= &lt;property_initializer&gt;]
    [&lt;getter&gt;]
    [&lt;setter&gt;]
</code></pre>

property_initializer，getter， setter 都是可选的，如果initializer存在，或者getter提供返回值可以让编译器推断出属性类型，则PropertyType也是可选的。

<pre><code>
var allByDefault: Int? // error: 需要明确的初始值
var initialized = 1 // 定义Int 类型的变量initialized，缺省的getter和setter函数
</code></pre>

不可变属性以val开头，且不允许又setter函数。

<pre><code>
val simple: Int? // Int类型变量simple， 默认的getter函数实现，因为是不可变，因此必须要在类的构造函数中初始化。
val inferredType = 1 // 取值为1的 Int 类型变量 inferredType，默认的 getter 函数实现。
</code></pre>

我们可以自定义 getter 函数：

<pre><code>
val isEmpty: Boolean
    get() = this.size == 0
</code></pre>

代码中，this关键字是对当前类实例的引用。

自定义 setter 函数：

<pre><code>
var stringRepresentation: String
    get() = this.toString()
    set(value) {
        setDataFromString(value) // parses the string and assigns values to other properties
    }
</code></pre>

缺省的setter如参叫value，大部分时候，直接的使用value这个名字会很方便，但是，我们可以使用一个自己的名字来代替value这个缺省值。

Kotlin 1.1以后，你可以通过自定义getter函数的返回类型来指定属性的类型。

<pre><code>
val isEmpty get() = this.size == 0 // has type Boolean
</code></pre>

如果我们只想改变属性的作用域，或者对其进行注解，但是不想自定义属性的 getter 和 setter函数，我们可以省略函数的body：

<pre><code>
var setterVisibility: String = "abc"
    private set // 这里不需要提供setter的body
var setterWithAnnotation: Any? = null
    @Inject set // 我们后面向大家介绍注解的概念
</code></pre>

辅助字段 （Backing Field）

Kotlin中的类是不支持字段的。但是，当我们实现自定义 setter 函数的时候，我们需要一个辅助字段来访问property，当且仅当在setter中，我们可以使用关键字field来访问property。

<pre><code>
var counter = 0 // value 的值被直接赋值给property， 通过使用辅助字段
    set(value) {
        if (value >= 0) field = value
    }
</code></pre>

field关键字仅可以在属性的访问函数（getter 和 setter）中使用。

当我们在属性访问函数中使用field，或者至少自定义一个访问函数时，辅助字段会被自动生成。

例如，下面这段代码中，field是不需要的

<pre><code>
val isEmpty: Boolean
    get() = this.size == 0
</code></pre>

### 辅助属性 (Backing Property)

如果 ，我们想实现的属性访问函数没法使用辅助字段来实现，我们可以声明一个私有的辅助属性。

<pre><code>
private var _table: Map<String, Int>? = null //辅助属性
public val table: Map<String, Int>
    get() {
        if (_table == null) {
        _table = HashMap() 
    }
    return _table ?: throw AssertionError("table属性被其它线程释放") //由于getter 外部使用了_table，因此，我们没办法使用field的方式，在getter中为属性赋值。因此必须使用辅助属性。
}
</code></pre>

辅助属性必须是私有的，因此，和java语言中访问私有属性的方式相同，且同样没有默认的getter和setter函数，因此，对辅助属性的访问，不会产生函数调用。

### 编译时常量

如果在编写代码的时候，一个属性的值就已经被确定，且不会再被改变（例如一些配置属性），则可以声明为编译时常量。我们可以为属性添加 "const" 来修饰。

编译时常量必须满足以下条件：

- 必须是全局或者属于一个对象
- 类型必须是string或者其它原始类型
- 没有自定义getter函数

编译时常量可以用在注解中（配置属性）

<pre><code>
const val SUBSYSTEM_DEPRECATED: String = "This subsystem is deprecated"
@Deprecated(SUBSYSTEM_DEPRECATED) fun foo() { ... }
</code></pre>

### 后赋值属性

Kotlin的属性分为可选属性用 ？注明（optional）和 非空属性 ！号注明，未指定时，属性默认都是非空属性。

非空属性必须要在构造函数中声明，但是有的时候，我们没法在构造函数中赋值，但是，又想声明成非空属性，这样引用这个属性的所有地方，就都不用做判空处理了。例如，在对象构造后，由依赖注入框架赋值或单元测试中的setup函数中赋值等，我们可以使用 lateinit 关键字来告诉编译器，稍后会第一时间为此属性赋值。

<pre><code>
public class MyTest {
    lateinit var subject: TestSubject
    @SetUp fun setup() {
        subject = TestSubject()
    }
    @Test fun test() {
        subject.method()
    }
    // dereference directly
}
</code></pre>

注意只有可变属性 var 才可以后赋值。如果在后赋值属性被初始化之前访问此属性，则会抛出特定的异常来告诉开发者。

## 接口 （Interface）

Kotlin的接口和java8十分相似,其实我觉得跟swift的protocol更像。接口是面向对象中非常重要的一个概念。但是，纯面向对象中的接口必须全部都是抽象函数，即只有函数的定义没有body，声明实现此接口的类，必须提供接口中抽象函数的实现。Kotlin中的接口，即可以是抽象函数，也可以提供默认实现，即非抽象函数。但是，接口是不可以保存属性的状态。因此，不可以在接口中给属性赋值，要么声明抽象属性，要么为属性提供自定义访问函数。

接口使用 interface 关键字来声明:

<pre><code>
interface MyInterface {
    fun bar()
    fun foo() {
        // 函数的实现是可选的。
    }
}
</code></pre>

### 实现接口

一个类可以声明实现多个接口:

<pre><code>
class Child : MyInterface {
    override fun bar() {
        // body
    }
}
</code></pre>

### 接口中的属性

接口中也可以声明属性。可以声明抽象接口定义，或者提供自定义的访问器函数。接口中的属性不能使用辅助域变量，因此，接口中的访问器不可以使用。

<pre><code>
interface MyInterface {
    val prop: Int // abstract
    val propertyWithImplementation: String
    get() = "foo"
    fun foo() {
        print(prop)
    }
}

class Child : MyInterface {
    override val prop: Int = 29
}
</code></pre>

### 作用域标识

类，对象，接口，构造函数，函数，属性和属性getter可以用作用域修饰来指定可见作用域。（Getter 的作用域与属性声明的作用域相同，不可以单独指定）一共有4中作用域：私有(private), 保护(protected), 内部(internal) 和 公共(public).未指名作用域的时候，默认的作用域是Public。

#### 包作用域 (Packages)

函数，属性，和类，类的对象和接口都可以被声明在Packages下。作为顶层元素。

<pre><code>
// file name: example.kt
package foo

fun baz() {}
class Bar {}

</code></pre>

- 如果在包内的顶层声明的元素，没有指定任何作用域，则都为public，这些元素将全局可见。
- 如果声明私有元素，则元素的作用域仅限于文件内部。
- 如果我们定义internal，则此元素仅模块内可见（Kotlin中Model被定义为，一组源代码文件被独立的编译并打包到一起）。
- 包的顶层元素不可以使用protected，因为protected主要用户在对象继承关系中，仅子类可见。因此，只在类内部使用。

<pre><code>
// file name: example.kt
package foo

private fun foo() {} // visible inside example.kt

public var bar: Int = 5 // property is visible everywhere
    private set // setter is visible only in example.kt

internal val baz = 6 // visible inside the same module

</code></pre>

#### 类和接口作用域

在类中，可以使用 private，protected，internal和public全部这四种修饰。

- private 私有作用域，进当前类可见。
- protected 仅当前类和其子类可见。
- internal 模块内可见。
- public 对任何类可见。

java开发者注意：在kotlin中，一个类的内部类的private元素对其宿主不可见。

<pre><code>
open class Outer {
    private val a = 1
    protected open val b = 2
    internal val c = 3
    val d = 4 // public by default

    protected class Nested {
        public val e: Int = 5
    }
}

class Subclass : Outer() {
    // a is not visible
    // b, c and d are visible
    // Nested and e are visible

    override val b = 5 // 'b' is protected
}

class Unrelated(o: Outer) {
    // o.a, o.b are not visible
    // o.c and o.d are visible (same module)
    // Outer.Nested is not visible, and Nested::e is not visible either
}
</code></pre>

#### 构造函数

为主要构造函数设置作用域的方式：

<pre><code>
class C private constructor(a: Int) { ... }
</code></pre>

未设置作用域时，主构造函数都为public，有时，如果一个类仅做工具类或者想确保全局仅有一个实例时，我们可以将构造函数私有化来禁止外面构造这个对象。

#### 模块

上面提过关键字internal作用域代表被修饰的元素尽在模块内可见。而一个模块为代码文件的集合，例如：
- Intellig IDEA module
- Maven或者gradle 的项目
- 或者是一个ant task 打的jar

##扩展（Extensions）

和 C#，swift相似，Kotlin可以在不需要继承基类的情况下直接对类进行扩展，也不需要通过任何设计模式来实现逻辑的绑定和注入。（适配器）Kotlin可以实现函数扩展和属性扩展。尤其是有的时候我们无法修改我们依赖的模块内的对象，但是又想对其进行一定的扩展的时候，我们可以很轻松的大道我们的目标。

### 函数扩展

声明对函数的扩展，只需要在扩展函数的名字前面，使用被扩展类的名字作为前缀，并用.来分割。下面的例子将函数swap扩展到MutableList&lt;Int&gt;:

<pre><code>
fun MutableList&lt;Int&gt;.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' corresponds to the list
    this[index1] = this[index2]
    this[index2] = tmp
}
</code></pre>

扩展函数内的this代表被扩展的类，现在我们为MutableList扩展了一个函数swap，现在我们可以调用这个扩展函数了。

<pre><code>
val l = mutableListOf(1, 2, 3)
l.swap(0, 2) // 'this' inside 'swap()' will hold the value of 'l'
</code></pre>

上面函数中用到了范型（generic）。MutableList本身是个范型化的容器，函数扩展也可以同样用在范型中，例如上面的函数扩展可以改写为：

<pre><code>
fun &lt;T&gt; MutableList&lt;T&gt;.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' corresponds to the list
    this[index1] = this[index2]
    this[index2] = tmp
}
</code></pre>

### 扩展的静态确定

扩展函数对类的扩展是静态时确定的，即编译时，一方面，扩展函数并不是被添加到类上的，同时也不是动态的为类增加了虚拟的函数。Kotlin仅仅通过增加了一个函数，并使这个函数可以通过变量.调用到而已。

由于扩展是静态确定的，因此，没有办法通过运行时根据对象的实际类型来确定调用的具体函数的实现。因此，只能通过写代码的时候，代码中的对象的类型，来确定调用哪个具体的扩展函数。就是说，子类扩展，是不能重载基类的扩展函数的，如果我们代码里声明的是基类类型的变量，则，只能调用到对基类的扩展函数。

<pre><code>
open class C

class D: C()

fun C.foo() = "c"

fun D.foo() = "d"

fun printFoo(c: C) { // 由于入参确定了类型为C， 因此即使我们传入的实际上是子类，只有C 类的扩展函数会被调用。
    println(c.foo())
}

printFoo(D()) // 输出 "c"
</code></pre>

最终结果是输出c， 因为目标扩展函数是通过入参 c 的声明类型来确定的，变量 c 的类型是 C， 因此对C的扩展函数C.foo()将会被调用。

如果类内成员函数已经有同名和相同入参的话，外部的同名相同入参的扩展函数会不起作用。例如：

<pre><code>
class C {
    fun foo() { println("member") }
}

fun C.foo() { println("extension") }
</code></pre>

输出结果为"member"

如果仅仅是名字相同，入参签名不同，则扩展函数可以起作用。

<pre><code>
class C {
    fun foo() { println("member") }
}
fun C.foo(i: Int) { println("extension") }

C().foo(1) //will print extension
</code></pre>

### 被扩展类可控

扩展函数可以使用？声明被扩展对象（receiver）为可空类型，因为可以调用变量对象上的扩展函数，因此，变量本身有可能为空，可以使用？修饰扩展函数，并在函数内对this进行非空判断。

下面的扩展允许我们可以放心的对任何对象变量调用toString()，不管变量是否为空。

<pre><code>
fun Any?.toString(): String {
    if (this == null) return "null"

    // after the null check, 'this' is autocast to a non-null type, so the toString() below
    // resolves to the member function of the Any class
    return toString()
}
</code></pre>

### 扩展属性

扩展属性和扩展函数非常相似：

<pre><code>
val <T> List<T>.lastIndex: Int
    get() = size - 1
</code></pre>

因为扩展机制是静态决建立的，实际上扩展属性并不在类内部，因此，扩展属性是没有办法有辅助字端的。这是为什么构造函数内不可以应用扩展属性的原因。

### 伙伴对象的扩展

如果类中存在伙伴对象定义，我们同样可以对其进行函数扩展和属性扩展。

<pre><code>
class MyClass {
    companion object { } // will be called "Companion"
}

fun MyClass.Companion.foo() {
    // ...
}
</code></pre>

对伙伴对象的调用方式与普通类的函数和属性没什么区别

<pre><code>
MyClass.foo()
</code></pre>

### 扩展的范围

通常我们直接在包下面定义扩展。

<pre><code>
package foo.bar

fun Baz.goo() { ... }
</code></pre>

如果想在包内使用包之外的扩展来扩展包内不的类，我们只需要对其进行导入，使用关键import。

<pre><code>
package com.example.usage

import foo.bar.goo // 导入foo.bar.goo 包下的所有扩展

// 或者

import foo.bar.* // 从 "foo.bar" 导入所有元素，包括扩展，类，函数 


fun usage(baz: Baz) {
    baz.goo()
)
</code></pre>

### 类内定义扩展

可以在类内部定义对外部类的扩展，在扩展函数内可以访问到类本身的所有对象成员和函数，就像这个类里的其他成员函数一样。

<pre><code>
class D {
    fun bar() { ... }
}

class C {
    fun baz() { ... }
    fun D.foo() {
        bar() // calls D.bar
        baz() // calls C.baz
    }
    fun caller(d: D) {
        d.foo() // call the extension function
    }
}
</code></pre>

如果类内扩展函数调用的函数，在被扩展类中也被声明，存在冲突。则在冲突发生的时候，被扩展类的实现将被调用，如果我们想调用宿主类中的函数，则需要@符号后跟类名来区分。

<pre><code>
class C {
    fun D.foo() {
        toString() // 调用 D.toString()
        this@C.toString() // 调用 C.toString()
    }
}
</code></pre>

类内部的函数扩展可以是open作用域的，且可以被子类重写。相当于，类中的成员扩展函数的实现取决于构造的是哪个类，基类还是子类，但是，对被扩展对象来说，其扩展函数的实现依然在静态时被确定。

<pre><code>
open class D {
}

classlass D1 : D() {
}

open class C {
    open fun D.foo() {
        println("D.foo in C")
    }

    open fun D1.foo() {
        println("D1.foo in C")
    }

    fun caller(d: D) {
        d.foo() // 静态时确定实现
    }
}

class C1 : C() {

    override fun D.foo() {
        println("D.foo in C1")
    }

    override fun D1.foo() {
        println("D1.foo in C1")
    }

}

C().caller(D()) // prints "D.foo in C"
C1().caller(D()) // prints "D.foo in C1" - caller函数静态确定调用D的扩展函数 foo() ,同时foo() 的实现被C1 类重写
C().caller(D1()) // prints "D.foo in C" - caller函数静态时去定调用对D的扩展函数 C.foo() 的扩展
</code></pre>

### 使用扩展的动机

在java语言中，我们有的时候会创建带有Utils后缀的类，用于放一些公共的无状态的静态工具函数。例如java中非常著名的集合工具类java.util.Collections中提供了一些工具函数来方便我们处理数据集。

<pre><code>
// Java
Collections.swap(list, Collections.binarySearch(list, Collections.max(otherList)), Collections.max(list))
</code></pre>

如果我们使用java 静态导入这个类。（Kotlin没有静态导入的概念，不需要）如果曾经用java开发过elasticsearch的查询条件构造的话，就更佳熟悉了。

<pre><code>
static import java.util.Collection.*
// java
swap(list, binarySearch(list, max(otherList)), max(list)) //函数实现是由Collection提供，因为交换需要对list对象进行操作，所以入参需要也将list本身作为参数传递给swap函数。
</code></pre>

使用Kotlin扩展，我们可以将这样的函数直接扩展到对象，我们可以直接对变量进行函数调用。而如果想在Java里实现下面的效果，我们就只能通过实现一个List类，并实现基类里的所有必需被子类实现的抽象函数。因为List本身是抽象类，里面定义了很多抽象函数。

<pre><code>

func List.swap(list: List) {}

list.swap(list.binarySearch(otherList.max()), list.max())
</code></pre>

通过上面的写法，我们仅仅需要实现一个函数，并扩展到了所有List类的实例上。

## 数据类 （Data Class）

在面向对象编程思想中，倾向于为每一个数据格式定义一个类，仅仅封装结构化数据，并提供一些机械化的成员函数，大部分java程序员都熟知如何用IDE的快捷键来快速生成这些机械化的代码。在Kotlin中可以仅仅使用data关键字来声明一个类，就可以了，连使用快捷键的操作都是多余的。

<pre><code>
data class User(val name: String, val age: Int)
</code></pre>

编译器将会自动根据主构造函数的参数列表，自动生成下面的成员函数：
- data对象的equals 和 hashCode对
- toString方法 返回内容如： "User(name=John, age=42)"
- componentN() 一种根据索引N获取参数值的成员函数，编译器会根据类的属性声明的顺序来生成componentN()函数，例如，我们可以通过对User的实例 user 调用 user.component1() 来获得属性 name 的值。
- copy 函数。

如果上面的任何一个函数在当前类的body中定义，或者被子类重写，则编译器将不会生成默认实现。

编译器自动生成的函数需要对数据对象提供一致性，完整性。数据类需要满足一下几点：
- 主构造函数必须至少有一个属性参数。
- 所有主构造函数的属性必须用val 或者 var 来标记
- 对象类不可以是抽象类，open作用域，封闭类和内部类。（下一接会介绍封闭类）
- 在kotlin 1.1之前，数据类只能作为接口的实现

如果是在JVM上运行，则必须为数据类的所有构造函数的属性指定默认值。

<pre><code>
data class User(val name: String = "", val age: Int = 0)
</code></pre>

### 数据类的copy

数据类自动生成的copy函数可以实现在对对象的克隆的同时，允许我们更改部分属性的值，这会给我们编写函数式风格的程序带来极大的帮助。在函数式编程中，一个函数不改变外部变量，一个函数仅仅是对一个特定输入属性的特定处理，并返回结果。因此，函数式编程不提倡直接改变对象的值来影响函数外，而是通过copy并改变copy后的对象，并作为处理结果返回出来。这样每一个函数都是线程安全的，不需要额外的使用同步来保证线程安全。同时，也非常容易被测试，因此每一个输入，都有唯一的一个结果。而在Swift开发中，就没有这样的特性，因此我们会编写很多辅助函数，一般叫做透镜，来完成类似的逻辑。但是在Kotlin中，仅仅只需要一个关键字 data，太轻松了。

后面我们会介绍相应式函数编程（FRN），来为大家介绍一套比较完善的框架来实现状态管理和功能分离，通过信号绑定来实现与面向对象一样灵活的开发模式。

编译器自动生成的copy函数例子：

<pre><code>
fun copy(name: String = this.name, age: Int = this.age) = User(name, age)
</code></pre>

因此我们可以像下面的代码一样，改变age的值，并产生一个新的对象：

<pre><code>
val jack = User(name = "Jack", age = 1)
val olderJack = jack.copy(age = 2)
</code></pre>

### 数据类对象属性的拆包赋值 (Destructuring Declarations)

数据类自动生成的componentN函数的作用之一是辅助完成属性的拆包赋值:

<pre><code>
val jane = User("Jane", 35)
val (name, age) = jane
println("$name, $age years of age") // prints "Jane, 35 years of age"
</code></pre>

上面的代码中，我们将一个数据类赋值给一个又两个属性的元组

### 标准数据类

Kotlin标准库中，定义了两个数据类二元组（Pair）和 三元组 （Triple），Kotlin提供这连个标准数据类，是为了方便我们在不产生新的类的时候，可以方便的将已有的几个数据，快速组装成新的数据格式。面向对象编程倾向于在这样的场景下，创建专门的类来封装，函数式编程更倾向于使用已有的数据格式，即在函数式编程中，不是给这个新的数据格式定义类，而是，定义处理这个数据的函数。因为函数式编程中，会更多的使用通用的数据结构，map，list 和 元组来封装数据，同时为每一种数据提供丰富的函数来处理。

二元组和三元组的定义在kotlin-runtime.jar中的Tuples.kt中。

## 封闭类（Sealed Classes）

封闭类作用是封闭类的继承关系，当一个变量的类型只能是一组类型中的一种的时候，我们可以使用封闭类来进行限制。封闭类使用sealed来定义，封闭类仅仅只有一个类名称，没有构造函数，属性，和body。继承自同一个封闭类的子类形成了一个封闭的集合，当我们编写函数的时候，期望传入函数的属性必须是这个集合的类的情况下，可以将属性类型定义成这个集合的父类，即封闭类。这样我们可以用when语句来进行模式匹配，为每一个集合中的类匹配一种处理逻辑。使用封闭类定义入参就可以限定函数外只能传入属于这个集合的类型对象。when语句也可以用在枚举上，封闭类与枚举的区别在于，封闭类可以是多实例，且每个实例可以保存状态。（类因为可以有属性，属性保存的即为类的状态）

封闭类的一个约定是，一个封闭类的子类，都必须定义在与封闭类相同的文件中。

<pre><code>
sealed class Expr
data class Const(val number: Double) : Expr()
data class Sum(val e1: Expr, val e2: Expr) : Expr()
object NotANumber : Expr()

fun eval(expr: Expr): Double = when (expr) {
    is Const -> expr.number
    is Sum -> eval(expr.e1) + eval(expr.e2)
    NotANumber -> Double.NAN
    // 'else' 是不需要的，因为我们已经覆盖了Expr集合中的所有子类。
}
</code></pre>

注意，上面的代码中，数据类继承封闭类的特性只有在kotlin1.1之后才被支持，1.1以下，数据类是不允许继承其自其它类的。

在使用when进行模式匹配的时候，编译器会检测是否所有的类型我们都有一个处理，如果是，则不会强制我们使用else来设置默认处理分支，如果不是，则我们必须提供一个默认条件，否则编译器会警告。

## 范型（Generics）  

有的时候，我们称使用范型的类叫模板类，模板类也是一种抽象，一种逻辑的通用实现，与继承不同的是继承关系是在编译时就确定的（静态是），而模板类可以在运行时才取确定模板中操作的变量的类型，因此，我们可以针对具有同一种能力的不同类指定一套通用逻辑，并在构造模板类的时候，指定其操作的具体类型。这样，整个模板类，就会变成操作此类型对象的定制类。例如：

<pre><code>
class Box&lt;T&gt;(item: T) {
    var i = item
}

var box:Box&lt;T&gt; = Box&lt;T&gt;(1)

println("print box value: $box.i")
</code></pre>

代码中开始定义了一个Box容器类后面跟的&lt;T&gt;指定一个要被替换的符号，构造函数的入参item的类型将在运行时被指定为具体的type。紧跟着定义的box变量，构造了一个以Int类型为入参的Box对象，这个时候，我们才真正确定了T的类型。

如果构造函数中有出现模板中需要被确定的所有待定符号，则声明变量的时候，可以省区变量声明后面跟的类型。

<pre><code>
val box = Box(1)
</code></pre>

### 类型转换 (Variance)

java中有种巧妙的机制，叫通配符(wildcard) List&lt;? extends E&gt;，这样，任何继承自类型E的对象，都可以放入这个列表中去。Kotlin没有这样的机制，Kotlin引入两种机制来替代通配符：编译时变形和类型预测。

使用通配符的目的是使得我们写的代码有更好的可扩展性，&lt;? extends E&gt;代表匹配所有E的子类，但是我们在模板内部代码，仅仅只针对E来编写代码，也就是面向抽象层编写自己的模板，但是对于构造模板类的地方，则可以面向具体的实现来编写，构造的地方是确定构造的模板对象是针对那个具体的类型的，因此，可以减少很多强制类型转换，也使得模板类不仅仅可以出类E类型，也可以处理所有E的子类。

但是，java通配符也存在限制，首先通配符模板类是不可以类型转换的，例如List&lt;String&gt;不是List&lt;Object&gt;。如果允许这样的类型转换，意味着，转换后的List容器可以放入所有类型，而不仅仅只是String。因此下面的代码，将会在运行时抛出异常。（ClassCastException）

<pre><code>
// Java
List&lt;String&gt; strs = new ArrayList&lt;String&gt;();
List&lt;Object&gt; objs = strs; // !!! 实际上java是不允许这样的类型转换的，如果允许，则会存在接下来的异常的可能!
objs.add(1); // 我们这里在数组的最前面添加了一个Int 类型的对象。
String s = strs.get(0); // !!! ClassCastException: Cannot cast Integer to String
</code></pre>

上面的例子是为了说明，如果允许类型转换为什么会带来因为。实际上java是禁止这样的类型转换，以此来避免运行时的异常。但是这样限制也有一些不便，例如，我们看一下java中的Collection接口中的addAll()函数。addAll函数的定义是什么？比较直接的定义方式是：

<pre><code>
// Java
interface Collection&lt;E&gt; ... {
    void addAll(Collection&lt;E&gt; items);
}
</code></pre>

但是这个接口是不能在下面的场景中工作的。

<pre><code>
// Java
void copyAll(Collection&lt;Object&gt; to, Collection&lt;String&gt; from) {
    to.addAll(from); // !!! 这里无法通过编译 addAll:
    //Collection&lt;String&gt; 并不是 Collection&lt;Object&gt; 的子类
}
</code></pre>

因此实际上，java中Collection的addAll是这样定义的：

<pre><code>
// Java
interface Collection&lt;E&gt; ... {
    void addAll(Collection&lt;? extends E&gt; items);
}
</code></pre>

我们可以定义一个简单的原则，如果你只从容器里获取对象，则可以使用类型转换将String容器转换成Object容器，并从中读取对象。反过来，如果我们将String对象写入Object集合，也是可以的。在java中可以定义  List&lt;? super String&gt; 为List&lt;Object&gt;的子类，我们称作逆变换。

### 编译时类型转换

假如我们定义范型接口 Iterator&lt;T&gt; 其中没有函数以T类型作为入参，仅仅含有一个返回类型为T的函数next。

<pre><code>
// Java
interface Iterator&lt;T&gt; {
    T nextT();
}
</code></pre>

这样的场景下，将Iterator&lt;String&gt;类型转换成一个Iterator&lt;Object&gt;属性并使用是安全的，因为没有函数会试图像容器中添加对象。但是下面这种情况依然是不允许的：

<pre><code>
// Java
void demo(Iterator&lt;String&gt; strs) {
    Iterator&lt;Object&gt; objects = strs; // !!! 在java中，这样赋值是不允许的
    // ...
}
</code></pre>

为了解决这个问题，我们必须将迭代器声明为Iterator&lt;? extends Object&gt;,这样我们可以放入任何Object对象的子类，同时模板类中，仅调用对Object类型的代码，但是，我们不能再把这个迭代器再转化到Iterator&lt;String&gt;了，因为不确定Object容器内存放的是否都是String类型。

在Kotlin中，有一种方式可以告知编译器。就是编译时类型转换：我们可以对Iterator的类型参数前增加一个注解来告知编译器保证范型类型仅仅作为函数的结果返回类型,但是不会作为函数的入参。

<pre><code>
abstract class Iterator&lt;out T&gt; {
    abstract fun nextT(): T
}

fun demo(strs: Iterator&lt;String&gt;) {
    val objects: Iterator&lt;Any&gt; = strs // This is OK, since T is an out-parameter
    // ...
}
</code></pre>

out T 的含义就是告诉编译器，我们只会从迭代器中获取数据，但是不会像Iterator中添加对象。（只出不进）编译时类型转换，实际上含义是，我们在编写代码的时候确定通过out属性告诉编译器，我们不会试图像像容器中添加对象。

与out相呼应的是in注解，in注解用来在编译的时候告知编译器，相应的属性是可逆变的。含义与out相反，in修饰的属性仅仅能作为方法的入参，不能作为结果返回。例如：Comparable

<pre><code>
abstract class Comparable&lt;in T&gt; {
    abstract fun compareTo(other: T): Int
}

fun demo(x: Comparable&lt;Number&gt;) {
    x.compareTo(1.0) // 1.0 has type Double, which is a subtype of Number
    // Thus, we can assign x to a variable of type Comparable&lt;Double&gt;
    val y: Comparable&lt;Double&gt; = x // OK!
}
</code></pre>

上面代码中因为Comparable不存在以T为结果返回的函数，因此我们可以将Comparable&lt;Number&gt;赋值给Comparable&lt;Double&gt;的属性。因为Double是Number的子类型，只要子类型的容器只消费这个对象，并不将对象通过函数调用结果传递给容器外，就不会造成任何问题。

### 类型推测

类型推测是编译器在容器被使用的时候，通过上下文推测出范型参数的类型的方式。有的时候，我们实现的模板类没有办法保证只返回类型T，例如:

<pre><code>
class Array&lt;T&gt;(val size: Int) {
    fun get(index: Int): T { /* ... */ }
    fun set(index: Int, value: T) { /* ... */ }
}
</code></pre>

这个范型类不是非常的灵活，当我们实现一个数组拷贝函数的时候，我们同样只能实现两个Any的数组间的copy，但是我们没法通过范型指定任何Any的子类的数组copy到Any的数组中。

<pre><code>
fun copy(from: Array&lt;Any&gt;, to: Array&lt;Any&gt;) {
    assert(from.size == to.size)
    for (i in from.indices)
        to[i] = from[i]
}

val ints: Array&lt;Int&gt; = arrayOf(1, 2, 3)
val any = Array&lt;Any&gt;(3) { "" }
copy(ints, any) // Error: 这里会抛出ClassCaseException异常。
</code></pre>

在Kotlin中，我们可以通过使用时类型转换来解决这个问题，即不再Array类的参数上使用out，而是在使用这个模板的函数上使用out，以上面的copy为例，我们只需要在from数组上使用out就可以解决上面发生的异常。

<pre><code>
fun copy(from: Array&lt;out Any&gt;, to: Array&lt;Any&gt;) {
    // ...
}
</code></pre>

同样，kotlin也支持使用时逆变，即在使用模板的函数中使用in关键字。例如我们若要实现使用默认值填充整个数组：

<pre><code>
fun fill(dest: Array&lt;in String&gt;, value: String) {
    // ...
}
</code></pre>

这里使用in是因为，数组本身是Any类型的，fill函数本身并不从数组里获取对象，仅仅是将Any的子类String类型的对象赋值给数组中，也就是，dest数组仅仅消费value，但是不产出value。


### 函数范型

Kotlin的特点是函数式编程与面向对象编程正交，即同时存在且不相互产生副作用，因此，面向对象中的范型也被延伸到了函数中。

我们可以定义函数的范型：

<pre><code>
fun &lt;T&gt; singletonList(item: T): List&lt;T&gt; {
    // ...
}

fun &lt;T&gt; T.basicToString() : String { // extension function
    // ...
}
</code></pre>

与类不同，函数的类型参数，不单可以是函数的入参的类型，也可以作为对类型的扩展函数。singletonList函数声明输入T类型作为入参，返回容纳T类型的List，而basicToString的前面T代表将此函数扩展到T类上。

调用范型函数，我们需要在调用的时候，在函数名后面指定具体的类型：

<pre><code>
val l = singletonList&lt;Int&gt;(1)
</code></pre>


### 上界限制 （Upper bounds）

在开发中最普遍的约束限制就是指定范型类型的上界限制，上界限值对应java中的 extends 关键字：

<pre><code>
fun &lt;T : Comparable&lt;T&gt;&gt; sort(list: List&lt;T&gt;) {
    // ...
}
</code></pre>

值得一提的是，这里的范型是双重约束，即首先list的类型必须是T的子类，且T必须是Comparable&lt;T&gt;的子类。例如：

<pre><code>
sort(listOf(1, 2, 3)) // OK. Int is a subtype of Comparable&lt;Int&gt;
sort(listOf(HashMap&lt;Int, String&gt;())) // Error: HashMap&lt;Int, String&gt; is not a subtype of Comparable&lt;HashMap&lt;Int, String&gt;&gt;
</code></pre>

因为HashMap并未实现或者继承自Comparable接口，因此，HashMap&lt;Int, String&gt;不是Comparable&lt;HashMap&lt;Int, String&gt;&gt;的子类型。

## 内嵌类

我们可以在一个类内部声明一个内嵌类，声明内嵌类，一是可以空置其生命周期，而是可以空置可见范围。当内嵌类的类型为内部类（Inner Class）时，只有外层类实例在的时候，内部类才能存在，因此可以在内部类直接方便引用外部类的属性和调用方法。

### 内部类

内部类使用inner关键字定义，下面的代码实现了一个内部类，提供foo函数将外层类实例中的bar属性传递出去。

<pre><code>
class Outer {
    private val bar: Int = 1
    inner class Inner {
        fun foo() = bar //内部类直接引用外层属性，好像内部类Inner本身就是Outer的一部分
    }
}

val demo = Outer().Inner().foo() // == 1
</code></pre>

### 匿名内部类

有些时候一些很轻量的，没有重用需求的接口实现，我们没有必要创建一个专门的类来容纳接口的实现，我们可以使用object关键字来生成一个匿名内部类：

<pre><code>
window.addMouseListener(object: MouseAdapter() {

    override fun mouseClicked(e: MouseEvent) {
        // ...
    }

    override fun mouseEntered(e: MouseEvent) {
        // ...
    }
})
</code></pre>

如果要实现的接口仅仅只有一个抽象方法，则我们可以使用接口类型后跟一个lambda表达式来定义匿名内部类。

<pre><code>
val listener = ActionListener { println("clicked") }
</code></pre>

## 枚举类

有的时候，一个类的某一写属性是有限个数的一组值，例如，定义一个属性代表方向，且仅有 NORTH, SOUTH, WEST, EAST 这四种值，而一个组建的背景色可能只能取RED, GREEN, BLUE。这个时候，我们可以定义枚举类来限定一个属性的值的范围，且我们可以通过初始化枚举类来给每个值设定一个可读的含义。

<pre><code>
enum class Direction {
    NORTH, SOUTH, WEST, EAST
}
</code></pre>

这样我们可以在我们的类中，定义一个代表方向的属性，其类型为Direction类型。

<pre><code>


fun tackAction(direction: Direction) {
    val defaultDirection = Direction.EAST
    when (direction) {
        Direction.NORTH -&gt; println(direction)
        Direction.SOUTH -&gt; println(direction)
        Direction.WEST -&gt; println(direction)
        else -> { println(defaultDirection) }
    }

}

tackAction(Direction.WEST) //打印 WEST

</code></pre>

### 初始化
因为枚举类型本身是一个类，因此，枚举类可以又构造函数和方法,唯一的区别是，枚举类的构造是发生在类体中：

<pre><code>
enum class Color(val rgb: Int) {
    RED(0xFF0000),
    GREEN(0x00FF00),
    BLUE(0x0000FF)
}

println(Color.RED) // 打印0xFF0000
</code></pre>

### 匿名类

枚举类可以定义自己的匿名类，一般当一个内幕类定义了抽象函数要求每一个枚举类实例都实现的时候，我们可以在枚举类中定义抽象函数，例如：下面的例子实现一个非常简单的状态控制模型，每个状态都必须通过实现signal函数来指定下一个状态值。我们可以通过定义匿名类来为每一个状态提供signal的实现。

<pre><code>
enum class ProtocolState {
    WAITING {
        override fun signal() = TALKING
    },

    TALKING {
        override fun signal() = WAITING
    };

    abstract fun signal(): ProtocolState
}
</code></pre>

和java一样，枚举类提供方法来获取枚举类中实例的列表，以及通过名字获取枚举值的方法。

<pre><code>
EnumClass.valueOf(value: String): EnumClass
EnumClass.values(): Array&lt;EnumClass&gt;
</code></pre>

要注意的是，如果value对应的枚举实例名字不存在，则会抛出运行时不须查异常 IllegalArgumentException。

## 对象表达式和声明

有的时候我们需要创建一个对象来对一个以后的类的实现进行修改，但因为只有一处会用到，且不大可能有代码重用的必要，因此我们不想为此创建一个新类，我们可以使用对象表达式和对象声明。对象表达式最简形式甚至可以只有一行。

<pre><code>
val obj = object { val name: String = "Leo" } //这个对象在被定义的同时，被构造出来，同时赋值给了obj这个属性。
</code></pre>

### 对象表达式



我们使用关键字object来创建一个对象来实现匿名类：

<pre><code>
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
        // ...
    }

    override fun mouseEntered(e: MouseEvent) {
        // ...
    }
})
</code></pre>

实现UI组建的事件回调是使用匿名类的最普遍的场景，因为每一个事件的回调，都是一个用户特定的交互相互独立，但是不推荐实现逻辑非常复杂的匿名类，我们可以把这样的匿名类当作UI事件与业务逻辑接口之间的桥接，或者浇水。例如，如果我们构造一个匿名类，实现了接受一个json串并保存到数据库中，当另外一处事件同样需要做对相同数据格式的json数据进行持久的时候，我们就无法重用之间的匿名类了，如果我们这个时候copy代码到新的匿名类中来解决这个问题，那就等于是给自己埋下了隐患，copy的次数越多，我们的程序质量就会越差。

同时，也不要在匿名类中再实现新的匿名类，这样的行为会很容易产生怪兽级的代码块，更多的缩进，更长的代码块。一个比较有代表性的case是，如果我们实现了一个网络层框架，支持异步发送数据，并通过回调来返回服务器的respone，用户通过点击按钮来出发请求，返回的结果需要保存到数据库，这个时候，有很大的动机让我们写出匿名类中创建匿名类的设计，这样不好的是，我们把整个UI事件响应，数据串行化，反串行化，数据库持久和业务逻辑，全部all in one的实现在了一起，没有办法进行重用了。这样的设计最会引诱开发人员去简单粗暴的使用copy的方式，来克隆实现。即使你知道，应该怎么解决，你也没有办法保证你的团队的其它人，不这么做。

<pre><code>

window.addMouseLinstener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
        Network.post(object: Serialization(request: HttpRequest){
            //将UI中的数据串行化到request中
        }, object: OnSuccessed(response: HttpResponse){
            //反串行化数据,保存数据库，实现业务逻辑。
        })
    }

    override fun mouseEntered(e: MouseEvent) {
        // ...
    }
})

</code></pre>

解决的办法是将匿名类中要做的事情，放到我们的业务逻辑组建中，仅仅让匿名函数做为浇水来链接UI事件和处理逻辑的接口。

一个很想javascript的特性是，我们可以仅仅构造一个对象并在构造的同时定义它。也就是对象不一定非要有一个父类。早期的javascript定义一个对象，实际上就是现构造一个空对象实例，然后再在这个实例上添加这个对象的属性和函数，也就是，虽然是面向对象的，但是没有class。

<pre><code>
fun foo() {
    val adHoc = object {
        var x: Int = 0
        var y: Int = 0
    }

    print(adHoc.x + adHoc.y)
}
</code></pre>

### 对象声明

有的时候，我们需要构造一个单实例对象，单实例的含义是，保证一个类在当前进程中仅仅只被构造一次，即仅仅只有一个实例。单实例可以简化我们获得一个实例的方式，且不用去维护它的声名周期，反正他一直在哪里，直到进程被销毁。

Kotlin通过对象声明的方式，极大的简化了单实例类的实现。比Groovy还简单。

<pre><code>

object DataProviderManager {
    fun registerDataProvider(provider: DataProvider) {
        // ...
    }
    val allDataProviders: Collection&lt;DataProvider&gt;
        get() = // ...
}

</code></pre>

这种实现单实例的方法就是对象声明，object 后面必须有名字，像变量声明一样，对象声明不是一个表达式，因此不能作为赋值语句的表达式赋值给一个属性。应用和调用DataProviderManager的方法非常容易：

<pre><code>

DataProviderManager.registerDataProvider(...)

</code></pre>

且对象声明的实例可以继承或者实现接口：

<pre><code>

object DefaultListener : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
        // ...
    }

    override fun mouseEntered(e: MouseEvent) {
        // ...
    }
}

</code></pre>

单实例是一个很有用的设计模式，但是，大部分项目都被工程师滥用了。我之前遇到过一个情况就是，和一个工程师讨论了底层通信层的组建和组建间的接口，结果，最后发现，工程师确实定义了相应的接口文件，但是所有组建间的调用都是通过单例形式调用的，所有的接口都是摆设了。

使用单例的好处是，一些service级别的接口，是公共的需要一直存在，保存一些状态，以及接口的实例。但是，及其及其不推荐单例中的方法直接访问其它单例，这个是非常不好的行为，因为，单例调用单例代表，两个逻辑之间的关系是在编写代码的时候被确定的，我们不可能通过运行时的具体情况再去调整去选择其它的实现了，这个时候，可能我们就要开始写很多的when 和 if了。

单例被滥用的另外一个副作用是，其实很多时候，我们的组件之间的声明周期是有依赖关系的，一个组建能正常工作是依赖另外一个组建先构造。构造的工作就应该在构造函数中完成，但是单例可能会让本该保证存在的组件被延后构造，有可能会丢失一些状态。例如，一个单例中需要监听系统事件，但是，我们并没有在应用启动的时候，就构造它，而是等具体的地方第一次调用单例的时候，这个单实例才被创建出来，且永不释放。这个时候，实际上对系统事件的监听就被延后了，而不是在开始的组建构造阶段。因此一部分被监听的事件就可能被漏掉了。

做终端开发的时候，会更容易碰到这样的情况，例如，一个bug是因为一个单例被调用的太晚了，好把，我们把这行代码搬到上面去吧。我们现在要实现帐号切换了，ok，我们加个切换帐号的函数吧。不好，怎么上一个用户的名字会出现在新的用户界面中。

我们对单例的态度，应该是我会用这个设计模式，但是，我会选择在正确的地方来使用，而不是，所有的地方。

### 半生对象

当我们在实现一个类的时候，可能
