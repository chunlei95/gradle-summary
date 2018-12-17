#  构建spring boot应用程序
## 初始化项目结构
> 使用`gradle init --type java-application`创建一个模板
```text
        gradle init  --type java-application
```
## 添加spring boot插件
```groovy
        plugins {
            id 'java'
            id 'com.gradle.build-scan' version '1.16'  // 添加构建扫描插件
            id 'org.springframework.boot' version '2.0.5.RELEASE' // 添加spring boot插件
        }
```
## 添加依赖
```groovy
        dependencies {
            implementation 'org.springframework.boot:spring-boot-dependencies:2.0.5.RELEASE'
            implementation 'org.springframework.boot:spring-boot-starter-web'
        
            testImplementation 'org.springframework.boot:spring-boot-starter-test'
        
            components {
                withModule('org.springframework:spring-beans') {
                    allVariants {
                        withDependencyConstraints {
                            // Need to patch constraints because snakeyaml is an optional dependency
                            it.findAll { it.name == 'snakeyaml' }.each { it.version { strictly '1.19' } }
                        }
                    }
                }
            }
        }
```
> 为了符合Spring Boot BOM，需要使用components块来严格使用版本1.19的snakeyaml依
赖项，因为spring-beans依赖版具有1.20版本作为传递依赖.<br/>
> 如果使用Gradle 5.0以前的版本，我们需
要通过将以下行添加到项目根目录中的`settings.gradle`文件来启用此功能:
```groovy
        enableFeaturePreview('IMPROVED_POM_SUPPORT')
```
## 定义Spring Boot jar文件的主类名
> 我们需要在`bootJar`配置闭包上定义`mainClassName`属性。将以下代码段添加到build.gradle中，然后我们就可以运行Spring Boot应用程序了：
```groovy
        bootJar {
            mainClassName = 'hello.App' // spring boot入口类的全限定类名
        }
```
## 构建并运行Spring Boot应用程序
> 要构建可执行jar，可以执行以下命令:
```text
        ./gradlew bootJar
```
> 可执行jar位于`build / libs`目录中，可以通过执行以下命令来运行它:
```text
        java -jar build/libs/gradle-spring-boot-project.jar
```
> 运行应用程序的另一种方法是执行以下Gradle命令:
```text
        ./gradlew bootRun
```
> 此命令将直接在默认端口`8080`上运行Spring Boot应用程序