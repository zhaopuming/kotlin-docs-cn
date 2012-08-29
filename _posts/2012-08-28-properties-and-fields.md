---
layout: doc
title: 属性和字段
original-doc: http://confluence.jetbrains.net/display/Kotlin/Hello%2C+world%21
---

## Java的字段访问

在**Java**世界中，我们已经习惯了使用*getter*和*setter*方法来访问*字段*。
在[《Effective Java》](http://java.sun.com/docs/books/effective/)中的第14条说明：
**在公共类中，应当使用访问方法，而不是公共字段**。
这样的说法已经不言而喻了。所有的IDE都支持这个：他们可以生成getters和setters，所以写出这样一个类并不困难：

{% highlight java %}

// Java
public class Address {
 
    private String name;
    private String street;
    private String city;
    private String state;
    private String zip;
 
    public String getName() {
        return name;
    }
 
    public void setName(String name) {
        this.name = name;
    }
 
    public String getStreet() {
        return street;
    }
 
    public void setStreet(String street) {
        this.street = street;
    }
 
    public String getCity() {
        return city;
    }
 
    public void setCity(String city) {
        this.city = city;
    }
 
    public String getState() {
        return state;
    }
 
    public void setState(String state) {
        this.state = state;
    }
 
    public String getZip() {
        return zip;
    }
 
    public void setZip(String zip) {
        this.zip = zip;
    }
}
{% endhighlight %}


这个类里的大部分代码行都是架子代码(boilerplate code)。


## 摆脱字段/get/set组合

在Kotlin中，事实上并没有办法直接定义一个*字段*(fields)。你只能有*属性*(properties)。
可读/写的属性，使用`var`关键字来定义；而只读的属性，使用`val`关键字。
所以，上面的Java类在Kotlin里可以这么写：

{% highlight java %}
public class Address() { // 括号定义了主构造函数
  public var name : String = ... 
  public var street String = ... 
  public var city : String = ... 
  public var state : String? = ... 
  public var zip : String = ... 
}
{% endhighlight %}

这里我们有5个*可变*的属性，每一个属性，编译器会生成一个相应的*实际字段*来存储值，以及两个访问器：getter和setter。
所以，代码生成的bytecode和上面的Java代码几乎一样。唯一的区别是属性的初始化代码，参看[Null安全](posts/null-safety)

（还有更好的选择，参看[这里](posts/classes-and-inheritance#bean-class))

要使用一个属性，只需要使用名称引用即可，就相当于Java中的公共字段：

{% highlight java %}
fun copyAddress(address : Address) : Address { 
  val result = Address() // there's no 'new' keyword in Kotlin 
  result.name = address.name // accessors are called 
  result.street = address.street 
  // ... 
  return result 
}
{% endhighlight %}


## 声明属性和访问器

一个可变属性的完整定义语法如下：

{% highlight java %}
var <propertyName> : <PropertyType> [= <property_initializer>] 
  <getter> 
  <setter>
{% endhighlight %} <!--[]()-->

上面的定义中，初始器(initializer)、getter和setter都是可选的。
属性类型(PropertyType)如果可以从初始器或者父类中推导出来，也可以省略。

示例如下：

{% highlight java %}
var allByDefault : Int? // 错误：需要显式地声明初始化器，默认的getter和setter可以推导出来
var initialized = 1 // 类型是Int, 使用默认的getter和setter
var setterVisiblility : String = "abc" // 需要初始化，因为不是nullable的类型
  private set // setter是私有的，并且使用默认实现
{% endhighlight %}

注意公有的API(即**public**和**protected**)的属性，类型是不做推导的。
这么设计是为了防止改变初始化器时不小心改变了公有API。比如：

{% highlight java %}
public val example = 1 // 错误：公有的属性必须显示指定类型
{% endhighlight %}

*不可变属性*的定义和*可变属性*有两点区别：它以`val`开头，并且不允许设置setter：

{% highlight java %}
val simple : Int? // 类型是Int, 默认getter, 必须在构造函数里初始化
val inferredType = 1 // 类型是Int, 默认getter
{% endhighlight %}

我们可以自定义getter/setter，方式和定义普通函数很像。接着属性声明的后面，可以写函数体。例如：

{% highlight java %}
val isEmpty : Boolean
  get() = this.size == 0
{% endhighlight %}

因为这个属性是完全由其他的属性值推导出来的，编译器不会给它单独分配一个*实际字段*

自定义的setter是这个样子：

{% highlight java %}
var stringRepresentation : String
  get() = this.toString()
  set(value) {
    setDataFromString(value) // 从字符串中解析出来并向其他属性赋值
  }
{% endhighlight %}


## 实际字段

我们上面已经提到，有的属性编译器会生成一个*实际的字段*(backing field)用来存放值。
从用户的角度看，属性是一对(有时候是一个)访问器，但实际上这些访问器可能会读取或修改一个真实的字段。
在Kotlin里，直接声明一个字段是不允许的，编译器会替我们生成他们。

在简单的情况下， 当我们没有提供自定义的访问器实现，显然属性需要有一个实际字段。否则的话，默认的访问器该做什么呢？ 

{% highlight java %}
var counter : Int = 0
{% endhighlight %}

但是当有自定义访问器的时候，属性则不一定需要一个实际字段。
如果要访问一个属性x的实际字段，使用`$x`(在Kotlin中，美元符号不能作为标识符的开头，这一点和Java不同)

{% highlight java %}
var counter = 0 // 初始化的值被直接写入实际字段
  set(value) {
    if (value >= 0)
      $counter = value
  }
{% endhighlight %}

上面的`$counter`字段就可以在counter属性的访问器实现里读和写。并且只能在构造函数里赋值。在其他地方，都不能使用或访问`$counter`

编译器会查看访问器的内部， 如果他们使用了实际字段（或者访问器使用默认实现），那么将会生成一个实际字段，否则不会生成。

例如，下面的情况下， 就没有实际字段：

{% highlight java %}
val isEmpty : Boolean
  get() = this.size > 0
{% endhighlight %}

实际字段在这里不需要，是因为唯一的访问器并不引用它。


## 如果我想...怎么办？

如果你的需求不符合这套“隐式的实际字段“方案，那么总可以使用“后背支持属性”(backing property)的方法：

{% highlight java %}
private var _table : Map<String, Int>? = null 
public val table : Map<String, Int> 
  get() { 
    if (_table == null) 
      _table = HashMap() // 类型参数可以推导
    return _table ?: throw AssertionError("Set to null by another thread") 
  }
{% endhighlight %}

从各种角度看，这和在Java中定义Bean属性的方式一样。因为访问私有的属性的getter和setter函数，会被编译器优化成直接反问其实际字段。

## 覆盖属性

参看[覆盖属性](posts/classes-and-inheritance#overridable-properties)

## 这个特性的最佳实践
[《Effective Java 第二版》J.Bloch](http://java.sun.com/docs/books/effective/)
**第14条**: 在公共类中，使用访问函数，而不是直接使用公共字段。

另外参看：[JavaBeans](http://www.oracle.com/technetwork/java/javase/tech/index-jsp-138795.html)

## 其他语言的类似特性

Java的IDE会[自动](http://www.jetbrains.com/idea/features/code_generation.html#link1)生成字段的访问器

对于**Java**，有[Project Lombok](http://projectlombok.org/): 属性的语法是依赖于标注的。

在[**C#**](http://msdn.microsoft.com/en-us/library/x9fsa0sw.aspx), [**Groovy** Beans](http://groovy.codehaus.org/Groovy+Beans) 
以及[**Gosu**](http://gosu-lang.org/doc/wwhelp/wwhimpl/api.htm?&context=gosu&src=classes&topic=Properties)语言中，
仍然声明实际字段，并显式写出getter和setter，虽然使用时和属性的访问非常像。
[Scala](http://www.scala-lang.org/node/29)并不区分字段和属性，但是如果想要自定义一个setter，
需要单独写一个函数，其名称依赖于规范约定。

## 接下来

* [基本类型](posts/basic-types)



