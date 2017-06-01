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