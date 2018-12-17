# 生成构建扫描
## 自动应用构建扫描插件
> 从Gradle 4.3开始，可以在构建脚本中启用构建扫描，而无需任何其
他配置. 
使用命令行选项`--scan`发布构建扫描时，将自动应用所需的构建扫描
插件. 在构建结束之前，系统会要求在命令行上接受许可协议。<br/>
使用方式: 在命令行输入: `./gradlew build --scan`, 如果是
Windows系统，使用方法如下: `gradlew build --scan`<br/>
> 这种机制使得生成<strong>临时的一次性构建扫描</strong>变得
非常容易，而无需在构建中配置构建扫描插件。
## 在项目的所有版本上启用构建扫描
> 使用以下内容将插件块添加到根项目构建脚本文件: <br/>
```
        plugins {
            id 'com.gradle.build-scan' version '1.16' 
        }
```
> 如果已经有了插件块(`plugins {}`)，尽量先放置构建扫描插件。
在任何现有插件下面添加它仍然可以工作，但会遗漏有用的信息
## 接受许可协议
> 要将构建扫描发布到[https://scans.gradle.com](https://scans.gradle.com)，
需要接受许可协议, 这可以在发布时通过命令行进行临时完成，但也
可以在Gradle构建文件中指定，方法是添加以下部分: <br/>

> build scan plugin 1.12+
```groovy
        buildScan {
            termsOfServiceUrl = 'https://gradle.com/terms-of-service'
            termsOfServiceAgree = 'yes' // 接受许可协议
            publishAlways() // 每次构建运行的时候都会发布构建
        }
``` 
> build scan plugin < 1.12
```groovy
        buildScan {
            licenseAgreementUrl = 'https://gradle.com/terms-of-service'
            licenseAgree = 'yes'
        }
```
> `buildScan`块是用来配置插件的，上述文件设置了接受许可协议所
需的两个属性。也可以配置其他属性。
## 发布构建扫描
> 使用名为`--scan`的命令行标志发布构建扫描<br/>
> 使用`--scan`选项运行构建任务。构建完成后，将构建数据上
载到`scans.gradle.com`后，将看到一个链接以查看构建扫描, 
如下：
```text
        $ ./gradlew build --scan
        
        BUILD SUCCESSFUL in 0s
        
        Publishing build scan...
        https://gradle.com/s/uniqueid
```
> 如果是Windows系统，使用方式如下: `gradlew build --scan`
## 在线访问构建扫描
> 第一次访问链接时，系统会要求激活创建的构建扫描，会收到用
于激活构建扫描的电子邮件，按照电子邮件中提供的链接，将看到
创建的构建扫描，可以浏览构建扫描中包含的所有信息，包括执行
任务所花费的时间，构建的每个阶段所需的时间，任何测试的结果，
使用的插件和其他依赖项，使用的任何命令行开关等等
## 为所有构建启用构建扫描
> 可以使用`Gradle init`脚本(创建一个)避免向每个构建添加插件
和许可协议。在`〜/ .gradle / init.d`目录中创建一个`.gradle`
文件，添加以下内容：<br/>
```groovy
        initscript {
            repositories {
                // gradlePluginPortal()
                maven {
                    url 'https://plugins.gradle.org/m2'
                }
            }
        
            dependencies {
                // classpath 'com.gradle:build-scan-plugin:@scanPluginVersion@'
                classpath 'com.gradle:build-scan-plugin:1.16'
            }
        }
        
        rootProject {
            apply plugin: com.gradle.scan.plugin.BuildScanPlugin
        
            buildScan {
                termsOfServiceUrl = 'https://gradle.com/terms-of-service'
                termsOfServiceAgree = 'yes' // 接受许可协议
                publishAlways() // 每次构建运行的时候都会发布构建
            }
        }
```
> 如有必要，`init`脚本会下载构建扫描插件并将其应用于
每个项目，并接受许可协议。现在，可以在系统的任何构建
中使用`--scan`标志，可以添加其他功能到该脚本，例如在
什么条件下发布扫描信息。
## 构建扫描发布的位置
> 构建扫描可以发布到Gradle Enterprise或scans.gradle.com
>* Gradle Enterprise是一种商业产品，适用于可以在自己的系统上托管并随附构建扫描服务器和构建缓存后端实现的公司
>* scans.gradle.com是免费提供的构建扫描服务器，由Gradle Inc.托管
## Gradle Enterprise构建扫描
```groovy
        plugins {
            id 'com.gradle.build-scan' version '2.1'                    
        }
        
        buildScan {
            server = 'https://gradle-enterprise.mycompany.com' // 不同于发布到scans.gradle.com           
            publishAlways() // 每次构建运行的时候都会发布构建 
            allowUntrustedServer = true // 用于暂时解决不受信任证书的问题，不是长久解决方案                                          
        }
```
## Gradle构建扫描插件的版本
> Gradle 5.0+
```groovy
        plugins {
          id 'com.gradle.build-scan' version '2.1'
        }
```
> Gradle 2.1 - 4.10.2
```groovy
        plugins {
          id 'com.gradle.build-scan' version '1.16'
        }
```
> Gradle 2.0
```groovy
        buildscript {
          repositories {
              maven { url 'https://plugins.gradle.org/m2/' }
          }
          dependencies {
              classpath 'com.gradle:build-scan-plugin:1.16'
          }
        }
        apply plugin: 'com.gradle.build-scan'
```
## 选择何时发布构建
#### 按需发布
```groovy
        $ gradle assemble --scan
        $ gradle clean build --scan
        $ gradle buildScanPublishPrevious // 发布上一个构建版本
        $ gradle bSPP // buildScanPublishPrevious的缩写
```
#### 每次构建运行时发布
```groovy
        buildScan {
            publishAlways()
        }
```
#### 按条件发布
>* publishAlwaysIf(boolean) : 无论构建成功还是失败，如果给定条件为真，则发布构建扫描
>* publishOnFailure() : 仅在构建失败时发布构建扫描
>* publishOnFailureIf(boolean) : 仅在条件为真且构建失败时才发布构建扫描

> 例如，假设只想从CI系统发布构建扫描，该构建扫描通过具有CI环境变量来识别。以下配置可以解决这个问题: <br/>
```groovy
        buildScan {
            publishAlwaysIf(System.getenv('CI'))
        }
```
> 也可以以相同的方式使用其他选项。如果要根据一组条件配置多个内容，可以使用if条件，如下: <br/>
```groovy
        buildScan {
            if (System.getenv('CI')) {
                publishAlways()
                tag 'CI'
            }
        }
```