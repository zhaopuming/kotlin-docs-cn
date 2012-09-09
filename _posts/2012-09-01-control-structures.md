---
layout: doc
title: 控制结构
original-doc: http://confluence.jetbrains.net/display/Kotlin/Control+structures
---

## If-表达式

在Kotlin中，**if**是一个表达式，即它会返回一个值。
所以，Kotlin中并不需要*三体操作符*(ternary operator: `condition ? then : else`)，
因为**if**已经可以很好地做到这一点了。考虑下面的例子：

{% highlight java %}
// 传统用法
var max = a
if (a < b)
  max = b

// 使用else
var max : Int
if (a > b)
  max = a
else
  max = b

// 当作表达式
val max = if (a > b) a else b
{% endhighlight %}

**if**的分支可以是代码段，并且代码段的最后一个表达式的返回值作为整个代码段的返回值：

{% highlight java %}
val max = if (a > b) {
    print("Choose a")
    a
  }
  else {
    print("Choose b")
    b
  }
{% endhighlight %}

当**If**只有一个分支时，那么另一个分支返回类型[Unit](posts/functions#unit)。

在[这里](posts/grammar#if)参看**if**的语法

## <a id="when"><!----></a> When-表达式

**When**替代了**C**-风格语言的**switch**操作符。最简单的形式如下：

{% highlight java %}
when (x) {
  1 -> print("x == 1")
  2 -> print("x == 2")
  else -> { // 注意代码块
    print("x is neither 1 nor 2")
  }
{% endhighlight %}

**When**将它的参数和所有的分支条件进行比较，直到某个分支满足条件。
**When**是一个表达式，它的返回值就是满足条件的分支右侧的表达式的返回值。
注意如果有一个分支返回的值是[Unit](posts/functions#unit)，则整个表达式的类型也是Unit。  
注意**else**分支是必须的，除非编译器能够检测出所有的可能情况都已经覆盖了。

如果很多分支需要用相同的方式处理，则可以把多个分支条件放在一起, 用`,`逗号分隔：

{% highlight java %}
when (x) {
  0, 1 -> print("x == 0 or x == 1")
  else -> print("otherwise")
}
{% endhighlight %}

我们可以在判断分支条件的地方使用任何表达式，而不仅仅是常量(和switch不同)：

{% highlight java %}
when (x) {
  parsInt(s) -> print("s encodes x")
  else -> print("s does not encode x")
}
{% endhighlight %}

也可以用**in**或者**!in**来检测参数是不是在一个[范围](posts/ranges)中：

{% highlight java %}
when (x) {
  in 1..10 -> print("x is in the range")
  !in 10..20 -> print("x is outsite the range")
  else -> print("none of the above")
}
{% endhighlight %}

**When**-表达式也支持[模式匹配](posts/pattern-matching)，使用**is**和**!is**操作符：

{% highlight java %}
when (tuple) {
  is #(1, 2) -> ...
  is #(val a, 3) -> print(a) // 将元组的第一个元素绑定到变量a上。
  !is #(*, 1100) -> ...
  else -> ...
}
{% endhighlight %} <!--*-->

现在先不继续深入讨论模式识别。欲知详情，参考[这个页面](posts/pattern-matching)。


**When**也可以用来替代**if-else-if**链。如果不提供参数，所有的分支条件都是简单的布尔值，
而当一个分支的条件返回true时，则调用该分支：

{% highlight java %}
when {
  x.isOdd() -> print("x is odd")
  x.isEven() -> print("x is even")
  else -> print("x is funny")
}
{% endhighlight %}

### 在when表达式中使用continue

在**when**表达式中，使用**continue**可以直接跳转到下一个分支的条件判断，如果还有下一个分支的话：
{% highlight java %}
when (x) {
  in 1..100 ->
    if (x.isOdd())
      continue // 转到下一个分支，即 '3, 101 -> ...'
    else
      print("Event between 1 and 100)
  3, 101 -> print("3 or 101")
  1000 -> continue // Error: 最后一个分支上不允许使用continue
}
{% endhighlight %}

这个机制可以用来替代其他语言中的**警戒**(**guard**)机制。
比如**Scala**的**match**(相当与Kotlin的**when**)中有警戒**if**判断：
{% highlight java %}
// Scala
term match {
  case Fun(x, Var(y)) if x == y -> print(x)
  case _ -> print("Nope!")
}
{% endhighlight %} <!--_-->

在Kotlin里，这段代码可以写作：

{% highlight java %}
when(term) {
  if Fun#(val x, Var#(val y)) -> { if (x != y) continue; print(x) }
  else -> print("Nope!")
}
{% endhighlight %}

关于**continue**的更多信息，参看[返回和跳转](posts/returns-and-jumps)

在[这里](posts/grammar#when)可以参看**when**的语法。  
参看[模式匹配](posts/pattern-matching)

<div class="warn">
<strong>when中的continue还没有实现</strong>
参看相关的<a href="http://youtrack.jetbrains.com/issue/KT-771">issue</a>
</div>

## <a id="for"><!----></a> For 循环

**For**循环可以对任何提供迭代器(iterator)的集合进行遍历，语法如下：
{% highlight java %}
for (item in collection)
  print(item)
{% endhighlight %}

对于循环的变量，可以指定类型，以及**val**或**var**。循环体可以是一个代码块：

{% highlight java %}
for (val item : Int in ints) {
  // ...
}
{% endhighlight %}

上面提到，**for**可以遍历任何提供了**迭代器**(iterator)的集合，包括：

1. 拥有一个实例函数(译注：即成员函数)或者扩展函数iterator()，这个函数返回的迭代器对象需要满足:

1. 拥有一个示例函数或扩展函数next()
1. 拥有下面两者之一：
  1. Boolean类型的属性hasNext
  1. 一个函数hasNext()，返回类型为Boolean

如果想要遍历[数组](posts/basic-types#arrays)或者拥有下标索引的列表(list)，可以这么做：

{% highlight java %}
for (i in array.indices)
  print(arry[i])
{% endhighlight %} <!--[]()-->

注意这种“遍历一个范围”的函数会被编译器优化，不会产生额外的对象。

在[这里](/posts/grammar#for)参看**for**循环的语法定义。

## <a id="while"><!----></a> While和do..while循环

**While**和**do..while**和以往一样：
{% highlight java %}
while (x > 0) {
  x--
}

do {
  val y = retrieveData()
} while (y != null) // y 在这里可见!
{% endhighlight %}


## 循环中的break和continue
Kotlin在循环中支持传统的**break**和**continue**。
详情参看[返回和跳转](posts/returns-and-jumps)

## Try表达式
详情参看[异常处理](posts/exceptions)

## 接下来
* [函数字面量](posts/function-literals)
* [返回和跳转](posts/returns-and-jumps)
* [范围](posts/ranges)
* [This表达式](posts/this-expression)
* [元组](posts/tuples)
* [类型转换](posts/type-casts)
