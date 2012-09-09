---
layout: doc
title: 模式匹配 | Pattern Matching
original-doc: http://confluence.jetbrains.net/display/Kotlin/Pattern+matching
---

模式匹配让我们可以检查一个对象是否符合某种结构。
比如，一个二叉树的节点，是否包含两个分支，或者只有左侧分支，等等。

## 模式匹配操作符(**is**和**!is**)

模式匹配通过**is**操作符进行。如果匹配成功，**is**返回**true**，否则返回**false**。
**is**的反面形式是**!is**。
模式匹配的最简单形式，只检查一个对象是否满足一个类型；
类似于**Java**的**instanceof**检查 :

{% highlight java %}
// is
if (obj is String) {
  print(obj.length)
}

// !is
if (obj !is String) {
  print("Not a string")
}
else {
  print(obj.length)
}
{% endhighlight %}

参考[智能类型转换](posts/type-casts)

**is**和**!is**操作符也可以在**when**表达式中的分支判断语句中使用：

{% highlight java %}
when (x) {
  is Int -> print(x)
  is List<Int> -> print(x.sum())
  !is Number -> print("Not even a number")
  else -> print("can't do anything")
}
{% endhighlight %}

<div class="info">
  <strong>When-表达式在不使用模式匹配时，可以替换switch-表达式。</strong>
  参考<a href="posts/control-structures#when">这里</a>
</div>


## 模式
**is**和**!is**操作符右侧的模式，可以用来匹配类型、常量、元组和其他对象的结构，
以及将子模式匹配到变量上等。

参看[模式的语法定义](posts/grammar#pattern)

<div class="warn">
  <strong>复杂的模式延后到未来的版本实现</strong>
  <br/>
  现在只能使用<strong>is/!is</strong>来进行简单的类型匹配。
  参看相关的<a href="http://youtrack.jetbrains.com/issue/KT-186">issue</a>
</div>

## 接下来：

* [类和继承](posts/classes-and-inheritance)

