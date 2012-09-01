---
layout: doc
title: 范围 | Ranges
original-doc: http://confluence.jetbrains.net/display/Kotlin/Returns+and+jumps
---

Kotlin中有三种*结构跳转符号*，类似于**goto**，但是要好用很多。
他们分别是：**return**, **break**和**continue**。
他们的默认行为是：

* **return** - 返回到包含当前语句的最近的[函数](posts/functions)，而且不是[函数字面量](posts/function-literals)
* **break** - 中止包含当前语句的最近的一个[循环](posts/control-structures#loops)
* **continue** - 在最近的一个[循环](posts/control-structures#loops)中跳转到下一步，或者在[**when**表达式](posts/control-structures#when)跳转到下一个条件判断分支。

## 使用标签指定break和continue的跳转

在Kotlin中，任何一个表达式都可以用一个*标签*(label)来进行标记。
标签的的形式是一个`@`符号，后面跟随可选择的标识符。比如`@`, `@abc`, `@fooBar`都是合法的标签。
(参看[语法](posts/grammar#label))。
如果想要给一个表达式打上标签，我们只需要在之前放上标签即可：
{% highlight java %}
@loop for (i in 1..100) {
  ...
}
{% endhighlight %}

现在我们就可以给**break**和**continue**加上标签了：
{% highlight java %}
@loop for (i in 1..100) {
  for (j in 1..100) {
    if (..)
      break@loop
  }
}
{% endhighlight %}

带标签的**break**会跳转出标签所指示的那一个循环。
而带标签的**continue**会跳转到标签所指向的那个循环的下一步。


## 返回到标签

因为有[函数字面量](posts/function-literals)、[局部函数](posts/functions#local-functions)
和[对象表达式](posts/object-expressions)，在Kotlin中，函数也是有内嵌层级的。
而带标签的**return**让我们可以指定返回的目标。

带标签的**return**，最重要的一个用途，就是让我们可以从函数字面量中返回。
记得当我们写如下的代码时：
{% highlight java %}
fun foo() {
  ints.forEach {
    if (it == 0) return
    print(it)
  }
}
{% endhighlight %}
上面的**return**是从最近的一个*函数*返回(而不是forEach后面的那个函数字面量)，这里是从foo()返回。
如果我们想只从函数字面量里返回，则需要加上标签：

{% highlight java %}
fun foo() {
  ints.forEach @lit {
    if (it == 0) return@lit
    print(it)
  }
}
{% endhighlight %}
现在，这个返回就只是从@lit这里返回了。很多时候，直接使用最简单的标签会更方便：
{% highlight java %}
fun foo() {
  ints.forEach {
    if (it == 0) return@ // 如果在当前的作用域里只有一个函数字面量，就可以不写标签名称，直接用'@'
    print(it)
  }
}
{% endhighlight %}

注意这种非局部返回，只支持传入到内联函数的函数字面量上。

当需要返回一个值的时候，解析器优先解析标签，即：
{% highlight java %}
return@a 1
{% endhighlight %}
会被认为是"在标签@a上返回1"，而不是"返回一个带标签的表达式(@a 1)"。


有名字的函数，自动定义标签(和函数同名)：

{% highlight java %}
fun outer() {
  fun inner() {
    return@outer // 标签outer自动定义了
  }
}
{% endhighlight %}

<div class="warn">
<strong>非局部跳转现在还没有实现</strong>
参考相关的<a href="http://youtrack.jetbrains.com/issue/KT-1435">issue</a>
</div>

## 自定义结构中的break和continue

[内联函数](posts/functions#inline-functions)使得我们可以很容易的编写"自定义的控制结构"，比如，forEach() 函数对集合的每个元素
调用传入的函数字面量。

{% highlight java %}
inline fun <T> Collection<T>.forEach(body: (item : T) -> Unit) {
  for (item in this) {
    body(item)
  }
}
{% endhighlight %}
注意这个函数并不是一个很容易可以用**for**循环替代的冗余示例，考虑下面的代码：
{% highlight java %}
ints filter { it > 0 } sortby {-it} forEach {print(it)}
{% endhighlight %}

现在，如果我们在forEach的body里使用**break**或**continue**的话，会出现什么情况？
我们只会得到一个编译错误，因为从语法上来讲，并没有可以**break**的循环。
{% highlight java %}
ints forEach {
  if (it < 0) break // Error: 'break'并不在loop中
  print(it)
}
{% endhighlight %} <!-- > -->

但是，实际上还是有一个循环的，只是它隐藏在forEach函数里，而且它是被内联进来的，
所以我们应该能够告诉编译器去理解这个情况。实际上我们可以做到，只要在forEach函数里的循环上
加上loop标注。函数的参数也需要加上loopbody标注：
{% highlight java %}
inline fun <T> Collection<T>.forEach(loopbody body : (item : T) -> Unit) {
  [loop] for (item in this) {
    body(item)
  }
}
{% endhighlight %} <!--[]()-->

现在，编译器就允许在forEach传入的函数字面量中使用**break**和**continue**了。
而且这些操作的目标是标记为@@的循环。

<div class="warn">
<strong>自定义结构中的break和continue还没有实现</strong>
参看相应的<a href="http://youtrack.jetbrains.com/issue/KT-1436">issure</a>
</div>

## 带标注的this表达式

参看[This表达式](posts/this-expressions)

## 接下来

* [范围](posts/ranges)
* [This表达式](posts/this-expression)
* [元组](posts/tuples)
* [类型转换](posts/type-casts)

