---
layout: doc
title: Hello World!
original-doc: http://confluence.jetbrains.net/display/Kotlin/Comparison+to+Java
---

## 改进

**Java**语言中的安全问题，在**Kotlin**中有如下改进：

* Null引用受类型系统控制。也就是说，Koltin[没有NPE](posts/null-safety)
* 完整的类型信息[保留到了运行时](posts/generics#reified-generics)。也就是说，Kotlin有更好的反射和更安全的instancof检查
* [没有原始类型数据](posts/java-interoperability#java-generics)
* Kotlin中的数组是[不可变的](posts/basic-types#arrays)

**Java**语言中的可用性(Usablility)问题，在**Kotlin**中有如下改进:

* Kotlin有[高阶函数](posts/higher-order-functions)，即闭包
  * 并且在配合内联(inlining)功能后，这些闭包没有性能问题
* [使用处的类型可变，而不需要通配符](posts/generics#type-projections)
* [声明出的类型可变](posts/generics#declaration-site-variance)
* Kotlin[没有需查异常(checked exception)](posts/exceptions)

## Java有而Kotlin没有的

* [需查异常(checked exceptions)](posts/exceptions)
* [原始类型数据](posts/basic-types)，这些数据并不是object
* [静态成员](posts/classes-and-inheritance#class-objects)
* [非private字段](posts/properties-and-fields)
* [类型擦除](posts/java-interoperability#java-generics)
* [通配符类型](posts/generics#generic-variance)

## Kotlin有而Java没有

* [函数字面量](posts/function-literals)+[内联函数](posts/functions#inline-functions) = 高性能的自定义控制结构
* [Null安全](posts/null-safety)
* [智能类型转换](posts/type-casts)
* [字符串模板](posts/strings#string-templates)
* [属性(Properties)](posts/propeties-and-fields)
* [原始造函数](posts/classes-and-inheritance#primary-constructors)
* [汇件(Mixins)和一级类型代理(First class delegation)](posts/classes-and-inheritance#inheritance)
* [扩展函数](posts/extension-functions)
* [针对变量和属性类型的类型推导](posts/basic-syntax-walk-through#define-local-variable)
* [单例](posts/object-expressions-and-declarations)
* [声明处类型可变 & 类型映射](posts/generics#generic-variance)
* [模块](posts/modules-and-compilation)
* [范围表达式](posts/ranges)
* [模式匹配](posts/pattern-matching)
* [运行时保留泛型类型](posts/generics#reified-generics)
* [符号重载](posts/operator-overloading)
* [类对象](posts/generics#class-objects)


## 接下来

* [和Scala的比较](posts/comparison-to-scala)
