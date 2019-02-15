# 1.第一个springboot应用
首先需要在pom.xml中引入,***spring-boot-starter-parent*** 来指定现有应用的父项目
```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.1.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
```
或者：
```xml
<dependencyManagement> 
		<dependencies> 
		<dependency> 
			<！ - 从Spring Boot导入依赖关系管理 - > 
			<groupId> org.springframework.boot </ groupId> 
			<artifactId> spring-boot-dependencies </ artifactId> 
			<version> 2.1 .1.RELEASE </ version> 
			<type> pom </ type> 
			<scope> import </ scope> 
		</ dependency> 
	</ dependencies> 
</ dependencyManagement>
```
若不进行上述操作，则无法运行springboot应用！
在指定了父工程后，可以引入需要的starter来构建我们的系统。比如我想构建一个web应用，则至少需要引入web-starter
```xml
      <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
```
上述配置进行完毕后，如下代码可创建第一个springboot程序。
```java
@EnableAutoConfiguration
@RestController
public class Config {
    @RequestMapping("/")
    public String sayHello(){
        return "Hello SpringBoot！";
    }
    public static void main(String[] args) {
        SpringApplication.run(Config.class,args);
    }
}
```
```log
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.1.1.RELEASE)

2019-01-07 21:26:29.615  INFO 17580 --- [           main] com.pactera.firstsb.Config               : Starting Config on DESKTOP-11F53BF with PID 17580 (C:\Users\Kenton\IdeaProjects\firstsb\target\classes started by Kenton in C:\Users\Kenton\IdeaProjects\firstsb)
2019-01-07 21:26:29.619  INFO 17580 --- [           main] com.pactera.firstsb.Config               : No active profile set, falling back to default profiles: default
2019-01-07 21:26:30.413  INFO 17580 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2019-01-07 21:26:30.429  INFO 17580 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2019-01-07 21:26:30.429  INFO 17580 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/9.0.13
2019-01-07 21:26:30.434  INFO 17580 --- [           main] o.a.catalina.core.AprLifecycleListener   : The APR based Apache Tomcat Native library which allows optimal performance in production environments was not found on the java.library.path: [C:\Program Files\Java\jdk1.8.0_191\bin;C:\Windows\Sun\Java\bin;C:\Windows\system32;C:\Windows;D:\app\root\product\12.1.0\dbhome_2\bin;C:\Program Files (x86)\Common Files\Oracle\Java\javapath;C:\Program Files (x86)\Razer Chroma SDK\bin;C:\Program Files\Razer Chroma SDK\bin;C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Windows\System32\OpenSSH\;C:\Program Files\Git\cmd;C:\Program Files\TortoiseGit\bin;C:\Program Files\Microsoft VS Code\bin;C:\Program Files\Java\jdk-10.0.2\bin;C:\Windows\system32\config\systemprofile\AppData\Local\Microsoft\WindowsApps;C:\Program Files\Java\jdk1.8.0_191\bin;D:\Program Files\apache-maven-3.6.0\bin;C:\Program Files\MySQL\MySQL Server 5.5\bin;C:\Program Files\Microsoft SQL Server\Client SDK\ODBC\130\Tools\Binn\;C:\Program Files (x86)\Microsoft SQL Server\140\Tools\Binn\;C:\Program Files\Microsoft SQL Server\140\Tools\Binn\;C:\Program Files\Microsoft SQL Server\140\DTS\Binn\;C:\Program Files (x86)\Microsoft SQL Server\140\DTS\Binn\;C:\Program Files (x86)\Microsoft SQL Server\Client SDK\ODBC\130\Tools\Binn\;C:\Program Files (x86)\Microsoft SQL Server\140\Tools\Binn\ManagementStudio\;C:\Users\Kenton\AppData\Local\Microsoft\WindowsApps;C:\Program Files\Bandizip\;C:\Program Files\JetBrains\DataGrip 2018.3\bin;C:\Users\Kenton\AppData\Local\atom\bin;C:\Program Files\JetBrains\IntelliJ IDEA 2018.3.2\bin;;.]
2019-01-07 21:26:30.556  INFO 17580 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2019-01-07 21:26:30.556  INFO 17580 --- [           main] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 905 ms
2019-01-07 21:26:30.725  INFO 17580 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2019-01-07 21:26:30.873  INFO 17580 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2019-01-07 21:26:30.876  INFO 17580 --- [           main] com.pactera.firstsb.Config               : Started Config in 1.544 seconds (JVM running for 2.152)
2019-01-07 21:26:42.580  INFO 17580 --- [nio-8080-exec-2] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring DispatcherServlet 'dispatcherServlet'
2019-01-07 21:26:42.580  INFO 17580 --- [nio-8080-exec-2] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
2019-01-07 21:26:42.584  INFO 17580 --- [nio-8080-exec-2] o.s.web.servlet.DispatcherServlet        : Completed initialization in 4 ms
```
# 2. starter
