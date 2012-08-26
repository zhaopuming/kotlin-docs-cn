---
layout: doc
title: 扩展函数 | Extension functions
original-doc: http://confluence.jetbrains.net/display/Kotlin/Extension+functions
---


## 动机(Motivation)

在**Java**中，我们往往使用\*Utils：FileUtils, StringUtils等等。著名的java.util.Collections类也是这一脉。
但是这些Utils类也有令人不愉快的地方，代码像这个样子：

{% highlight java %}
// Java
Collections.swap(list, Collections.binarySearch(list, Collections.max(otherList), Collections.max(list))
{% endhighlight %}


那些类名总是碍眼。我们可以使用静态导入来解决这个问题： 
{% highlight java %}
// Java
swap(list, binarySearch(list, max(otherList), max(list))
{% endhighlight %}


这样稍微好些了，但是我们得不到IDE强大的自动完成功能的帮助。如果我们能这么写，就会好得多：

{% highlight java %}
// Java
list.swap(list.binarySearch(otherList.max()), list.max())
{% endhighlight %}


但是我们不想(或不能)在List类里实现所有的这些函数，对吧？

所以，Kotlin(跟随着**C#**和**Gosu**)，引入了*扩展函数*：在List类之外定义的函数，可以当作List类的扩展来使用。
这种定义通过在函数声明中加上“接受类型”(要被扩展的类型)的标记来指定：

{% highlight java %}
fun List<T>.swap(x : Int, y : Int) {
  val tmp = this[x] // 'this'表示当前的List对象
  this[x] = this[y]
  this[y] = tmp
}
{% endhighlight %}
<!--[]()-->

扩展函数中的**this**关键字，用来表示当前的*接受对象*（调用时在'.'前面那个）。
现在我们可以在任意的List&lt;T&gt;类型的对象上调用swap()了

{% highlight java %}
val l = list(1, 2, 3)
l.swap(0, 2) // swap中的'this'，在这里代表对象l
{% endhighlight %}


当然，这个函数对任何List&gt;T&lt;都有意义，我们也可以把它做成泛型函数：
{% highlight java %}
fun <T> List<T>.swap(x : Int, y : Int) {
  val tmp = this[x] // 'this'表示当前的List对象
  this[x] = this[y]
  this[y] = tmp
}
{% endhighlight %}
<!--[]()-->

在“接受者”声明前方加上泛型类型声明就可以了。参看[泛型函数](posts/generics#generic-functions)

## 扩展函数是静态解析的

为了避免困惑，我们需要强调一下，扩展函数是*静态*解析的，也就是说他们不是随着“接受者类型”而有**虚函数**特点的。
另一方面，扩展函数也可以定义在某个类中，从而成为那个类的成员函数，参与到那个类的类型树中。

##  其他语言的类似属性

**AspectJ**有[跨类型定义](http://www.eclipse.org/aspectj/doc/released/adk15notebook/ataspectj-itds.html)

**Groovy**有[元类型](http://groovy.codehaus.org/JN3525-MetaClasses)

**Scala**使用隐式转换，即在运行时使用包装类封装原对象的方式来扩展。

[C#](http://msdn.microsoft.com/en-us/library/bb383977.aspx)和[Gosu](http://gosu-lang.org/doc/wwhelp/wwhimpl/api.htm?&context=gosu&src=enhancements&topic=Using_Enhancements)都有和我们差不多的方式实现的扩展函数机制。

## 接下来

* [函数字面量](posts/function-literals)
* [类型安全的Groovy风格构建器](posts/type-safe-groovy-style-builders)





