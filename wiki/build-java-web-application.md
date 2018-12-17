# 构建Java web应用程序
> Gradle包含一个用于构建Java Web应用程序的`war`
插件，社区提供了一个优秀的插件，名为gretty，用于
在Jetty或Tomcat上测试和部署Web应用程序
## 创建web应用程序的结构
> `war`插件扩展了`java`插件，以增加对Web应用程序的支持。默认
情况下，它使用名为`src / main / webapp`的文件夹作为与Web相关
的资源.
### 创建步骤
>1. 首先手动创建以下文件夹结构：
```text
        webdemo/
            src/
                main/
                    java/
                    webapp/
                test
                    java/
```
>1. 添加Gradle构建文件`build.gradle`, 并添加如下内容：
```groovy
        plugins {
            id 'war'  
        }
        
        repositories {
            jcenter()
        }
        
        dependencies {
            providedCompile 'javax.servlet:javax.servlet-api:3.1.0' 
            testCompile 'junit:junit:4.12'
        }
```
>* war插件添加了`includesCompile`和`providedRuntime`的配置，类似于常
规Java应用程序中的编译和运行时，用于表示本地需要的但不应添加
到生成的webdemo.war文件中的依赖项。
>* `plugins{}`语法用于应用`java`和`war`插件。两者都不需要版本，
因为它们包含在Gradle中.
>* 通过执行`wrapper`任务为项目生成Gradle包装器是一个好习惯
```
        $ gradle wrapper --gradle-version=4.10-rc-2
        :wrapper
```
>   这将生成`gradlew`和`gradlew.bat`脚本以及带有包装jar的gradle文件夹
## 将servlet和元数据添加到项目中
> 有两种用于定义Web应用程序元数据的选项。在servlet规范3.0版之前，
元数据驻留在项目的WEB-INF文件夹中名为web.xml的部署描述符中,
从3.0开始，可以使用注释定义元数据.<br/>

> 在`src / main / java`文件夹下创建一个包文件夹`org / gradle / demo`。
添加一个servlet文件`HelloServlet.java`，其中包含以下内容:
```java
        package org.gradle.demo;
        
        import java.io.IOException;
        import javax.servlet.ServletException;
        import javax.servlet.annotation.WebServlet;
        import javax.servlet.http.HttpServlet;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        
        @WebServlet(name = "HelloServlet", urlPatterns = {"hello"}, loadOnStartup = 1) 
        public class HelloServlet extends HttpServlet {
            protected void doGet(HttpServletRequest request, HttpServletResponse response)
                throws ServletException, IOException {
                response.getWriter().print("Hello, World!");  
            }
        
            protected void doPost(HttpServletRequest request, HttpServletResponse response)
                throws ServletException, IOException {
                String name = request.getParameter("name");
                if (name == null) name = "World";
                request.setAttribute("user", name);
                request.getRequestDispatcher("response.jsp").forward(request, response); 
            }
        }
```
> servlet使用`@WebServlet`注释进行配置。 `doGet`方法通过编写`“Hello，World！”`来响应HTTP GET请求。
字符串到输出编写器, 它通过查找名为name的请求参数并将其作为名为user的属性添加到请求，
然后转发到`response.jsp`页面来响应HTTP POST请求.
## 将JSP页面添加到应用程序
> 通过在`src / main / webapp`文件夹中创建文件`index.html`，将`index.html`添加到应用程序的根目录，
其中包含以下内容:
```html
        <html>
        <head>
          <title>Web Demo</title>
        </head>
        <body>
        <p>Say <a href="hello">Hello</a></p> 
        
        <form method="post" action="hello">  
          <h2>Name:</h2>
          <input type="text" id="say-hello-text-input" name="name" />
          <input type="submit" id="say-hello-button" value="Say Hello" />
        </form>
        </body>
        </html>
```
> `index.html`页面使用链接向servlet提交HTTP GET请求，并使用表单提交HTTP POST请求。
该表单包含一个名为`name`的文本字段，该字段由servlet在其doPost方法中访问.<br/>
> 在其doPost方法中，servlet将控制转发到另一个名为`response.jsp`的JSP页面。因此，
使用以下内容在`src / main / webapp`中定义`response.jsp`文件:
```jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
        <head>
            <title>Hello Page</title>
        </head>
        <body>
            <h2>Hello, ${user}!</h2>
        </body>
    </html>
```
> 响应页面从请求中访问用户变量并将其呈现在h2标记内
## 添加`gretty`插件并运行应用程序
```groovy
        plugins {
            id 'war'
            id 'org.gretty' version '2.2.0' 
        }
```
> `gretty`插件向应用程序添加了大量任务，对于在Jetty或Tomcat环境中运行或测试非常有用。
现在，可以使用`appRun`任务构建应用程序并将其部署到默认（Jetty）容器.
```text
        $ ./gradlew appRun
        :prepareInplaceWebAppFolder
        :createInplaceWebAppFolder UP-TO-DATE
        :compileJava
        :processResources UP-TO-DATE
        :classes
        :prepareInplaceWebAppClasses
        :prepareInplaceWebApp
        :appRun
        12:25:13 INFO  Jetty 9.2.15.v20160210 started and listening on port 8080
        12:25:13 INFO  webdemo runs at:
        12:25:13 INFO    http://localhost:8080/webdemo
        Press any key to stop the server.
        > Building 87% > :appRun
        
        BUILD SUCCESSFUL
```
> 现在可以访问`http：// localhost：8080 / webdemo`上的Web应用程序，
然后单击链接以执行GET请求或提交表单以执行POST请求.

> 虽然输出显示按任意键以停止服务器，但Gradle不会截取标准输入。要停止该过程，请按<kbd>ctrl-C</kbd>
## 使用`Mockito`对servlet进行单元测试
> 将Mockito依赖项添加到testCompile配置下的构建脚本中:
```groovy
        dependencies {
            providedCompile 'javax.servlet:javax.servlet-api:3.1.0'
            testCompile 'junit:junit:4.12'
            testCompile 'org.mockito:mockito-core:2.7.19'  
        }
```
> 要对servlet进行单元测试，
在`src / test / java`下创建一个包文件夹`org.gradle.demo`。
使用以下内容添加测试类文件`HelloServletTest.java`:
```java
       package org.gradle.demo;
       
       import org.junit.Before;
       import org.junit.Test;
       import org.mockito.Mock;
       import org.mockito.MockitoAnnotations;
       
       import javax.servlet.RequestDispatcher;
       import javax.servlet.http.HttpServletRequest;
       import javax.servlet.http.HttpServletResponse;
       import java.io.PrintWriter;
       import java.io.StringWriter;
       
       import static org.junit.Assert.assertEquals;
       import static org.mockito.Mockito.*;
       
       public class HelloServletTest {
           @Mock private HttpServletRequest request;
           @Mock private HttpServletResponse response;
           @Mock private RequestDispatcher requestDispatcher;
       
           @Before
           public void setUp() throws Exception {
               MockitoAnnotations.initMocks(this);
           }
       
           @Test
           public void doGet() throws Exception {
               StringWriter stringWriter = new StringWriter();
               PrintWriter printWriter = new PrintWriter(stringWriter);
       
               when(response.getWriter()).thenReturn(printWriter);
       
               new HelloServlet().doGet(request, response);
       
               assertEquals("Hello, World!", stringWriter.toString());
           }
       
           @Test
           public void doPostWithoutName() throws Exception {
               when(request.getRequestDispatcher("response.jsp"))
                   .thenReturn(requestDispatcher);
       
               new HelloServlet().doPost(request, response);
       
               verify(request).setAttribute("user", "World");
               verify(requestDispatcher).forward(request,response);
           }
       
           @Test
           public void doPostWithName() throws Exception {
               when(request.getParameter("name")).thenReturn("Dolly");
               when(request.getRequestDispatcher("response.jsp"))
                   .thenReturn(requestDispatcher);
       
               new HelloServlet().doPost(request, response);
       
               verify(request).setAttribute("user", "Dolly");
               verify(requestDispatcher).forward(request,response);
           }
       } 
```
> 现在可以使用Gradle使用`test`任务（或任何依赖于它的任务，如`build`）来测试servlet, 
可以从`build / reports / tests / test / index.html`访问测试输出结果.
## 添加功能测试
> `gretty`插件与Gradle结合使用，可以轻松地向Web应用程序添加功能测试。将以下行添加到构建脚本中:
```groovy
        gretty {
            integrationTestTask = 'test'  // 告诉gretty启动并停止测试服务器
        }
        
        // ... rest from before ...
        
        dependencies {
            providedCompile 'javax.servlet:javax.servlet-api:3.1.0'
            testCompile 'junit:junit:4.12'
            testCompile 'org.mockito:mockito-core:2.7.19'
            testCompile 'io.github.bonigarcia:webdrivermanager:1.6.1'  // 自动安装浏览器驱动程序
            testCompile 'org.seleniumhq.selenium:selenium-java:3.3.1'  // 使用Selenium进行功能测试
        }
```
> 在`src / test / java`目录中将以下功能测试添加到项目中:
```java
        package org.gradle.demo;
        
        import io.github.bonigarcia.wdm.ChromeDriverManager;
        import org.junit.After;
        import org.junit.Before;
        import org.junit.BeforeClass;
        import org.junit.Test;
        import org.openqa.selenium.By;
        import org.openqa.selenium.WebDriver;
        import org.openqa.selenium.chrome.ChromeDriver;
        
        import static org.junit.Assert.assertEquals;
        
        public class HelloServletFunctionalTest {
            private WebDriver driver;
        
            @BeforeClass
            public static void setupClass() {
                ChromeDriverManager.getInstance().setup();  // 如有必要，下载并安装浏览器驱动程序
            }
        
            @Before
            public void setUp() {
                driver = new ChromeDriver();  // 启动浏览器自动化           
            }
        
            @After
            public void tearDown() {
                if (driver != null)
                    driver.quit();   // 完成后关闭浏览器                      
            }
        
            @Test
            public void sayHello() throws Exception {      
                // 使用Selenium API运行功能测试
                driver.get("http://localhost:8080/webdemo");
        
                driver.findElement(By.id("say-hello-text-input")).sendKeys("Dolly");
                driver.findElement(By.id("say-hello-button")).click();
        
                assertEquals("Hello Page", driver.getTitle());
                assertEquals("Hello, Dolly!", driver.findElement(By.tagName("h2")).getText());
            }
        }
```
> 运行功能测试:
```text
        $ ./gradlew test
        :prepareInplaceWebAppFolder UP-TO-DATE
        :createInplaceWebAppFolder UP-TO-DATE
        :compileJava UP-TO-DATE
        :processResources UP-TO-DATE
        :classes UP-TO-DATE
        :prepareInplaceWebAppClasses UP-TO-DATE
        :prepareInplaceWebApp UP-TO-DATE
        :compileTestJava UP-TO-DATE
        :processTestResources UP-TO-DATE
        :testClasses UP-TO-DATE
        :appBeforeIntegrationTest
        12:57:56 INFO  Jetty 9.2.15.v20160210 started and listening on port 8080
        12:57:56 INFO  webdemo runs at:
        12:57:56 INFO    http://localhost:8080/webdemo
        :test
        :appAfterIntegrationTest
        Server stopped.
        
        BUILD SUCCESSFUL
```