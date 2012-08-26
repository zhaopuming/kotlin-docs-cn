---
layout: doc
title: 函数重载 | Operator overloading
original-doc: http://confluence.jetbrains.net/display/Kotlin/Operator+overloading
---

## 惯例

这里我们描述控制各种符号的重载的惯例。

### 一元符号

| 表达式 | 翻译成    |
| --     |  --       |
| +a     | a.plus    |
| -a     | a.minus() |
| !a     | a.not()   |

这个表内容说明当编译器处理一个表达式(如`+a`)时，经过如下几步：

1. 确定a的类型，设为T
1. 寻找一个函数plus()，没有参数，接受类型为T
1. 如果函数找不到或者有歧义，则抱编译错误
1. 如果函数存在，并且返回类型为R，则`+a`的返回类型为R

**注意**这些符号，以及其他的符号，对[基本类型](posts/basic-types)都进行了性能优化，所以对它们的函数调用并不带来性能损耗。

| 表达式 | 翻译成             |
|  --    |  --                |
|  a++   | a.inc() + 参看下方 |
|  a--   | a.dec() + 参看下方 |

这些操作符会修改他们的接受者，并且(可能)返回一个值

<div class="warn">
inc()/dec()不应该修改接受对象
我们说“修改他们的接受者”，是指接受变量，而不是接受对象
</div>

编译器依据如下步骤来解析一个**后缀**操作符(如`a++`)：

1. 确定a的类型，设为T
1. 寻找一个函数inc()，没有参数，接受类型为T
1. 如果函数返回的类型为R，那么它必须是T的子类

这个表达式计算的效果是：

1. 存放初始的值到一个临时存储a0上
1. 将a.inc()的结果赋值给a
1. 将a0的值作为表达式的结果返回

对于`a--`，上述步骤是类似的。

对于**前缀**的形式，如`++a`和`--a`，解析的方式一样，但是效果不同：

1. 将a.inc()的结果赋值给a
1. 将a的新值作为表达式的结果返回


### 二元操作符

|  表达式  |  翻译成      |
|   ---    |   ---        |
|  a + b   |  a.plus(b)   |
|  a - b   |  a.minus(b)  |
|  a \* b  |  a.times(b)  |
|  a / b   |  a.div(b)    |
|  a % b   |  a.mod(b)    |
|  a..b    |  a.rangeTo(b)|

对于上表中的操作符，编译器只简单的翻译成相应的函数。


|  表达式  |  翻译成        |
|   ---    |   ---          |
|  a in b  |  b.contains(a) |
|  a !in b | !b.contains(a) |

对于**in**和**!in**过程是一样的，但是参数的顺序是反的。


|  表达式              |  翻译成                 |
|   ---                |   ---                   |
|  a\[i\]                |  a.get(i)               |
|  a\[i, j\] |  a.get(i, j)                        |
|  a\[i_1, ..., i_n\]    |  a.get(i_1, ..., i_n)   |
| a\[i\] = b             |  a.set(i, b)            |
| a\[i, j\] = b          |  a.set(i, j, b)         |
| a\[i_1, ..., i_n\] = b | a.set(i_1, ..., i_n, b) |

<!--[]() -->

放括号翻译成get和set的调用，参数随之调整。


|  表达式  |  翻译成          |
|   ---    |   ---            |
|  a += b  |  a.plusAssign(b) |
|  a -= b  |  a.minusAssign(b)|
|  a \*= b |  a.timesAssign(b)|
|  a /= b  |  a.divAssign(b)  |
|  a %= b  |  a.modAssign(b)  |

对于赋值操作符，比如 `a += b`, 编译器有如下步骤：

1. 如果上表右侧的函数能够找到
  a. 如果对应的二元操作符函数能够找到（比如`plusAssign()`对应的是`plus()`），则报错（歧义）。
  b. 保证它的返回类型是Unit，否则报错
  c. 生成代码`a.plusAssign(b)`
1. 如果找不到，则尝试生成代码`a = a + b`（这里包含类型检查：`a+b`的类型必须是a的子类）

**注意**：赋值语句在Kotlin中**不是**表达式。


|  表达式  |  翻译成                                   |
|   ---    |   ---                                     |
|  a == b  | a?.equals(b) ?: b.identityEquals(null)    |
|  a != b  | !(a?.equals(b) ?: b.identityEquals(null)) |

**注意**：`identityEquals`检查两个引用是否指向同一个对象。

`==`这个操作符有两点特殊：

1. 它被翻译成复杂的表达式来隔离null，而且 null == null返回true
1. 它查找的函数不但有特别的名称，而且需要特别的*签名*。这个函数必须声明如下： <!--[]() -->
{% highlight java %}
fun equals(other: Any?) : Boolean
{% endhighlight %}
或者是一个有相同参数列表与返回类型的扩展函数。


|  表达式  |  翻译成              |
|   ---    |  ---                 |
|  a > b   | a.compareTo(b) > 0   |
|  a < b   | a.compareTo(b) < 0   |
|  a >= b  | a.compareTo(b) >= 0  |
|  a <= b  | a.compareTo(b) <= 0  |

<!--  > -->
所有的比较都翻译成`compareTo`，这个函数必须返回Int数值。


### 命名函数的中序调用

我们可以使用[中序函数调用](posts/functions#infix-calls)来模拟中序操作符。

## 接下来
* [扩展函数](posts/extension-functions)
* [函数字面量](posts/function-literals)
* [类型安全的Groovy风格构建器](posts/type-safe-groovy-style-builders)
