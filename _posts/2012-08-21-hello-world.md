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

第一行是可选的[package声明](posts/packages#package-header)

接下来我们声明一个package层次的函数`main`，
它的返回值类型为[Unit](posts/functions#functions-unit)，
参数是字符串数组([Array](posts/basic-types#array))

注意：[分号是可选的](posts/grammar#semicolons)


