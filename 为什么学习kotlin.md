# 为什么选择 Kotlin

Kotlin 是一种以 Java 平台为基础的全新的语言，就像 Groovy 或 Scala 那样，他们都可以运行在 JVM 上，并且可以零成本的与已有的 Java 库相互融合。但不同的是，Kotlin 的代码也可以以 JavaScript 的形式运行。现在，Kotlin 还可以通过 LLVM 实现的 Kotlin 编译器生成本地库来支持 iOS 平台的运行。

Kotlin 是一种简洁的、安全的（静态强类型）、实用的、且可以和 Java 代码相互融合的语言，这使得成熟的项目也可以平滑地过度到 Kotlin 语言上，同时避免我们被迫重新实现一些本来已经被维护的非常好的 Java 库资源。Kotlin 的代码运行的性能与 Java 代码相当，因此，我们在运用 Kotlin 的强大特性来提高我们开发效率的同时，无须担心会引入额外的性能风险（当然，是在我们的设计本身没有缺陷的情况下。）

丰富的表达能力：Kotlin 是一种函数式编程和面向对象编程正交的语言，同时支持一些全新的语法特性，例如 type-safe builder、 delegated properties 等。这些特性可以帮助我们构建功能强大，且易于使用、扩展和维护的组件。

## 一个 Kotlin 程序的例子

让我们从一个简单的例子，来看一下 Kotlin 语言的风格。这个例子定义了一个 People 类和一个存放 People 实例的集合，以及一个实现从集合中找到年纪最大的一个人的实例。
**注：这里引用了 Kotlin In Action 中的例子，这个例子足够体现 Kotlin 的代码风格。例子虽小，但却包含了 lambda 表达式、函数式编程和 data class（Kotlin 特有的数据类）。最有意思的是，代码仅寥寥几行。大家可以在下面这个链接中运行这个程序。**

https://try.kotl.in/#/Kotlin%20in%20Action/Chapter%201/1.1/1.1_ATasteOfKotlin.kt

<pre><code>

data class Person(val name: String,
                  val age: Int? = null)

fun main(args: Array<String>) {
    val persons = listOf(Person("Alice"),
                         Person("Bob", age = 29))

    val oldest = persons.maxBy { it.age ?: 0 }
    println("The oldest is: $oldest")
}

// The oldest is: Person(name=Bob, age=29)

</code></pre>

- 当 data 关键字修饰类时，Kotlin 编译器会自动帮助我们生成一个 Data Entry 所必需的实现。Java 开发者如果使用过 Lambok 框架和 IDE 插件的话，应该瞬间就能明白编译器会对 Data Class 生成什么代码了。这个地址提供了一个视频，来展现 Lombok 中 @Data 注解替我们都做了什么（https://projectlombok.org/）。现在使用Kotlin，我们可以不需要额外安装插件和库，仅仅使用data就可以实现这一切。

- persons.maxBy { ... } 使用了 lambdas 表达式，Higher-Order-Function 的概念。如果是用 Java 实现，我们需要做更多的工作。

<pre><code>
//People.java

import org.jetbrains.annotations.NotNull;

/**
 * Created by leo on 2017/7/9.
 */
public class People implements Comparable {
    private String name;
    private int age;

    public People(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof People)) return false;

        People people = (People) o;

        if (getAge() != people.getAge()) return false;
        return getName().equals(people.getName());
    }

    @Override
    public int hashCode() {
        int result = getName().hashCode();
        result = 31 * result + getAge();
        return result;
    }

    @Override
    public String toString() {
        return "People{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    @Override
    public int compareTo(@NotNull Object o) {
        if (o instanceof People) {
            People p = (People) o;
            return this.age - p.age;
        }
        return -1;
    }
}

// PeopleUtil.java 来实现找到age最大的对象

import java.util.List;

/**
 * Created by leo on 2017/7/9.
 */
abstract public class PeopleUtil {
    static People max(List&lt;People&gt; peoples) {
        People result = null;
        for (People people : peoples) {
            if (result == null){
                result = people;
            } else {
                if (result.compareTo(people) < 0){
                    result = people;
                }
            }
        }
        return result;
    }
}

</code></pre>

这样比较一下，是不是差别很大！Kotlin、Swift、Rust 这一代的编程语言，都更倾向于让编译器做更多的事情，接管内存管理，数据处理和一些普遍且重复性的工作，把这些工作从编码中迁移到编译器编译过程。把我们从重复性工作中解脱出来，从而把精力放在更有创造性的工作上去。而且编译器开发人员的工作重心在更高的级别上，在某些方面，他们能做的更高效、优化更强大。而我们的重心是用更快的速度去实现创意和满足用户日新月异的需求。可以说，把用户惯坏是我们成功的一种表象。

## Kotlin 的特性

### 跨平台特性

Kotlin 可以运行在 JVM 运行时环境，Android、JavaScript 运行时，和 Native 编译成本地库使用。

尤其是在 JVM 环境下，Kotlin 已经被长期应用在很多大型商业化产品中，包括 Google、Twitter、Linkedin 等公司的系统中，实践证明运用 Kotlin 可以极大的简化开发人员的日常工作，且可以很好的与已有的 Java 库共同工作。

目前应用最多的领域包括：

- 服务器端应用，作为 Web 应用的后台实现
- 移动 Android 端开发（iOS 端使用 Swift 作为官方语言已有数年）

但是 Kotlin 仍然可以用在更多的地方。例如，使用 Intel Multi-OS Engine (https://software.intel.com/en-us/multi-os-engine) 将 Kotlin 库运行在 iOS 环境中，某些公司正在尝试使用 rust 来实现相同的目标，且已经迈出了一步。

### 静态类型

像 Java 一样，Kotlin 是静态类型语言，即所有表达式赋值的类型都是编译时确定的，同时编译器会替开发人员做编译时的检测，以确定要访问的方法和属性的类型是否正确。

静态强类型语言：所有变量和属性的类型是在编译时确定的，即编译器在编译代码的时候，就确定每一个变量和对象的类型，如果代码中存在将错误的类型赋值给一个属性，则编译就会中断并抛出异常。

而动态语言：如（JavaScript、Groovy）则是动态强类型语言，即在声明一个属性的时候，不用指定其类型。在程序运行时，在属性初始化时会确定其类型，对类型合法性的检测是在运行时进行的。动态强类型可以让开发人员写出更简短的代码。但是因为缺少静态检测，如果没有非常完备的代码覆盖测试，很容易由于拼写错误或者对使用其他开发人员编写的程序不熟悉而造成问题甚至程序闪退。而这样的情况往往发生在程序被 release 之后。

如 C++ 语言，则是静态弱类型，即属性的类型是编译时确定的。但是，开发人员可以把一个 int 赋值给一个 char，甚至可以把一个指针赋值给一个 int 都不会有问题。至于有什么副作用，就没办法预估了。

Kotlin 与 Java 相比，虽然都是静态类型语言，但大部分时间开发人员可以省略为变量指定类型，且编译器可以推演出其类型。例如，当编译器在编译开发人员定义的一个数值型常量的时候，通过分析这个常量赋值为数字 1 来确定变量的类型是 Int。

<pre><code>
val x = 1
</code></pre>

相对于动态类型，静态类型会给实际开发带来一些好处：

- 更好的性能。因为类型是编译时确定的，函数调用的关系也是编译时确定的，因此，在运行时就不用额外的工作来确定调用的函数是哪个类的。
- 更可靠的代码。编译器会帮助检测校验代码的正确性，因此，很少有可能在运行时造成程序崩溃和闪退。
- 更好的可维护性。团队合作的时候，使用和维护其他开发人员开发的代码更容易，因为你可以很容易的确定所使用的每一个对象的类型。
- 工具支持。静态类型语言可以在代码中携带更多的信息，这些信息可以帮助 IDE 和编译器提供更多工具帮助开发人员选项完成特定的工作。

### 函数式编程和面向对象编程

#### 面向对象编程

相信很多人对于面向对象并不陌生，面向对象语言的核心思想是将代码端以类为单位进行组织并提高代码重用能力。面向对象中涉及的概念有类、对象（实例）、类型（type）、属性（property）、接口（interface）等。开发中将代码以类为单位组织，一个类可以包含属性、成员函数和多个构造函数。在设计一个类的时候，要合理的运用面向对象中的继承、接口隔离、抽象类和抽象函数来达到对内的内聚性和对外的封装性。在复杂项目中，要设计好静态编译时的代码之间的依赖，实现面向接口编程和组件化，不断产出健壮的组件来组合成具体业务。同时，在程序运行时，同样要处理好对象之间的依赖关系，通过组合、聚合等概念，在运行时提升程序的灵活性。

面向对象编程中开发人员会接触到很多设计模式，创造设计模式就是为了模式化一些设计思想和案例来帮助开发人员合理运用面向对象概念和思想来达到上面的目标。对面向对象及设计模式的讲解并非本书的目标，因此不会专门介绍，但在后面的章节中，会逐步介绍一些常用的设计模式，并通过 Kotlin 语言运用到程序开发中。

#### 函数式编程

我们可以把大部分我们需要实现的程序分解成状态，控制和功能。当我们构造一个类实例，并为类实例中的成员变量赋值时，这个类就带有了状态，我们可以把内存中的变量都理解为状态，一个数据，一个代表开关含义的变量都可以叫做状态。而控制部分则是根据当前的状态确定程序行为的逻辑。而功能则可以理解为一个无状态的纯函数，它不做任何判断，当函数被调用的时候，一组特定的入参会传入函数，函数仅根据入参来确定行为并返回一个特定的结果。我们可以把功能函数理解成一个100%服从命令的士兵，对调用控制模块绝对服从。

这样设计的目的在于，对状态部分，我们保证对其访问的线程安全性，并实现对状态改变的监听，而控制模块则负责根据不同的状态来调动不同的功能函数来响应和处理，并将功能函数的结果更新到状态中去。通过这样的设计我们可以很容易的编写测试代码来分别测试控制和功能部分的代码。每一个功能的函数的只能非常单一且做到自包含，而控制函数则更加容易进行测试，我们可以方便地模拟状态事件，并在调用的时候注入功能函数来验证控制逻辑。而函数式响应编程(FRP)为我们提供了完整的控制功能分离的解决方案。

- 无状态 函数内逻辑不受函数外的状态影响，即每个特定的输入都有一个特定的结果。函数不更改外部状态，且函数一旦被运行，外部状态的改变也不会影响函数的行为。从而最大限度地确保线程安全，且函数极其容易被测试。

- 高阶函数（Higher-Order functions）高阶函数的定义是，一个函数以其它函数作为入参，或者以函数作为结果return。高阶函数是通过First Class Function机制实现的函数首先是一个类，我们可以把函数理解成只有一个成员方法的类。函数式编程思想中，经常会通过高阶函数的方式抽象数据处理的机制，例如，集合类中的map、filter等方法实现了低层次的对数据的处理流程。但是这些函数依赖开发人员实现的高阶函数来提供对特定集合中的数据的处理逻辑，并可以通过高阶函数来调整整个低层次处理流程的运转机制。，。，。，。，。
 
- Lambdas函数式语言提供Higher-Order functions特性。Lambdas可以被理解为Higher-Order function的极简形式。Java8中开始引入了Lambdas，从此使得Java语言有了函数式编程的特性，而之前只能通过匿名内部类来实现类似的目的。Lambdas使得代码更加简洁，可读性更高。
注意：Lambdas表达式实现的逻辑不宜过重，一般推荐使用lambdas表达式作为胶水将事件和处理逻辑进行粘合，即表达式只实现对功能函数的调用或者实现对特定数据的解析和转化，例如实现集合类filter函数中数据过滤的规则。

### 其它特性：

- 可扩展性性：Kotlin提供的coroutines扩展模块可以帮助开发人员快速搭建服务器应用，并扩展到多终端应用。

- 互通能力：Kotlin完全支持与Java语言库的互相调用，因此Java开发者可以在充分利用现有成熟的Java开源库搭建应用的同时享受到现代语言带来的开发乐趣。

- 迁移可行性：因为Kotlin全面支持与Java的互相调用，因此开发人员可以稳健地将旧Java逻辑替换成Kotlin实现。为已有复杂应用引入Kotlin，来降低风险和难度。

### 学习难度

对于熟悉Java的程序员，学习和开发Kotlin是非常容易的事情。同时Kotlin还提供plugin工具帮助自动转换Java版本为Kotlin版本。Kotlin Koans提供了一系列的交互式教程，帮助练习并逐步掌握Kotlin的关键feature。本书也会提供大量程序样例来帮助读者更好的理解和运用Kotlin的特性。

Kotlin开发的应用可以发布到任何支持Java Web中间件运行环境中去，包括Amazon Web Services和Google Cloud Platform。同时Kotlin可以在Spring Framework等第三方框架正交使用。因此任何Kotlin应用或是Kotlin与Java混合编译的应用都可以发布到任何Java的中间件平台上。

### 使用Kotlin开发Android应用
Kotlin支持在JDK6下运行，因此Kotlin应用完全可以运行在早期的Android设备上。目前Android Studio已完全支持使用Kotlin语言开发Android程序以及真机运行。同时Kotlin应用运行速度几乎和纯Java开发的Android程序一样快，甚至lambda表达式部分代码运行速度甚至好于Java代码。同样Android内的Kotlin也可以与Java代码互相调用，同样可以方便开发者在已有的项目中零风险的引入Kotlin。

Kotlin是开放的语言，它本身拥有很丰富的方言和语法糖，同时Kotlin组织还欢迎广大开发者将自己最喜欢的方言或者语法糖告诉Kotlin组织。也许有一天你会发现新版本的Kotlin已经采纳了你的意见，并将它们变为现实。开发人员每天想的都是如何更敏捷地响应客户的需求，当你有了一个好创意，你必然也想用最小的代价将创意实现并 release给用户，来验证这个想法能否被大众接受。我们仅仅多付出一些学习成本，掌握这些方言和语法糖，就可以用更少的代码来实现强大的功能。这也许就是决定成败的关键。
