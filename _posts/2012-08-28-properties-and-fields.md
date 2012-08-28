---
layout: doc
title: 属性和字段
original-doc: http://confluence.jetbrains.net/display/Kotlin/Hello%2C+world%21
---

## Java的字段访问

在**Java**世界中，我们已经习惯了使用*getter*和*setter*方法来访问*字段*。
在[《Effective Java》](http://java.sun.com/docs/books/effective/)中的第14条说明：
**在公共类中，应当使用访问方法，而不是公共字段**。
这样的说法已经不言而喻了。所有的IDE都支持这个：他们可以生成getters和setters，所以写出这样一个类并不困难：

{% highlight java %}

// Java
public class Address {
 
    private String name;
    private String street;
    private String city;
    private String state;
    private String zip;
 
    public String getName() {
        return name;
    }
 
    public void setName(String name) {
        this.name = name;
    }
 
    public String getStreet() {
        return street;
    }
 
    public void setStreet(String street) {
        this.street = street;
    }
 
    public String getCity() {
        return city;
    }
 
    public void setCity(String city) {
        this.city = city;
    }
 
    public String getState() {
        return state;
    }
 
    public void setState(String state) {
        this.state = state;
    }
 
    public String getZip() {
        return zip;
    }
 
    public void setZip(String zip) {
        this.zip = zip;
    }
}
{% endhighlight %}


这个类里的大部分代码行都是架子代码(boilerplate code)。


## 摆脱字段/get/set组合

在Kotlin中，没有办法可以定义一个*字段*(fields)。你只有*属性*(properties)。
可读/写的属性，使用`var`关键字来定义；而只读的属性，使用`val`关键字。
所以，上面的Java类在Kotlin里可以这么写：

{% highlight java %}
public class Address() { // 括号定义了主构造函数
  public var name : String = ... 
  public var street String = ... 
  public var city : String = ... 
  public var state : String? = ... 
  public var zip : String = ... 
}
{% endhighlight %}

这里我们有5个*可变*的属性，每一个都有一个*实际字段*来存储值，以及两个访问器：getter和setter。
所以，代码生成的bytecode和上面的Java代码几乎一样。唯一的区别是属性的初始化代码，参看[Null安全](posts/null-safety)



