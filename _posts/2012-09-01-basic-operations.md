---
layout: doc
title: 基本操作
original-doc: http://confluence.jetbrains.net/display/Kotlin/Basic+operations
---


## 相等性

在Kotlin中有两种类型的相等性：

* 引用相等(两个引用指向同一个对象)(译注：和前面提到的*相同性*是一个意思)
* 结构相等(检查equals())(译注：即值相等)

### 引用相等

在Kotlin中并没有内置的检查引用相等的操作符，因为我们认为这个需求很少。
但是Kotlin中有一个[内联函数](posts/functions#inline-functions)`identityEquals()`，可以用如下的方式调用：

{% highlight java %}
a.identityEquals(b)
// 或者
a identityEquals b // 中序调用
{% endhighlight %}

并且这个函数只有当a和b都指向同一个对象时，才返回true。


### 结构相等

结构相等可以使用`==`符号(以及对应的`!=`)来判断。
根据编译器的[约定](posts/operator-overloading#equals)，表达式`a == b`会被翻译成：
{% highlight java %}
a?.equals(b) ?: b.identityEquals(null)
{% endhighlight %}

即，如果a不是**null**，则调用equals(Any?)函数来判断，否则检查b是不是也是null

注意在代码中，遇到检查是不是**null**时，不需要担心性能问题，编译器会直接将`a == null`翻译成`a.identityEquals(null)`


## 接下来

* [控制结构](posts/control-structures)
* [函数字面量](posts/function-literals)
* [返回和跳转](posts/returns-and-jumps)
* [范围](posts/ranges)
* [This表达式](posts/this-expression)
* [元组](posts/tuples)
* [类型转换](posts/type-casts)

