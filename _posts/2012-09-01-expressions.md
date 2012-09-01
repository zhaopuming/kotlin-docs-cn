---
layout: doc
title: 表达式
original-doc: http://confluence.jetbrains.net/display/Kotlin/Expressions
---


这里是对Kotlin的表达式的一个快速概览：

## 原子表达式

原子表达式包括：

* 字面量常量（查看[基本类型](posts/basic-types)，[字符串](posts/strings))
* [元组字面量](posts/tuples)
* [变量名称和字段引用](posts/properties-and-fields)
* [对象表达式](posts/object-expressions)
* 括号包括起来的表达式
* [控制结构(if, when, try)](posts/control-structures)
* [函数字面量](posts/function-literals)
* [**this**表达式](posts/this-expression)
* [**break**, **continue**或**return**](posts/returns-and-jumps)
  * 注意这些表达式的返回类型是[Nothing](posts/returns-and-jumps#nothing)

参看[原子表达式的语法](posts/grammar#atomic-expression)

## 操作符

[语法](posts/grammar#expressions)里定义了一些用符号表示的操作符。
这些符号的优先级列表在[这里](posts/grammer#precedence)。
这里很多的操作符都可以[重载](posts/operator-overloading)。

* `.` - 用来访问成员 <!--abc-->
* `?.` 和 `?:` - 处理[Null安全](posts/null-safety) <!--abc-->
* `&&` 和 `||` - 处理布尔表达式解析 <!--abc-->

## 接下来：

* [基本操作](posts/basic-operations)
* [控制结构](posts/control-structures)
* [函数字面量](posts/function-literals)
* [返回和跳转](posts/returns-and-jumps)
* [范围](posts/ranges)
* [This表达式](posts/this-expression)
* [元组](posts/tuples)
* [类型转换](posts/type-casts)

