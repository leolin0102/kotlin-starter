# 为什么选择Kotlin

Kotlin是一个以Java平台为基础的全新的语言，就像Groovy或scala那样，他们都可以运行在JVM上，并且可以零成本的与已有的java库相互融合。但不同的是，Kotlin的代码也可以以javascript的形式运行。现在，Kotlin还可以通过LLVM实现的Kotlin编译器生成本地库来支持IOS平台的运行。

Kotlin是一种简洁的，安全的（静态强类型），实用的，且可以和java代码相互融合的语言，这使得成熟的项目也可以平滑地过度到Kotlin语言上，同时避免我们被迫重新实现一些本来被维护的非常好的java库资源。Kotlin的代码运行的性能与Java代码相当，因此，我们在运用Kotlin的强大特性来提高我们开发效率的同时，无须担心会引入额外的性能风险（当然，是在我们的设计本身没有缺陷的情况下。）

丰富的表达能力：Kotlin语言是一个函数式编程和面向对象编程正交的语言，同时支持一些全新的语法特性，例如type-safe builder, delegated properties。这些特性可以帮助我们构建功能强大，且易于使用、扩展和维护的组件。

## 一个Kotlin程序的样子

让我们从一个简单的例子，来看一下Kotlin语言的风格。这个例子定义了一个People类和一个存放People实例的集合，以及一个实现从集合中找到年纪最大的一个人的实例。
注：这里我引用了Kotlin In Action中的这个例子，我觉得这个例子足够体现一个Kotlin风格代码的样子。这个例子虽小，但却包含了lambda表达式、函数式编程和data class（Kotlin特有的数据类）。最有意思的是，代码仅寥寥几行。大家可以在下面这个链接中运行这个程序。

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

- 当data关键字修饰类时，Kotlin编译器会自动帮我们生成一个Data Entry所必需的实现。Java开发者如果使用过Lambok框架和IDE插件的话，应该瞬间就明白编译器会对Data Class生成什么代码了。这个地址提供了一个视频，来展现Lombok中@Data注解替我们都做了什么（https://projectlombok.org/）。现在使用Kotlin，我们可以不需要额外安装插件和库，仅仅使用data就可以实现这一切。

- persons.maxBy { ... } 使用了lambdas表达式，Higher-Order-Function的概念。如果是用Java实现，我们需要做更多的工作。

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

这样比较一下，是不是差别很大！Kotlin，Swift，Rust这一代的编程语言，都更倾向于让编译器做更多的事情，接管内存管理，数据处理和一些普遍且重复性的工作，把这些工作从编码中迁移到编译器编译过程去做。把我们从重复性工作中解脱出来，从而把精力放在更有创造性的工作上去。而且编译器开发人员的工作重心在更高的级别上，在某些方面，他们能做的更高效、优化更强大。而我们的重心是用更快的速度去实现创意和满足用户日新月异的需求。可以说，把用户惯坏是我们成功的一种表象。

## Kotlin的特性

### 跨平台特性

Kotlin可以运行在JVM运行时环境，Android，javascript运行时，和Native编译成本地库使用。

尤其是在JVM环境下，Kotlin已经被长期应用在很多大型商业化产品中，包括Google，Twitter，Linkedin等公司的系统中，实践证明运用Kotlin可以极大的简化开发人员的日常工作，且可以很好的与已有的java库共同工作。

目前应用最多的领域包括：

- 服务器端应用，作为web应用的后台实现
- 移动端Android开发（IOS端使用swift作为官方语言已有数年）

但是Kotlin仍然可以用在更多的地方。例如，使用Intel Multi-OS Engine (https://software.intel.com/en-us/multi-os-engine) 将Kotlin库运行在IOS环境中，某些公司正在尝试使用rust来实现相同的目标，且已经迈出了一步。

### 静态类型

像Java一样，Kotlin是静态类型语言，即所有表达式赋值的类型都是编译时确定的，同时编译器会替我们做编译时的检测，以确定我们要访问的方法和属性的类型是否正确。

静态强类型语言：所有变量和属性的类型是在编译时确定的，即编译器在编译我们的代码的时候，就确定每一个变量和对象的类型，如果我们的代码存在将错误的类型赋值给一个属性，则编译就会中断并抛出异常。

而动态语言：如（JavaScript, Groovy）则是动态强类型语言，即我们在声明一个属性的时候，不用指定其类型。在程序运行时，对属性初始化时会确定其类型，对类型合法性的检测是在运行时进行的。动态强类型可以让我们写出更简短的代码。但是因为缺少静态检测，如果没有非常完备的代码覆盖测试，很容易由于拼写错误或者对使用其他人编写的程序不熟悉而造成问题甚至闪退。而这样的情况往往发生在程序被release之后。

如C++语言，则是静态弱类型，即属性的类型是编译时确定的。但是，我们可以把一个int赋值给一个char，甚至可以把人和一个指针赋值给一个int都不会有问题。至于有什么副作用，就没办法预估了。

Kotlin与Java相比，虽然都是静态类型语言，但大部分时候我们可以省却为变量指定类型，且编译器可以推演出其类型。例如，当编译器在编译我们定义的一个数值型常量的时候，通过分析我们为这个常量赋值为数字1来确定变量的类型是Int。

<pre><code>
val x = 1
</code></pre>

相对于动态类型，静态类型会给我们带来一些好处：

- 更好的性能。因为类型是编译时确定的，函数调用的关系也是编译时确定的，因此，在运行时就不用额外的工作来确定调用的函数是哪个类的。
- 更可靠的代码。编译器会帮我们检测校验我们的代码的正确性，因此，很少有可能在运行时造成崩溃和闪退。
- 更好的可维护性。团队合作的时候，使用和维护其他小伙伴开发的代码更容易，因为你可以很容易的确定你所使用的每一个对象的类型。
- 工具支持。静态类型语言可以在代码中携带更多的信息，这些信息可以帮助IDE和编译器提供更多工具帮助我们完成特定的工作。

## 函数试编程和面向对象编程

### 面向对象编程

相信很多人对于面向对象并不陌生，面向对象语言的核心思想是将我们的代码端以类为单位进行组织并提高代码重用能力。面向对象中设计到的概念有类、对象（实例）、类型（type）、属性（property）、接口（interface）等。我们将代码以类为单位组织，一个类可以包含属性、成员函数和多个构造函数。在设计一个类的时候，要合理的运用面向对象中的继承、接口隔离、抽象类和抽象函数来达到对内的内聚性和对外的封装性。在复杂项目中，要设计好静态编译时的代码之间的依赖，实现面向接口编程和组件化，不断产出健壮的组建来组合成我们的具体业务。同时，在程序运行时，我们同样要处理好对象之间的依赖关系，通过组合、聚合等概念，在运行时提升程序的灵活性。

面向对象编程中我们会接触到很多设计模式，我们创造设计模式就是为了模式化一些设计思想和案例来帮助我们合理运用面向对象概念和思想来达到上面的目标。对面向对象及设计模式的讲解并非本书的目标，因此不会专门介绍，但我们会在后面的章节，逐步介绍一些常用的设计模式，并通过Kotlin语言运用到我们的程序开发中。

### 函数试编程

Kotlin支持典型的函数式编程范式，与面向对象编程的区别体现在以下几点：
- 无状态函数 无状态的含义是，每一个函数都接受一组输入，并返回一个或者多个结果。函数本身并不改变本函数作用于外的任何对象或者变量，且任何一种输入，都仅有一种特定的结果被返回。因此，函数本身是完全无副作用的，且非常容易被测试。想想我们在Java面向接口编程的时候，要想覆盖一个类的所有代码，我们不得不Mockup这个类需要依赖的所有接口和对象的行为，以此来确保测试代码会运行被测试类代码里的每一个逻辑分支。而且能做到这点的前提，还是能做到依赖反转，面向接口等原则。如果开始设计的时候，就没有关注过代码本身的可测试性，基本上连运行起来都很难。且如果你不Mockup所有依赖的类，而是直接调用依赖的类来运行你的测试，这很难叫做单元测试。
- Higher-Order functions 可以让我们可以实现可读性很高的尾递归逻辑，函数可以作为参数传入，也可以被函数返回，因此我们可以以函数为单位实现类似面向对象中的抽象工厂模式，Builder模式，同时又不需要花过多精力去管理对象。Higher-Order function在增加了函数式编程在runtime级别灵活性和逻辑抽象的能力的同时，又不需要引入大量的类定义和过多的数据格式。
- Lambdas 既然函数式语言提供Higher-Order functions特性，我们就可以理解Lambdas的用法了，Lambdas可以理解为Higher-Order function的极简形式，我们可以把Higher-Order function理解为仅有一个默认函数的匿名类的话，Lambdas则是定义这个匿名类的最简单方法。这里值得补充的是，当你将一个lanbdas作为一个函数的结果return出去的时候，Lambdas内会带着这个函数内的上下文状态的copy。因此，Lambdas内部如果引用函数内的变量，因此无论任何地方调用这个Lambdas函数，都可以得到构造这个Lambdas的函数当时的那个对象。后面我们会更加细致的介绍Lambdas上下文特性。


可扩展性性：Kotlin提供的coroutines扩展模块可以帮助我们快速搭建服务器应用，并扩展到多终端应用。

互通能力：Kotlin完全支持与java语言的库互相调用，因此java开发者可以在充分利用现有成熟的java开源库搭建应用的同时，享受到现代语言带来的开发乐趣。

迁移可行性：因为Kotlin全面支持与java的互相调用，因此，开发人员可以稳健地将旧java逻辑替换成Kotlin实现。为已有复杂应用引入Kotlin降低风险和难度。

学习难度：对于一个Java程序员，学习和开发Kotlin是非常容易的事情。同时Kotlin还提供plugin工具帮助自动转换java代码为Kotlin实现。Kotlin Koans提供了一系列的交互式教程，帮助练习并逐步掌握Kotlin的关键feature。本书也会提供大量程序样例来帮助大家更好的理解和运用Kotlin的特性。

Kotlin开发的应用可以发布到任何支持Java Web中间件运行环境中去，包括Amazon Web Services，Google Cloud Platform。同时Kotlin可以Spring Framework等第三方框架正交使用。因此，任何Kotlin应用或是Kotlin与Java混合编译的应用，都可以发布到任何java的中间件平台上。

使用Kotlin开发Android应用
Kotlin完全支持可以在JDK6下运行，因此Kotlin应用完全可以运行在早期的android设备上。目前Android Studio已完全支持使用Kotlin语言开发Android程序以及真机运行。同时Kotlin应用运行速度几乎和纯java开发的android一样快，甚至lambda表达式部分代码运行速度甚至好于java代码。同样Android内的Kotlin也可以与java代码互相调用，同样可以方便开发者在已有的项目中零风险的引入Kotlin。

Kotlin是个开放的语言，本身Kotlin拥有很丰富的方言和语法糖，同时Kotlin组织还欢迎广大开发者将自己最喜欢的方言或者语法糖告诉给Kotlin组织，也许有一天你会发现新版本的Kotlin已经采纳了你的意见，并将它们变为现实。想想我们每天都在想如何更敏捷的响应客户的需求，当你有一个很好的创意，你也想用最小的代价将你的创意实现并release给用户，来验证是否被大家接收，我们仅仅多付出一些学习成本，掌握这些方言和语法糖，就可以让我们用更少的代码来实现强大的功能，有的时候，这个能力也许就是决定成败的关键。
