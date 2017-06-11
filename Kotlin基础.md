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
</code></pre>

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

text变量中的 '|' 是默认的消除空格的前缀，不已 ‘|’ 开始的行，不会消除句首的空格。此函数接受一个参数来允许用户自定义 前缀。例如： text.trimMargin(">")

### 字符串模板

string类型支持内嵌表达式，从而支持模版功能，在执行将字符串赋值的时候，表达式会被执行。字符串中的表达式以 '$'开始后跟简单变量名：

<pre><code>
val i = 10
val s = "i = $i" // 执行后 s 值为 "i = 10"
</code></pre>

或者后面跟以大括号内嵌表达式的形式：

<pre><code>
val s = "abc"
val str = "$s.length is ${s.length}" // 执行后 str 的值为 "abc.length is 3"
</code></pre>

多行文本同样支持模板功能。但是如果我们想在文本内输出 ‘$’ 符号本身，就只能通过这种形式了 ${'$'}.这样挺麻烦的，不过还好，我们不会经常碰到这样的情况。

## 包 (Packages)

一个源代码文件可以以一个Package 声明开始。

<pre><code>
package foo.bar
fun baz() {}
class Goo {}
</code></pre>

文件头部的package声明作用于整个文件，声明中包 foo.bar 是整个文件中函数和类的容器。因此函数 baz() 的全名是 foo.bar.baz, 同样类 Goo的全名是 foo.bar.Goo。

如果我们创建一个没有包声明的源文件，则文件内的所有类和函数会被包含在一个没有名字的默认包容器中。默认包会被默认倒入到所有的源代码文件中，可以直接被引用。
我们在开发过程中，需要尽量避免使用默认作用域从而避免命名冲突，尤其是具有一定通用含义的函数或者类。加入我们提供一个叫open()的函数来实现打开一个文件，但是，如果我们这时引入的数据库链接库也有这么一个默认包下的open()是建立一个数据库链接。这时，要么就是我们改代码，要么就是换个数据库库了。

## 导入包

当我们在一个源文件下，想调用特定包下的函数，或者引用特定包下的类，我们需要通过import来倒入指定的包。
<pre><code>
pcakage org.leo.log
import foo.*
import foo.bar.Goo as g //通过 as 关键字我们可以给导入的对象指定别名

fun debug() {
    baz()
}

</code></pre>

与java相同，Kotlin预设了默认倒入包，所有的源代码文件都可以不通过显示的导入来直接调用或者引用它们。

### 默认导入包：

Kotlin:
- kotlin.*
- kotlin.annotation.*
- kotlin.collections.*
- kotlin.comparisons.* (since 1.1)
- kotlin.io.*
- kotlin.ranges.*
- kotlin.sequences.*
- kotlin.text.*

还有一部分默认导入包是根据运行时环境的不同而决定的：

JVM:
- java.lang.*
- kotlin.jvm.*

JS:
- kotlin.js.*

在上面的例程中我们提到，import不仅可以声明导入包，还可以导入其它元素：

- 顶级函数(function)和属性(properties)
- 对象内的函数和属性
- 枚举常量

与java不同的是，Kotlin没有静态导入import static的语法，java中用静态导入来简化对指定类中静态函数的调用，有java同学开发elasticsearch 驱动查询数据的时候，会用到静态导入的方式，构建搜索查询条件。elasticsearch query查询条件的构造也适用了“构造器”设计模式，query条件的构造是一种典型的使用数据结构，组合的形式实现算法的设计方式。也就是我们的查询算法是靠一个数据结构描述的模型驱动的，而不是一个写出来的代码块。

Kotlin中也同样可以实现静态函数的导入，只是不需要额外的添加static描述，统一用import就可以。

顶级函数的作用域

 如果我们在顶级函数前面加上 private 的修饰，那么这个函数将是private的，仅在这个文件内可见。

（这部分我实在车里写完的，我老婆是司机）

## 控制流 （Control Flow）

### If 表达式

在Kotlin语言中if 是表达式，因此，question语法不再需要（condition ? value1 : value2)。因为if表达式本身就可以实现。

因此，我们可以这样完成question的工作：
<pre><code>
// java中
int max = a;
if (a < b) max = b;

// 或者使用 else
int max;
if (a > b) {
    max = a;
} else {
    max = b;
}

// Kotlin 表达式赋值
val max = if (a > b) a else b
</code></pre>

如果我们的分支由代码块实现，且最后一行是表达事，将被默认作为结果返回。代码如下

<pre><code>
val max = if (a > b) {
    print("Choose a")
    a // return a
} else {
    print("Choose b")
    b // reutrn b
}
</code></pre>

这里如果我们使用if表达式，而不是if语句，则else 分支是必须的，也就是 "val a =" 后跟if表达式的时候，必须有else。

### When 表达式

When表达式替换了从前我们在C, java语言中使用的switch语法，一个when表达式的极简形式像这样：

<pre><code>
// 个人挺喜欢这样的写法。
when (x) {
    1 -> print("x == 1")
    2 -> print("x == 2")
    else -> { // Note the block
        print("x is neither 1 nor 2")
    }
}
</code></pre>

when会顺序匹配分支条件，直到匹配到第一个满足条件的分支。when既可用在表达式上也可以用在语句上。如果when用作表达式，被匹配的分支代码块中返回的结果将作为整个when语句的结果。when作为语句时，被匹配到的分支即使有返回结果，也会被忽略。

上面的程序中，else 就等同与default分支，即如果没有条件被匹配，则执行else。在when表达式中，else语句时不可以省略的，除非编译器可以推演出所有的数据，都被条件覆盖。既然是表达式，逻辑上就必然会有一个结果。例如我们输入的参数是一个枚举类型的变量，且所有的可能的值都被when的分支覆盖了，编译器就不会强制我们必须要有一个else。

如果过个条件分支应用同一个处理逻辑，则我们可以使用逗号 ',' 来合并多个条件：

<pre><code>
when (x) {
    0, 1 -> print("x == 0 or x == 1")
    else -> print("otherwise")
}
</code></pre>

Kotlin中的when与很多语言中的switch有很大的不同，它不紧紧局限于一个常量，我们可以使用一个函数来在运行时根据上下文返回分支的条件：

<pre><code>
when (x) {
    parseInt(s) -> print("s encodes x") //s 是函数上下文中的一个变量
    else -> print("s:${s} does not encode x: ${x}")
}
</code></pre>

我们可以使用 'in' 或者 '!in' 关键字来确定输入的参数是否在range (我们在后面会介绍range概念) 或者 集合内（Collection）:

<pre><code>
when (x) {
    in 1..10 -> print("x is in the range")
    in validNumbers -> print("x is valid")
    !in 10..20 -> print("x is outside the range")
    else -> print("none of the above")
}
</code></pre>

同样，可以使用 'is' 或者 '!is' 来根据输入的参数的类型进行匹配。

<pre><code>
val hasPrefix = when(x) {
    is String -> x.startsWith("prefix") //这里我们就可以直接将x 作为string类型来使用，不用再做类型检测。
    else -> false
}
</code></pre>

when语句可以用来代替 if - else if 链:

<pre><code>
when {
    x.isOdd() -> print("x is odd")
    x.isEven() -> print("x is even")
    else -> print("x is funny")
}
</code></pre>

### For Loops

For Loop 语句可以对任何支持 iterator 迭代能力的对象进行迭代（遍历）:

<pre><code>
for (item in collection) print(item) //collection内的所有对象都会被打印

for (item: String in collection) {
    print(item)
}
</code></pre>

任何具有iterator 能力的对象都可以用在for语句中：

- 有 iterator()成员函数，或者扩展函数（extension），同时函数返回的对象有 next() 和 hasNext() 成员函数或者扩展函数的对象。
注意：所有者三个函数都必须被标记为操作符 operator。

我们后面会介绍扩展（extension）的语法及使用技巧。

for循环语句使用索引形式循环访问“数组” 或 “列表”内的每一个元素，而不是迭代器方式，如果我们需要使用迭代的方式，需要使用index 变量来便利整个数组或是列表，我们可以通过获取一个索引迭代器来实现。有的时候，我们需要有选择的迭代，一次可以通过index来筛选。

<pre><code>
val arr = arrayOf(2, 4, 6, 8)
for (i in arr.indices) {
    if (i % 2 == 0) print("${arr[i]} ")
}

// print 2 6
</code></pre>

上面的逻辑同样可以使用函数试来实现，灵活性更高，且容易被优化。

//todo 增加函数试实现的代码并进行讲解。

注意：对range的迭代 "1..10" 经过编译器优化，因此，在内存中，不会产生额外的range对象。 

一个替代形式是我们可以调用库函数 withIndex：

<pre><code>
//有点像python中的iter库函数
for ((index, value) in array.withIndex()) {
    println("the element at $index is $value")
}
</code></pre>

### While Loops

Kotlin 支持 while 和 do while 两种 While Loops 的形式。

while 语句含义为，当条件为true的时候会一直循环执行，当为false时，则跳出循环。

<pre><code>
//当 x 大于 0 的时候，则反复执行 x--, 知道 x == 0则跳出while循环。
val x = 100
while (x > 0) {
    x--
}

//注意，这里while 用户判断的条件引用的变量时循环体内部的变量，而不是循环体外面的变量，因此此代码不能转换成上面的形式，这时while 和 do while的区别。
do {
    val y = retrieveData()
} while (y != null) // y is visible here!
</code></pre>

这里我们可以看到，do while 这个代码的写法，是线程安全的，因为循环体只修改循环体内的数据，因此 do while 这样的写法是没有副作用的。

### break 和 continue

我们可以使用 break 和 continue 来在循环体内部控制循环， 当 break 被执行，则直接跳出整个循环逻辑，即使此时while的条件为true。 当 continue 被执行，则仅跳过循环体 continue 后面的语句，进行下一次判断，如果while 的条件返回true，则继续循环，否则，跳出整个循环。

### Returns 和 Jumps

Kotlin有三种跳转形式：

- return 默认情况下return将从函数内活着匿名函数内返回。
- break 从loop内部跳转，终止循环
- continue 挑出本次loop循环，进行下一次

所有的跳转符号都可以嵌入更大的表达式

<pre><code>
val s = person.name ?: return
</code></pre>

//TODO 解释这个含义 The type of these expressions is the Nothing type.

### Break 和 Continue 标记

任何表达式都可以被打标记，标记格式以标记名称后跟一个 ‘@’ 符号。我们只需要将标记添加在表达式的前面就可以。

<pre><code>
loop@ for (i in 1..100) {
    // do nothing
}
</code></pre>

标记用于指定break 和 continue跳出的loop位置，当我们有loop嵌套的逻辑时，可以通过跳转到标记，从而直接从内层嵌套内控制直接跳转外层嵌套。

<pre><code>
loop@ for (i in 1..100) {
    for (j in 1..100) {
        if (...) break@loop
    }
}
</code></pre>

### Return 返回指定的标记

函数试迭代数组时，例如forEach函数接收一个对象表达式（闭包），这样我们就形成了一个内嵌对象。在内嵌对象中return，会直接从外部函数返回。

<pre><code>
fun foo() {
    ints.forEach {
        if (it == 0) return //从foo函数直接返回，而不是从内嵌类返回运行下一次迭代。
        print(it)
    }
}
</code></pre>

如果我们想实现仅从本次迭代返回，进行下一次循环，而不挑出foo函数，我们就需要对内嵌对象进行标记。

<pre><code>
fun foo() {
    ints.forEach lit@ {
        if (it == 0) return@lit
        print(it)
    }
}
</code></pre>

大多数时候，我们可以将函数名作为隐含的标记来跳转。如果你觉得函数名称本身就可以反映跳转逻辑，就可以省去手动的标记。但是，省事不代表是对的，如果跳转的函数，没有任何逻辑性，我们还是有必要从语言上给代码与读者一个信息。

<pre><code>
fun foo() {
    ints.forEach {
        if (it == 0) return@forEach //这里使用forEach函数名作为隐含标签，但是，这样比较通用的标签，不太适合表达业务逻辑。
        print(it)
    }
}
</code></pre>

我们也可以定义一个匿名函数来实现内嵌对象（后面我们会介绍high order function的时候，也会提到），在匿名函数中的return，则仅仅从匿名函数内返回。

<pre><code>
fun foo() {
    ints.forEach(fun(value: Int) {
        if (value == 0) return //跳出本地迭代，进行下一次循环
        print(value) 
    })
}
</code></pre>

我们可以在返回结果的同时，指定返回的位置。例如：return@a 1 的执行结果是，跳出标记的语句并返回结果1.
