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

