# Kotlin基础

## 基本数据类型
在Kotlin中，基本上所有的成员函数，成员变量都是以对象形式存在。Kotlin仍然定义了一些内建类型，目的就是优化对基础类型数据处理的性能。但是，对于开发者而言，他们仍然可以拿这些内建数据类型当作普通的类看待，这与早起的java语言有很大的不同。本章节，我们将介绍numbers, characters, booleans 和 arrays这集中数据类型。

### Number类型
Kotlin处理Number类型的方式与java很接近，但是不是完全一样。尤其是不通的Number类型之前是不支持隐性的位扩展的，即不能直接将Int类型的变量赋值给一个Long。

与java相同，Kotlin提供一下内建Number数据类型。

<table>
    <tr>
        <td>类型</td>
        <td>位宽</td>
    </tr>
    <tr>
        <td>Double</td>
        <td>64</td>
    </tr>
    <tr>
        <td>Float</td>
        <td>32</td>
    </tr>
    <tr>
        <td>Long</td>
        <td>64</td>
    </tr>
    <tr>
        <td>Int</td>
        <td>32</td>
    </tr>
    <tr>
        <td>Short</td>
        <td>16</td>
    </tr>
    <tr>
        <td>Byte</td>
        <td>8</td>
    </tr>
</table>

这里要注意Kotlin没有把字符型引入Number中。

### 整型：
十进制：10000

    十进制长整型采用数字后加字符 ‘L’ 表示：123L

十六进制： 十六进制数以 '0x' 开始。 0x1F

二进制：二进制数以 '0b' 开始. 0b10010101

注意：Kotlin中不支持八进制类型。

### 浮点数：
如果不明确指明类型，默认类型为Doubles。175.34, 1.5e10
单精度浮点 Float 采用数字后跟 'F' 或者 'f'：3.45f

支持使用下划线分隔符增加数字可读性。
<pre><code>
val oneMillion = 1_000_000
val creditCardNumber = 1234_5678_9012_3456L
val socialSecurityNumber = 999_99_9999L
val hexBytes = 0xFF_EC_DE_5E
val bytes = 0b11010010_01101001_10010100_10010010
</code></pre>

在java运行时环境中，Kotlin的numbers类型以虚拟机原型数据存储，除非我们生命为nullable类型（例如 Int?）或者我们显示的调用构造函数创建一个Number实例。同时后者 "nullable" 和 "显示的构造" 是number封箱（boxing）的。

后者封箱的numbers是有独立的地址的。

<pre><code>
val a: Int = 10000

print(a === a) // 打印 'true'

print(a == a) // 打印 'true'

val boxedA: Int? = a
val anotherBoxedA: Int? = a

print(boxedA === anotherBoxedA) // !!!打印 'false'!!!

print(boxedA == anotherBoxedA) // !!!打印 'true'!!!

</code></pre>

显式的类型转换

上面提到过Kotlin的nubers不支持类型之间的自动转化，即自动位扩展。因此虽然同是整形，Int不能自动转换成Long从而将一个Int类型数据赋值给Long。原因是如果支持隐式类型转换，我们很容易会写出下面的代码，而且期望 == 返回的结果是true。

<pre><code>
val a: Int? = 1
val b: Long? = a //由于nulable被封箱（boxing）a 和 b 虽然数值是相等的，但是却是两个实例。
print(a == b) //我们的期望是true，但是实际上结果是false
</code></pre>

因此，Kotlin不提供隐式的类型转换，意在避免我们在不明确两个Number变量类型的情况下，使用==比较。不允许也就不会出现不符合预期的错误了，而且极难发现。
例如：算数运算符操作会自动做位扩展，因此，这样的类型转换时很难被注意到的。
<pre><code>
val l = 1L + 2 // Long + Int => Long 因为自动为扩展，因此l 实际上是64为的Long。
</code></pre>

当需要做类型转换的时候，我们可以直接调用numbers提供的转换函数
<pre><code>
val i: Int = b.toInt()
</code></pre>

所有的numbers类型（Double, Float, Long, Int, Short, Byte）都支持下面的用于类型转换的函数:

    toByte(): Byte

    toShort(): Short

    toInt(): Int

    toLong(): Long

    toFloat(): Float

    toDouble(): Double

    toChar(): Char

操作符

Kotlin的nubers提供标准算数运算符集，每一个运算符都有提供一个可选的类实现。（编译器会根据情况选择最优的实现）

下面是所有为操作的操作符实现：（仅Int， Long）

    shl(bits) – 有符号左移 (Java's << )

    shr(bits) – 有符号右移 (Java's >> )

    ushr(bits) – 无符号右移 (Java's >>> )

    and(bits) – 按位与

    or(bits) – 按位或

    xor(bits) – 按位异或 （true xor true == false, false xor false == false, 其它情况为 true, 一般用于计算出哪些位置不一样的时候使用)

    inv() – 位反转

### 字符型 （characters）

在Kotlin中字符型是Char类型。Char不属于numbers。

<pre><code>
fun check(c: Char) {
    if (c == 1) { // ERROR: 没有Char 与 Int的算术运算符，不能做比较
        // ...
    }
}
</code></pre>

字符型用单引号声明：’1‘。 特殊字符可以使用反斜杠进行转译。'\t', '\b', '\n', '\r' , '\'' , '\"' , '\\' 和 ‘\$’， 其它字符则使用Unicode来表示。'\uFF00'

字符型数据可以转换成Int：

<pre><code>
fun decimalDigitValue(c: Char): Int {
    if (c !in '0'..'9')
        throw IllegalArgumentException("Out of range")
    return c.toInt() - '0'.toInt() // 显式的转换为numbers
}
</code></pre>

与numbers相同，nullable引用字符型同样会转换成封箱对象。

### 布尔型（Booleans）
Boolean类型仅有两个值：true 和 false。

同样将Booleans 赋值给nullable引用时，会被封箱成新的Boolean实例。

内建操作符包括：

    || 懒分离（lazy disjunction，即当操作符左边为true，则直接返回true，不判断右侧的值

    && 懒结合 (lazy conjunction), 当操作符两遍都为true, 才返回true。

    ！取反

数组（Arrays）

Kotlin的数组实现是Array类，包含get和set函数(Kotlin重载[]操作符来简化这两个函数的调用)，同时提供size变量和一些常用的数组函数：

<pre><code>
class Array<T> private constructor() {
    val size: Int
    operator fun get(index: Int): T
    operator fun set(index: Int, value: T): Unit
    operator fun iterator(): Iterator<T>
    // ...
}   
</code></pre>

可以用arrayOf()来构造一个数组实例并通过传参为数组赋值， arrayOf(1, 2, 3) 来构造数组 [1, 2, 3]。arrayOfNulls()库函数可以构造初始容量尾n的空置数组，arrayOfNulls(3)构造数组[null, null, null]。

另外一种构造数组的方式是使用构造函数。

<pre><code>
//构造数组 Array<String> 类型的实例 ["0", "1", "4", "9", "16"]
val asc = Array(5, { i -> (i * i).toString() })
</code></pre>

我们可以使用[]来set或者get数组内指定位置的value。（最终会调用get或者set函数）

注意：与java不同的是，Arrays是不可变的。因此不能将一个Array<String>赋值给Array<Any>，从而避免可能的runtime异常。（可以赋值给Array<out Any>）后面会在 Type Projections中介绍。

Kotlin还为原始数据类型定义了对应的类不需要将原始类封箱成对象才能放入数组中。（ByteArray，ShortArray， IntArray等）这些类型并非是Array的子类，但是，他们都实现了和Array同样的get，set等函数，也都有个size变量。而且，每一个都有对应的构造函数：

<pre><code>
val x: IntArray = intArrayOf(1, 2, 3)
x[0] = x[1] + x[2]
</code></pre>

字符串型（Strings）

字符串型的实现类为String。字符串是不可变。字符串是由一串字符组成的，每个字符可以用[]来获得。可以用for-loop来迭代正组例的么一个字符：

<pre><code>
for (c in str) {
    println(c)
}
</pre></code>

Kotlin有两种方式：一种是和java一样的"Hello, world\n Kotlin\n"，print这个String会输出两行文本。
另外一种创建多行文本的方式是通过"""：

<pre><code>
val text = """
    for (c in "foo")
    print(c)
"""
</code></pre>

我们可以通过调用trimMartgin()函数来消除每一行句首的空格。

<pre><code>
val text = """
    |Tell me and I forget.
    |Teach me and I remember.
    |Involve me and I learn.
    |(Benjamin Franklin)
    """.trimMargin()
</code></pre>

上面这段代码的输出结果：
Tell me and I forget.
Teach me and I remember.
Involve me and I learn.
(Benjamin Franklin)

text变量中的 '|' 是默认的消除空格的前缀，不已 ‘|’ 开始的行，不会消除句首的空格。此函数接受一个参数来允许用户自定义 前缀。例如： text.trimMargin(">")

### 字符串模板

string类型支持内嵌表达式，从而支持模版功能，在执行将字符串赋值的时候，表达式会被执行。字符串中的表达式以 '$'开始后跟简单变量名：

<pre><code>
val i = 10
val s = "i = $i" // 执行后 s 值为 "i = 10"
</code></pre>

或者后面跟以大括号内嵌表达式的形式：

<pre><code>
val s = "abc"
val str = "$s.length is ${s.length}" // 执行后 str 的值为 "abc.length is 3"
</code></pre>

多行文本同样支持模板功能。但是如果我们想在文本内输出 ‘$’ 符号本身，就只能通过这种形式了 ${'$'}