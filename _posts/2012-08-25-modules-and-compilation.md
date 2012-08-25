---
layout: default
title: Modules and Compilation
original-doc: http://confluence.jetbrains.net/display/Kotlin/Modules+and+Compilation
---


<div class="warn">
模块功能正在重设计中
</div>


**模块**(Module)是Kotlin中的*编译单元*。物理上，模块使用*构建脚本*来描述。
这种脚本通过程序的方式来组建*模块描述*，内含模块的相关信息：组成模块的源文件、模块的依赖以及构建模块用到的编译器选项等。

作为一个编译单元，意味着所有的分析和优化都可以在模块层面进行；并且兼容性问题也限制在模块层次内出现。

## 模块描述以及根名字空间

每一个模块必须有一个唯一的标识符。这个标识符也就是模块的*根名字空间* - 该模块中所有的名字空间都依赖于这个根名字空间。


## 默认导入 <a id="default-imports">#</a>

模块的构建脚本可以声明一系列*默认导入*。这样默认导入共有的库（如集合库，UI库等），非常方便。



## 文件系统的组织

Kotlin并不要求源文件在文件系统中按照某种特定的方式组织：一个*名字空间*并不需要对应一个目录，一个*类*并不需要对应一个文件。
也就是说，我们可以把整个模块的所有源码放到一个文件上。



## 依赖

模块可以依赖于其他模块，并且指定依赖的模块的版本。

模块依赖的声明API，还没有完全确定。模块的构建脚本大致会是这个样子：

{% highlight java %}
val homeDir = "..."

module("org.jetbrains.test") {
  depends(MavenRepo["junit.junit@4.8.2"])

  importNamespace("java.lang")
  importNamespace("org.junit")

  sourceRoot("$homeDir/src")
  testSourceRoot("$homeDir/tests")
}

{% endhighlight %}


## 接下来
* [包](posts/packages)

