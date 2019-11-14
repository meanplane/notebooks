## spring xml

> Spring是分层的 Java SE/EE应用 full-stack 轻量级开源框架，以
>
>  **IoC（Inverse Of Control：反转控制）**和 
>
>  **AOP（Aspect Oriented Programming：面向切面编程）**为内核，
>
> 提供了展现层 SpringMVC 和持久层 Spring JDBC 以及业务层事务管理等众多的企业级应用技术，还能整合开源世界众多著名的第三方框架和类库，逐渐成为使用最多的Java EE 企业应用开源框架。

### 构建项目

> maven构建pom文件

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.0.9.RELEASE</version>
</dependency>
```

> 用到的jar包

+ org.springframework:spring-aop
+ org.springframework:spring-beans
+ org.springframework:spring-context
+ org.springframework:spring-core
+ org.springframework:spring-expression (spel)
+ org.springframework:spring-jcl (org.apache.commons.logging)

> 配置文件 beans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <bean id="user" class="com.mp.pojo.User"/>
</beans>
```

> 基本使用

```java
@Test
public void test1(){
    ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
    
    User user = (User) context.getBean("user");
    User user1 = context.getBean("user", User.class);

    System.out.println(user);
    System.out.println(user1);
}
```

### ApplicationContext的三个实现类

#### 1 ClassPathXmlApplicationContext

> 它可以加载类路径下的配置文件，要求配置文件必须在类路径下。不在的话，加载不了。(更常用)

#### 2 FileSystemXmlApplicationContext

> 它可以加载磁盘任意路径下的配置文件(必须有访问权限）

#### 3 AnnotationConfigApplicationContext

> 它是用于读取**注解**创建容器的

### xml创建bean的三种方式

#### 1 使用默认构造函数创建

> 在spring的配置文件中使用bean标签，配以id和class属性之后，且没有其他属性和标签时。
> 采用的就是默认构造函数创建bean对象，此时如果类中没有默认构造函数，则对象无法创建。

```xml
<bean id="user" class="com.mp.pojo.User"/>
```

#### 2 使用普通工厂中的方法创建对象

> 使用某个类中的方法创建对象，并存入spring容器

```xml
<bean id="factory" class="com.mp.factory.InstanceFactory"/>
<bean id="factoryUser" factory-bean="factory" factory-method="getUser"/>
```

```java
public class InstanceFactory {
    public User getUser(){
        return new User();
    }
}

User facotryUser = (User) context.getBean("factoryUser");
System.out.println(facotryUser);
```

#### 3 使用工厂中的静态方法创建对象

> 使用某个类中的静态方法创建对象，并存入spring容器

```xml
<bean id="staticUser" class="com.mp.factory.StaticFacory" factory-method="getUser"/>
```

```java
public class StaticFacory {
    public static User getUser(){
        return new User();
    }
}

User staticUser = (User) context.getBean("staticUser");
System.out.println(staticUser);
```

### 作用范围

+ singleton：单例的（默认值）
+ prototype：多例的
+ request：作用于web应用的请求范围
+ session：作用于web应用的会话范围
+ global-session：作用于集群环境的会话范围（全局会话范围），当不是集群环境时，它就是session

### 生命周期

+ 单例对象

> 出生：当容器创建时对象出生
> 活着：只要容器还在，对象一直活着
> 死亡：容器销毁，对象消亡
> 总结：单例对象的生命周期和容器相同

+ 多例对象

> 出生：当我们使用对象时spring框架为我们创建
> 活着：对象只要是在使用过程中就一直活着。
> 死亡：当对象长时间不用，且没有别的对象引用时，由Java的垃圾回收器回收

+ init-method & destroy-method

  ```java
  <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl"
            scope="prototype" init-method="init" destroy-method="destroy"></bean>
            
  public class AccountServiceImpl implements IAccountService {
      public AccountServiceImpl(){
          System.out.println("对象创建了");
      }
  
      public void  saveAccount(){
          System.out.println("service中的saveAccount方法执行了。。。");
      }
  
      public void  init(){
          System.out.println("对象初始化了。。。");
      }
      public void  destroy(){
          System.out.println("对象销毁了。。。");
      }
  }
  ```



### 依赖注入

> Dependency Injection
>
> IOC的作用：
>             降低程序间的耦合（依赖关系）
> 依赖关系的管理：
>             以后都交给spring来维护
>        	 在当前类需要用到其他类的对象，由spring为我们提供，我们只需要在配置文件中说明
> 依赖关系的维护：
>             就称之为依赖注入。
>
> 能注入的数据：有三类
>                 基本类型和String
>                 其他bean类型（在配置文件中或者注解配置过的bean）
>                 复杂类型/集合类型
> 注入的方式：有三种
>                 第一种：使用构造函数提供
>                 第二种：使用set方法提供
>                 第三种：使用注解提供

#### 1 构造函数注入

> 使用的标签:**constructor-arg**
>         标签出现的位置：bean标签的内部
>         标签中的属性
>             **type**：用于指定要注入的数据的数据类型，该数据类型也是构造函数中某个或某些参数的类型
>             **index**：用于指定要注入的数据给构造函数中指定索引位置的参数赋值。索引的位置是从0开始
>             **name**：用于指定给构造函数中指定名称的参数赋值 (**常用的**)
>            以上三个用于指定给构造函数中哪个参数赋值
>             **value**：用于提供基本类型和String类型的数据
>             **ref**：用于==指定其他的bean类型数据==。它指的就是在spring的Ioc核心容器中出现过的bean对象
>
>         优势：
>             在获取bean对象时，注入数据是必须的操作，否则对象无法创建成功。
>         弊端：
>             改变了bean对象的实例化方式，使我们在创建对象时，如果用不到这些数据，也必须提供。

```xml
<!-- 配置一个日期对象 -->
<bean id="now" class="java.util.Date"></bean>

<bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl">
    <constructor-arg name="name" value="泰斯特"></constructor-arg>
    <constructor-arg name="age" value="18"></constructor-arg>
    <constructor-arg name="birthday" ref="now"></constructor-arg>
</bean>
```

```java

public class AccountServiceImpl implements IAccountService {

    //如果是经常变化的数据，并不适用于注入的方式
    private String name;
    private Integer age;
    private Date birthday;

    public AccountServiceImpl(String name,Integer age,Date birthday){
        this.name = name;
        this.age = age;
        this.birthday = birthday;
    }

}
```



#### 2 set方法注入

> 涉及的标签：property
>         出现的位置：bean标签的内部
>         标签的属性
>             **name**：用于指定注入时所调用的set方法名称
>             **value**：用于提供基本类型和String类型的数据
>             **ref**：用于指定其他的bean类型数据。它指的就是在spring的Ioc核心容器中出现过的bean对象
>
>       ```
>   优势：
>   	创建对象时没有明确的限制，可以直接使用默认构造函数
>   弊端：
>   	如果有某个成员必须有值，则获取对象是有可能set方法没有执行。
>       ```

```xml
<bean id="accountService2" class="com.itheima.service.impl.AccountServiceImpl2">
    <property name="name" value="TEST" ></property>
    <property name="age" value="21"></property>
    <property name="birthday" ref="now"></property>
</bean>
```

```java
public class AccountServiceImpl2 implements IAccountService {

    //如果是经常变化的数据，并不适用于注入的方式
    private String name;
    private Integer age;
    private Date birthday;

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }
}

```



#### 3 复杂类型注入

> 用于给List结构集合注入的标签：
>             list array set
> 用于个Map结构集合注入的标签:
>             map  props
> 结构相同，标签可以互换

```java
public class AccountServiceImpl3 implements IAccountService {

    private String[] myStrs;
    private List<String> myList;
    private Set<String> mySet;
    private Map<String,String> myMap;
    private Properties myProps;

    public void setMyStrs(String[] myStrs) {
        this.myStrs = myStrs;
    }

    public void setMyList(List<String> myList) {
        this.myList = myList;
    }

    public void setMySet(Set<String> mySet) {
        this.mySet = mySet;
    }

    public void setMyMap(Map<String, String> myMap) {
        this.myMap = myMap;
    }

    public void setMyProps(Properties myProps) {
        this.myProps = myProps;
    }

}
```

```xml
<bean id="accountService3" class="com.itheima.service.impl.AccountServiceImpl3">
    <!-- string 数组 -->
    <property name="myStrs">
        <set>
            <value>AAA</value>
            <value>BBB</value>
            <value>CCC</value>
        </set>
    </property>

    <!-- list -->
    <property name="myList">
        <array>
            <value>AAA</value>
            <value>BBB</value>
            <value>CCC</value>
        </array>
    </property>

    <!-- set -->
    <property name="mySet">
        <list>
            <value>AAA</value>
            <value>BBB</value>
            <value>CCC</value>
        </list>
    </property>

    <!-- map -->
    <property name="myMap">
        <props>
            <prop key="testC">ccc</prop>
            <prop key="testD">ddd</prop>
        </props>
    </property>

    <!-- Properties -->
    <property name="myProps">
        <map>
            <entry key="testA" value="aaa"></entry>
            <entry key="testB">
                <value>BBB</value>
            </entry>
        </map>
    </property>
</bean>
```



## spring 注解

### 基本使用

> #### 配置文件 beans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <!--告知spring在创建容器时要扫描的包，配置所需要的标签不是在beans的约束中，而是一个名称为
    context名称空间和约束中-->
    <context:component-scan base-package="com.itheima"></context:component-scan>
</beans>
```

> #### 配置 bean

```java
@Service("accountService")
//@Scope("prototype")
public class AccountServiceImpl implements IAccountService {

//    @Autowired
//    @Qualifier("accountDao1")
    @Resource(name = "accountDao2")
    private IAccountDao accountDao = null;

    @PostConstruct
    public void  init(){
        System.out.println("初始化方法执行了");
    }

    @PreDestroy
    public void  destroy(){
        System.out.println("销毁方法执行了");
    }

    public void  saveAccount(){
        accountDao.saveAccount();
    }
}
```

> #### 使用

```java
//1.获取核心容器对象
// ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
ClassPathXmlApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
//2.根据id获取Bean对象
IAccountService as  = (IAccountService)ac.getBean("accountService");
```

### 创建对象

> 他们的作用就和在XML配置文件中编写一个<bean>标签实现的功能是一样的

+ **@Component**
*	作用：用于把当前类对象存入spring容器中
   *	属性:
     + value：**用于指定bean的 id**。当我们不写时，它的默认值是当前类名，且首字母改小写。
   
+ **@Controller**

+ **@Service**

+ **@Repository**

> 以上三个注解他们的作用和属性与Component是一模一样。
>
> 他们三个是spring框架为我们提供明确的三层使用的注解，使我们的三层对象更加清晰

### 注入数据

> 他们的作用就和在xml配置文件中的bean标签中写一个<property>标签的作用是一样的

+ **@Autowired**

  > 作用：自动按照类型注入。只要容器中有唯一的一个bean对象类型和要注入的变量类型匹配，就可以注入成功
  >  *                如果ioc容器中没有任何bean的类型和要注入的变量类型匹配，则报错。
  >  *                如果Ioc容器中有多个类型匹配时：
  >  *          出现位置：可以是变量上，也可以是方法上
  >  *          细节：在使用注解注入时，set方法就不是必须的了。

+ **@Qualifier**

  > 作用：在按照类中注入的基础之上再按照名称注入。它在给类成员注入时不能单独使用。但是在给方法参数注入时可以（稍后我们讲）
  >
  > 属性：
  >
  > + value：用于指定注入bean的id。

  ```java
  @Autowired
  @Qualifier("accountDao1")	// 必须和autowire一起使用,指定名字
  private IAccountDao accountDao = null;
  ```

+ **@Resource**

  > 作用：直接按照bean的id注入。它可以独立使用
  >
  > 属性：
  >
  > + name：用于指定bean的id。

  ```java
  @Resource(name = "accountDao2") // 替代autowire 和 qualifier
  private IAccountDao accountDao = null;
  ```

+ **@Value**

  > 作用：用于注入基本类型和String类型的数据
  >
  > 属性：
  >
  >  *          value：用于指定数据的值。它可以使用spring中SpEL(也就是spring的el表达式）
  >  *                      SpEL的写法：${表达式}

### 作用范围

> 他们的作用就和在bean标签中使用scope属性实现的功能是一样的
>
> @Scope
>  *          作用：用于指定bean的作用范围
>  *          属性：
>             + value：指定范围的取值。常用取值：**singleton**   **prototype**



### 生命周期

> 他们的作用就和在bean标签中使用init-method和destroy-methode的作用是一样的
>  *      PreDestroy
>         + 作用：用于指定销毁方法
>  *      PostConstruct
>         + 作用：用于指定初始化方法

```java
@Service("accountService")
//@Scope("prototype")
public class AccountServiceImpl implements IAccountService {

    //@Autowired
    //@Qualifier("accountDao1")
    @Resource(name = "accountDao2")
    private IAccountDao accountDao = null;

    @PostConstruct
    public void  init(){
        System.out.println("初始化方法执行了");
    }

    @PreDestroy
    public void  destroy(){
        System.out.println("销毁方法执行了");
    }

    public void  saveAccount(){
        accountDao.saveAccount();
    }
}

```

## spring纯注解配置

### @Configuration

> 作用：
>
> + 用于指定当前类是一个 spring **配置类**，当创建容器时会从该类上加载注解。获取容器时需要使用
> **AnnotationApplicationContext**(有@Configuration 注解的类.class)。
>
> 属性：
>
> + value:用于指定配置类的字节码



### @ComponentScan

> 作用：
>
> + 用于指定 spring 在初始化容器时要扫描的包。作用和在 spring 的 xml 配置文件中的：
>   **<context:component-scan base-package="com.itheima"/>**是一样的。
>
> 属性：
>
> + basePackages：用于指定要扫描的包。和该注解中的 value 属性作用一样。

> 配置类 SpringConfiguration.java

```java
package com.mp.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan("com.mp")
public class SpringConfiguration {
}
```

> 使用

```java
AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(SpringConfiguration.class);
InstanceFactory factory = context.getBean(InstanceFactory.class);
User user = factory.getUser();
System.out.println(user);
```

### @Bean

> + 作用：用于把当前方法的返回值作为bean对象存入spring的ioc容器中
> + 属性:
>    *          name:用于指定bean的id。当不写时，默认值是当前方法的名称
> + 细节：
>    *          当我们使用注解配置方法时，如果方法有参数，spring框架会去容器中查找有没有可用的bean对象。
>    *          查找的方式和Autowired注解的作用是一样的

demo

```java
@Configuration
@ComponentScan("com.mp")
public class SpringConfiguration {

    @Bean
    public QueryRunner queryRunner(DataSource dataSource){
        return new QueryRunner(dataSource);
    }

    @Bean
    public DataSource dataSource(){
        DruidAbstractDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/db2?useUnicode=true&characterEncoding=UTF8");
        dataSource.setUsername("root");
        dataSource.setPassword("xxx123");
        return dataSource;
    }
}

@Test
public void test7(){
    AnnotationConfigApplicationContext context = new                       AnnotationConfigApplicationContext(SpringConfiguration.class);
    AccountDao accountDao = context.getBean(AccountDao.class);
    List<Account> allAccounts = accountDao.findAllAccounts();
    System.out.println(allAccounts);
}
```

### @Import

> 作用：用于导入其他的配置类
>
> 属性：
>
>  *          value：用于指定其他配置类的字节码。
>  *                  当我们使用Import的注解之后，有Import注解的类就父配置类，而导入的都是子配置类



### @PropertySource

> 作用：用于指定properties文件的位置
>
> 属性：
>
>  *          value：指定文件的名称和路径。
>  *                  关键字：classpath，表示类路径下

### @Qualifier

> 当有多个同类型的 bean时, 直接在参数中使用

```java
@Bean(name="runner")
@Scope("prototype")
public QueryRunner createQueryRunner(@Qualifier("ds2") DataSource dataSource){
    return new QueryRunner(dataSource);
}
```



###  完整demo

####  JdbcConfig.properties

```pasca
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/db2?useUnicode=true&characterEncoding=UTF8
jdbc.username=root
jdbc.password=xxx123
```

####  JdbcConfig.java

```java
@Configuration
@PropertySource({"classpath:jdbcConfig.properties","classpath:log4j.properties"})
public class JdbcConfig {

    @Value("${jdbc.driver}")
    private String driver;

    @Value("${jdbc.url}")
    private String url;

    @Value("${jdbc.username}")
    private String username;

    @Value("${jdbc.password}")
    private String password;

    @Bean
    public QueryRunner queryRunner(DataSource dataSource){
        return new QueryRunner(dataSource);
    }

    @Bean
    public DataSource dataSource(){
        DruidAbstractDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName(driver);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }
}
```

#### SpringConfiguration.java

```java
@Configuration
@Import(JdbcConfig.class)
@ComponentScan("com.mp")
public class SpringConfiguration {}
```

> 使用

```java
AnnotationConfigApplicationContext context = new                                                      AnnotationConfigApplicationContext(SpringConfiguration.class);
AccountDao accountDao = context.getBean(AccountDao.class);
List<Account> allAccounts = accountDao.findAllAccounts();
System.out.println(allAccounts);
```

## spring整合junit

### 分析

>1、应用程序的入口
>		main方法
>
>2、junit单元测试中，没有main方法也能执行
>		junit集成了一个main方法
>		该方法就会判断当前测试类中哪些方法有 @Test注解
>		junit就让有Test注解的方法执行
>
>3、junit不会管我们是否采用spring框架
>		在执行测试方法时，junit根本不知道我们是不是使用了spring框架
>		所以也就不会为我们读取配置文件/配置类创建spring核心容器
>
>4、由以上三点可知
>		当测试方法执行时，没有Ioc容器，就算写了Autowired注解，也无法实现注入

### Spring整合junit的配置

>1、导入spring整合junit的jar(坐标)
>
>2、使用Junit提供的一个注解把原有的main方法替换了，替换成spring提供的 
>
>​		**@Runwith**
>
>3、告知spring的运行器，spring和ioc创建是基于xml还是注解的，并且说明位置
>
>​		**@ContextConfiguration**
>
>​			locations：指定xml文件的位置，加上classpath关键字，表示在类路径下
>
>​			classes：指定注解类所在地位置
>
>​			当我们使用spring 5.x版本的时候，要求junit的jar必须是4.12及以上

### demo

> SpringJunit4ClassRunner 所需依赖

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.0.9.RELEASE</version>
</dependency>
```

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfiguration.class)
public class AccountServiceTest {

    @Autowired
    private IAccountService as = null;


    @Test
    public void testFindAll() {
        //3.执行方法
        List<Account> accounts = as.findAllAccount();
        for(Account account : accounts){
            System.out.println(account);
        }
    }
}
```

## spring整合mybatis

### pom

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.0.9.RELEASE</version>
    </dependency>
    // 之前缺这个 一直报错
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.0.9.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>5.0.9.RELEASE</version>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.6</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.2</version>
    </dependency>
    
    // 这玩意就是spring整合mybatis必须的
    // 注意版本
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>2.0.3</version>
    </dependency>
    
    // datasource 连接池
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.1.21</version>
    </dependency>

    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.12</version>
    </dependency>

    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.2</version>
    </dependency>

    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
    
</dependencies>
```

### jdbcConfig.properties 数据库配置

```
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/db2?useUnicode=true&characterEncoding=UTF8
jdbc.username=root
jdbc.password=xxx123
```

### log4j.properties 日志配置

```
# Set root category priority to INFO and its only appender to CONSOLE.
#log4j.rootCategory=INFO, CONSOLE            debug   info   warn error fatal
log4j.rootCategory=debug, CONSOLE, LOGFILE

# Set the enterprise logger category to FATAL and its only appender to CONSOLE.
log4j.logger.org.apache.axis.enterprise=FATAL, CONSOLE

# CONSOLE is set to be a ConsoleAppender using a PatternLayout.
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=%d{ISO8601} %-6r [%15.15t] %-5p %30.30c %x - %m\n

# LOGFILE is set to be a File appender using a PatternLayout.
log4j.appender.LOGFILE=org.apache.log4j.FileAppender
log4j.appender.LOGFILE.File=d:/work/java/mybatis01/axis.log
log4j.appender.LOGFILE.Append=true
log4j.appender.LOGFILE.layout=org.apache.log4j.PatternLayout
log4j.appender.LOGFILE.layout.ConversionPattern=%d{ISO8601} %-6r [%15.15t] %-5p %30.30c %x - %m\n
```

### spring配置类 SpringConfiguration.java

```java
@Configuration
@Import(JdbcConfig.class)
@ComponentScan(basePackages = {"com.mp.dao"})
public class SpringConfiguration {}
```

### db配置类JdbcConfig.java

```java
@Configuration
@PropertySource({"classpath:jdbcConfig.properties","classpath:log4j.properties"})
@MapperScan(basePackages = {"com.mp"})
public class JdbcConfig {

    @Value("${jdbc.driver}")
    private String driver;

    @Value("${jdbc.url}")
    private String url;

    @Value("${jdbc.username}")
    private String username;

    @Value("${jdbc.password}")
    private String password;

    @Bean
    public DataSource dataSource(){
        DruidAbstractDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName(driver);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }

    @Bean
    public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
        SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
        factoryBean.setDataSource(dataSource());
        return factoryBean.getObject();
    }
}
```

### test测试

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = {SpringConfiguration.class})
public class Test3 {

    @Autowired
    IUserMapper mapper;

    @Test
    public void test1(){
        List<User> all = mapper.findAll();
        System.out.println(all);
    }

}
```

## spring AOP
### AOP底层2种实现

> 1. jdk动态代理 Proxy.newProxyInstance  只能对实现了接口的类生成代理，而不能针对类
> 2. cglib  是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法
> 3. 如果目标对象**实现了接口**，默认情况下会采用**JDK的动态代理**实现AOP ;如果目标对象**没有实现了接口**，必须采用**CGLIB库**，spring会自动在JDK动态代理和CGLIB之间转换

#### 基于接口的动态代理 Proxy.newProxyInstance (jdk动态代理)

> jdk官方提供

```java
public interface IProducer {
    public void saleProduct(float money);
    public void afterService(float money);
}

public class Producer implements IProducer{
    public void saleProduct(float money){
        System.out.println("销售产品，并拿到钱："+money);
    }
    public void afterService(float money){
        System.out.println("提供售后服务，并拿到钱："+money);
    }
}

public static void main(String[] args) {
        final Producer producer = new Producer();

        /**
         * 动态代理：
         *  特点：字节码随用随创建，随用随加载
         *  作用：不修改源码的基础上对方法增强
         *  分类：
         *      基于接口的动态代理
         *      基于子类的动态代理
         *  基于接口的动态代理：
         *      涉及的类：Proxy
         *      提供者：JDK官方
         *  如何创建代理对象：
         *      使用Proxy类中的newProxyInstance方法
         *  创建代理对象的要求：
         *      被代理类最少实现一个接口，如果没有则不能使用
         *  newProxyInstance方法的参数：
         *      ClassLoader：类加载器
         *          它是用于加载代理对象字节码的。和被代理对象使用相同的类加载器。固定写法。
         *      Class[]：字节码数组
         *          它是用于让代理对象和被代理对象有相同方法。固定写法。
         *      InvocationHandler：用于提供增强的代码
         *          它是让我们写如何代理。我们一般都是些一个该接口的实现类，通常情况下都是匿名内部类，但不是必须的。
         *          此接口的实现类都是谁用谁写。
         */
       IProducer proxyProducer = (IProducer) Proxy.newProxyInstance(producer.getClass().getClassLoader(),
                producer.getClass().getInterfaces(),
                new InvocationHandler() {
                    /**
                     * 作用：执行被代理对象的任何接口方法都会经过该方法
                     * 方法参数的含义
                     * @param proxy   代理对象的引用
                     * @param method  当前执行的方法
                     * @param args    当前执行方法所需的参数
                     * @return        和被代理对象方法有相同的返回值
                     * @throws Throwable
                     */
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        //提供增强的代码
                        Object returnValue = null;

                        //1.获取方法执行的参数
                        Float money = (Float)args[0];
                        //2.判断当前方法是不是销售
                        if("saleProduct".equals(method.getName())) {
                            returnValue = method.invoke(producer, money*0.8f);
                        }
                        return returnValue;
                    }
                });
        proxyProducer.saleProduct(10000f);
    }

```

#### 基与子类的动态代理 cglib

> pom 依赖

```xml
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>2.1_3</version>
</dependency>
```

```java
public static void main(String[] args) {
        final Producer producer = new Producer();

        /**
         * 动态代理：
         *  特点：字节码随用随创建，随用随加载
         *  作用：不修改源码的基础上对方法增强
         *  分类：
         *      基于接口的动态代理
         *      基于子类的动态代理
         *  基于子类的动态代理：
         *      涉及的类：Enhancer
         *      提供者：第三方cglib库
         *  如何创建代理对象：
         *      使用Enhancer类中的create方法
         *  创建代理对象的要求：
         *      被代理类不能是最终类
         *  create方法的参数：
         *      Class：字节码
         *          它是用于指定被代理对象的字节码。
         *
         *      Callback：用于提供增强的代码
         *          它是让我们写如何代理。我们一般都是些一个该接口的实现类，通常情况下都是匿名内部类，但不是必须的。
         *          此接口的实现类都是谁用谁写。
         *          我们一般写的都是该接口的子接口实现类：MethodInterceptor
         */
        Producer cglibProducer = (Producer)Enhancer.create(producer.getClass(), new MethodInterceptor() {
            /**
             * 执行北地阿里对象的任何方法都会经过该方法
             * @param proxy
             * @param method
             * @param args
             *    以上三个参数和基于接口的动态代理中invoke方法的参数是一样的
             * @param methodProxy ：当前执行方法的代理对象
             * @return
             * @throws Throwable
             */
            @Override
            public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
                //提供增强的代码
                Object returnValue = null;

                //1.获取方法执行的参数
                Float money = (Float)args[0];
                //2.判断当前方法是不是销售
                if("saleProduct".equals(method.getName())) {
                    returnValue = method.invoke(producer, money*0.8f);
                }
                return returnValue;
            }
        });
        cglibProducer.saleProduct(12000f);
    }
```



### spring中的AOP

#### 相关术语

+ **Joinpoint( 连接点):**
  所谓连接点是指那些被拦截到的点。在 spring 中,这些点指的是方法,因为 spring 只支持方法类型的
  连接点。
+ **Pointcut( 切入点):**
  所谓切入点是指我们要对哪些 Joinpoint 进行拦截的定义。
+ **Advice( 通知/ 增强):**
  所谓通知是指拦截到 Joinpoint 之后所要做的事情就是通知。
  通知的类型：前置通知,  后置通知,  异常通知,  最终通知,  环绕通知。
+ **Introduction( 引介):**
  引介是一种特殊的通知在不修改类代码的前提下, Introduction 可以在运行期为类动态地添加一些方
  法或 Field。
+ **Target( 目标对象):**
  代理的目标对象。
+ **Weaving( 织入):**
  是指把增强应用到目标对象来创建新的代理对象的过程。
  spring 采用动态代理织入，而 AspectJ 采用编译期织入和类装载期织入。
+ **Proxy （代理）:**
  一个类被 AOP 织入增强后，就产生一个结果代理类。
+ **Aspect( 切面):**
  是切入点和通知（引介）的结合。

#### pom

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjwraver</artifactId>
    <version>1.8.7</version>
</dependency>
```

#### spring配置(xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 配置srping的Ioc,把service对象配置进来-->
    <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl"></bean>

    <!--spring中基于XML的AOP配置步骤
        1、把通知Bean也交给spring来管理
        2、使用aop:config标签表明开始AOP的配置
        3、使用aop:aspect标签表明配置切面
                id属性：是给切面提供一个唯一标识
                ref属性：是指定通知类bean的Id。
        4、在aop:aspect标签的内部使用对应标签来配置通知的类型
               我们现在示例是让printLog方法在切入点方法执行之前之前：所以是前置通知
               aop:before：表示配置前置通知
                    method属性：用于指定Logger类中哪个方法是前置通知
                    pointcut属性：用于指定切入点表达式，该表达式的含义指的是对业务层中哪些方法增强

            切入点表达式的写法：
                关键字：execution(表达式)
                表达式：
                    访问修饰符  返回值  包名.包名.包名...类名.方法名(参数列表)
                标准的表达式写法：
                    public void com.itheima.service.impl.AccountServiceImpl.saveAccount()
                访问修饰符可以省略
                    void com.itheima.service.impl.AccountServiceImpl.saveAccount()
                返回值可以使用通配符，表示任意返回值
                    * com.itheima.service.impl.AccountServiceImpl.saveAccount()
                包名可以使用通配符，表示任意包。但是有几级包，就需要写几个*.
                    * *.*.*.*.AccountServiceImpl.saveAccount())
                包名可以使用..表示当前包及其子包
                    * *..AccountServiceImpl.saveAccount()
                类名和方法名都可以使用*来实现通配
                    * *..*.*()
                参数列表：
                    可以直接写数据类型：
                        基本类型直接写名称           int
                        引用类型写包名.类名的方式   java.lang.String
                    可以使用通配符表示任意类型，但是必须有参数
                    可以使用..表示有无参数均可，有参数可以是任意类型
                全通配写法：
                    * *..*.*(..)

                实际开发中切入点表达式的通常写法：
                    切到业务层实现类下的所有方法
                        * com.itheima.service.impl.*.*(..)
    -->

    <!-- 配置Logger类 -->
    <bean id="logger" class="com.itheima.utils.Logger"></bean>

    <!--配置AOP-->
    <aop:config>
        <!--配置切面 -->
        <aop:aspect id="logAdvice" ref="logger">
            <!-- 配置通知的类型，并且建立通知方法和切入点方法的关联-->
            <aop:before method="printLog" pointcut="execution(* com.itheima.service.impl.*.*(..))"></aop:before>
        </aop:aspect>
    </aop:config>

</beans>
```

#### 4种常用通知 级环绕通知(around)

```java
/**
 * 用于记录日志的工具类，它里面提供了公共的代码
 */
public class Logger {

    /**
     * 前置通知
     */
    public  void beforePrintLog(){
        System.out.println("前置通知Logger类中的beforePrintLog方法开始记录日志了。。。");
    }

    /**
     * 后置通知
     */
    public  void afterReturningPrintLog(){
        System.out.println("后置通知Logger类中的afterReturningPrintLog方法开始记录日志了。。。");
    }
    /**
     * 异常通知
     */
    public  void afterThrowingPrintLog(){
        System.out.println("异常通知Logger类中的afterThrowingPrintLog方法开始记录日志了。。。");
    }

    /**
     * 最终通知
     */
    public  void afterPrintLog(){
        System.out.println("最终通知Logger类中的afterPrintLog方法开始记录日志了。。。");
    }

    /**
     * 环绕通知
     * 问题：
     *      当我们配置了环绕通知之后，切入点方法没有执行，而通知方法执行了。
     * 分析：
     *      通过对比动态代理中的环绕通知代码，发现动态代理的环绕通知有明确的切入点方法调用，而我们的代码中没有。
     * 解决：
     *      Spring框架为我们提供了一个接口：ProceedingJoinPoint。该接口有一个方法proceed()，此方法就相当于明确调用切入点方法。
     *      该接口可以作为环绕通知的方法参数，在程序执行时，spring框架会为我们提供该接口的实现类供我们使用。
     *
     * spring中的环绕通知：
     *      它是spring框架为我们提供的一种可以在代码中手动控制增强方法何时执行的方式。
     */
    public Object aroundPringLog(ProceedingJoinPoint pjp){
        Object rtValue = null;
        try{
            Object[] args = pjp.getArgs();//得到方法执行所需的参数

            System.out.println("Logger类中的aroundPringLog方法开始记录日志了。。。前置");

            rtValue = pjp.proceed(args);//明确调用业务层方法（切入点方法）

            System.out.println("Logger类中的aroundPringLog方法开始记录日志了。。。后置");

            return rtValue;
        }catch (Throwable t){
            System.out.println("Logger类中的aroundPringLog方法开始记录日志了。。。异常");
            throw new RuntimeException(t);
        }finally {
            System.out.println("Logger类中的aroundPringLog方法开始记录日志了。。。最终");
        }
    }
}
```



```xml
<!--配置AOP-->
<aop:config>
    <!-- 配置切入点表达式 id属性用于指定表达式的唯一标识。expression属性用于指定表达式内容
              此标签写在aop:aspect标签内部只能当前切面使用。
              它还可以写在aop:aspect外面，此时就变成了所有切面可用
          -->
    <aop:pointcut id="pt1" expression="execution(* com.itheima.service.impl.*.*(..))"></aop:pointcut>
    <!--配置切面 -->
    <aop:aspect id="logAdvice" ref="logger">
        <!-- 配置前置通知：在切入点方法执行之前执行
            <aop:before method="beforePrintLog" pointcut-ref="pt1" ></aop:before>-->

        <!-- 配置后置通知：在切入点方法正常执行之后值。它和异常通知永远只能执行一个
            <aop:after-returning method="afterReturningPrintLog" pointcut-ref="pt1"></aop:after-returning>-->

        <!-- 配置异常通知：在切入点方法执行产生异常之后执行。它和后置通知永远只能执行一个
            <aop:after-throwing method="afterThrowingPrintLog" pointcut-ref="pt1"></aop:after-throwing>-->

        <!-- 配置最终通知：无论切入点方法是否正常执行它都会在其后面执行
            <aop:after method="afterPrintLog" pointcut-ref="pt1"></aop:after>-->

        <!-- 配置环绕通知 详细的注释请看Logger类中-->
        <aop:around method="aroundPringLog" pointcut-ref="pt1"></aop:around>
    </aop:aspect>
</aop:config>
```

#### 基于注解的aop配置

```java
// 开启
@EnableAspectJAutoProxy  
public class SpringConfiguration {}
```

```java
/**
 * 用于记录日志的工具类，它里面提供了公共的代码
 */
@Component("logger")
@Aspect//表示当前类是一个切面类
public class Logger {

//    @Pointcut("execution(* com.mp.service.impl.*.*(..))")
//    private void pt1(){}

    @Pointcut("execution(* com.mp.service.impl.AccountServiceImpl.testAop(..))")
    private void pt1(){}

    /**
     * 前置通知
     */
//    @Before("pt1()")
    public  void beforePrintLog(){
        System.out.println("前置通知Logger类中的beforePrintLog方法开始记录日志了。。。");
    }

    /**
     * 后置通知
     */
//    @AfterReturning("pt1()")
    public  void afterReturningPrintLog(){
        System.out.println("后置通知Logger类中的afterReturningPrintLog方法开始记录日志了。。。");
    }
    /**
     * 异常通知
     */
//    @AfterThrowing("pt1()")
    public  void afterThrowingPrintLog(){
        System.out.println("异常通知Logger类中的afterThrowingPrintLog方法开始记录日志了。。。");
    }

    /**
     * 最终通知
     */
//    @After("pt1()")
    public  void afterPrintLog(){
        System.out.println("最终通知Logger类中的afterPrintLog方法开始记录日志了。。。");
    }

    /**
     * 环绕通知
     * 问题：
     *      当我们配置了环绕通知之后，切入点方法没有执行，而通知方法执行了。
     * 分析：
     *      通过对比动态代理中的环绕通知代码，发现动态代理的环绕通知有明确的切入点方法调用，而我们的代码中没有。
     * 解决：
     *      Spring框架为我们提供了一个接口：ProceedingJoinPoint。该接口有一个方法proceed()，此方法就相当于明确调用切入点方法。
     *      该接口可以作为环绕通知的方法参数，在程序执行时，spring框架会为我们提供该接口的实现类供我们使用。
     *
     * spring中的环绕通知：
     *      它是spring框架为我们提供的一种可以在代码中手动控制增强方法何时执行的方式。
     */
    @Around("pt1()")
    public Object aroundPringLog(ProceedingJoinPoint pjp){
        Object rtValue = null;
        try{
            Object[] args = pjp.getArgs();//得到方法执行所需的参数

            System.out.println("Logger类中的aroundPringLog方法开始记录日志了。。。前置");

            rtValue = pjp.proceed(args);//明确调用业务层方法（切入点方法）

            System.out.println("Logger类中的aroundPringLog方法开始记录日志了。。。后置");

            return rtValue;
        }catch (Throwable t){
            System.out.println("Logger类中的aroundPringLog方法开始记录日志了。。。异常");
            throw new RuntimeException(t);
        }finally {
            System.out.println("Logger类中的aroundPringLog方法开始记录日志了。。。最终");
        }
    }
}

```

