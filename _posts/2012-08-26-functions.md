---
layout: default
title: Functions
original-doc: http://confluence.jetbrains.net/display/Kotlin/Functions
---

Kotlin中的函数使用**fun**关键字来定义：

{% highlight java %}
fun doube(x : Int) : Int {
  return x * 2
}
{% endhighlight %}

<!--*-->

函数的调用使用传统的方式：

{% highlight java %}
val two = demo(1)
{% endhighlight %}

参看[中序调用](posts/functions#infix-calls)和[符号重载](posts/operator-overloading)

## 单表达式函数

如果函数只返回一个单独的表达式的值，我们可以在函数声明后加上'='，再跟着表达式。这种情况下不需要使用**return**关键字。

{% highlight java %}
fun double(x : Int) : Int = x * 2
{% endhighlight %}

<!-- * -->

在这种情况下，可以省略掉返回类型的标记，编译器会根据表达式进行类型推导。

{% highlight java %}
fun double(x : Int) = x * 2
{% endhighlight %}
<!-- * -->


## 返回Unit类型的函数

如果函数不存在有用的返回值，则返回值设为Unit(译注：相当与C里的void)。
Unit是一种[元组](posts/tuples)类型，其元数是0。Unit是Tuple0类型的化名。
它只有一个值--一个包含0个元素的元组，写作{}。这个值不需要显式地返回：

{% highlight java %}
fun printHello(name : String?) : Unit {
  if (name != null)
    print("hello, $name!")
  else
    print("Hi there!")
  // 我们不需要写'return ()'或者'return'，虽然这么写也可以。
}
{% endhighlight %}

Unit的返回类型的函数，也不需要显示指定返回类型：
如果一个函数有代码块主体(在花括号之间，不是'='后)，并且返回Unit，则函数声明可以省略掉返回值类型的标记：

{% highlight java %}
fun printHello(name : String?) {

  // ...
}
{% endhighlight %}


## 局部函数
Kotlin支持局部函数，即我们可以在函数内部定义一个函数。

{% highlight java %}
fun dfs(graph : Graph){
  fun dfs(current : Vertex, visited : Set<Vertex>) {
    if (!visited.add(current)) return
    for (v in current.neighbors)
      dfs(v, visited)
  }
  dfs(graph.vertices[0], HashSet())
}
{% endhighlight %}
<!--[]()-->

局部函数可以“看见”包含它的函数(即闭包)，所以我们可以把visited变量声明成局部变量，而不是作为参数传进去。

{% highlight java %}
fun reachable(from : Vertex, to : Vertex) : Boolean { 
  val visited = HashSet<Vertex>() 
  fun dfs(current : Vertex) { 
    // here we return from the outer function: 
    if (current == to) return@reachable true 
    // And here -- from local function: 
    if (!visited.add(current)) return 
    for (v in current.neighbors) 
      dfs(v) 
  } 
  dfs(from) 
  return false // if dfs() did not return true already 
}
{% endhighlight %}

## 成员函数

成员函数是指定义在类中的函数。

{% highlight java %}
class Sample() {
  fun foo() { print("foo") }
}
{% endhighlight %}

成员函数使用'.'来调用：


{% highlight java %}
Sample().foo() // 新建一个Sample对象，并调用foo()
{% endhighlight %}

参看[类和继承](posts/classes-and-inheritance)

## 泛型函数

函数可能需要泛型参数，我们使用尖括号，在函数名后面, 参数列表之前声明泛型类型：

{% highlight java %}
fun singletonArray<T>(item : T) : Array<T> { 
  val result = Array<T>(1) 
  result[0] = item 
  return result 
}
{% endhighlight %}
<!--[]()-->

更多关于泛型函数的资料可以参看[泛型](posts/generics)


## 变长参数

函数参数列表的最后一个参数，可以标记为**vararg**:

{% highlight java %}
fun asList<T>(vararg ts : T) : List<T> { 
  val result = ArrayList<T> 
  for (t in ts) // ts is an Array 
    result.add(t) 
  return result 
}
{% endhighlight %}

默认情况下，**vararg**会创建一个数组，但我们可以通过提供数据类型来指定vararg参数的类型：

{% highlight java %}
fun asList<T>(vararg<ArrayList<T>> ts : T) : List<T> = ts // ts现在是List类型了
{% endhighlight %}

使用**vararg**标注的参数，表明是一个builder类型。这样的参数调用编译如下：

{% highlight java %}
asList(0, 1, 2)
// 编译成如下代码 
val list = ArrayList<Int>(3) // 3 是调用的参数的数量
list.add(0)
list.add(1)
list.add(2)
asList(list.build()) // 对于ArrayList, build直接返回list自己
{% endhighlight %}

所以**vararg**的builder必须满足如下条件：

* 有一个构造函数，接受一个Int型的参数
* 有一个add()函数 
* 有一个build()函数
* **vararg**参数的类型和build()返回类型相同

## 默认参数

函数的参数可以有*默认值*，当相应的参数再调用时被省略掉，则会取默认值。这样先对于**Java**，可以减少函数重载。

{% highlight java %}
// 在 java.io.InputStream 中，这个函数有两个版本
fun read(b : Array<Byte>, off : Int = 0, len : Int = -1) { 
  val actualLength = if (len == -1) b.length else len 
  // ... 
}
{% endhighlight %}

这个函数可以用三种形式调用：

{% highlight java %}
read(b, off, len)
read(b, off) // len = -1, 也就是b.length
read(b) // off = 0, len = -1
{% endhighlight %}

## 命名参数

如果函数有很多参数，并且不少是有默认值的，那么如果能在调用的地方看到参数名称，会很方便。考虑下面的函数：

{% highlight java %}
fun reformat( 
  str : String, 
  normalizeCase : Boolean = true, 
  uppercaseFirstLetter : Boolean = true, 
  divideByCamelHumps : Boolean = false, 
  wordSeparator : Character = ' ' 
) { 
  // ... 
}
{% endhighlight %}

最好的调用情况是：

{% highlight java %}
reformat(str)
{% endhighlight %}

但是如果我们需要换一个分隔符，却不换其他设置？

{% highlight java %}
reformat(str, true, true, false, '_')
{% endhighlight %}

<!--_-->

没有命名参数的话，函数调用会很难读。若要说明清楚，我们可以给参数加上名称：

{% highlight java %}
reformat(str, 
    normalizeCase = true, 
    uppercaseFirstLetter = true, 
    divideByCamelHumps = false, 
    wordSeparator = '_' 
  )
{% endhighlight %}
<!--_-->

这样就已经改进了，不过我们还可以做得更好：如果使用默认值，我们不需要指定所有的参数。

{% highlight java %}
reformat(str, wordSeparator = '_')
{% endhighlight %}
<!--_-->


##  <a id="infix-calls"><!----></a> 中序调用

如果成员函数(或者[扩展函数](posts/extension-functions)只有一个参数，那么可以用*中序*的方式调用。
即参数不需要"."和括号：

{% highlight java %}
1 shl 2
// 和下面相同
1.shl(2)
{% endhighlight %}

一个中序调用必须有两个参数。一个参数是不允许的，比如'print 1'，因为这样在print的左侧就什么都没有了。

中序调用的优先级是左关联的，即 `1 foo 2 bar 3` 意味着：`(1 foo 2) bar 3`

了解优先级，参看[语法](posts/grammar#precedence)


## 扩展函数

[扩展函数](posts/extension-functions)使我们能定义一个函数，指定某种类型作为接收者。

{% highlight java %}
fun Int.abs() : Boolean = if (this >=0) this else -this
{% endhighlight %}

这样我们对任何Int类对象，都可以调用abs()函数，就像它是Int的一个成员函数一样。

{% highlight java %}
print(-1.abs())
{% endhighlight %}

## <a id="higher-order-functions"><!-- --></a> 高阶函数

*高阶函数*是指接受函数作为参数的函数，或者返回一个函数的函数。一个很好的例子是lock()函数：
接受参数为锁定的对象和一个函数，接着将对象锁住，运行函数，再释放锁定。

{% highlight java %}
fun lock<T>(lock : Lock, body : () -> T) : T {
  lock.lock()
  try {
    return body()
  }
  finally {
    lock.unlock()
  }
}
{% endhighlight %}

让我们仔细分析上面的代码：body参数是[函数类型](posts/function-literals#function-types)：`() -> T`，
它应该是一个没有参数、并返回T类型的值的函数。body()在**try**代码块中调用，受锁定的保护，并且它的结果被lock函数返回。

如果我们想要调用lock()，可以传入一个[函数字面量](posts/function-literals)作为参数：

{% highlight java %}
val result = lock(lock, { sharedResource.operation() })
{% endhighlight %}

函数字面量在[这个页面](posts/function-literals)里详细介绍。现在我们只给一个简单的描述：

* 函数字面量总是用花括号包围
* 它的参数定义在`->`之前，参数的类型可以省略
* 函数体在`->`之后。

在上面的例子中，没有函数参数，所以不需要写`->`，整个花括号里的内容都是函数体。

Kotlin还有一个惯例(Convention)，让我们上面的例子看起来更简洁：

* 如果函数的最后一个参数本身是函数，那么这个参数可以放在参数列表的括号外面声明，即：

{% highlight java %}
lock (lock) {
  sharedResource.operation()
}
{% endhighlight %}

这样我们的代码就看起来更像是一个'控制结构' (译注：类似if, while等)

另一个高阶函数的示例是map() (在Map/Reduce而言):

{% highlight java %}
fun <T, R> List<T>.map(transform : (T) -> R) : List<R> { 
  val result = ArrayList<R>() 
  for (item in this) 
    result.add(transform(item)) 
  return result 
}
{% endhighlight %}

这个函数可以如此调用：

{% highlight java %}
val doubled = ints.map { it -> it * 2}
{% endhighlight %}<!--*-->

另一个惯例在这里可以帮助我们：

* 如果函数字面量只有一个参数，那么它的参数声明可以省略掉(同时'->'也省略掉)，而使用`it`来表示这个参数：

{% highlight java %}
ints map {it * 2} // 中序调用 + 隐式的'it'
{% endhighlight %} <!--*-->

这些惯例使得我们可以写出[LINQ](http://msdn.microsoft.com/en-us/library/bb308959.aspx)风格的表达式：

{% highlight java %}
strings filter {it.length == 5} sortby {it} map {it.toUpperCase()}
{% endhighlight %}

## 扩展函数字面量

和命名的扩展函数一样，函数字面量也可以声明*接收类型参数*，这样我们可以编写类型安全的[Groovy风格的构建器](http://groovy.codehaus.org/Builders)

了解更多，请参看[这里](posts/function-literals#extensions)和[这里](posts/type-safe-groovy-style-builders)


## 内联函数

使用高阶函数会带来一些运行时代价：每个函数都是一个对象，它捕获一个闭包，即那些变量可以在函数内访问。
内存分配(函数对象和类)以及虚函数调用都会带来运行时开销。

但是很多情况下，如果我们使用*内联*，这种开销就可以消灭掉。上面的lock示例就是一个这个情况的很好的说明。
lock()函数可以在调用时很容易得内联。考虑下面的情形：

{% highlight java %}
lock(l) {foo()}
{% endhighlight %}

与其新建一个函数对象病生成一个调用，编译器可以生成如下代码：

{% highlight java %}
lock.lock()
try {
  foo()
}
finally {
  lock.unlock()
}
{% endhighlight %}

这不正是我们最开始就想要达到的效果么？

为了让编译器进行内联，我们需要给lock()函数的声明上加上**inline**标记：

{% highlight java %}
inline fun lock<T>(lock : Lock, body : () -> T) : T {
  // ...
}
{% endhighlight %}

内联可能会造成生成的代码变长，但是如果我们合理地使用（比如不内联大的函数)，那么它会在运行效率上带来很大提高，特别是是在循环内的调用场合。

<div class="warn">
<strong>内联函数现在还没有实现</strong>
参看这个<a href="http://youtrack.jetbrains.com/issue/KT-1434">issue</a>
</div>

## 返回多个值

有的时候我们需要一个函数返回好几个值。有的语言使用[out-参数](http://msdn.microsoft.com/en-us/library/aa645764.aspx)来实现这个，还有的使用*元组*(tuples)。我们使用后一种方式。

元组在[这个页面](posts/tuples)有详细描述。这里我们给出一个例子：

{% highlight java %}
fun splitEmail(email : String) : #(username : String, host : String) { 
  return #(email.substringUntil("@"), email.substringAfter("@")) 
}
{% endhighlight %}


## 接下来
* [符号重载](posts/operator-overloading)
* [扩展函数](posts/extension-functions)
* [函数字面量](posts/function-literals)
* [类型安全的Groovy风格构建器](posts/type-safe-groovy-style-builders)
