# Gradle构建语言参考
## 一些基础知识
> 首先，Gradle脚本是配置脚本。在脚本执行时，它配置特定类型的对象。例如，在构建脚本执行时，它会配置`Project`类型的对象。
此对象称为脚本的委托对象。下表显示了每种Gradle脚本的委托：
<table width = "888" style = "width: 888px">
    <tr>
        <th>脚本类型</th>
        <th>委托对象</th>
    </tr>
    <tr>
        <td>Build script</td>
        <td>Project</td>
    </tr>
    <tr>
        <td>Init script</td>
        <td>Gradle</td>
    </tr>
    <tr>
        <td>Settings script</td>
        <td>Settings</td>
    </tr>
</table>

> 其次，每个Gradle脚本都实现了`Script`接口。此接口定义了许多可在脚本中使用的属性和方法

## 构建脚本结构
> 构建脚本由零个或多个语句和脚本块组成, 语句可以包括方法调用，属性赋值和局部变量定义, 脚本块是一种方法调用，它将闭包作为参数
闭包被视为一个配置闭包，它在执行时配置一些委托对象。顶级脚本块如下所示:
<table width = "888" style = "width: 888px">
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

> 构建脚本也是一个Groovy脚本，因此可以包含Groovy脚本中允许的那些元素，例如方法定义和类定义
## 核心类型
> 下面列出了Gradle脚本中使用的一些核心类型:

<table>
    <tr>
        <th>类型</th>
        <th>描述</th>
    </tr>
    <tr>
        <td>Project</td>
        <td>此接口是用于从构建文件与Gradle交互的主要API。从项目中，可以以编程方式访问Gradle的所有功能</td>
    </tr>
    <tr>
        <td>Task</td>
        <td>Task表示构建的单个原子工作，例如编译类或生成javadoc</td>
    </tr>
    <tr>
        <td>Gradle</td>
        <td>表示Gradle的调用</td>
    </tr>
    <tr>
        <td>Settings</td>
        <td>声明实例化和配置要参与构建的Project实例的层次结构所需的配置</td>
    </tr>
    <tr>
        <td>IncludedBuild</td>
        <td>复合中包含的构建</td>
    </tr>
    <tr>
        <td>Script</td>
        <td>此接口由所有Gradle脚本实现，以添加一些特定于Gradle的方法。由于编译后的脚本类将实现此接口，因此可以直接在脚本中使用此接口声明的方法和属性</td>
    </tr>
    <tr>
        <td>JavaToolChain</td>
        <td>一组用于从Java源构建的工具</td>
    </tr>
    <tr>
        <td>SourceSet</td>
        <td>SourceSet表示Java源和资源的逻辑组</td>
    </tr>
    <tr>
        <td>SourceSetOutput</td>
        <td>所有输出目录的集合（已编译的类，已处理的资源等） - 请注意SourceSetOutput扩展了FileCollection</td>
    </tr>
    <tr>
        <td>SourceDirectorySet</td>
        <td>SourceDirectorySet表示由一组源目录组成的一组源文件，以及相关的包含和排除模式</td>
    </tr>
    <tr>
        <td>IncrementalTaskInputs</td>
        <td>提供对增量任务需要处理的任何输入文件的访问</td>
    </tr>
    <tr>
        <td>Configuration</td>
        <td>表示一组工件及其依赖项</td>
    </tr>
    <tr>
        <td>ResolutionStrategy</td>
        <td>定义依赖解析的策略。例如，强制某些依赖项版本，替换，冲突解决方案或快照超时</td>
    </tr>
    <tr>
        <td>ArtifactResolutionQuery</td>
        <td>用于构造可以解析指定组件的选定软件工件的查询的构建器</td>
    </tr>
    <tr>
        <td>ComponentSelection</td>
        <td>表示模块的组件选择器的元组和要在组件选择规则中评估的候选版本</td>
    </tr>
    <tr>
        <td>ComponentSelectionRules</td>
        <td>表示组件选择规则的容器。规则可以作为配置的一部分应用，策略可以明确地接受或拒绝单个组件,既不接受也不拒绝的组件将受默认版本匹配策略的约束</td>
    </tr>
    <tr>
        <td>ExtensionAware</td>
        <td>可以在运行时使用其他对象扩展的对象</td>
    </tr>
    <tr>
        <td>ExtraPropertiesExtension</td>
        <td>Gradle域对象额外的ad-hoc,属性</td>
    </tr>
    <tr>
        <td>PluginDependenciesSpec</td>
        <td>用于声明要在脚本中使用的插件的DSL</td>
    </tr>
    <tr>
        <td>PluginDependencySpec</td>
        <td>对插件的依赖性的可变规范</td>
    </tr>
    <tr>
        <td>PluginManagementSpec</td>
        <td>配置插件的解析方式</td>
    </tr>
    <tr>
        <td>ResourceHandler</td>
        <td>提供对特定于资源的实用程序方法的访问，例如创建各种资源的工厂方法</td>
    </tr>
    <tr>
        <td>TextResourceFactory</td>
        <td>创建由字符串，文件和存档条目等源支持的TextResources</td>
    </tr>
</table>