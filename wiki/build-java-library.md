# 构建Java库项目(.jar)
## 使用`gradle init`创建项目
> 要构建Java库项目，在使用`gradle init`时需要指定`--type`参数为`java-library`, 如下：<br/>
```groovy
        gradle init --type java-library
```
## 使用`gradle build`构建项目
> 第一次运行构建时，Gradle将检查〜/ .gradle目录
下的缓存中是否已经有JUnit库和其他列出的依赖项, 
如果没有，库将被下载并存储在那里, 下次运行构建时，
将使用缓存版本。构建任务编译类，运行测试并生成测
试报告,可以通过打开位于build / reports / tests / test / index.html
的HTML输出文件来查看测试报告<br/>
> 可以在build / libs目录中找到名为building-java-libraries.jar的新打包的JAR文件。通过运行以下命令验证存档是否有效<br/>
```groovy
            $ jar tf build/libs/building-java-libraries.jar
            META-INF/
            META-INF/MANIFEST.MF
            Library.class
```
## 自定义库jar文件属性
>1. 通常希望JAR文件的名称包含库版本。
通过在构建脚本中设置顶级版本属性可以轻松实现这一点，就像这样: <br/>
```groovy
        version = '0.1.0'
```
> 运行jar任务: <br/>
```groovy
        gradlew jar
```
> 此时注意到build / libs / building-java-libraries-0.1.0.jar中生成的JAR文件包含预期的版本。
>2. 另一个常见要求是通常通过添加一个或多个属性来自定义清单文件(`META-INF/MANIFEST.MF`), 
我们可以通过配置jar任务在清单文件中包含库名和版本。将以下内容添加到构建脚本的末尾：
```groovy
        jar {
            manifest {
                attributes('Implementation-Title': project.name,
                           'Implementation-Version': project.version)
            }
        }
```
> 要确认这些更改是否按预期工作，运行jar任务，并且从JAR解压缩清单文件：
```
        $ ./gradlew jar
        $ jar xf build/libs/building-java-libraries-0.1.0.jar META-INF/MANIFEST.MF
```
> 查看META-INF / MANIFEST.MF文件的内容，可以看到以下内容：
```
        Manifest-Version: 1.0
        Implementation-Title: building-java-libraries
        Implementation-Version: 0.1.0
```
## 使用`gradlew javadoc`生成api文档
```
        $ ./gradlew javadoc
        
        > Task :compileJava
        > Task :processResources NO-SOURCE
        > Task :classes
        > Task :javadoc
        
        BUILD SUCCESSFUL in 1s
        2 actionable tasks: 2 executed
```
> 如果碰到乱码问题可以添加以下代码到`build.gradle`文件中：
```groovy
        tasks.withType(Javadoc) {
            options.encoding = 'UTF-8'
        }
        // Javadoc不行可以试试JavaCompile
```

