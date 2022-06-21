# Spring Boot 

## 1、第一章 Xml 和 JavaConfig

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



## 2、 第二章 Spring Boot 入门

### 2.1、相关注解

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

### 2.2、配置文件

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



### 2.3、多环境配置

有开发环境，测试环境，上线环境

每个环境有不同的配置信息，例如端口，上下文件，数据库url，用户名，密码等等

使用多环境配置文件，可以方便的切换不同的配置。

使用方式：创建多个匹配文件，名称规则：application-环境名称.yml

举例：

在resources目录下创建

- application.yml 并指向 application-pro.yml

```yaml
spring:
  profiles:
    active: pro
```

- application-dev.yml

```yaml
server:
  port: 8083
  servlet:
    context-path: /dev
```

- application-test.yml

```yaml
server:
  port: 8083
  servlet:
    context-path: /test
```

- application-pro.yml

```yaml
server:
  port: 8083
  servlet:
    context-path: /pro
```



### 2.4、Spring Boot 自定义配置

#### 2.4.1、@Value 注解

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

#### 2.4.2、@ConfigurationProperties 注解

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



### 2.5、CommandLineRunner 接口

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



## 3、第三章 Spring Boot  和 Web 组件

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



## 4、第四章 Spring Boot 操作 MySQL

使用 MyBatis 框架操作数据库， 在Spring Boot  中集成 MyBatis

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



### 4.2 事务

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



## 5、第五章 RESTful 风格接口

### 5.1 RESTful 接口特点

1. 每一个URI代表1种资源
2. 客户端使用GET、POST、PUT、DELETE4个表示操作方式的动词对服务端资源进行操作：GET用来获取资源，POST用来新建资源（也可以用于更新资源），PUT用来更新资源，DELETE用来删除资源
3. 通过操作资源的表现形式来操作资源
4. 资源的表现形式是XML或者HTML
5. 客户端与服务端之间的交互在请求之间是无状态的，从客户端到服务端的每个请求都必须包含理解请求所必需的信息



### 5.2 注解

@RestController：复合注解，是 @Controller 和 @ResponseBody 的组合。在类上面使用 @RestController，表示当前类所有的方法都加入了 @ResponseBody

@PathVariable：从 url 中获取数据

@GetMapping：支持 get 请求方式，等同于@RequestMapping(method = RequestMethod.GET)

@PostMapping：支持 post 请求方式，等同于@RequestMapping(method = RequestMethod.POST)

@PutMapping：支持 put 请求方式，等同于@RequestMapping(method = RequestMethod.PUT)

@DeleteMapping：支持 delete 请求方式，等同于@RequestMapping(method = RequestMethod.DELETE)

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



## 6、第六章 Spring Boot 集成 Redis

### 6.1、Redis 介绍

Redis：是一个 NoSQL 数据库，常用作为缓存使用（cache）。Redis 是一个中间件，是一个独立的服务器。Java 中著名的客户端有：Jedis，lettuce，Redisson。在 Spring Boot 中 用 RedisTemplate（StringRedisTemplate），处理和 Redis 交互。

Redis：的数据类型有:

1. string（字符串）
2. hash（哈希）
3. list（列表）
4. set（集合）
5. zset（sorted set：有序集合）

