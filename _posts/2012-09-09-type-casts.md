---
layout: doc
title: 类型转换(Type casts)
original-doc: http://confluence.jetbrains.net/display/Kotlin/Type+casts
---

类型转换符号可以在进行运行时检验后改变一个表达式的静态类型。

## 智能转换

在Kotlin中，很多情况下不需要显式地使用类型转换符号，因为编译器
会跟踪[不可变量的**is**检验](posts/pattern-matching)，并在需要的地方自动插入类型转换：

{% highlight java %}
fun demo(x : Any) {
  if (x is String) {
    print(x.length) // x 被自动转换为String类型
  }
}
{% endhighlight %}

自动转换在[**when**表达式](posts/control-structures#when)和
[**while**循环](posts/control-structures#while)中也适用：

{% highlight java %}
when (x) {
  is Int -> print(x + 1)
  is String -> print(x.length + 1)
  is Array<Int> -> print(x.sum())
}
{% endhighlight %}


## "不安全"的转换操作符

通常，当转换不能进行时，转换操作符会抛出一个异常。因此我们称之为*不安全*。
在Kotlin中，这种*不安全*的转换是通过中序操作符**as**来实现的。
(参看[操作符优先级](posts/grammar#precedence))

{% highlight java %}
val x : String = y as String
{% endhighlight %}

注意**null**不能被转换成String类型，因为String不是[可空(nullable)类型](posts/null-safety)，
即，如果y是**null**, 上述代码会抛出一个异常。  
为了能够匹配Java的转换语法，我们需要使用可空类型，如：

{% highlight java %}
val x : String? = y as String?
{% endhighlight %}


"安全"(可空)的转换操作符

为了避免抛出异常，我们可以使用“安全”的转换操作符**as?**，这个符号如果遇到错误，则会返回**null**：

{% highlight java %}
val x : String? = y as? String
{% endhighlight %}

注意，即使**as?**符号右侧的类型是不可空的String，但转换之后的结果是可空的String?类型。

## 接下来：

* [模式匹配](posts/pattern-matching)

