---
layout: default
title: Functions
original-doc: http://confluence.jetbrains.net/display/Kotlin/Functions
---

Kotlin中的函数使用**fun**关键字来定义：

{% highlight java %}
fun doube(x : Int) : Int {
  return x * 2
}
{% endhighlight %}

<!--*-->

函数的调用使用传统的方式：

{% highlight java %}
val two = demo(1)
{% endhighlight %}

参看[中序调用](posts/functions#infix-calls)和[符号重载](posts/operator-overloading)

## 单表达式函数

如果函数只返回一个单独的表达式的值，我们可以在函数声明后加上'='，再跟着表达式。这种情况下不需要使用**return**关键字。

{% highlight java %}
fun double(x : Int) : Int = x * 2
{% endhighlight %}

<!-- * -->


## 中序调用 <a id="infx-calls">#</a>


{% highlight java %}
{% endhighlight %}

