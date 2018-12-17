# Gradle构建语言参考
## 一些基础知识
> 首先，Gradle脚本是配置脚本。在脚本执行时，它配置特定类型的对象。例如，在构建脚本执行时，它会配置`Project`类型的对象。
此对象称为脚本的委托对象。下表显示了每种Gradle脚本的委托：
```text
        脚本类型	            委托对象
        Build script	    Project
        Init script	        Gradle
        Settings script	    Settings
```
> 其次，每个Gradle脚本都实现了`Script`接口。此接口定义了许多可在脚本中使用的属性和方法

## 构建脚本结构
> 构建脚本由零个或多个语句和脚本块组成, 语句可以包括方法调用，属性赋值和局部变量定义, 脚本块是一种方法调用，它将闭包作为参数
闭包被视为一个配置闭包，它在执行时配置一些委托对象。顶级脚本块如下所示:
<table>
    <tr>
        <th>Block</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>allprojects { }</td>
        <td>配置此项目及其每个子项目</td>
    </tr>
    <tr>
        <td>artifacts { }</td>
        <td>配置此项目的已发布工件</td>
    </tr>
    <tr>
        <td>buildscript { }</td>
        <td>配置此项目的构建脚本类路径</td>
    </tr>
    <tr>
        <td>configurations { }</td>
        <td>配置此项目的依赖关系配置</td>
    </tr>
    <tr>
        <td>dependencies { }</td>
        <td>配置此项目的依赖项</td>
    </tr>
    <tr>
        <td>repositories { }</td>
        <td>配置此项目的存储库</td>
    </tr>
    <tr>
        <td>sourceSets { }</td>
        <td>配置此项目的源集</td>
    </tr>
    <tr>
        <td>subprojects { }</td>
        <td>配置此项目的子项目</td>
    </tr>
    <tr>
        <td>publishing { }</td>
        <td>配置发布插件添加的PublishingExtension</td>
    </tr>
</table>