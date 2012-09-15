---
layout: doc
title: Kotlin Doc
original-doc: http://confluence.jetbrains.net/display/Kotlin/Kotlin+Doc
---


## KotlinDoc

KotlinDoc相当与Java语言中的javadoc，用来注释公有API。
当前配合KotlinDoc的注释方式推荐如下：

* 使用Java风格的`/** comments */`注释语法
* 第一句话用来总结类/函数/属性的作用，而其他行是详细内容。
* 可以使用HTML标签或者[markdown](http://daringfireball.net/projects/markdown/syntax#block)标记语言来美化注释，可以使用粗提字(bold), 点列表(bullet-list)，连接(links)等
* 链接包、类、函数和属性时，我们支持WikiLink的标注方式`[[expression]]`，这里expression可以是包名、类名、className.function()或className.property
* 可以将测试代码引入(include)到注释中，而不要直接在注释中引入源码，因为那样很容易失去维护而过时，或者对重构很不友好。可以使用`@includeFunction`宏来按照相对路径引入测试代码，如下示例所示：

{% highlight java %}
/**
 * This is **bold**
 *
 * New paragraph using newlines.
 *
 * We can also support wiki links notation
 * for example [[List]] and [[Collection]] and [[java.util.Collection.size()]] will
 * generate hypertext links to classes
 *
 * @includeFunctionBody ../test/MyTest.kt someFunction
 */ 
fun anotherMethod(): Unit { ... }
{% endhighlight %}


这里是一些[示例](https://github.com/JetBrains/kotlin/blob/master/libraries/stdlib/src/kotlin/JLangIterables.kt).



