---
layout: doc
title: 标注 | Annotations
original-doc: http://confluence.jetbrains.net/display/Kotlin/Annotations
---


在Kotlin中，可以使用*标注*的方式在声明的地方附加元信息。
定义一个标注，只需要普通的类定义之前加上*annotation*标注即可。

{% highlight java %}
annotation class fancy {}
{% endhighlight %}

(注意，依照Kotlin的语言约定，标注类的名称是小写开头；原因从下面的例子可以看出来)  
现在我们可以使用这个fancy标注来对某个声明或表达式进行标注了。
一般情况下，使用方括号将标注名扩起来：
{% highlight java %}
[fancy] class Foo {
  [fancy] fun baz([fancy] foo : Int) : Int {
    return [fancy] 1
  }
}
{% endhighlight %} <!--[]() -->

<div class="warn">
<strong>标注功能还在开发中</strong>
</div>


当标注一个声明时（即函数或类），方括号可以省略掉：
{% highlight java %}
fancy class Foo() {
  fancy fun baz(fancy foo: Int) {
    return [fancy] 1
  }
}
{% endhighlight %} <!--[]() -->

注意在标注表达式时，方括号是必需的。

标注类的构造函数也可以传入参数： 
{% highlight java %}
annotation class special(val why : String)

special("example") class Foo {}
{% endhighlight %}

如你所见，如果想向标注传入参数，只要调用其构造函数即可。

在JVM平台下，可以直接复用Java的标注：
{% highlight java %}
import org.junit.Test 
import org.junit.Assert.* 
 
class Tests { 
  Test fun simple() { 
    assertEquals(42, getTheAnswer()) 
  } 
}
{% endhighlight %} <!--* -->

如果你希望使用的Java标注看起来更像修饰符，可以在导入时进行重命名：
{% highlight java %}
import org.junit.Test as test

class Tests {
  test fun simple() {
    // ...
  }
}
{% endhighlight %}

