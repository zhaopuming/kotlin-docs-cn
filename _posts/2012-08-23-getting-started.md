---
layout: doc
title: Getting Started
original-doc: http://confluence.jetbrains.net/display/Kotlin/Getting+Started
---

## 设置IntelliJ IDEA

首先需要安装IntelliJ IDEA 11.1或更高版本。免费的社区版本可以从下面的地址下载：

* [Release版](http://www.jetbrains.com/idea/) - IntelliJ IDEA的正式发布版
* [EAP site](http://www.jetbrains.com/idea/eap) - 先期预览版(Early Access Program) 

### 里程碑版本

Kotlin的稳定里程碑版本可以从JetBrains的[插件库](http://www.jetbrains.com/idea/plugins/index.html)获取。

在IDEA里，访问 设置(Settings) -> 插件(Plugins) -> 浏览插件库(Browse Repositories)

选择Kotlin插件，并选择"下载和安装"即可


### 使用Kotlin集成构建库

在IDEA里，访问 设置(Settings) -> 插件(Plugins) -> 浏览插件库(Browse Repositories)，并输入:

* [http://www.jetbrains.com/kotlin/eap-plugin-repository/updatePlugins.xml](http://www.jetbrains.com/kotlin/eap-plugin-repository/updatePlugins.xml)

这样你就可以选择Kotlin插件并下载和安装。上述的插件库里包含了相对稳定的构建版本，差不多每周发布一次新版。



<div class="warn">
  <em>如果你想要通过插件库来获取每夜构建版本，请使用:</em><br/>
  IntelliJ IDEA 11: <a href="http://teamcity.jetbrains.com/guestAuth/repository/download/bt389/.lastSuccessful/updatePlugins.xml">http://teamcity.jetbrains.com/guestAuth/repository/download/bt389/.lastSuccessful/updatePlugins.xml</a>
  IntelliJ IDEA 12 EAPS: <a href="http://teamcity.jetbrains.com/guestAuth/repository/download/bt345/.lastSuccessful/updatePlugins.xml">http://teamcity.jetbrains.com/guestAuth/repository/download/bt345/.lastSuccessful/updatePlugins.xml</a>
</div>


### 手动下载Kotlin插件

你也可以选择从我们的[构建服务器](http://teamcity.jetbrains.com/viewType.html?tab=buildTypeStatusDiv&buildTypeId=bt345&guest=1)手动下载IDEA插件的每夜构建版本。在"Artifacts"板块里可以找到。

下载后，可以在IDEA里访问 设置(Settings) -> 插件(Plugins) -> 从硬盘安装(Install plugin from Disk...)


## 新建一个工程


* 安装好插件后，就可以在IDEA中选择 文件(File) -> 新工程(New Project)
* 新建一个普通的工程，内含src目录
* 在src目录上右键选择 新建(New) -> Kotlin file
* 输入文件名，比如Foo
* 这时候IDE会在编辑窗口的右上方提示你选择Kotlin运行环境(Kotlin Runtime)
* 你需要指定到下载的Kotlin运行环境jar包(看下面)
* 输入如下的代码:

{% highlight java %}
    package hello

    fun main(args : Array<String>) {
      println("Hello, world!")
    }
{% endhighlight %}

* 现在应该可以在Foo文件上右键选择“运行”(Run)了
* Enjoy!


