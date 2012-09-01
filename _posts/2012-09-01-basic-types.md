---
layout: doc
title: 基本类型
original-doc: http://confluence.jetbrains.net/display/Kotlin/Basic+types
---

在Kotlin中，所有的事物都是对象，可以调用成员函数和属性。
有的类型是内置的，因为他们的实现是特别优化过；
但对于使用者来说，他们看起来和普通的类型没有区别。
这一节我们介绍这些基本类型的大部分：[数](posts/basic-types#numbers)，
[字符](posts/basic-types#characters)，[布尔值](posts/basic-types#booleans)
和[数组](posts/basic-types#arrays)。

## <a id="numbers"><!----></a> 数

Kotlin处理数的方式和**Java**类似，但又不完全相同。
比如，Kotlin中并没有隐式的*向上类型转换*(译注：如int转换为long)，
并且某些字面量的写法也有区别。

Kotlin提供如下这些内置的类型用来表达数(这一点和**Java**类似)

| 类型    |  二进制位数 |
| ---     |   ---       |
| Double  |  64         |
| Float   |  32         |
| Long    |  64         |
| Int     |  32         |
| Short   |  16         |
| Byte    |  8          |

注意在Kotlin中，[字符](posts/basic-types#characters)并不是数


### 存储方式

在**Java**平台中，数值物理上存放为JVM的原始类型数据，
除非当我们需要一个[可空](posts/null-safety)类型的数据(如`Int?`)，
或者当需要泛型的时候。在后一种情况下，数值会被*盒装*(boxed)

**注意** 盒装的数值并不保存其[相同性](posts/basic-operations)(identity): 

{% highlight java %}
val a : Int = 10000
print(a identityEquals a) // 输出 'true'
val boxedA : Int? = a
val anotherBoxedA : Int? == a
print(boxedA identityEquals anotherBoxedA) // !!! 输出 'false' !!!
{% endhighlight %}


### 显示类型转换

因为使用不同的存储方式，小的类并不是大的类的子类。  
如果是的话，会出现下述的问题：

{% highlight java %}
// 假想的代码，并不能编译
val a : Int? = 1 // a 被盒装成 Int (java.lang.Integer)
val b : Long? = a // 隐式转换得到一个盒装的 Long (java.lang.Long)
print (a == b)  // Surprise! 这会输出 'false'，因为Long的equals()函数在检查相等之前，会检查另一个参数是不是Long类
{% endhighlight %}

所以不仅是相同性(identity)，甚至是相等性(equality)都丢掉了。

所以我们决定，小的类型**不能**隐式地转换为大的类型。
这意味着不能把Byte类的对象直接赋值给Integer的变量，除非显式地加上类型转换。

{% highlight java %}
val b : Byte = 1 // OK, 字面量是静态检查的
val i : Int = b // ERROR
{% endhighlight %}



可以使用如下的方式来**显式地进行向上类型转换**：

{% highlight java %}
val i : Int = b.int // OK: 显示扩展
{% endhighlight %}

每个数值类型都支持下列的转换：

* toByte() : Byte
* toShort() : Short
* toInt() : Int
* toFloat(): Float
* toDouble(): Double
* toChar() : Char

失去隐式类型转换，其实并没有带来多少困扰，因为使用字面量的时候是没有代价的，因为字面量的类型是推导出来的；
另一方面，算数运算操作都针对不同类型的参数做好了重载，比如：

{% highlight java %}
val l = 1.toLong() + 3 // Long + Int => Long
{% endhighlight %}


### 字面量

所有的整型字面量都是相同的写法：

* 十进制：123, 123.5, 123.5e10
* 十六进制：0x0F
* 二进制: 0b00001011

**注意**：不支持八进制。

Kotlin里并没有"L"或者其他标记用来表示字面量的类型。如果出现歧义，可以使用显示的类型转换来得到需要的类型：

{% highlight java %}
val list = list(1.toLong(), 100000000000, 2.toLong())
{% endhighlight %}


###  操作符

Kotlin支持标准的算数操作符，并在相应的类上定义为成员函数（但编译器会针对运算进行优化，将函数调用优化成直接的算数操作）。
参看[操作符重载](posts/operator-overloading)。  
对于按位操作(bitwise operation)，没有特别的符号来表示，而是直接使用命名函数，
通过[中序方式](posts/functions#infix-calls)调用即可。例如：

{% highlight java %}
val x = (1 shl 2) and 0x000FF000
{% endhighlight %}

下面是所有按位操作的函数(只在Int和Long类型支持):

* shl(bits) - 有符号左移位(**Java**的`<<`)
* shr(bits) - 有符号右移位(**Java**的`>>`)
* ushr(bits) - 无符号右移位(**Java**的`>>>`)
* and(bits) - 按位与
* or(bits) - 按位或
* xor(bits) - 按位异或
* inv() - 按位反转


## <a id="characters"><!----></a> 字符

字符是使用`Char`类型来表示的。它不能当作数值类型来直接操作。

{% highlight java %}
fun check(c : Char) {
  if (c == 1) { // ERROR: 类型不匹配
    // ...
  }
}
{% endhighlight %}

字符类型的字面量使用单引号包括：'1', '\n', '\uFF00'。  
可以调用显示转换将字符转化为一个Int整数：
{% highlight java %}
fun decimalDigitValue(c : Char) : Int {
  if (c !in '0'..'9')
    throw IllegalArgumentException("Out of range")
  return c.toInt() - '0'.toInt() // 显示转换为Int数值
}
{% endhighlight %}

和数值类型一样，当使用可空引用时，字符也会被**盒装**。这个过程也不保证*相同性*。


## <a id="booleans"><!----></a> 布尔值

类型`Boolean`用于表示布尔值，有两个值：true 和 false。

如果使用可空引用，布尔值也会被盒装。

布尔值类型的内置操作有：

* `||` - 惰性析取(lazy disjunction)
* `&&` - 惰性合取(lazy conjunction)

## <a id="arrays"><!----></a> 数组

Kotlin里的数组用`Array`类表示。`Array`类定义了`get`和`set`函数（使用时可以用`[]`，通过[符号重载的约定](posts/operator-overloading)转换)，
以及`size`和其他一些有用的成员函数。

{% highlight java %}
class Array<T>(val size : Int, init : (Int) -> T) {
  fun get(index : Int) : T
  fun set(index : Int, value : T) : Unit

  fun iterator() : Iterator<T>

  val indices : IntRange
}
{% endhighlight %}

想要新建一个数组，需要调用它的[构造函数](posts/classes-and-inheritance#primary-constructors)，
提供数组的大小和一个[函数](posts/function-literals)来说明如何初始化数组的元素。

{% highlight java %}
val asc = Array<Int>(5, {i -> i * 1} // 新建一个数组：[0, 1, 2, 3, 4]
{% endhighlight %} <!-- * -->

或者，也可以使用库函数`array()`，并直接传入元素的值即可。
即`array(1, 2, 3)`会新建一个数组`[1, 2, 3]`。

上面已经提到，`[]`操作符代表着成员函数`get()`和`set()`，
但是当编译成JVM代码时，对于数组，编译器进行了特别的优化，
这样就没有多余的运行代价，而且所有的数组操作都和**Java**中一样：

{% highlight java %}
val array = array(1, 2, 3, 4)
array[x] = array[x] * 2 // 实际上没有调用 get() 和 set()
for (x in array) // 实际上没有生成iterator
  print(x)
{% endhighlight %} <!--[]() -->

甚至当我们使用下标索引来访问数组元素时，也没有多余代价：

{% highlight java %}
for (i in array.indices) // 没有生成iterator
  array[i] += 2
{% endhighlight %} <!--[]() -->

最后，**in**检查也没有带来代价：

{% highlight java %}
if (i in array.indices) { // 和 (i >= 0 && i < array.size) 一样
  print(array[i])
}
{% endhighlight %} <!---->

**注意**: 数组是[不变的](posts/java-interoperability#arrays)(invariant)。
如果想在JVM上获得最好的性能，
请使用[类型特别化的数组类](posts/java-interoperability#arrays)。

## 接下来
* [字符串](posts/strings)







