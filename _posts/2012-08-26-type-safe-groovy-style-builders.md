---
layout: doc
title: 类型安全的Groovy风格构建器
original-doc: http://confluence.jetbrains.net/display/Kotlin/Type-safe+Groovy-style+builders
---

[构建器](http://groovy.codehaus.org/Builders)(builders)的概念在**Groovy**社区非常热门。
使用构建器我们可以用半声明(semi-declarative)的方式定义数据。
构建器非常适合用来生成[XML](http://groovy.codehaus.org/GroovyMarkup)，[组装UI组件](http://groovy.codehaus.org/GroovySWT), [描述3D场景](http://www.artima.com/weblogs/viewpost.jsp?thread=296081)，以及很多其他功能...

很多情况下，Kotlin允许**检查类型**的构建器，这样比**Groovy**本身提供的构建器更有吸引力。

其他情况下，Kotlin也支持**动态**的构建器。

## 一个类型安全的构建器的示例

考虑下面的代码。这段代码是从[这里](http://groovy.codehaus.org/Builders)摘出来并稍作修改的

{% highlight java %}
import html.* 

fun result(args: Array<String>) =
  html {
    head {
      title {+"XML encoding with Kotlin"}
    }
    body {
      h1 {+"XML encoding with Kotlin"}
      p {+"this format can be used as an alternative markup to XML"}

      // 一个元素，拥有属性和文字内容
      a(href = "http://jetbrains.com/kotlin"){+"Kotlin"}

      // 混合内容
      p {
        +"This is some"
        b {+"mixed"}
        +"text. For more see the"
        a(href = "http://jetbrains.com/kotlin") {+"Kotlin"}
        +"project"
      }
      p {+"some text"}

      // 可以生成内容
      p {
        for (arg in args)
          +arg
      }
    }
  }
{% endhighlight %}
<!-- * -->

这是一段完全合法的Kotlin代码。(在IDEA中)，可以点击函数名称浏览他们的定义代码。

## 构建器的实现原理

让我们一步一步了解Kotlin中的类型安全构建器是如何实现的。
首先我们需要定义构建的模型，在这里我们需要构建的是HTML标签的模型。
用一些类就可以轻易实现。比如**HTML**是一个类，描述`<html>`标签；它定义了子标签`<head>`和`<body>`。
（在[下面](posts/type-safe-groovy-style-builders#difinitions)可以查看他们的定义）

现在我们先回忆一下我们在构建器代码中这么声明:

{% highlight java %}
html {
  // ...
}
{% endhighlight %}

这实际上是一个函数，其参数是一个[函数字面量](posts/function-literals)(查看[这个页面](posts/functions#higher-order-functions)的详细说明)
这个函数定义如下：

{% highlight java %}
fun html(init : HTML.() -> Unit) : HTML {
  val html = HTML()
  html.init()
  return html
}
{% endhighlight %}

这个函数定义一个叫做init的参数，本身是个函数。实际上，它是一个[扩展函数](posts/extension-functions)，其接受者类型为HTML(并且返回[Unit](posts/functions#unit))。
所以，当我们传入一个函数字面量作为参数时， 它被认定为一个扩展函数，从而在内部就可以使用**this**引用了。

{% highlight java %}
html {
  this.head { /* ... */ }
  this.body { /* ... */ }
}
{% endhighlight %}

(head 和 body都是HTML类的成员函数)

现在，和平时一样，**this**可以省略掉，所以我们就可以得到一段已经很有构建器风格的代码：

{% highlight java %}
html {
  head { /* ... */ }
  body { /* ... */ }
}
{% endhighlight %}

那么，这个调用做了什么？ 让我们看看上面定义的html函数的函数体。它新建了一个HTML对象，
接着调用传入的函数来初始化它，（在我们上面的HTML例子中，在html对象上调用了body()函数），
接着返回this实例。这正是构建器所应做的。

HTML类里定义的`head`和`body`函数的定义类似于`html`函数。唯一的区别是，它们将新建的实力先添加到html的children属性上，再返回：

{% highlight java %}
fun head(init: Head.() -> Unit) {
  val head = Head()
  head.init()
  children.add(head)
  return head
}

fun body(init : Body.() -> Unit) { 
  val body = Body() 
  body.init() 
  children.add(body) 
  return body 
}
{% endhighlight %}

实际上这两个函数做的是完全相同的事情，所以我们可以定义一个泛型函数`initTag`：


{% highlight java %}
protected fun initTag<T : Element>(init : T.() -> Unit) : T 
    where class object T : Factory<T> { 
  val tag = T.create() 
  tag.init() 
  children.add(tag) 
  return tag 
}
{% endhighlight %}

这个函数使用了[类对象](posts/classes-and-inheritance#class-objects)来初始化类。
它依赖于下面定义的`Factory`类：

{% highlight java %}
abstract class Factory<T> {
  fun create() : T
}
{% endhighlight %}

现在，类`Head`和`Body`中定义一个继承`Factory`的类对象，比如：


{% highlight java %}
class Head() : TagWithText("head") {
  class object : Factory<Head> {
    override fun create() = Head()
  }
  // ...
}
{% endhighlight %}

<Head></Head>

所以我们的函数就很简单：

{% highlight java %}
fun head(init : Head.() -> Unit) = initTag(init)

fun body(init : Body.() -> Unit) = initTag(init)
{% endhighlight %}


这样我们就可以用它们来构建`<head>`和`<body>`标签了。

另外一个需要讨论的是如何给标签添加文本内容。在上面的例子里我们使用了如下的方式：

{% highlight java %}
html { 
  head { 
    title {+"XML encoding with Kotlin"} 
  } 
  // ... 
}
{% endhighlight %}

所以基本上，我们直接在标签体中添加文字，但前面需要在前面加一个"+"符号。事实上她

