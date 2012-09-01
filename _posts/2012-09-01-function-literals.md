---
layout: doc
title: 函数字面量
original-doc: http://confluence.jetbrains.net/display/Kotlin/Function+literals
---

*函数字面量*是一个"匿名函数"，即一个函数没有定义声明(declaration)部分，而是直接作为一个表达式传入作为参数使用。
考虑下面的例子：
{% highlight java %}
max(strings, {a, b -> a.length < b.length})
{% endhighlight %} <!-- > -->

函数max是一个[高阶函数](posts/functions#higher-order-function)，即它接受一个函数作为第二个参数。
这第二个参数是一个本身是函数的表达式，即*函数字面量*。
作为函数，它和下面的函数相同：
{% highlight java %}
fun compare(a : String, b: String) : Boolean = a.length < b.length
{% endhighlight %} <!-- > -->

## 函数类型

如果想让函数接受一个函数作为参数，需要指定参数的*函数类型*。
比如上面的例子中的函数max定义如下：
{% highlight java %}
fun max<T>(collection : Collection<out T>, less : (T, T) -> Boolean) : T? {
  var max : T? = null
  for (it in collection)
    if (max == null || less(max, it))
      max = it
  return max
}
{% endhighlight %} <!-- > -->

参数less的类型是`(T, T) -> Boolean`，即一个函数，有两个类型为T的参数，并返回一个Boolean类型的值：
如果第一个参数比第二个参数小，就返回**true**。

在函数正体中，第4行，less被当作一个函数使用：传入两个类型为T的参数并调用。

*函数类型*用上面的方式来声明，或者也可以加上参数名称，这样可以用作文档用途，或者当作[命名参数](posts/functions#named-arguments)来使用。
{% highlight java %}
val compare : (x : T, y : T) -> Int == ...
{% endhighlight %}

## 函数字面量的语法形式

函数字面量的*完整*语法形式，如下：

{% highlight java %}
val sum = {(x : Int, y : Int) : Int -> x + y}
{% endhighlight %}

* 函数字面量总是用花括号包括 <!--li-->
* 在完整语法形式中，参数声明是用括号括起来，而且参数有*可选择*的参数类型。<!--li-->
* *可选择*的返回值类型跟随在参数列表后面 <!--li-->
* 函数体在'->'符后后面 <!--li-->

如果我们把所有的可选标记都省略掉，则剩下的定义如下：
{% highlight java %}
val sum : (Int, Int) -> Int = {(x, y) -> x + y)}
{% endhighlight %}

因为这个是最常用的形式，所以Kotlin在函数参数不指定类型的时候，也可以把括号省略掉。
这样就是函数字面连过的*短语法形式*：
{% highlight java %}
val sum : (Int, Int) -> Int = {x, y -> x + y}
{% endhighlight %}

函数字面量只有一个参数的情形也很常见。如果Kotlin能够自己推导出函数的签名，则不需要我们声明这唯一的参数，
而由Kotlin隐式地声明一个**it**参数：
{% highlight java %}
ints.filter {it > 0} // 这个字面量的类型是 '(it : Int) -> Boolean'
{% endhighlight %}

注意如果函数的最后一个参数是函数类型，则可以把这最后的参数放到参数列表的扩后后面。
参看[高阶函数](posts/higher-order-functions)以及[调用后缀](posts/grammar#call-suffix)的语法定义。

函数字面量的语法参看[这里](posts/grammar#function-literals)


## 闭包

函数字面量(以及[局部函数](posts/functions#local-functions)和[对象表达式](posts/object-expressions)可以反问他们的*闭包*，
即包含他们的作用域中定义的变量。和**Java**不同，闭包变量是可以修改的。
{% highlight java %}
var sum = 0
ints filter {it > 0} forEach {
  sum += it
}
print(sum)
{% endhighlight %}


## 扩展函数字面量

除了普通函数，Kotlin还支持[扩展函数](pots/extension-functions)。
这种函数是如此有用，所以Kotlin也支持了扩展函数的字面量。
扩展函数字面量最重要的用途示例之一是[类型安全的Groovy风格构建器](posts/type-safe-groovy-style-builders)。

扩展函数和普通函数的不同之处在于它有一个*接受者类型*。扩展函数的字面量也可以指定其接受者类型：
{% highlight java %}
val sum = {Int.(other: Int) : Int -> this + other}
{% endhighlight %}

接受者类型只能在*完整语法形式*下指定。注意参数的类型和返回类型在完整形式下也是可以省略的。

这样的字面量拥有的函数类型也带上接受者：

{% highlight java %}
sum: Int.(other : Int) -> Int
{% endhighlight %}

这个函数可以使用'.'或者中序模式(因为只有一个参数)来调用：
{% highlight java %}
1.sum(2)
1 sum 2
{% endhighlight %}


### 和this表达式的歧义处理
参看[This表达式](posts/this-expressions)

   

## 接下来

### 表达式
* [返回和跳转](posts/returns-and-jumps)
* [范围](posts/ranges)
* [This表达式](posts/this-expression)
* [元组](posts/tuples)
* [类型转换](posts/type-casts)
### 函数
* [类型安全的Groovy风格构建器](posts/type-safe-groovy-style-builders)

