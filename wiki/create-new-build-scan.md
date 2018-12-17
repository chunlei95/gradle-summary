# 创建新的Gradle构建
## 步骤
>1. 创建一个文件夹，进入到该文件夹
```
        > mkdir basic-demo
        > cd basic-demo
```
>2. 使用Gradle的init命令生成一个简单的项目, 例如:
```
        ❯ gradle init 
        Starting a Gradle Daemon (subsequent builds will be faster)
        
        BUILD SUCCESSFUL in 3s
        2 actionable tasks: 2 executed
```
> 生成的项目结构如下
```
        ├── build.gradle  
        ├── gradle
        │   └── wrapper
        │       ├── gradle-wrapper.jar  
        │       └── gradle-wrapper.properties  
        ├── gradlew  
        ├── gradlew.bat  
        └── settings.gradle  
```
## 构建`init`插件
>* `gradle init`可以生成各种不同类型的项目，甚至知道如何将简单的pom.xml文件转换为Gradle
>* 通过提供`--type`参数值来指定类型 

> `init`支持构建以下几种类型的项目：<br/>
>* `pom`  : 通过将POM转换为一个或多个Gradle文件来实现, 只有
在调用`init`任务的目录中有一个有效的“pom.xml”文件，或者在指
定的项目目录中通过“-p”命令行选项调用时，才能使用它, 如果存在
这样的文件，将自动推断出这种“pom”类型。
>* `java-application`   : “java-application”构建init类型是不可推断的。必须明确指定, 它具有以下功能:<br/>
>>1. 使用“application”插件生成使用Java实现的命令行应用程序
>>1. 使用“jcenter”依赖库
>>1. 使用JUnit进行测试
>>1. 在常规位置具有源代码的目录
>>1. 如果没有现有的源文件或测试文件，则包含样本类和单元测试

> 可以通过提供--test-framework参数值来指定备用测试框架。要使用其他测试框架，请执行以下命令之一
>>* gradle init --type java-application --test-framework spock : 使用Spock进行测试而不是JUnit
>>* gradle init --type java-application --test-framework testng: 使用TestNG进行测试而不是JUnit

>* `java-library(也就是jar包项目)`  : “java-library”构建init类型是不可推断的。必须明确指定，它具有以下功能:<br/>
>>1. 使用“java”插件生成一个库Jar
>>1. 其余同`java-application`(除了引用的插件不一样，一个是"application", 一个是"java"),

>* `scala-library`
>* `groovy-library`
>* `groovy-application`
>* `basic`   :  “basic”构建init类型对于创建全新的Gradle项目非常有用。
它创建了一个示例build.gradle文件，其中包含注释和链接以帮助入门。未
明确指定类型并且不能推断任何类型时使用此类型。
## 创建一个task
> 下面是一个简单的例子, 将`src`目录下的资源复制到`dest`目录下，`dest`目录会自动创建: <br/>
```groovy
        task copy(type: Copy, group: "Custom", description: "Copies sources to the dest directory") {
            from "src"
            into "dest"
        }
```
> Gradle附带了一个可以在自己的项目中配置的任务库,如上面的`Copy`<br/>
> `group`和`description`可以是想要的任何内容, 可以省略它们，但这样做也会在以后使用的任务报告中省略它们<br/>

> 执行task:
```
        ./gradlew copy
```
## 应用插件
> Gradle包含一系列插件，Gradle插件门户提供了许多插件, 该发行版附带的插件之一是`base`插件, 结合名为`Zip`的核心类型，可以使用已配置的名称和位置创建项目的zip存档。<br/>
> 使用插件语法将基本插件添加到构建脚本文件中。务必在<strong>文件顶部</strong>添加`plugins {}`块:
```groovy
        plugins {
            id "base"
        }
```
> 现在添加一个从src目录创建zip存档的任务:<br/>
```groovy
        task zip(type: Zip, group: "Archive", description: "Archives sources in a zip file") {
            from "src"
            setArchiveName "basic-demo-1.0.zip"
        }
```
> `base`会插件使用这些设置在`build / distributions`文件夹中创建名为`basic-demo-1.0.zip`的存档文件。
> 在这种情况下，只需运行新的zip任务，然后会看到生成的zip文件就在期望的位置。
```
        ./gradlew zip
```
## 探索和调试构建
### 发现可用的`task`
> `tasks`命令列出可以调用的Gradle任务，包括`base`插件添加的任务，以及自定义任务:
```
        ./gradlew tasks
```
### 发现可用的`properties`
> `properties`命令获取项目的属性: 
```
        ./gradlew properties
        
        ------------------------------------------------------------
        Root project
        ------------------------------------------------------------
        
        buildDir: /Users/.../basic-demo/build
        buildFile: /Users/.../basic-demo/build.gradle
        description: null
        group:
        name: basic-demo
        projectDir: /Users/.../basic-demo
        version: unspecified
        
        BUILD SUCCESSFUL
```
> 可以更改许多属性。例如，可以尝试将以下行添加到构建脚本文件中，然后重新执行gradle属性:
```groovy
        description = "A trivial Gradle build"
        version = "1.0"
```

