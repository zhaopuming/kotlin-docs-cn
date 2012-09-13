---
layout: doc
title: Java互操作性 | Java Interoperability
original-doc: http://confluence.jetbrains.net/display/Kotlin/Java+interoperability
---

Kotlin从最初设计时就一直考虑到和**Java**的互操作性。
已有的**Java**代码在Kotlin中可以非常自然地调用，而从**Java**中调用Kotlin代码，也相当顺畅。

## 在Kotlin中调用Java代码

这一节我们介绍在Kotlin中调用Java代码的一些细节。在大多数情况下，直接使用即可：
{% highlight java %}
import java.util.*
fun demo(source : List<Int>) { 
  val list = ArrayList<Int>() 
  // 'for'循环对Java集合也通用
  for (item in source) 
    list.add(item) 
  // 操作符传统也保留下来
  for (i in 0..source.size() - 1) 
    list[i] = source[i] // 数组操作符'[]'，实际调用的是get()和set()
}
{% endhighlight %} <!-- * []() -->

### 返回void的方法
如果一个**Java**方法返回**void**，那么在Kotlin中，它会返回*Unit*。
万一有人使用它的返回值，Kotlin的编译器会在调用的地方赋值，因为这个值本身已经提前可以预知了(这个值就是`()`)。

### 将Java代码中与Kotlin关键字冲突的标识符进行转义
Kotlin的关键字中，有一些在Java中是合法的变量**标识符**，如**in**, **object**, **is**等。
所以在你喜欢的**Java**库中，可能已经使用了这些标识符作为变量或方法名称。
在这种情况下，你仍然可以使用那些方法，但需要对其标识符进行转义，以免和Kotlin的关键字冲突。
转义的方法是使用倒引号`\``：
{% highlight java %}
foo.`is`(bar)
{% endhighlight %}

### <a id="null-safety"><!----></a> Null安全

**Java**中的所有引用都可能是null值。所以，所有的**Java**调用都返回[可空引用](posts/null-safety)，
除了那些标记为@NotNull的方法之外。这样的调用规则，使得Kotlin可以保证所有的代码，除非显式地抛出NullPointerException，
或者在调用的**Java**代码内部抛出，其他地方都没有NPE。

考虑下面的例子：
{% highlight java %}
val list = ArrayList<Int>() // 非null (构造函数的结果)
val size = list.size()  // 非null (原始类型)
val iterator = list.iterator() // 可空 (普通方法)
{% endhighlight %}





