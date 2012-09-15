---
layout: doc
title: 编程风格 | Coding Conventions
original-doc: http://confluence.jetbrains.net/display/Kotlin/Coding+Conventions
---

# Kotlin代码风格

本页包含当前Kotlin语言的编码风格：

* 如果有疑惑，可以参照[Java编程规范](http://www.oracle.com/technetwork/java/codeconv-138413.html)，比如：<!--li-->
  * 变量名使用驼峰风格(如camelCase，并且避免使用下划线) <!--li-->
  * 类型名称以大写字母起头<!--li-->
  * 方法和属性以小写字母起头<!--li-->
  * 使用4空格的缩进<!--li-->
* 公有函数应该使用[Kotlin Doc](posts/kotlin-doc)进行注释。
* Kotlin没有Java中的私有字段这个概念 -- 只有属性。避免在属性名使用前缀，如`_`或`m_`或者其他的表示法；
如果你想直接访问某个属性的实际字段，请使用`$`符号：$foo表示foo属性的实际字段；绝对不要建立一个私有属性并起名为`_foo`


## 分号

当分号用来分隔类型与其基类时，分号前应该有空格；如果是用来分隔变量与其类型时，不加空格：
{% highlight java %}
trait Foo : Bar {
  fun foo(a: Int): String
}
{% endhighlight %}

## 参看
[Kotlin Doc](posts/kotlin-doc)
