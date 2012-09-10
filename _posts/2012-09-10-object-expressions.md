---
layout: doc
title: 对象表达式和声明 | Object expressions and Declarations
original-doc: http://confluence.jetbrains.net/display/Kotlin/Object+expressions+and+Declarations
---


有时候我们需要新建一个类的对象，但希望对其稍作修改，又不想要单独这显式地新建一个子类。
**Java**使用*匿名内部类*来实现这个功能。
Kotlin将此概念稍稍泛化，因此产生了**对象**表达式和**对象**声明。

## 对象表达式

若想新建某类的一个匿名子类的对象，可以这么写：
{% highlight java %}
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e : MouseEvent) {
      // ...
    }
    //
    override fun mouseEntered(e : MouseEvent) {
      // ...
    }
})
{% endhighlight %}

如果父型有构造函数，则相应的构造函数参数必须传入到对象表达式上。
如果有多个父型，则可以使用逗号隔开：
{% highlight java %}
open class A(x : Int) {
  public open val y : Int = x
}

trait B {...}

val ab = object : A(1), B {
  override val y = 15
}
{% endhighlight %}


如果万一我们只需要一个对象，并不需要特别的父型，则可以直接这么写：
{% highlight java %}
val adHoc = object {
  var x : Int = 0
  var y : Int = 0
}

print(adHoc.x + adHoc.y)
{% endhighlight %}


## 对象声明
[单例](http://en.wikipedia.org/wiki/Singleton_pattern)是一个很实用的模式，
而Kotlin(跟随**Scala**)设计使得我们可以非常容易得声明单例：

{% highlight java %}
object DataProviderManager {
  fun registerDataProvider(provider: DataProvider) {
    // ...
  }

  val allDataProviders : Collection<DataProvider>
    get() = // ...
}
{% endhighlight %}

这被称为*对象声明*。如果**object**关键字后面跟随了一个名称，那么就不再是一个*表达式*了。
我们不能把这个对象声明赋值给其他变量，但是我们可以通过名称引用它。这种对象也可以有父型：
{% highlight java %}
object DefaultListener : MouseAdapter() { 
  override fun mouseClicked(e : MouseEvent) { 
    // ... 
  } 
 
  override fun mouseEntered(e : MouseEvent) { 
    // ... 
  } 
}
{% endhighlight %}

## 对象表达式和对象声明在语义上的区别

**对象**表达式和**对象**声明在语义上有一个重要的区别：
* **对象**声明是延迟初始化的--当首次被访问时才初始化
* **对象**表达式是立即执行(以及初始化)的，即在它们使用的地方

## 接下来
* [泛型](posts/generics)


