---
layout: doc
title: Hello World!
original-doc: http://confluence.jetbrains.net/display/Kotlin/Basic+syntax+walk-through
---


### 定义一个包

{% highlight java %}
package my.demo // 每个文件一个

import std.io.*

// ...*

{% endhighlight %}

参看 [包](posts/packages)


### 定义一个函数

{% highlight java %}
// 返回类型是必须声明的
fun sum(a : Int, b : Int) : Int {
  return a + b
}
{% endhighlight %}

或者

{% highlight java %}
// 返回类型可以推导出来
fun sum(a : Int, b : Int) = a + b
{% endhighlight %}

当不存在有用的返回值的时：

{% highlight java %}
fun printSum(a : Int, b : Int) : Unit {
  print(a + b)
}
{% endhighlight %}

或

{% highlight java %}
// 当返回Unit时，返回类型是可以不写的
fun printSum(a : Int, b : Int) {
  print(a + b)
}
{% endhighlight %}

参看[函数](posts/functions)  
参看[Hello, world!](posts/hello-world)

### 定义一个局部变量

赋值一次（只读）的局部变量：

{% highlight java %}
val a : Int = 1
val b = 1 // 类型可以推导
val c : Int // 当没有赋值时，类型是必需的
c = 1 // 定义赋值
{% endhighlight %}
注意[分号是可以不写的](posts/grammar#semicolons)

可变变量：

{% highlight java %}
var x = 5 // 类型可以推导
x += 1
{% endhighlight %}

参看[属性和字段](posts/properties-and-fields)

### 使用字符串模板

{% highlight java %}
fun main(args : Array<String>) {
  if (args.size == 0) return

  print("First argument: ${args[0]}")
}
{% endhighlight %}

参看[字符串模板](posts/string-templates)  
参看[数组](posts/basic-types#arrays)

### 使用条件表达式

{% highlight java %}
fun max(a : Int, b : Int) : Int {
  if (a > b)
    return a
  else
    return b
}
{% endhighlight %}

或者

{% highlight java %}
// 'if' 是一个表达式
fun max(a : Int, b : Int) = if (a > b) a else b
{% endhighlight %}

参看[**if**-表达式](posts/control-structures#if)

#### 检查Null

一个引用必须显示的声明为*可以为Null*，才能存放null值

{% highlight java %}
package multiplier

// 如果str不是数字形式，则返回null
fun parseInt(str : String) : Int? {
  // ...
}


fun main(args : Arry<String>) {
  if (args.size < 2) {
    print("No number supplied")
  }
  val x = parsInt(args[0])
  val y = parsInt(args[1])

  // 我们现在不能直接使用 'x * y'，因为他们可能含有null值

  if (x != null && y != null) {
    print(x * y) // 现在可以了
  }
}

{% endhighlight %}

<span></span>

或者

{% highlight java %}
// ...
  if (x == null) {
    print("Wrong number format in '${args[0]}'")
    return
  }
  if (y == null) {
    print("Wrong number format in '${args[1]}'")
    return
  }
  print(x * y) // 现在我们知道x和y不是null
{% endhighlight %}

参看[Null安全](posts/null-safety)

#### is-检查和自动转型
**is**操作符用来检查一个表达式是否是一个类型的实力（还有[更多功能](posts/pattern-matching)).
如果我们使用**is**检查某个变量或属性的类型之后，就没必要再手动将它转化成为检查的类型了

{% highlight java %}
fun getStringLength(obj : Any) : Int? {
  if (obj is String)
    return obj.length // 这里不需要再将obj转化为String类型了
  return null
}
{% endhighlight %}

或

{% highlight java %}
fun getStringLength(obj : Any) : Int? {
  if (obj !is String)
    return null
  return obj.length // 不许要转化为String类型
{% endhighlight %}

参看[类与继承](posts/classes-and-inheritance)  
参看[类型转换](posts/type-casts)


### 使用for-循环

{% highlight java %}
fun main(args : Array<String>) {
  for (arg in args)
    print(arg)

  // 或

  for (i in args.indices)
    print(args[i])
}
{% endhighlight %}

参看[for-循环](posts/control-structures#for)

### 使用while-循环

{% highlight java %}
fun main(args : Array<String>) {
  var i = 0
  while (i < args.size)
    print(args[i++])
}
{% endhighlight %}

参看[while-循环](posts/control-structures#while)
<span></span>

### 使用模式匹配

{% highlight java %}
fun cases(obj : Any) {
  when (obj) {
    1           -> print("One")
    "Hello"     -> print("Greeting")
    is Long     -> print("Long")
    !is String  -> print("Not a string")
    else        -> print("Unknown")
  }
}
{% endhighlight %}
参看[模式匹配](posts/pattern-matching)

### 使用范围和in

检查一个数字是否在一个范围内

{% highlight java %}
if (x in 1..y-1)
  print("OK")
{% endhighlight %}

检查一个数字是否在范围之外

{% highlight java %}
if (x !in 0..array.lastIndex)
  print("Out")
{% endhighlight %}

检查一个集合是否包含某个元素

{% highlight java %}
if (obj in collection) // collection.contains(obj) 被调用了
  print("Yes")
{% endhighlight %}

遍历一个范围

{% highlight java %}
for (x in 1..5)
  print(x)
{% endhighlight %}

参看[范围](posts/ranges)

### 使用函数字面量来过滤(filter)和映射(map)一个集合

{% highlight java %}
names filter {it.startsWith("A")} sortby {it} map {it.toUpperCase()} foreach {print(it)}
{% endhighlight %}

参看[高阶函数](posts/functions/higher-order-functions)和[函数字面量](posts/function-literals)

## 接下来
* [地址簿示例](posts/address-book)

