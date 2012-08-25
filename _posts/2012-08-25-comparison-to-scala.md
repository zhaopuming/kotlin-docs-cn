---
layout: doc
title: Hello World!
original-doc: http://confluence.jetbrains.net/display/Kotlin/Comparison+to+Scala
---

Kotlin的[主要设计目标](posts/docs-home#design-goals)中有如下两条：

* **编译速度**最少和**Java**一样快
* 在保持功能和表达能力的同时，尽量设计得**简单**(keep it simple).

<div class="info">
如果你已经对<strong>Scala</strong>很满意了，那么可能你不需要Kotlin
</div>


## Scala有而Kotlin没有

* 隐式转换，隐含参数等
  * 在**Scala**中，有时候不使用debugger的话，很难弄清楚代码运行的怎么回事，因为有太多隐藏转换牵涉。
  * 如果需要给你的类型扩展功能(译注：Scala通过隐式转换实现这种功能)，在Kotlin中可以使用[扩展函数](posts/extension-functions)
* 类型成员可以在子类里覆盖重载(override)
* 路径依赖类型(Path-dependent types)
* 存在类型
  * [类型映射](posts/generics#type-projections)是这个功能的一个特殊情况
* 特型(Traits)的复杂初始化逻辑
  * 参看[类和继承](posts/classes-and-inheritance)
* 自定义符号操作符
  * 参看[符号重载](posts/operator-overloading)
* 内置XML
  * 参看[类型安全的Groovy风格构建器](posts/type-safe-groovy-style-builders)

Scala已有，将来会在Kotlin中添加的功能：
* 高阶类型(Higher kinds)
* 结构类型(Structural types)
* 衍出符号(Yield Operator)
* 行者(Actor)
* 并行集合(Parallel collections)
* .NET支持

## Kotlin有而Scala没有的

* 无代价Null安全
  * **Scala**使用Option来处理Null安全，但这是语法层的包装，在运行时有代价
* [智能类型转换](posts/type-casts)
* 静态[扩展函数](posts/extension-functions)
  * 而不是在运行时包装(Scala 2.10使用隐式值类型(implicit value classes)来消除运行时的代价)
* Kotlin的[内联函数](posts/functions/inline-functions)支持[非局部跳转(Nonlocal jumps)](posts/returns-and-jumps#custom)
* [字符串模板](posts/strings#string-templates)
  * Scala 2.10添加了字符串模板的支持
* [类型代理](posts/classes-and-inheritance#delegation). Scala的第三方插件[Autoproxy](https://github.com/kevinwright/Autoproxy-Lite)实现了这个功能
* [模块](posts/modules)

## 接下来
* [模块和编译](posts/modules-and-compilation)
