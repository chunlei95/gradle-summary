# groovy构建脚本入门
## `Project`对象
> `build.gradle`文件中的配置内容都属于`Project`对象,例如以下的配置:
```groovy
        version = '1.0.0.GA'
        
        configurations {
        }
```
> `version`和`configurations{}`都是`org.gradle.api.Project`的一部分.
如果看到一个不合格的元素并且不知道它的定义位置，请始终参考Project API文档
## 属性
```text
        <obj>.<name>                // 获取属性值
        <obj>.<name> = <value>      // 给属性设置一个新的值
        "$<name>"                   // 在字符串中嵌入属性值
        "${<obj>.<name>}"           // 与上面相同（嵌入值）
```
> 例子:
```groovy
        version = '1.0.1'
        myCopyTask.description = 'Copies some files'
        
        file("$buildDir/classes")
        println "Destination: ${myCopyTask.destinationDir}"
```
## 方法
```text
        <obj>.<name>()              // 没有参数的方法调用
        <obj>.<name>(<arg>, <arg>)  // 使用多个参数调用方法
        <obj>.<name> <arg>, <arg>   // 使用多个args的方法调用（没有括号）
```
> 例子:
```groovy
        myCopyTask.include '**/*.xml', '**/*.properties'
        
        ext.resourceSpec = copySpec()   // `copySpec()` comes from `Project`
        
        file('src/main/java')
        println 'Hello, World!'
```
## 块
```text
        <obj>.<name> {
             ...
        }
        
        <obj>.<name>(<arg>, <arg>) {
             ...
        }
```
> 例子:
```groovy
        configurations {
            assets
        }
        
        sourceSets {
            main {
                java {
                    srcDirs = ['src']
                }
            }
        }
        
        project(':util') {
            apply plugin: 'java-library'
        }
```
> 块是一种用于一次配置构建元素的多个方面的机制, 它们还提供了一种嵌套配置的方法，从而形成一种结构化数据<br/>

> 应该了解块的两个重要方面：
>* 它们被实现为具有特定签名的方法
>* 他们可以更改不合格方法和属性的目标（“委托”）
### 块方法签名
>* 它必须至少有一个参数
>*  最后一个参数必须是`groovy.lang.Closure`或`org.gradle.api.Action`类型, 块`{}`中的内容可以
认为是方法的实现.其余类型的参数还是写在方法后面的`()`中的。如果没有`()`,说明该方法只有一个参数，
参数类型为`groovy.lang.Closure`或`org.gradle.api.Action`。
## 局部变量
```text
        def <name> = <value>        // 无类型变量
        <type> <name> = <value>     // Typed variable
```
> 例子:
```groovy
        def i = 1
        String errorMsg = 'Failed, because reasons'
```
> 局部变量是一个Groovy构造 - 与额外属性不同 - 可用于在构建脚本中共享值