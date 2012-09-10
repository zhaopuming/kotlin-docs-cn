---
layout: doc
title: 内嵌类 | Nested classes
original-doc: http://confluence.jetbrains.net/display/Kotlin/Nested+classes
---

在类中可以内嵌定义其他的类：

{% highlight java %}
class Outer() {
  private val bar : Int = 1
  class Nested() {
    fun foo() = 2
  }
}

val demo = Outer.Inner().foo() // == 2
{% endhighlight %}

## 内部类 (Inner classes)

一个类可以标记为**inner**，这样可以访问其上层类的成员。内部类的可以携带上层类的成员的引用。

{% highlight java %}
class Outer() {
  private val bar : Int = 1
  inner class Inner() {
    fun foo() = bar
  }
}

val demo = Outer().Inner().foo() // == 1
{% endhighlight %}

参看[带限定的**this**表达式](posts/this-expressions#qualified)了解如何区别内部类和外部类的**this**


<div class="warn">
<strong>内部类还在开发中</strong>
<br/>
参看相应的<a href="http://youtrack.jetbrains.com/issue/KT-1174">issue</a>
</div>

## 接下来
* [对象表达式和声明](posts/object-expressions)

