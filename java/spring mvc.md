## Tomcat & web

### 安装配置

1. 下载：http://tomcat.apache.org/

2. 安装：解压压缩包即可。
	* 注意：安装目录建议不要有中文和空格
3. 卸载：删除目录就行了
4. 启动：
	* bin/startup.bat ,双击运行该文件即可
	* 访问：浏览器输入：http://localhost:8080 回车访问自己
					  http://别人的ip:8080 访问别人
	
	* 可能遇到的问题：
		1. 黑窗口一闪而过：
			* 原因： 没有正确配置JAVA_HOME环境变量
			* 解决方案：正确配置JAVA_HOME环境变量

		2. 启动报错：
			1. 暴力：找到占用的端口号，并且找到对应的进程，杀死该进程
				* netstat -ano
			2. 温柔：修改自身的端口号
				* conf/server.xml
				* <Connector port="8888" protocol="HTTP/1.1"
	               connectionTimeout="20000"
	               redirectPort="8445" />
				* 一般会将tomcat的默认端口号修改为80。80端口号是http协议的默认端口号。
					* 好处：在访问时，就不用输入端口号
5. 关闭：
	1. 正常关闭：
		* bin/shutdown.bat
		* ctrl+c
	2. 强制关闭：
		* 点击启动窗口的×
6. 配置:
	* 部署项目的方式：
		1. 直接将项目放到webapps目录下即可。
			* /hello：项目的访问路径-->虚拟目录
			* 简化部署：将项目打成一个war包，再将war包放置到webapps目录下。
				* war包会自动解压缩

		2. 配置conf/server.xml文件
			在<Host>标签体中配置
			<Context docBase="D:\hello" path="/hehe" />
			* docBase:项目存放的路径
			* path：虚拟目录

		3. 在conf\Catalina\localhost创建任意名称的xml文件。在文件中编写
			<Context docBase="D:\hello" />
			* 虚拟目录：xml文件的名称
	
	* 静态项目和动态项目：
		* 目录结构
			* java动态项目的目录结构：
				-- 项目的根目录
					-- WEB-INF目录：
						-- web.xml：web项目的核心配置文件
						-- classes目录：放置字节码文件的目录
						-- lib目录：放置依赖的jar包

### Servlet: server applet

> * 概念：运行在服务器端的小程序
> 	* Servlet就是一个接口，定义了Java类被浏览器访问到(tomcat识别)的规则。
> 	* 将来我们自定义一个类，实现Servlet接口，复写方法。

> 原理

![](pic\Servlet执行原理.bmp)

#### web.xml

```xml
 <!--配置Servlet -->
<servlet>
    <servlet-name>demo1</servlet-name>
    <servlet-class>cn.itcast.web.servlet.ServletDemo1</servlet-class>
</servlet>

<servlet-mapping>
    <servlet-name>demo1</servlet-name>
    <url-pattern>/demo1</url-pattern>
</servlet-mapping>
```

#### 执行原理

> 1. 当服务器接受到客户端浏览器的请求后，会解析请求URL路径，获取访问的Servlet的资源路径
> 2. 查找web.xml文件，是否有对应的<url-pattern>标签体内容。
> 3. 如果有，则在找到对应的<servlet-class>全类名
> 4. tomcat会将字节码文件加载进内存，并且创建其对象
> 5. 调用其方法

#### 生命周期

> 1. 被创建：执行init方法，只执行一次
> 		* Servlet什么时候被创建？
> 			* 默认情况下，第一次被访问时，Servlet被创建
> 		* 可以配置执行Servlet的创建时机。
> 			* 在<servlet>标签下配置
> 				1. 第一次被访问时，创建
>               		
> 		       		* <load-on-startup>的值为负数
> 		     2. 在服务器启动时，创建
> 		    
> 		         + <load-on-startup>的值为0或正整数
> 		    
> 		         * Servlet的init方法，只执行一次，说明一个Servlet在内存中只存在一个对象，Servlet是单例的
> 		         			* 多个用户同时访问时，可能存在线程安全问题。
> 		         	* 解决：尽量不要在Servlet中定义成员变量。即使定义了成员变量，也不要对修改值
> 	
> 2. 提供服务：执行service方法，执行多次
>
>   * 每次访问Servlet时，Service方法都会被调用一次。
> 3. 被销毁：执行destroy方法，只执行一次
>   * Servlet被销毁时执行。服务器关闭时，Servlet被销毁
>   * 只有服务器正常关闭时，才会执行destroy方法。
>   * destroy方法在Servlet被销毁之前执行，一般用于释放资源

#### Servlet3.0 注解配置

> 可以不用web.xml了

> @WebServlet

#### IDEA与tomcat的相关配置

> 1. IDEA会为每一个tomcat部署的项目单独建立一份配置文件
> 	* 查看控制台的log：Using CATALINA_BASE:   "C:\Users\fqy\.IntelliJIdea2018.1\system\tomcat\_itcast"
>
> 2. 工作空间项目    和     tomcat部署的web项目
> 	* tomcat真正访问的是“tomcat部署的web项目”，"tomcat部署的web项目"对应着"工作空间项目" 的web目录下的所有资源
> 	* **WEB-INF目录下的资源不能被浏览器直接访问。**
> 3. 断点调试：使用"小虫子"启动 dubug 启动

#### Servlet体系结构

> Servlet -- 接口
> 		|
> 	GenericServlet -- 抽象类
> 		|
> 	HttpServlet  -- 抽象类
>
> 	 GenericServlet：将Servlet接口中其他的方法做了默认空实现，只将service()方法作为抽象
> 		* 将来定义Servlet类时，可以继承GenericServlet，实现service()方法即可
> 	
> 	 HttpServlet：对http协议的一种封装，简化操作
> 		1. 定义类继承HttpServlet
> 		2. 复写doGet/doPost方法

#### servlet相关配置

> urlpartten:Servlet访问路径
> 		1. 一个Servlet可以定义多个访问路径 ： @WebServlet({"/d4","/dd4","/ddd4"})
> 		2. 路径定义规则：
> 			1. /xxx：路径匹配
> 			2. /xxx/xxx:多层路径，目录结构
> 			3. *.do：扩展名匹配



### Request

#### request继承解构

> ServletRequest		--	接口
> 		|	继承
> HttpServletRequest	-- 接口
> 		|	实现
> org.apache.catalina.connector.RequestFacade 类(tomcat)

#### request功能



### Response

### ServletContext对象

>  概念：代表整个web应用，可以和程序的容器(服务器)来通信

+ 获取

  1. 通过request对象获取
     		request.**getServletContext**();
  2. 通过HttpServlet获取
     		this.**getServletContext**();

+ 功能

  1.  获取MIME类型：
     + MIME类型:在互联网通信过程中定义的一种文件数据类型
     +  格式： 大类型/小类型   text/html  image/jpeg
     +  获取：String **getMimeType**(String file) 

  2. 域对象：共享数据

     + setAttribute(String name,Object value)
     + getAttribute(String name)
     +  removeAttribute(String name)
     + **ServletContext对象范围：所有用户所有请求的数据**

  3. 获取文件的真实(服务器)路径

     ```java
     String getRealPath(String path)  
     String b = context.getRealPath("/b.txt");//web目录下资源访问
     System.out.println(b);
     	
     String c = context.getRealPath("/WEB-INF/c.txt");//WEB-INF目录下的资源访问
     System.out.println(c);
     	
     String a = context.getRealPath("/WEB-INF/classes/a.txt");//src目录下的资源访问
     System.out.println(a);
     ```

     

### filter 过滤器

#### 配置

> 注解: @WebFilter ( "/*" )

```xml
<!-- web.xml -->
<filter>
    <filter-name>demo1</filter-name>
    <filter-class>cn.itcast.web.filter.FilterDemo1</filter-class>
</filter>
<filter-mapping>
    <filter-name>demo1</filter-name>
    <!-- 拦截路径 -->
    <url-pattern>/*</url-pattern>
</filter-mapping>
```



### listener 监听器











## 三层架构 和 spring mvc

### 三层架构

1. 咱们开发服务器端程序，一般都基于两种形式，一种C/S架构程序，一种B/S架构程序

2. 使用Java语言基本上都是开发B/S架构的程序，B/S架构又分成了三层架构

3. 三层架构

   + 表现层：WEB层，用来和客户端进行数据交互的。表现层一般会采用MVC的设计模型

   + 业务层：处理公司具体的业务逻辑的

   + 持久层：用来操作数据库的


### MVC模型
1. MVC全名是Model View Controller 模型视图控制器，每个部分各司其职。
2. Model：数据模型，JavaBean的类，用来进行数据封装。
3. View：指JSP、HTML用来展示数据给用户
4. Controller：用来接收用户的请求，整个流程的控制器。用来进行数据校验等。

## spring mvc 入门配置

 ![](pic\springmvc-structure.png)

### pom

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.mp</groupId>
  <artifactId>mvc-demo</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>

  <name>mvc-demo Maven Webapp</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <spring.version>5.0.9.RELEASE</spring.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>servlet-api</artifactId>
      <version>2.5</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>jsp-api</artifactId>
      <version>2.0</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.2</version>
    </dependency>

  </dependencies>

  <build>
    <finalName>mvc-demo</finalName>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.1.0</version>
        </plugin>
        <!-- see http://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_war_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>2.22.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-war-plugin</artifactId>
          <version>3.2.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>2.5.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>

```

### web.xml

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>

  <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>

  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
</web-app>

```

### springmvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 开启注解扫描 -->
    <context:component-scan base-package="com.mp"/>

    <!-- 视图解析器对象 -->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!-- 开启SpringMvc框架注解的支持 -->
    <mvc:annotation-driven/>

</beans>
```

### HelloController.java

```java
@Controller
public class HelloController {

    @RequestMapping("/hello")
    public String sayHello(){
        System.out.println("hello springMvc");
        return null;
    }
}
```

### 流程图

<img src="pic\springmvc-process.bmp" style="zoom: 200%;" />

### 执行过程分析

+ 入门案例的执行流程
  1. 当启动Tomcat服务器的时候，因为配置了load-on-startup标签，所以会创建DispatcherServlet对象，
     就会加载springmvc.xml配置文件
  2. 开启了注解扫描，那么HelloController对象就会被创建
  3. 从index.jsp发送请求，请求会先到达DispatcherServlet核心控制器，根据配置@RequestMapping注解
     找到执行的具体方法
  4. 根据执行方法的返回值，再根据配置的视图解析器，去指定的目录下查找指定名称的JSP文件
  5. Tomcat服务器渲染页面，做出响应

+ 入门案例中的组件分析
  1. 前端控制器（DispatcherServlet）
  2. 处理器映射器（HandlerMapping）
  3. 处理器（Handler）
  4. 处理器适配器（HandlAdapter）
  5. 视图解析器（View Resolver）
  6. 视图（View）



### @RequestMapping

1. RequestMapping注解的作用是建立请求URL和处理方法之间的对应关系

2. RequestMapping注解可以作用在方法和类上
   + 作用在类上：第一级的访问目录
   + 作用在方法上：第二级的访问目录
   + 细节：路径可以不编写 / 表示应用的根目录开始
   + 细节：${ pageContext.request.contextPath }也可以省略不写，但是路径上不能写 /
3. RequestMapping的属性
   + path  指定请求路径的url
   + value value属性和path属性是一样的
   + mthod 指定该方法的请求方式
   + params 指定限制请求参数的条件
   + headers 发送的请求中必须包含的请求头

### 请求参数中文乱码

> 在web.xml中配置spring的过滤器

```xml
<!-- 配置过滤器，解决中文乱码的问题 -->
<filter>
	<filter-name>characterEncodingFilter</filter-name>
	
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter
    </filter-class>
	
    <!-- 指定字符集 -->
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```



#### 自定义类型转换器

>  表单提交的任何数据类型全部都是 字符串类型 

```xml
<!--配置自定义类型转换器-->
<bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
    <property name="converters">
        <set>
            <bean class="cn.itcast.utils.StringToDateConverter"/>
        </set>
    </property>
</bean>
```

```java
/**
 * 把字符串转换日期 2011-11-22 ==> Date()
 */
public class StringToDateConverter implements Converter<String,Date>{

    /**
     * String source    传入进来字符串
     * @param source
     * @return
     */
    public Date convert(String source) {
        // 判断
        if(source == null){
            throw new RuntimeException("请您传入数据");
        }
        DateFormat df = new SimpleDateFormat("yyyy-MM-dd");

        try {
            // 把字符串转换日期
            return df.parse(source);
        } catch (Exception e) {
            throw new RuntimeException("数据类型转换出现错误");
        }
    }

}
```



## 参数绑定 & 类型转换

#### @RequestParam

> 把请求中的指定名称的参数传递给控制器中的形参

```java
@RequestMapping("/testRequestParam")
public String testRequestParam(@RequestParam(name="name") String username){
    System.out.println("执行了...");
    System.out.println(username);
    return "success";
}
```

#### @RequestBody

> 获取请求体的内容

```java
@RequestMapping(path="/hello")
public String sayHello(@RequestBody String body) {
    System.out.println("aaaa");
    System.out.println(body);
    return "success";
}
```

#### @Pathvariable

>  绑定url中的占位符. 例如: url中有/delete/{id}，{id}就是占位符

```java
<a href="user/hello/1">入门案例</a>
/**
* 接收请求
* @return
*/
@RequestMapping(path="/hello/{id}")
	public String sayHello(@PathVariable(value="id") String id) {
	System.out.println(id);
	return "success";
}
```

#### @RequestHeader

> 获取指定请求头的值

```java
@RequestMapping(path="/hello")
public String sayHello(@RequestHeader(value="Accept") String header) {
	System.out.println(header);
	return "success";
}
```

#### @CookieValue

>获取指定cookie名称和值

```java
@RequestMapping(path="/hello")
public String sayHello(@CookieValue(value="JSESSIONID") String cookieValue) {
	System.out.println(cookieValue);
	return "success";
}
```

#### @ModelAttribute

>+ 出现在**方法**上：表示当前方法会在控制器方法执行前线执行。
>+ 出现在**参数**上：获取指定的数据给参数赋值。



#### @SessionAttribute

> 用于多次执行控制器方法间的参数共享



















