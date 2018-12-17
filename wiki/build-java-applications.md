# 构建Java应用程序
## `init`命令初始化项目
```
        $ gradle init --type java-application
        Select build script DSL:
          1: groovy
          2: kotlin
        Enter selection (default: groovy) [1..2]
        
        Select test framework:
          1: junit
          2: testng
          3: spock
        Enter selection (default: junit) [1..3]
        
        Project name (default: demo):
        Source package (default: demo):
        
        BUILD SUCCESSFUL
```
> `init`任务首先运行`wrapper`任务，生成`gradlew`和`gradlew.bat`包装器脚本.然后它使用以下结构创建新项目:
```
        ├── build.gradle
        ├── gradle    
        │   └── wrapper
        │       ├── gradle-wrapper.jar
        │       └── gradle-wrapper.properties
        ├── gradlew
        ├── gradlew.bat
        ├── settings.gradle
        └── src
            ├── main
            │   └── java  
            │       └── demo
            │           └── App.java
            └── test      
                └── java
                    └── demo
                        └── AppTest.java
```
> 构建文件添加了`java`和`application`插件,前
者支持Java项目。后者允许将一个类指定为具有main方法，该方法可以通过命令行的构建来执行
## 执行构建
```
        $ ./gradlew build
        > Task :compileJava
        > Task :processResources NO-SOURCE
        > Task :classes
        > Task :jar
        > Task :startScripts
        > Task :distTar
        > Task :distZip
        > Task :assemble
        > Task :compileTestJava
        > Task :processTestResources NO-SOURCE
        > Task :testClasses
        > Task :test
        > Task :check
        > Task :build
        
        BUILD SUCCESSFUL
        8 actionable tasks: 8 executed
```
> `build`任务编译类，运行测试并生成测试报告,可以通过打开位于`build / reports / tests / test / index.html`
的HTML输出文件来查看测试报告
## 运行应用程序
> 首先，使用`tasks`任务查看插件添加了哪些任务:
```
        $ ./gradlew tasks
        :tasks
        
        ------------------------------------------------------------
        All tasks runnable from root project
        ------------------------------------------------------------
        
        Application tasks
        -----------------
        run - Runs this project as a JVM application
        
        // ... many other tasks ...
```
> `run`任务告诉Gradle在分配给`mainClassName`属性的类中执行main方法
```
        $ ./gradlew run
        > Task :compileJava UP-TO-DATE
        > Task :processResources NO-SOURCE
        > Task :classes UP-TO-DATE
        
        > Task :run
        Hello world.
        
        BUILD SUCCESSFUL in 0s
        2 actionable tasks: 1 executed, 1 up-to-date
        
        
        BUILD SUCCESSFUL
```