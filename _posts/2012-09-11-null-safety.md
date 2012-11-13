---
layout: doc
title: Null安全 | Null-safety
original-doc: http://confluence.jetbrains.net/display/Kotlin/Null-safety
---


## 可空类型和不可空类型

Java编程中最常见的问题是访问一个null成员引用，从而导致NullPointerException异常。
这个问题如此常见，正式因为几乎所有的Java对象，都是可以为空的。这个错误是如此常见，
以至于我们给它取了个缩略词：NPE。另外可以参看这个讲演："[价值十亿美金的错误](http://qconlondon.com/london-2009/presentation/Null+References:+The+Billion+Dollar+Mistake)"

Kotlin的类型系统设计目标之一就是从我们的代码中，消灭NullPointerExcetpion。NPE产生的来源只有下面几种：
1. 有人显式地抛出它(throw NullPointerException())
1. 有人调用了**Java**代码，其中产生了NPE
1. 数据不一致（比如一个构造函数中没有初始化的this被其他地方使用了）

在Kotlin中，类型系统区分那些可以为null的引用(*可空*引用)，以及那些不能的(*非空*引用)。
比如，String类的一个普通变量是不能为null的。
{% highlight java %}
var a : String = "abc"
a = null // 编译错误
{% endhighlight %}

如果需要允许null，必须声明类型为*可空String*，写做`String`?：

{% highlight java %}
var b : String? = "abc"
b = null // OK
{% endhighlight %}


现在，你可以安全地调用a的方法，因为编译器已经保证它不可能抛出NPE：
{% highlight java %}
val l = a.length()
{% endhighlight %}

但是如果你希望调用b的相同方法，就可能不安全了，所以编译器会报错：
{% highlight java %}
val l = b.length() // error: 变量'b'可能为null
{% endhighlight %}

但是我们仍然需要调用这个方法，对吧？ 有下面几种办法可以做到这一点：


## 在条件判断中检测null

首先，你可以显式地检查b是不是null，并且根据不同情况分别处理：
{% highlight java %}
val l = if (b != null) b.length else -1
{% endhighlight %}

编译器会跟踪你检查后的信息，并允许你在if分支里调用b.length()。
更复杂的条件也支持：
{% highlight java %}
if (b != null && b.length() > 0)
  print("String of length ${b.length()}")
else
  print("Empty String")
{% endhighlight %}


注意这种方式只有当b是不可变(immutable)的时候才有效。即b是一个局部**val**变量或者成员**val**变量，其实际存储是不可覆盖的)。
因为如果不是这样的话，那么b的值可能在检查之后被修改为null


## 安全调用
第二种选择是使用*安全调用*操作符，写做`?.`：
{% highlight java %}
b?.length()
{% endhighlight %}

这个操作符在b不是null时会返回b.length()，否则直接返回null。
这个表达式的类型是`Int?`。

安全调用在链式调用时非常方便。比如Bob是一个雇员(Employee)，属于一个部门(Department)，该部门有一个经理(Employee)，
那么我们若想得到Bob的部门经理的名称，可以如下调用：
{% highlight java %}
bob?.department?.head?.name
{% endhighlight %}

这样的调用链在其中任何一步中遇到null时，会直接返回null。

## 猫王操作符(Elvis Operator)
当我们得到一个可空的引用r时，我们常常说“如果r不是null，使用它，否则使用一个非空的值x”：
{% highlight java %}
val l : Int = if (b!= null) b.length() else -1
{% endhighlight %}

除了可以用完整的if表达式，我们也可以用*猫王操作符*，写做`?:`：

{% highlight java %}
val l = b?.length() ?: -1
{% endhighlight %}

如果`?:`左侧的表达式不是null，那么猫王操作符直接返回它，否则返回操作符右侧的表达式值。
注意操作符右侧的表达式只有在左侧为**null**时，才会去解析。

## `!!`操作符

第三个选择是为那些NPE喜好者准备的。可以直接写`b!!`，这个表达式，如果b不是null，则返回b，
如果是null，则抛出NPE异常。
{% highlight java %}
val l = b!!.length()
{% endhighlight %}

所以，如果你想要NPE，我们就给你NPE，但是必须你主动显式地要求，而不会突然出现。

顺便提一下，`!!`是为了代码风格精简而添加的，并且之前是使用扩展函数在标准库中实现的，如下：
{% highlight java %}
inline fun <T : Any> T?.sure() : T = 
  if (this == null) 
      throw NullPointerException() 
    else 
        this
{% endhighlight %}

## 安全转换
普通的类型转换(casts)可能会产生ClassCastException，如果转换的目标类和当前对象不匹配的话。
所以我们引入了安全转换符号`as?`，如果类型不匹配，则返回null。如下：
{% highlight java %}
val aInt : Int? = a as? Int
{% endhighlight %}

## 与这个语言特性相关的最佳实践
[Effective Java第二版](http://java.sun.com/docs/books/effective/) J.Bloch
**第38条**：检查参数的有效性
**第43条**：返回空的数组或者集合，而不是null

[Null对象设计模式](http://en.wikipedia.org/wiki/Null_Object_pattern)
最早由[B. Woolf](http://portal.acm.org/citation.cfm?id=273448.273450)提出。


## 其他语言中的类似特性
在**Java**中，看诶在方法和参数上添加标注(当JSR-308通过时，也可以在类型上标注)，
这些标注可以使用第三方工具检查代码的Null安全性。比如[IntelliJ IDEA支持@NotNull标注](http://www.jetbrains.com/idea/documentation/howto.html)。

**Scala**推广使用[选项类型(Option Type)](http://www.scala-lang.org/api/current/scala/Option.html)
来控制缺少的值(即null)。这种方法会带来运行时的消耗，因为值需要包装在Option类的对象中，并且使用时的语法也稍显罗嗦。

**C#**拥有[可空类型](hhttp://msdn.microsoft.com/en-us/library/1t3y8s4s.aspx)，其原理是将引用包裹进分配在栈上的*值类型*中，并且实际上并没有静态检查来避免调用null对象的方法。

**Groovy**有[安全导航](http://docs.codehaus.org/display/GROOVY/Operators#Operators-SafeNavigationOperator%28%3F.%29)和[猫王操作符](http://docs.codehaus.org/display/GROOVY/Operators#Operators-ElvisOperator%28%3F%3A%29)

**Gosu**的[方法](http://gosu-lang.org/doc/wwhelp/wwhimpl/api.htm?&context=gosu&src=expressions&topic=Handling_Null_Values_In_Expressions)和我们差不多。


## 接下来

* [异常处理](posts/exceptions)

