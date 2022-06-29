# SpringBoot 

## 1、Xml 和 JavaConfig

### 1.1、pom.xml 配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.1</version>
        <relativePath/>
    </parent>

    <groupId>com.cy</groupId>
    <artifactId>002-springboot-start</artifactId>
    <version>1.0.0</version>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>

        <!-- web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- mybatis -->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.2.2</version>
        </dependency>

        <!-- mysql驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>

        <!-- 测试 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <!-- 测试 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <scope>test</scope>
        </dependency>

    </dependencies>

    <build>
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                </plugin>
            </plugins>
        </pluginManagement>
    </build>
</project>

```

#### 1.1.1、parent

```xml
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.4.1</version>
	<relativePath/>
</parent>

<!-- parent 最大的作用是，在内部指定了常用工具的版本号。使得我们在使用这些库的时候，可以不用指定版本号。 -->
```

#### 1.1.2、starter

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- starter 最大的作用是，内部集成了很多相关的工具，我们不需要挨个集成这些工具，只需要依赖xxx.starter即可 -->
```

#### 1.1.3、exclusion

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
	<exclusions>
		<exclusion>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-tomcat</artifactId>
		</exclusion>
	</exclusions>
</dependency>

<!-- exclusion 排除依赖，上述例子中导入了 spring-boot-starter-web，但是不想使用其内部的 tomcat 
		 就可思议使用 exclusion 标签进行排除依赖 -->
```



### 1.2、什么是 JavaConfig

JavaConfig：使用 Java 类作为 XML 配置文件的替代，是配置 Spring 容器的纯 Java 的方式。在这个 Java 类中可以创建

Java 对象，把对象放入 Spring 容器中（注入到容器）。

优点：

1. 可以使用面向对象的方式，一个配置类可以继承配置类，可以重新方法
2. 避免繁琐的 XML 配置

使用两个注解：

1. @Configuration：放到一个类的上面，表示这个类是作为配置文件使用的
2. @Bean：声明对象，把对象注入到容器中。

举例：

1. 创建 java 类，并且声明 @Configuration 注解，和用@Bean 声明要注入的对象

```java
@Configuration
public class SpringConfig {

	@Bean
	public Student createStudent() {
		Student student = new Student();
		student.setName("李四");
		student.setAge(12);
		student.setSex("男");
		return student;
	}

}
```

2. 测试

```java
/**
* 使用 JavaConfig
*/
@Test
public void test02() {
	ApplicationContext applicationContext =
	new AnnotationConfigApplicationContext(SpringConfig.class);
	Student student = (Student) applicationContext.getBean("createStudent");
	System.out.println(student);
}
```



### 1.3、@ImporResource

@ImportResource 作用导入其他的 XML 配置文件

举例：

1. 在 resources 目录下创建 applicationContext.xml 配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd ">

    <bean id="myCat" class="com.cy.vo.Cat">
        <property name="name" value="白猫"/>
        <property name="age" value="3"/>
    </bean>

</beans>
```

2. 配置类中引入 Xml 配置文件

```java
@Configuration
@ImportResource(value = "classpath:applicationContext.xml")
public class SpringConfig {

}
```

3. 测试

```java
/**
 * 使用 JavaConfig,导入 XML 中的bean
 */
@Test
 public void test03() {
		ApplicationContext applicationContext =
                new AnnotationConfigApplicationContext(SpringConfig.class);
		Cat cat = (Cat) applicationContext.getBean("myCat");
		System.out.println(cat);
}
```



### 1.4、@PropertyResource

@PropertyResource 读取 properties 属性配置文件。使用属性配置文件可以实现外部化配置，在程序代码之外提供数据。

举例：

1. 在 resources 目录下，创建 properties 文件，使用 k = v 的格式提供数据

```properties
tiger.name=东北虎
tiger.age=3
```

2. 在 PropertyResource 指定  properties 文件的位置，和对应的 Java Bean所在的包

```java
@Configuration
@PropertySource(value = "classpath:config.properties")
@ComponentScan(basePackages = "com.cy.vo")
public class SpringConfig {


}

```

3. 使用@Value（ value = "${key}"）

```java
package com.cy.vo;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component("tiger")
public class Tiger {

    @Value("${tiger.name}")
    private String name;
    @Value("${tiger.age}")
    private int age;

    @Override
    public String toString() {
        return "Tiger{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

}
```

4. 测试

```java
/**
* 得到 config.properties 里面的值
*/
@Test
public void test04() {
	ApplicationContext applicationContext =
	new AnnotationConfigApplicationContext(SpringConfig.class);
	Tiger tiger = (Tiger) applicationContext.getBean("tiger");
	System.out.println(tiger);
}
```



## 2、SpringBoot 入门

### 2.1、引导类

```Java
@SpringBootApplication
public class App{
	public static void main(String[] args) {
		SpringApplication.run(App.class, args);
	}
}
```

- SpringBoot的引导类是Boot工程的执行入口，运行main方法就可以启动项目
- SpringBoot工程运行后初始化容器，扫描引导类所在包加载bean

### 2.2、相关注解

```java
@SpringBootApplication
是复合注解，由下面几个注解组成
1. @SpringBootConfiguration
   允许在 Spring 上下文中注册额外的 bean 或导入其他配置类
2. @EnableAutoConfiguration
   启用 SpringBoot 的自动配置机制。帮助SpringBoot应用将所有符合条件的@Configuration配置都加载到当前SpringBoot，
   并创建对应配置类的Bean，并把该Bean实体交给IoC容器进行管理。
3. @ComponentScan
   扫描被@Component (@Service,@Controller)注解的 bean，注解默认会扫描该类所在的包下所有的类。
```

### 2.3、配置文件

配置文件名称：application 扩展名有 application.properties 和 application.yml

1. application.properties

```properties
# 设置端口号
server.port=8082
# 设置访问应用上下文路径，contextPath
server.servlet.context-path=/myboot
```

2. application.yml

```yaml
server:
  port: 8083
  servlet:
    context-path: /myboot2
```



### 2.4、多环境配置

#### 2.4.1、基本使用

resources目录下创建

- application.yml
- application-dev.yml
- application-pro.yml

三个配置文件，其中 application-dev.yml 和 application-pro.yml 里面做具体配置的内容，application.yml 指定使用某一个配置文件。

举例：

application.yml：

```yaml
spring:
  profiles:
    active: pro
```

application-dev.yml

```yaml
server:
  port: 8081
```

application-pro.yml

```yaml
server:
  port: 8082
```

#### 2.4.2、分组

分组可以认为是颗粒度是更加精细的配置文件。把一个配置文件，根据模块进一步拆分。由 application.yml 去指定使用哪一个分组。

举例：

把上面的 application-dev.yml 进行拆分  application-devDB.yml 和 application-devMyBatisPlus.yml ，在主配置中指定分组和使用分组

```yaml
spring:
  profiles:
    active: dev
    group:
      "dev": devDB,devMyBatisPlus
      "pro": proDB,proMyBatisPlus
```

这样运行的时候，会加载  application-dev.yml 、application-devDB.yml、 application-devMyBatisPlus.yml 三个配置文件

#### 2.4.3、配置文件与maven联动

修改maven里面的配置，指定环境

1. pom.xml 中 增加配置

```xml
    <profiles>
        <profile>
            <id>env_dev</id>
            <properties>
                <profile.active>dev</profile.active>
            </properties>
        </profile>
        <profile>
            <id>env_pro</id>
            <properties>
                <profile.active>pro</profile.active>
            </properties>
          <!-- 指定使用pro环境 -->
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
        </profile>
    </profiles>
```

2. 在 application.yml 引用变量

```yaml
spring:
  profiles:
    active: @profile.active@
    group:
      "dev": devDB,devMyBatisPlus
      "pro": proDB,proMyBatisPlus
```

备注：在maven中切换环境之后，idea运行不起作用。可以 compile 一下在试试。 

### 2.5、SpringBoot 自定义配置

#### 2.5.1、@Value 注解

1. 在yml文件里面配置

```yaml
server:
  port: 8083
  servlet:
    context-path: /pro

persion:
  name: 张三
  age: 18

dog:
  name: 小白
```

2. 在 Controller 里面取值

```java
@Controller
public class BootController {

    @Value("${server.port}")
    private Integer port;

    @Value("${persion.age}")
    private Integer age;

    @Value("${persion.name}")
    private String persionName;

    @RequestMapping("/hello")
    @ResponseBody
    public String hello() {
        return "port----->" + port + "-----age--->" + age + "--persionName-->" + persionName;
    }

}
```

#### 2.5.2、@ConfigurationProperties 注解

1. 在yml文件里面配置

```yaml
dog:
  name: 小白
```

2. 创建 bean 对象

```java
@Component
@ConfigurationProperties(prefix = "dog")
public class Dog {

    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Dog{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

}
```

3. 测试

```java
@Controller
public class BootController {

    @Resource
    private Dog dog;

    @RequestMapping("/dog")
    @ResponseBody
    public String dog() {
        return "dog----->" + dog;
    }

}

```



### 2.6、CommandLineRunner 接口

配置类实现 CommandLineRunner 接口，并重写run() 方法。容器启动之后会自动执行 run() 方法，有需要在容器启动之后执行一些内容。比如读取配置文件，数据库连接之类的。可以吧逻辑写在该方法里面

```java
@SpringBootApplication
public class App implements CommandLineRunner {

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
    }
}
```



## 3、SpringBoot  和 Web 组件

### 3.1、拦截器

拦截器是SpringMVC中一种对象，能拦截对Controller的请求。

拦截器框架中有系统的拦截器，还可以自定义拦截器。实现对请求预先处理。

1. 创建拦截器类，实现 HandlerInterceptor 接口，重写 preHandle() 方法

```java
public class LoginInterceptor implements HandlerInterceptor {

    /**
     * @param request
     * @param response
     * @param handler 被拦截器的控制器对象
     * @return
     *  true: 请求能被Controller处理
     *  false:请求被截断
     * @throws Exception
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("执行了preHandle");
        return true;
    }

}
```

2. 创建配置类，把拦截器注册到配置类里面

```java
@Configuration
public class MyAppConfig implements WebMvcConfigurer {

    /**
     * 把拦截器对象，注入到容器中
     */
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // 拦截器对象
        LoginInterceptor loginInterceptor = new LoginInterceptor();
        // 拦截的地址
        String[] path = {"/boot/**"};
        // 不拦截的地址
        String[] excludePath = {"/boot/hello"};
        registry.addInterceptor(loginInterceptor).addPathPatterns(path).excludePathPatterns(excludePath);
    }

}
```



### 3.2、过滤器

1. 创建过滤器类，实现 Filter 接口，重写 doFilter() 方法

```jaav
public class MyFilter implements Filter {

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("doFilter");
    }

}
```

2. 创建配置类，把过滤器注册到配置类里面

```java
@Configuration
public class MyAppConfig implements WebMvcConfigurer {
  
    /**
     * 把过滤器对象，注入到容器中
     */
    @Bean
    public FilterRegistrationBean filterRegistrationBean() {
        FilterRegistrationBean bean = new FilterRegistrationBean();
        MyFilter myFilter = new MyFilter();
        bean.setFilter(myFilter);
        bean.addUrlPatterns("/boot/dog");
        return bean;
    }

}

```



## 4、SpringBoot 操作 MySQL

使用 MyBatis 框架操作数据库， 在SpringBoot  中集成 MyBatis

### 4.1 使用步骤

1. pom.xml 中增加 mybatis 和 mysql 驱动依赖

```xml
 <!-- mybatis -->
<dependency>
	<groupId>org.mybatis.spring.boot</groupId>
	<artifactId>mybatis-spring-boot-starter</artifactId>
	<version>2.2.2</version>
</dependency>

<!-- mysql驱动 -->
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>8.0.29</version>
</dependency>
```

2. application.yml 中配置 mybatis 和 mysql连接信息

```yaml
mybatis:
	# 项目中 mapper 文件路径
	mapper-locations: classpath*:mapper/*Mapper.xml
  # 开启 mysql 的日志
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl

#  mysql 链接信息
spring:
  datasource:
    username: root
    password: 12345678
    url: jdbc:mysql://127.0.0.1:3306/demo?serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=utf-8&useSSL=false&allowPublicKeyRetrieval=true
    driver-class-name: com.mysql.cj.jdbc.Driver
```

3. App 启动类 增加 mapper 扫描注解

```java
@SpringBootApplication
@MapperScan("com.cy.mapper")
public class App {

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }

}

```

4. 创建实体类对象

```java
package com.cy.bean;

public class Student {

    private int id;
    private String name;
    private int gradeid;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getGradeid() {
        return gradeid;
    }

    public void setGradeid(int gradeid) {
        this.gradeid = gradeid;
    }

    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", gradeid=" + gradeid +
                '}';
    }

}
```

5. 增加 mapper 包，创建 mapper 接口

```java
package com.cy.mapper;

import com.cy.bean.Student;
import org.springframework.stereotype.Repository;

@Repository
public interface StudentMapper {

    Student selectById(Integer id);

}

```

6. 在 resource 目录下 创建 mapper 目录并创建对应的 StudentMapper.xml 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.cy.mapper.StudentMapper">

    <select id="selectById" resultType="com.cy.bean.Student">
        select * from student where id=#{id}
    </select>

</mapper>
```

7. 增加 service 包，创建 StudentService 接口

```java
package com.cy.service;

import com.cy.bean.Student;

public interface StudentService {

    Student queryStudent(int id);

}
```

8. 在 service 包中增加 impl 子包，并且实现 StudentService 接口

```java
package com.cy.service.impl;

import com.cy.bean.Student;
import com.cy.mapper.StudentMapper;
import com.cy.service.StudentService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class StudentServiceImpl implements StudentService {

    @Autowired
    private StudentMapper studentMapper;

    @Override
    public Student queryStudent(int id) {
        Student student = studentMapper.selectById(id);
        return student;
    }

}
```

9. 在 Controller 中调用 Service 

```java
@Controller
public class StudentController {

    @Autowired
    private StudentService studentService;

    @RequestMapping("/student/query")
    @ResponseBody
    public String queryStudent(int id) {
        Student student = studentService.queryStudent(id);
        return student.toString();
    }

}
```



### 4.2、事务

在需要增加事务的方法上增加注解 @Transactional 即可

```java
@Transactional
@Override
public void addStudent(Student student) {
	int rows = studentMapper.addStudent(student);
	int a = 10 / 0;
}
```

注意点:

1. 只有Public方法才能开启事务
2. 检查类有没有被Spring管理（方法被标注了@Transactional，但是类没有注解，没有被Spring管理也不会生效）
3. 检查是否在事务中新开启了一个线程（因为spring实现事务的原理是通过ThreadLocal把数据库连接绑定到当前线程中，新开启一个线程获取到的连接就不是同一个了。）



## 5、RESTful 风格接口

### 5.1、RESTful 接口特点

1. 每一个URI代表1种资源
2. 客户端使用GET、POST、PUT、DELETE4个表示操作方式的动词对服务端资源进行操作：GET用来获取资源，POST用来新建资源（也可以用于更新资源），PUT用来更新资源，DELETE用来删除资源
3. 通过操作资源的表现形式来操作资源
4. 资源的表现形式是XML或者HTML
5. 客户端与服务端之间的交互在请求之间是无状态的，从客户端到服务端的每个请求都必须包含理解请求所必需的信息
5. 隐藏资源的访问行为，无法通过地址得知对资源是何种操作
5. 书写简化

### 5.2、REST风格和传统风格对比

- 传统风格资源描述形式

http://localhost/user/getById?id=1

http://localhost/user/saveUser

- REST风格描述形式

http://localhost/user/1

http://localhost/user

### 5.3、注解

@RestController：复合注解，是 @Controller 和 @ResponseBody 的组合。在类上面使用 @RestController，表示当前类所有的方法都加入了 @ResponseBody

@PathVariable：从 url 中获取数据

@GetMapping：支持 get 请求方式，等同于@RequestMapping(method = RequestMethod.GET)

@PostMapping：支持 post 请求方式，等同于@RequestMapping(method = RequestMethod.POST)

@PutMapping：支持 put 请求方式，等同于@RequestMapping(method = RequestMethod.PUT)

@DeleteMapping：支持 delete 请求方式，等同于@RequestMapping(method = RequestMethod.DELETE)

@RequestBody：用于接收 json 数据

@RequestParam：用于接收 url 地址传参或者表单传参

```java
// 访问下面 URL
http://192.168.72.32:8082/dev/student/2

// 得到上面 URL 的 2 
@GetMapping("/student/{stuId}")
@ResponseBody
public String queryStudentById(@PathVariable("stuId") int id) {
		Student student = studentService.queryStudent(id);
		return student.toString();
}
```



## 6、SpringBoot 整合第三方技术

### 6.1、整合 JUnit

5. pom.xml 文件中增加 spring-boot-starter-test

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-test</artifactId>
	<scope>test</scope>
</dependency>
```

2. 创建需要测试的类，并把该类添加到容器里面

```java
@Component
public class StringUtil {

    public boolean testIsEmpty(String str) {
        return str.isEmpty();
    }

}
```

3. 在 test/java/包名 下创建Test.java 文件，与SpringBoot启动类的包名层级一样。运行test()方法即可

```java
@SpringBootTest
public class AppTest {

    @Autowired
    StringUtil stringUtil;

    @Test
    void test() {
        boolean result = stringUtil.testIsEmpty("haha");
        System.out.println(result);
    }

}

```



## 7、SpringBoot 打包与运行

### 7.1、打包

1. 在idea右侧Maven插件中运行 package
2. 打包成功后，会在项目根目录生成 target 文件夹，在该文件夹下生成 项目名称.jar



### 7.2、运行

1. pom.xml配置插件

```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```

2. 运行jar

```she
java -jar /Users/cuiyue/Desktop/BookDemo-1.0.0.jar
```



## 8、日志

### 8.1、日志打印

使用 lombok 提供的日志系统打印，增加注解 @Slf4j，用 log 对象打印即可

```java
@Slf4j
@RestController
@RequestMapping("/books")
public class BookController {

    @Autowired
    BookService bookService;

    @GetMapping()
    public R getAll() {
        log.debug("debug");
        log.info("info");
        log.warn("warn");
        log.error("error");
        List<Book> all = bookService.getAll();
        return new R(true, all);
    }

}
```

### 8.2、日志配置（分组、显示级别、文件存储）

pom.xml 中配置：

```yaml
logging:
	# 创建了book 组
  group:
    book: com.cy.book
  level:
  	# 项目日志级级别为 info 级别
    root: info
    # book组日志级别为 debug 级别
    book: debug
  file:
    # 日志文件名称：server.log
    name: server.log
  logback:
    rollingpolicy:
      # 日志文件名称：每个日志文件的大小
      max-file-size: 3KB
      # 日志文件名称：每个日志文件的名称
      #	server.2022-06-29.0.log
      #	server.2022-06-29.1.log
      #	server.2022-06-29.2.log
      #	server.2022-06-29.3.log
      file-name-pattern: server.%d{yyyy-MM-dd}.%i.log
```

