---
layout: doc
title: Java互操作性 | Java Interoperability
original-doc: http://confluence.jetbrains.net/display/Kotlin/Java+interoperability
---

Kotlin从最初设计时就一直考虑到和**Java**的互操作性。
已有的**Java**代码在Kotlin中可以非常自然地调用，而从**Java**中调用Kotlin代码，也相当顺畅。

# 在Kotlin中调用Java代码

这一节我们介绍在Kotlin中调用Java代码的一些细节。在大多数情况下，直接使用即可：
{% highlight java %}
import java.util.*
fun demo(source : List<Int>) { 
  val list = ArrayList<Int>() 
  // 'for'循环对Java集合也通用
  for (item in source) 
    list.add(item) 
  // 操作符传统也保留下来
  for (i in 0..source.size() - 1) 
    list[i] = source[i] // 数组操作符'[]'，实际调用的是get()和set()
}
{% endhighlight %} <!-- * []() -->

### 返回void的方法
如果一个**Java**方法返回**void**，那么在Kotlin中，它会返回*Unit*。
万一有人使用它的返回值，Kotlin的编译器会在调用的地方赋值，因为这个值本身已经提前可以预知了(这个值就是`()`)。

### 将Java代码中与Kotlin关键字冲突的标识符进行转义
Kotlin的关键字中，有一些在Java中是合法的变量**标识符**，如**in**, **object**, **is**等。
所以在你喜欢的**Java**库中，可能已经使用了这些标识符作为变量或方法名称。
在这种情况下，你仍然可以使用那些方法，但需要对其标识符进行转义，以免和Kotlin的关键字冲突。
转义的方法是使用倒引号`` ` ``：

{% highlight java %}
foo.`is`(bar)
{% endhighlight %}

### <a id="null-safety"><!----></a> Null安全

**Java**中的所有引用都可能是null值。所以，所有的**Java**调用都返回[可空引用](posts/null-safety)，
除了那些标记为@NotNull的方法之外。这样的调用规则，使得Kotlin可以保证所有的代码，除非显式地抛出NullPointerException，
或者在调用的**Java**代码内部抛出，其他地方都没有NPE。

考虑下面的例子：
{% highlight java %}
val list = ArrayList<Int>() // 非null (构造函数的结果)
val size = list.size()  // 非null (原始类型)
val iterator = list.iterator() // 可空 (普通方法)
{% endhighlight %}


### Kotlin中调用Java泛型

Kotlin的泛型和**Java**稍有不同(参看[泛型](posts/generics))。在Kotlin中导入**Java**的类型时，
我们需要进行以下转换：

* **Java**的通配符需要转换成Kotlin的[类型映射](posts/generics#type-projections)
  * `Foo<? extends Bar>` 变成 `Foo<out Bar>`
  * `Foo<? super Bar>` 变成 `Foo<in Bar>`
* **Java**的生肉类型需要转换成Kotlin的[星号映射](posts/generics#star-projections)
  * `List`变成`List<*>`，即`List<out Any?>`

另外，**Java**的类型信息不会[保留到运行时](posts/generics#reified)，即**Java**的对象不会
携带传入它的构造函数的真实类型参数的信息。即，`new ArrayList<Integer>()`和`new ArrayList<Character>()`在运行时无法区分。
这使得在运行时不可能进行牵涉到**Java**泛型的**instanceof**判定。
Koltin只允许[星号映射](posts/generics#star-projections)的**Java**泛型类型进行**is**检查。

{% highlight java %}
if (a is java.util.List<Int>) // Error: 无法检测是否是Int的List
// 但是
if (a is java.util.List<*>) // OK: 但是无法保证列表的内容的类型。
{% endhighlight %}

## 不可型变数组 (Invariant arrays)
Kotlin中的数组是[不可型变](posts/generics#declaration-site-variance)的，这一点和[**Java**](http://c2.com/cgi/wiki?JavaArraysBreakTypeSafety)不同。
这意味着，Kotlin不允许我们将`Array<String>`赋值给`Array<Any>`，以避免潜在的运行时错误。
它也不允许我们在调用**Java**方法时，将子类的数组值传入到父类的数组参数上。
大多数情况下，这一点并不会带来阻碍，但是如果真的需要在传递数组参数的时候支持型变，可以显式地做类型转换。

在Java平台下，使用泛型类Array来表示数组，会带来大量的盒装和盒取(boxing and unboxing)操作。
因为数组大多数是使用在性能关键的场景中，我们采取了补救方式，并定义了类型IntArray, DoubleArray,
CharArray等等。这些类和Array类并没有关系，并且直接编译成Java的原生类型。

### 对象方法

当**Java**的类型被导入到Kotlin中时，所有类型为java.lang.Object的引用都被转化成Any?。
java.lang.Object和Any的最大区别，是Any并没有定义*任何*方法。这是由于Kotlin的[继承规则](posts/classes-and-inheritance#overriding-rules)所致。
那么我们如果需要toString()、equals()时怎么办呢？

#### toString()
toString()定义为一个[扩展函数](posts/extension-functions)。它会在对象中寻找toString()成员方法，并调用。如果不存在这个方法，则返回默认的String值：
`this.javaClass.getName() + "@" + System.indentityHashCode(this)`。
从程序员的角度来看，这几乎和**Java**没什么区别：  所有已经存在的toString()实现都正常工作，
而如果你需要给自己的类自定义toString()时，直接定义这个函数就可以：
{% highlight java %}
calss A() {
  fun toString() : String = "A"
}
{% endhighlight %}

你不需要声明它为**virtual** ，而且只有当你将其父类的toString()声明为**virtual**时，才可以在前面加上**override**。


#### equals()
在Kotlin中，`==`代表[受保护的equals()调用](posts/basic-operations#equality)。
操作符左侧的表达式必须有一个equals的方法，接受Any?类型的参数并返回Boolean值。
所以，任何**Java**对象已经符合条件了。另一方面，和toString()情形类似，我们也有一个扩展函数equals()。

#### hashCode()

hashCode() 对**Java**对象可用。

在即将来临的Kotlin标准库中，我们计划设计一个Hashable接口。若要往一个非同一性Hash映射(non-identity hash-map)中放置对象，则必须继承这个接口。

#### wait()/notify()
[Effective Java](http://java.sun.com/docs/books/effective)
第69条建议我们**优先使用并行计算工具类(concurrency utilities)，而不是wait和notify**。
所以，这些函数在Any中并没有，只有Java对象才有。

#### getClass()
想要获取对象的类型信息，可以使用扩展函数[javaClass](posts/runtime)。getClass()在**Java**对象中可用。

#### finalize()
和toString()一样，finalize()可以被覆盖

#### clone()
clone()可以和toString()一样覆盖，但是必须有Cloneable父型。
不要忘记[Effective Java](http://java.sun.com/docs/books/effective)第11条：**谨慎使用clone**。


### 继承Java类
Kotlin类最多只能继承一个**Java**类，(可以继承任意数量的**Java**接口)，这个基类必须放在继承列表的第一个位置。

### 访问静态成员
**Java**类的静态成员组成这个类的"类对象"。这种"类对象"并不能当作值来随便传递，但是仍然可以显式地访问其成员，如下：
{% highlight java %}
if (Character.isLetter(a)) {
  // ...
}
{% endhighlight %}


# 在Java代码中调用Kotlin代码

我们计划在将来定向到更多的平台，但是现在，Kotlin只编译到**Java**平台。
这意味着编译器生成**Java**字节码，所以我们用Kotlin写的程序也可以用**Java**调用。
尽管如此，Kotlin语言中有一些概念在**Java**中没有。这一节，我们简要描述一下这些概念如何映射到**Java**的概念。

### 包级别的函数 (Package-level functions)

所有在**包**`package org.foo.bar`中定义的函数和属性，会放到*Java*的类中，类名是org.foo.bar.namespace。
{% highlight java %}
pakcage demo {
  class Foo() {
  }
  fun bar() {
  }
}
{% endhighlight %}
{% highlight java %}
// Java
new Foo();
demo.namespace.bar();
{% endhighlight %}


### 需检异常(Checked exceptions)

上面提到，Kotlin并没有需检异常机制。所以，正常情况下，Kotlin定义的函数在**Java**中的签名，
并不包含throw语句。所以如果我们在Kotlin中定义如下函数：
{% highlight java %}
package demo

fun foo() {
  throw IOException()
}
{% endhighlight %}

并且我们在**Java**中调用这个函数，并试图捕获异常： 
{% highlight java %}
// Java
try {
  demo.namespace.foo();
}
catch (IOException e) { // Error: foo()并没有在throws列表中声明会抛出IOException
  // ..
}
{% endhighlight %}

则我们会遇到**Java**编译错误，因为foo()函数并没有声明IOException。
这时候应该怎么办呢？ 有几种方法：

* 第一种办法（下面的注释）是建立一个假的抛出异常的函数： 
{% highlight java %}
// Java
<E extends Throwable> void mayThrow(Class<E> eClass) throws E {
  // 什么都不做
}
{% endhighlight %}

并这么使用：
{% highlight java %}
// Java
try {
  mayThrow(IOException.class);
  demo.namespace.foo();
}
catch (IOException e) { // 没问题了
  // ...
}
{% endhighlight %}

* 第二种办法是捕获Throwable并且做一个*instanceof*检查。这样并不优雅，但是可以成功。
* 第三种办法是在**Java**中写一个包裹函数：
{% highlight java %}
void foo() throws IOException { // 在声明会抛出一个异常时，Java并不要求我们真的需要抛出一个异常。
  demo.namespace.foo()
}
{% endhighlight %}
现在可以直接调用foo()方法并捕获异常了。
* 第四种办法是在Kotlin中使用**throws**标注给函数签名加上抛出列表：
{% highlight java %}
throws<IOException> fun foo() {
  throw IOException();
}
{% endhighlight %}

### 运行时类型信息
<div class="warn">
<strong>运行时泛型现在还没有实现</strong>
</div>

Kotlin的泛型会保留到运行时，这意味在新建对象的时候，我们会将类型信息作为参数传入到构造函数中：
{% highlight java %}
// Java
new SomeClassDesclaredInKotlin<Integer>(TypeInfos.Int()) // 显式地传入一个TypeInfo对象
{% endhighlight %}

对于泛型函数来说，情况一致。


### Null安全

当从**Java**中调用Kotlin函数时，没有人阻止我们给非空参数传入一个null值了。
这就是为何Kotlin对所有的**public**函数，会生成运行时检查，以防止遇到null。
这样如果遇到null，会立即抛出NullPointerException。

### 属性

属性的getter会转换成get方法，setters变成set方法。




