---
layout: doc
title: Hello World!
original-doc: http://confluence.jetbrains.net/display/Kotlin/Hello%2C+world%21
---

这个页面展示Kotlin的一些基本示例。

## 最简单的版本

{% highlight java %}
package demo

fun main(args : Array<String>) {
  println("Hello, world!")
}
{% endhighlight %}

第1行是可选的[package声明](posts/packages#package-header)

接下来我们声明一个package层次的函数`main`，
它的返回值类型为[Unit](posts/functions#functions-unit)，
参数是字符串数组([Array](posts/basic-types#array))

注意：[分号是可以省略的](posts/grammar#semicolons)

## 从命令行读入一个名字

{% highlight java %}

fun main(args : Array<String>) { 
  if (args.size == 0) { 
    println("Please provide a name as a command-line argument") 
    return 
  } 
  println("Hello, ${args[0]}!") 
}

{% endhighlight %}

第6行展示了[字符串模板](posts/strings#string-templates)和[数组访问](posts/basic-types#arrays)。

## “面向对象”的Hello World

{% highlight java %}
class Greeter(val name : String) { 
  fun greet() { 
    println("Hello, ${name}"); 
  } 
} 
 
fun main(args : Array<String>) { 
  Greeter(args[0]).greet() 
}
{% endhighlight %}

这里我们建立了一个简单的[类](posts/classes-and-inheritance)，包含一个*主构造函数*以及一个*成员函数*

注意在Kotlin里新建一个对象时不需要**new**。

## “多语言”的Hello World

{% highlight java %}
fun main(args : Array<String>) { 
  val language = if (args.size == 0) "EN" else args[0] 
  println(when (language) { 
    "EN" -> "Hello!" 
    "ES" -> "¡Hola!" 
    "RU" -> "Привет!" 
    else -> "Sorry, I can't greet you in $language yet" 
  }) 
}
{% endhighlight %}

在这个例子里，**val**表示声明一个**只读**的局部变量，使用一个[if-表达式](posts/control-structures#if-expression)来初始化赋值。接着我们使用了很基础的[模式匹配](posts/pattern-matching)表达式。


## 扩展函数hello()

{% highlight java %}
fun String.hello() {
  println("Hello, $this!")
}

fun main(args : Array<String>) {
  "world".hello() // prints "Hello, world!"
}
{% endhighlight %}

在这段代码里，我们定义了`String`类的一个[扩展函数](posts/extension-functions)，函数中的[字符串模板](posts/string#string-templates)使用了**this**表达式。
接着我们在一个字符串`"world"`上调用了这个函数。即，我们把这个字符串当作接受参数(**this**-参数)传递给了hello()。


## 类似LINQ的Hello

{% highlight java %}
fun main(args : Array<String>) {
  args filter {it.length() > 0} forEach {print("Hello, $it!")}
}
{% endhighlight %}

这里我们首先调用[高阶函数](posts/functions#higher-order-functions)filter()来选择args参数中长度大于0的字符串。为了达到这个目的，我们给filter传入一个检查参数长度的[函数字面量](posts/function-literals)，`it`是函数字面量中参数的默认名称。

Filter产生一个集合，然后我们对这个集合进行foreach()操作，对每个元素调用我们传入的函数字面量。这里我们直接使用传入的字符串打印出问候语。


## 接下来
* [基本语法](posts/basic-syntax-walkthrough)
