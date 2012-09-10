---
layout: doc
title: 枚举类 | Enum classes
original-doc: http://confluence.jetbrains.net/display/Kotlin/Enum+classes
---

**枚举**类最常见的用途是实现[和**Java**类型安全枚举](http://www.javacamp.org/designPattern/enum.html)：
{% highlight java %}
enum class Direction {
  NORTH; SOUTH; WEST; EAST
}
{% endhighlight %}

和Kotlin其他地方一样，如果每个枚举值单独一行的话，可以省略掉分号：
{% highlight java %}
enum class Direction {
  NORTH
  SOUTH
  WEST
  EST
}
{% endhighlight %}

<div class="warn">
<strong>枚举还在开发中</strong>
<br/>
参看相关的<a href="http://youtrack.jetbrains.com/issues/KT?q=%23unresolved+enum">issue</a>
</div>


## 开放枚举

在Kotlin中，**枚举**可以是**开放**(open)的，即它可以被其他枚举继承：
{% highlight java %}
open enum class OptionKeys { 
  OPTION1 
} 
enum class ExtraOptionKeys : OptionKeys { 
  OPTION2 
} 
fun demo() { 
  ExtraOptionKeys.OPTION2 : ExtraOptionKeys // legal 
  ExtraOptionKeys.OPTION1 : OptionKeys // legal 
  OptionKeys.OPTION2 // error: OPTION2 is not a member of OptionKeys 
}
{% endhighlight %}

枚举类可以扩展其他的枚举和**征**(trait)。

## 枚举常量是对象

因为枚举常量是**枚举类**的不同实例，所以他们可以进行不同的初始化：
{% highlight java %}
enum class Color(val rgb : Int) {
  RED : Color(0xFF0000)
  GREEN : Color(0x00FF00)
  BLUE : Color(0x0000FF)
}
{% endhighlight %}


## 枚举常量定义(匿名)类

每个枚举常量都可以定义自己的成员，或者覆盖**枚举类**的成员：
{% highlight java %}
enum class ProtocolState { 
  WAITING { 
    override fun signal() = TALKING 
  } 
 
  TALKING { 
    override fun signal() = WAITING 
  } 
 
  abstract fun signal() : ProtocolState 
}
{% endhighlight %}

## 接下来
* [内嵌类](posts/nested-classes)
* [对象表达式和声明](posts/object-expressions)


