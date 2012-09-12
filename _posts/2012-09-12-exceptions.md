---
layout: doc
title: 异常处理 | Exceptions
original-doc: http://confluence.jetbrains.net/display/Kotlin/Exceptions
---

Kotlin中的异常和其他的语言没有多大不同。
和*Java*的一个巨大的不同，是Kotlin中没有需查异常(checked exceptions)，
你可以在[下面](posts/exceptions)看到这样设计的动机。

## 异常类

在Kotlin中，所有的异常类都是Exception类的子类。
每个异常都有一个信息(message)，一个跟踪栈(stack trace)，和可省略的异常起因(cause)。

若要抛出一个异常，可以使用**throw**关键字：
{% highlight java %}
throw MyException("Hi there!")
{% endhighlight %}

若要捕获一个异常，可以使用**try**表达式：
{% highlight java %}
try {
  // 代码
}
catch (e : SomeException) {
  // 处理函数
}
finally {
  // 可省略的最终代码段
}
{% endhighlight %}

和通常一样，**catch**代码块可以有0到多个，而**finally**块可以省略掉。但是**catch**和**finally**两者最少要有一个存在。

注意**try**也是一个*表达式*，所以它可以有一个值：
{% highlight java %}
val a : Int? = try { parseInt(input) } catch (e : NumberFormatException) { null }
{% endhighlight %}

如果没有**finally**代码块，那么**try**表达式的返回值，是**try**代码块的最后一个表达式的值
或者**catch**块的最后一个表达式的值。

如果**finally**代码块存在，那么它的最后一个表达式的值就是整个**try**表达式的值。

## <a id="why"><!----></a> 为什么Kotlin没有需查异常

有**Java**背景的人可能会奇怪为什么Kotlin中没有需查异常(checked exceptions)。
这个话题已经被讨论太多，因此我们只提供几段引用，并举个简单的例子：

我们的例子是java.lang.Appendable，JDK的一个接口，被StringBuilder实现。和其他接口类似，它有如下定义：
{% highlight java %}
Appendable append(CharSequence csq) throws IOException;
{% endhighlight %}

这个签名说明了什么？ 说明我们*每次*往一个Appendable对象(如一个StringBuilder, 一段log，或者命令行)上附加一个字符串时，
都*必须*捕获那些IO异常。为什么？ 因为它*可能*进行IO操作(Writer也实现Appendable)...所以结果是所有的地方都会出现这样的代码：
{% highlight java %}
try {
  log.append(message);
}
catch (IOException e) {
  // 一定是安全的
}
{% endhighlight %}

而这样并不好。参见[Effective Java](http://java.sun.com/docs/books/effective)
的第65条：*不要忽略异常*。

## 其他讨论的引用

Bruce Eckel 在[Java需要需查异常吗？](http://www.mindview.net/Etc/Discussions/CheckedExceptions)一文中说：
> 观察小的程序的出这样的结论：要求必须写出异常的说明，既可以提高开发效率，也可以提高代码可读性；
> 但是大规模的软件项目中，往往有相反的结论：开发效率降低，而提高的代码质量很少甚至没有。
> (Examination of small programs leads to the conclusion that requiring exception specifications could both enhance developer productivity and enhance code quality, but experience with large software projects suggests a different result – decreased productivity and little or no increase in code quality.)

其他类似的引用：
1. Rod Waldhoff - [Java的需查异常是错误的](http://radio-weblogs.com/0122027/stories/2003/04/01/JavasCheckedExceptionsWereAMistake.html)
1. Anders Hejlsberg - [需查异常引起的麻烦](http://www.artima.com/intv/handcuffs.html)

## Java互操作性
可以在[这里](posts/java-interoperability#checked-exceptions)参看**Java**的互操作性。

## 接下来
* [Java互操作性](posts/java-interoperability)



