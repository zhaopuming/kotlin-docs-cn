---
layout: doc
title: Kotlin构建工具 | Kotlin Build Tools
original-doc: http://confluence.jetbrains.net/display/Kotlin/Kotlin+Build+Tools
---

# Ant

### 使用本地的Kotlin设置定义&lt;kotlinc&gt;任务

{% highlight xml %}
<taskdef resource="org/jetbrains/jet/buildtools/ant/antlib.xml" classpath="${kotlin.home}/lib/kotlin-ant.jar"/>
{% endhighlight %}

### &lt;kotlinc&gt;的属性

| 名称           |    描述                                 |    是否必要            |   默认值    |
| ---            |    ---                                  |    ---                 |    ---      |
| src            | 需要编译的kotlin源码文件或目录          | "src"或"module"需要指定|             |
| module         | 需要编译的Kotlin[module](posts/modules) | "src"或"module"需要指定|             |
| output         | 目标目录                                | 如果使用"src"，则"output"或"jar"需要指定 | |
| jar            | 目标jar文件                             | 如果使用"src"，则"output"或"jar"需要指定；如果使用"module"，则只能指定"jar"，或者忽略 | "moduleName.jar" |
| classpath      | 编译的class path                        | false                  |      |
| classpathref   | 编译的class path引用                    | false                  |      |
| stdlib         | "kotlin-runtime.jar"的路径              | false                  | ""   |
| includeRuntime | 如果使用"jar" - Kotlin运行库是否被导入  | false                  | true |


### 示例

{% highlight xml %}

tlinc src = "test/longer-examples/Bottles.kt" output = "dist"/> 
<kotlinc src = "test/longer-examples" output = "dist"/> 
 
<kotlinc src = "test/longer-examples/Bottles.kt" jar = "dist.jar"/> 
<kotlinc src = "test/longer-examples" jar = "dist.jar"/> 
 
<kotlinc module = "test/modules/smoke/Smoke.kts" jar = "dist.jar"/> 
<kotlinc module = "test/modules/smoke/Smoke.kts"/> => "smoke.jar"
{% endhighlight %}

"Smoke.kts":

{% highlight java %}

import kotlin.modules.* 
fun project() { 
    module("smoke") { 
        sources += "Smoke.kt"
    }
}
{% endhighlight %} <!--*-->

"Smoke.kt":

{% highlight java %}
package Smoke 
  
fun main(args: Array<String>) { 
    print("${args[0]}|${args[1]}|${args[2]}") 
}
{% endhighlight %} <!--[]() -->

### Classpath示例
{% highlight xml %}
<path id="junit-jar"> 
    <fileset file="lib/junit.jar"/> 
</path> 
  
<kotlinc src = "src/unit-tests" jar = "tests.jar" classpath = "lib/junit.jar"/>
  
<kotlinc src = "src/unit-tests" jar = "tests.jar" classpathref = "junit-jar"/> 
  
<kotlinc src = "src/unit-tests" jar = "tests.jar"> 
    <classpath> 
        <path refid="junit-jar"/> 
    </classpath> 
</kotlinc> 
  
<kotlinc src = "src/unit-tests" jar = "tests.jar"> 
    <classpath> 
        <fileset file="lib/junit.jar"/> 
    </classpath> 
</kotlinc>
{% endhighlight %}


## Maven

"kotlin-maven-plugin" 可以编译Kotlin源码和模块
注意：现在只支持Maven 3

<div class="warn">
老版本的<a href="http://evgeny-goldin.com/wiki/Kotlin-maven-plugin">"kotlin-maven-plugin"</a>已经不再支持了。
</div>

### 资源库
Kotlin的maven组件发布到 <http://repository.jetbrains.com/>

在你的pom或者settings文件里加入这个资源库：
{% highlight xml %}

{%java  endhighlight %}
sitories>
    <repository>
        <id>jetbrains-all</id>
        <url>http://repository.jetbrains.com/all</url>
    </repository>
</repositories>
  
<pluginRepositories>
    <pluginRepository>
        <id>jetbrains-all</id>
        <url>http://repository.jetbrains.com/all</url>
    </pluginRepository>
</pluginRepositories>

### 版本
用**kotlin.version**定义Kotlin的版本。可能的值如下：
* **X.Y-SNAPSHOT** -- **X.Y**发布版的快照版本。每次我们的持续集成服务器成功构建后，都会发布。
  非常不问定，只推荐作为测试新编译器功能之用。
  现在所有的构建都是发布到**0.1-SNAPSHOT**。
* **X.Y.Z** -- 发布版或者里程碑版 **X.Y.Z**，手动发布，稳定。

现在已有的里程碑版本如下：

| 里程碑   | 版本     |
| ---      | ---      |
| M1       | 0.1.2090 |
| M2       | 0.1.2580 |


### 依赖
Kotlin已经有一个完善的标准库，可以在你的工程中依赖使用：
{% highlight xml %}
<dependencies> 
    <dependency> 
        <groupId>org.jetbrains.kotlin</groupId> 
        <artifactId>kotlin-stdlib</artifactId> 
        <version>${kotlin.version}</version> 
    </dependency> 
</dependencies>
{% endhighlight %}

### 编译Kotlin源码

在`<build>`标签上定义源码目录：
{% highlight xml %}
<sourceDirectory>${project.basedir}/src/main/kotlin</sourceDirectory> 
<testSourceDirectory>${project.basedir}/src/test/kotlin</testSourceDirectory>
{% endhighlight %}

在`<plugin>`标签中引用kotlin-maven-plugin来编译源码： 
{% highlight xml %}
<plugin> 
    <artifactId>kotlin-maven-plugin</artifactId> 
    <groupId>org.jetbrains.kotlin</groupId> 
    <version>${kotlin.version}</version> 
  
    <executions> 
        <execution> 
            <id>compile</id> 
            <phase>compile</phase> 
            <goals> <goal>compile</goal> </goals> 
        </execution> 
  
        <execution> 
            <id>test-compile</id> 
            <phase>test-compile</phase> 
            <goals> <goal>test-compile</goal> </goals> 
        </execution> 
    </executions> 
</plugin>
{% endhighlight %}

## 编译Kotlin/Java混合的代码
如果想要编译混合代码的工程，Kotlin编译器需要在Java编译器之前调用。  
用maven的术语表达，就是说kotlin-maven-plugin需要在maven-compiler-plugin之前运行。

可以将Kotlin编译放到compile之前的阶段来达到这个目的，比如**process-resources** (如果您有更好的方案，请提出建议)：

{% highlight xml %}
<plugin> 
    <artifactId>kotlin-maven-plugin</artifactId> 
    <groupId>org.jetbrains.kotlin</groupId> 
    <version>0.1-SNAPSHOT</version> 
  
    <executions> 
        <execution> 
            <id>compile</id> 
            <phase>process-sources</phase> 
            <goals> <goal>compile</goal> </goals> 
        </execution> 
  
        <execution> 
            <id>test-compile</id> 
            <phase>process-test-sources</phase> 
            <goals> <goal>test-compile</goal> </goals> 
        </execution> 
    </executions> 
</plugin>
{% endhighlight %}

### 示例
在[这里](http://confluence.jetbrains.net/download/attachments/45253645/maven-hello-world-examples.zip?version=2&modificationDate=1339408978000)可以下载一个简单的Hello World工程。


## Gradle
[参考Evgeny Goldin的工作](http://evgeny-goldin.com/blog/gradle-kotlin-teamcity-console/)


## Griffon
参考["griffon-kotlin-plugin"](https://github.com/griffon/griffon-kotlin-plugin)


