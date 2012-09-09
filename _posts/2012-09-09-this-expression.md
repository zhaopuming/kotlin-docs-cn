---
layout: doc
title: This表达式
original-doc: http://confluence.jetbrains.net/display/Kotlin/This+expressions
---

我们用**this**表达式来表示当前的接受者。

* 在[类](posts/classes-and-inheritance)的成员函数中，**this**表示这个类当前的对象。
  * **this**&gt;Supertype&lt;用来调用父类的成员函数实现，参看[继承覆盖规则](posts/classes-and-inheritance#overriding-rules)
* 在[扩展函数](posts/extension-functions)或[扩展函数字面量](posts/function-literals#extensions)中，
**this**代表调用函数的那个对象，即函数的*接受者*参数。

如果**this**没有使用前缀，则默认引用最里层的作用域。如果想使用**this**引用外层作用域，则需要加上*标签前缀*。

## 带前缀的**this**

如果想用this访问外层的作用域（如[类](posts/classes-and-inheritance)、[扩展函数](posts/extension-functions)或者带标签的
[扩展函数字面量](posts/function-literals#extensions))，可以写做`**this**@label`。
这里`@label`表示this想要引用的作用域的[标签](posts/returns-and-jumps)

{% highlight java %}
class A { // 隐含标签 @A
  class B { // 隐含标签 @B
    fun Int.foo() { // 隐含标签 @foo
      //
      val a = this@A // 类A的this
      val b = this@B // 类B的this
      //
      val c = this // foo()的接受者，即一个Int对象
      val c1 = this@foo // foo()的接受者，即一个Int对象
      //
      val funLit = { String.() -> // 隐含标签 @
        val d = this // funLit的接受者，即一个String对象
        val d1 = this@ // funLit的接受者，即一个String对象
      }
      //
      val funLit2 = { (s: String) ->
        val d1 = this // foo()的接受者。因为funLit2是普通的函数字面量，并没有定义接受者。
      }
    }
  }
}
{% endhighlight %}

## 接下来：
* [元组](posts/tuples)
* [类型转换](posts/type-casts)
