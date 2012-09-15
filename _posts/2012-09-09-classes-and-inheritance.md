---
layout: doc
title: 类和继承 | Classes and inheritance
original-doc: http://confluence.jetbrains.net/display/Kotlin/Classes+and+Inheritance
---


## 类和原始构造函数(Primary constructor)

下面是Kotlin中定义一个类的示例：
{% highlight java %}
class Example(param: Int) {
  val property = param
}
{% endhighlight %}

我们从第一行的类声明头说起：

{% highlight java %}
class Example(param : Int) {
{% endhighlight %}

这一行声明了类`Example`，它有一个构造函数，并且接收一个Int型参数。
这种直接定义在类声明头中的构造函数，称为*原始构造函数*(primary constructors)。

若要新建一个Example类的实例，只要直接调用构造函数即可：
{% highlight java %}
val e = Example(10)
{% endhighlight %}
(注意，和**Java**不同，不需要**new**关键字)

那么原始构造函数做了哪些事情呢？首先它会按照声明的顺序，运行各个属性(properties)的初始化函数。
在上面的例子中，构造函数唯一的任务就是初始化property属性。

另外，在类代码中可以添加"匿名初始化器"，只要将代码用花括号包起来即可：
{% highlight java %}
class ExampleWithAnonymousInitializer(param: Int) {
  val property = HashSet<Int>()
  {
    property.add(param)
  }
}
{% endhighlight %}

这里，原始构造函数首先新建一个HashSet，并赋值给property，再将param的值加到propert中。


原始构造函数的参数可以前缀**val**或者**var**，这样就可以直接定义一个属性：
{% highlight java %}
class Bean(val prop1: Int, val prop2: String)
{% endhighlight %}

类的正体是可以省略的，所以上面的例子是一个完整的类定义代码，这个类有两个属性：**prop1**和**prop2**，
一个带两个参数的原始构造函数，并且直接初始化两个属性。

所以，在Kotlin中，定义一个*bean*风格的数据类非常容易，比Java少很多代码。


## 类成员

类有如下几种成员：

* [函数](posts/functions)
* [属性](posts/properties-and-fields)
* [其他类](posts/nested-classes)
* [对象定义](posts/object-expressions)


## 继承

Kotlin中所有的类都有一个共同的父类`Any`。这个类也新建类型时的默认父类：
{% highlight java %}
class Example // 隐式地继承Any
{% endhighlight %}


Any并不是java.lang.Object；特别地，它并没有任何成员，甚至连equals()，hashCode()和toString()都没有。
这并不意味着你不能在对象上调用toString()；你可以，因为Kotlin中有[扩展函数](posts/extension-functions)。
详情请参看[Java互操作#Object方法](posts/java-interoperability#object-methods)一节。

若要显式地声明一个父类，可以在类声明头后面加上冒号:
{% highlight java %}
open class Base(p : Int)

class Derived(p : Int) : Base(p)
{% endhighlight %}

如上所见，父类可以（并且必须）在声明继承的地方，用原始构造函数初始化。

父类上的**open**标注相当与**Java**中的**final**的反面：
它表示该类允许其他类继承它。
Kotlin的类，默认是**final**的，
这和[*Effective Java*](http://java.sun.com/docs/books/effective)
一书中的第17条：**设计并显示标注继承，否则就禁止它**
(Design and document for inheritance or else prohibit it)相符。


一个类可以有*很多基类*。详情参考这篇[文章](http://blog.jetbrains.com/kotlin/2011/08/multiple-inheritance-part-2-possible-directions/)
    

## 覆盖成员

我们之前提到过，Kotlin力求清晰显式。所以和**Java**不同，在继承父类并覆盖父类函数时，Kotlin要求父类必须有**open**标注，
被覆盖的函数必须有**open**标注，并且子类的函数必须加上**override**标注。

{% highlight java %}
open class Base {
  open fun v() {}
  fun nv() {}
}

class Derived() : Base() {
  override fun v() {}
}
{% endhighlight %}

Derived.v()函数上必须加上**override**标注。如果没写，编译器将会报错。
如果父类的这个函数没有标注**open**，则子类中不允许定义同名函数，不论加不加**override**。
在一个**final**类中（即没有声明**open**的类），函数上也不允许加**open**标注。


如果一个成员加上了**override**标注，那么它本身也是**open**的，即它可以被子类的函数覆盖。
如果这个时候你想要禁止被继承，则显示地加上**final**:

{% highlight java %}
open class AnnotherDerived() : Base() {
  final override fun v() {}
}
{% endhighlight %}


### 等等!!这样我怎么hack我的库？

我们这样设计继承和覆盖的方式(类和成员默认**final**)，会让人很难继承第三方的类，因此很难进行hack。

我们认为这并不是缺点，如下原因：

1. 最佳实践已经表明不应该使用这些hacks
1. 其他的有类似机制的语言(C++, C#)已经证明是成功的
1. 如果人们实在想hack，仍然有办法：比如某些情况下可以使用Java进行hack，再用Kotlin调用；或者使用面向切面的框架(Aspect)


## 征(Trait)

*征(traits)*其实就是接口加上可以省略的函数体。详情参考这篇[文章](http://blog.jetbrains.com/kotlin/2011/08/multiple-inheritance-part-2-possible-directions/)。


## 覆盖规则

在Kotlin中，实现的继承遵从如下规则：
如果一个类的成员从其直接父类中继承了多个实现，则必须覆盖这个函数，并提供自己的实现(当然，可以调用父类的实现)。
在调用父类的成员时，使用**this**加上类型名称来指定哪一个父类的实现：

{% highlight java %}
open class A {
  open fun f() {print("A")}
  fun a() { print("a") }
}

trait B {
  open fun f() { print("B") }
  open fun b() { print("b") }
}

class C() : A(), B {
  // 编译器要求f()必须是override
  override fun f() {
    super<A>.f() // 调用A.f()
    super<B>.f() // 调用B.f()
  }
}
{% endhighlight %} <!----> 

<B>haha</B>

类C同时继承A和B是可以的，而且我们在调用a()和b()函数时没有任何问题，因为他们在C的基类中只有一个实现。
但是f()函数则在A,B中都有实现，所以我们*必须*在C中覆盖f()，并且提供我们的实现以消除歧义。


## 抽象类 (Abstract class)

和**Java**一样，类和其中的某些实现可以声明为**abstract**。
一个**抽象**的成员，在本类中可以不用实现。所以在子类继承该类时，它也没有为子类提供一个实现。
{% highlight java %}
abstract class A {
  abstract fun f()
}

trait B {
  open fun f() { print("B") }
}

class C() : A(), B {
  // 我们并不需要覆盖f()函数
}
{% endhighlight %}

注意我们并不需要标注一个**抽象**类为**open**，因为这自明其义。**抽象**函数也不需要标注**open**


我们可以**覆盖**一个非抽象的**open**的成员，使之成为一个**抽象**的成员
{% highlight java %}
open class Base {
  open fun t() {}
}

abstract class Derived : Base() {
  override abstract fun f()
}
{% endhighlight %}


## 可覆盖的属性和访问器

和函数一样，属性也可以标注为**open**。这实际上意味着这个属性的*访问器*(accessors)可以被覆盖。

{% highlight java %}
open class Base { 
  open val p : Int 
    get() = 1 
} 
class Derived : Base() { 
  override val p : Int 
    get() = 2 
}
{% endhighlight %}

也可以更精细地单独设置getter或者setter为**open**，并在子类中单独覆盖它们：

{% highlight java %}
open class Base { 
  var p : Int 
    get() = 1 
    open set(value) { /* 什么都不做 */ } 
} 
class Derived : Base() { 
  var p : Int 
    /* get直接继承过来了 */
    override set(value) { print(value) } 
}
{% endhighlight %}



## 代理

[代理模式](http://en.wikipedia.org/wiki/Delegation_pattern)已经被证明是继承实现的一个很好的替代方法(替代类继承模式)。
Kotlin直接支持这个模式，并且不需要过多的框架代码。
子类Derived可以继承一个征Base，并且代理所有的公有函数到一个特定的对象上：

{% highlight java %}
it Base { 
  fun print() 
} 
 
class BaseImpl(val x : Int) : Base { 
  override fun print() { print(x) } 
} 
 
class Derived(b : Base) : Base by b 
 
fun main() { 
  val b = BaseImpl(10) 
  Derived(b).print() // prints 10 
}
{% endhighlight %}

上面的**by**语句表明b对象将被存放在Derived类的对象之中，并且编译器会生成Derived的所有函数，并转发调用b对象的函数。


## 泛型类

参看[泛型](posts/generics)


## 类对象
在Kotlin中，和**Java**不一样，类中没有**静态**方法(static methods)。

大多数情况下，[名字空间级别的函数](posts/packages)是静态方法的很好替代，但也有些情况下不是。
这些情况下，我们需要访问类内部的信息(如私有成员)。

例如：如果想替换构造函数而使用[工厂方法](http://en.wikipedia.org/wiki/Factory_method_pattern)，
通常的做法是将构造函数声明为**私有**，再提供函数来调用构造函数。
但是如果这个函数在类之外，则无法访问到私有的构造函数了。

为了解决这个问题(并提供其他一些有趣的特性)，Kotlin引入了**类对象**(class object)的概念。
其他语言中，最相似的概念是**Scala**的[伴侣对象](http://programming-scala.labs.oreilly.com/ch06.html#CompanionObjects)

粗略地说，类C的**类对象**是和C相关联的一个对象(在[对象声明](posts/object-expressions)的角度看)。
每个类最多只能有一个类对象。类对象定义在所属的类之中，所以可以访问其**私有**成员。
C类的类对象，往往不是其实例。比如：

{% highlight java %}
class C() {
  class object {
    fun create() = C()
  }
}

fun main() {
  val c = C.reate() // 这里的C表示C类的类对象
}
{% endhighlight %}


乍看之下可能会觉得这只是一种将静态函数组织起来而不和其他函数混杂在一起的方式：
在Java中，调用静态函数也是使用C.foo()的方式，和Kotlin的类对象函数一致。
但是实际上还是有重要的区别：**类对象**可以有其他的基类；并且C，作为这个类对象的表达式，可以当作值来传递，
可以作为函数的参数使用。让我们修改上面的例子来说明这一点：

{% highlight java %}
abstract class Factory<out T> { 
  fun create() : T 
} 
 
open class C() { 
  class object : Factory<C> { 
    override fun create() : C = C() 
  } 
} 
 
fun main() { 
  val factory = C // C 表示类对象
  val c = factory.create() 
}
{% endhighlight %}


<div class="warn">
<strong>类对象的类型限定现在还不支持</strong>
<br/>
参看相应的<a href="http://youtrack.jetbrains.com/issue/KT-1437">issue</a>
</div>


**注意**类对象从来不继承：
{% highlight java %}
class D : C()
val d = D.create() // Error: D没有类对象
{% endhighlight %}


**类对象**带来的其他有趣的特性，在[泛型限制](posts/generics#class-objects)一节中描述。

**注意**：如果你认为**类对象**是在Kotlin中实现单例的好方法，请参看[对象表达式和声明](posts/object-expressions)

## 关于这个特性的最佳实践

Joshua Bloch的[Effective Java第二版](http://java.sun.com/docs/books/effective/)  
**第16条**: 组合优于继承
**第17条**: 设计并显式指明继承，否则就禁止它
**第18条**: 接口优先于抽象类


## 其他语言的类似特性

IDE可以自动[生成代理方法和](http://www.jetbrains.com/idea/features/code_generation.html#link1)


[Lombok工程](http://projectlombok.org/features/Delegate.html)在**Java**中使用标注实现了代理功能

**Scala**有特征(Traits)

**Groovy**有[类别和汇件](http://groovy.codehaus.org/Category+and+Mixin+transformations)(Categories and Mixins)


## 接下来
* [枚举类](posts/enum-classes)
* [内嵌类](posts/nested-classes)
* [对象表达式和声明](posts/object-expressions)

