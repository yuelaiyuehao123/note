# Spring Boot 

## 1.1、第一章 Xml 和 JavaConfig

### 1.1.1、pom.xml 配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.cy</groupId>
    <artifactId>001-springboot-pre</artifactId>
    <version>1.0.0</version>

    <dependencies>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.3.20</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>

    </dependencies>

    <build>
        <plugins>
            <!-- 编译插件 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <!-- 插件的版本 -->
                <version>3.8.1</version>
                <!-- 编译级别 -->
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <!-- 编码格式 -->
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```



### 1.1.2、什么是 JavaConfig

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



### 1.1.3、@ImporResource

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



### 1.1.4、@PropertyResource

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



## 1.2、 第二章 Spring Boot 入门

### 1.2.1、 相关注解

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

### 1.2.2、配置文件

配置文件名称：application 扩展名有 application.properties 和 application.yml

1. application.properties

```properties
# 设置端口号
server.port=8082
# 设置访问应用上下文路径，contextPath
server.servlet.context-path=/myboot
```

2. application.yml

```yml
server:
  port: 8083
  servlet:
    context-path: /myboot2
```



