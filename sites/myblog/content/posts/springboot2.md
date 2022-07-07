---
title: "Springboot笔记-入门"
date: 2022-06-27T13:52:20+08:00
draft: false
categories: ["java框架"]
tags: ["Springboot","基础"]
---
# Springboot笔记-入门

## 基础

### 整合junit

- @SpringBootTest
  - 类型：测试类注解
  - 位置：测试类定义上方
  - 作用：设置JUnit加载的SpringBoot启动类

- 导入springboot-test包

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
  </dependency>
  ```

- 编写测试类和测试方法，在测试类上加注解@SpringBootTest

  ```java
  @SpringBootTest(classes = Springboot02JunitApplication.class)
  class Springboot02JunitApplicationTests {
  
      @Autowired
      private BookService bookService;
  
      @Test
      public void testBookService(){
          bookService.run();
      }
  
      @Test
      void contextLoads() {
          System.out.println("test...");
      }
  
  }
  ```

- > 如果测试类和springboot启动类不在一个层级，通过设置class属性来指定启动类

### 整合MyBatis

- 核心配置：数据库连接相关信息（连什么？连谁？什么权限）

- 映射配置：SQL映射（XML/注解）

- 导包

  ```xml
  <!-- mybatis  -->
  <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>2.2.2</version>
  </dependency>
  
  <!-- mysql  -->
  <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <scope>runtime</scope>
  </dependency>
  
  <!-- 数据源  -->
  
  ```

- 配置参数

  ```yaml
  spring:
    datasource:
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://localhost:3306/ssm
        username: root
        password: 1234
  ```

- 编写dao接口和mapper

### 整合ssmp

- 导包

  ```xml
  <dependencies>
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
      </dependency>
      <!--        <dependency>-->
      <!--            <groupId>org.mybatis.spring.boot</groupId>-->
      <!--            <artifactId>mybatis-spring-boot-starter</artifactId>-->
      <!--            <version>2.2.2</version>-->
      <!--        </dependency>-->
      <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
          <scope>runtime</scope>
      </dependency>
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-test</artifactId>
          <scope>test</scope>
      </dependency>
  
  
      <!--        添加lombok依赖-->
      <dependency>
          <groupId>org.projectlombok</groupId>
          <artifactId>lombok</artifactId>
      </dependency>
      <!--mybatis-plus-->
      <dependency>
          <groupId>com.baomidou</groupId>
          <artifactId>mybatis-plus-boot-starter</artifactId>
          <version>3.4.2</version>
      </dependency>
      <!--        druid -->
      <dependency>
          <groupId>com.alibaba</groupId>
          <artifactId>druid-spring-boot-starter</artifactId>
          <version>1.2.6</version>
      </dependency>
  
      <!--        热部署-->
      <!--        开启开发者工具-->
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-devtools</artifactId>
          <optional>true</optional>
      </dependency>
  
  </dependencies>
  ```

- yml配置

  ```yml
  spring:
    datasource:
      druid:
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://localhost:3306/ssm
        username: root
        password: 1234
  
  # mybatis-plus
  mybatis-plus:
    configuration:
      log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    global-config:
      db-config:
        id-type: auto
    mapper-locations: classpath:com/ssm/Dao/*.xml
  
  
  #mybatis:
  #  mapper-locations: classpath:com/ssm/Dao/*.xml
  ```

- 实体类开发，使用lombok工具，使用`@Data`则为当前实体类在编译器设置对应的get/set方法，toString().hascode等方法。

## 运维实用

### 项目打包

- **对SpringBoot项目打包（执行Maven构建指令package) `mvn package`**
- **运行项目（执行启动指令）`java –jar springboot.jar`**

## 开发实用

### 热部署

### 配置高级

- **使用@ConfigurationProperties为第三方bean绑定属性**

  - **自定义类**

  ```java
  @Component
  @Data
  @ConfigurationProperties(prefix = "servers")
  public class ServerConfiguration {
      private String ipAddress;
      private int port;
      private int timeout;
  }
  
  ```

  - 为第三方类绑定属性

  ```java
  @Bean
  @ConfigurationProperties(prefix = "datasource")
  public DruidDataSource dataSource(){
      DruidDataSource ds=new DruidDataSource();
      return ds;
  }
  ```

  - yml文件

  ```yml
  servers:
    ipAddress: 123132
    port: 23
    timeout: 45
  
  
  datasource:
    DriverClassName: com.mysql.jdbc.Driver22
  ```

- **@EnableConfigurationProperties注解可以将使用@ConfigurationProperties注解对应的类加入Spring容器**

  ```java
  @SpringBootApplication
  @EnableConfigurationProperties(ServerConfig.class)
  public class DemoApplication {
  }
  
  ----------
      
  //@Component
  @Data
  @ConfigurationProperties(prefix = "servers")
  public class ServerConfig {
  }
  ```

  > @EnableConfigurationProperties与@Component不能同时使用

- **宽松绑定**

  - @ConfigurationProperties绑定属性支持属性名宽松绑定
  - 驼峰模式`ipAddress`
  - 下划线模式`ip_address`
  - 中划线模式`ip-address`
  - 常量模式`IP_ADDRESS`
  - @ConfigurationProperties绑定属性支持属性名宽松绑定

  > 宽松绑定不支持注解@Value引用单个属性的方式

- **常用计量单位**

  ```java
  @Component
  @Data
  @ConfigurationProperties(prefix = "servers")
  public class ServerConfiguration {
      private String ipAddress;
      private int port;
      private int timeout;
  
  // JDK8支持的时间与空间计量单位
      @DurationUnit(ChronoUnit.SECONDS)
      private Duration serverTimeout;
  
      @DataSizeUnit(DataUnit.MEGABYTES)
      private DataSize dataSize;
  }
  ```

- 数据校验

  开启数据校验有助于系统安全性，J2EE规范中JSR303规范定义了一组有关数据校验相关的API

  1. 添加JSR303规范坐标与Hibernate校验框架对应坐标

  ```xml
  <dependency>
      <groupId>javax.validation</groupId>
      <artifactId>validation-api</artifactId>
  </dependency>
  <dependency>
      <groupId>org.hibernate.validator</groupId>
      <artifactId>hibernate-validator</artifactId>
  </dependency>
  ```

  2. 对Bean开启校验功能,设置校验规则

  ```java
  @Component
  @Data
  @ConfigurationProperties(prefix = "servers")
  @Validated
  public class ServerConfiguration {
      @Max(value = 8888,message = "最大不超过8888")
      private int port;
  }
  ```

- yaml语法，字面值表达方式

  ```yaml
  boolean: TRUE  #TRUE,true,True,FALSE,false，False均可
  float: 3.14    #6.8523015e+5  #支持科学计数法
  int: 123       #0b1010_0111_0100_1010_1110    #支持二进制、八进制、十六进制
  null: ~        #使用~表示null
  string: HelloWorld      #字符串可以直接书写
  string2: "Hello World"  #可以使用双引号包裹特殊字符
  date: 2018-02-17        #日期必须使用yyyy-MM-dd格式
  datetime: 2018-02-17T15:02:31+08:00  #时间和日期之间使用T连接，最后使用+代表时区
  ```

  > 注意int类型支持二进制、八进制、十六进制
  >
  > **注意yaml文件中对于数字的定义支持进制书写格式，如需使用字符串请使用引号明确标注**

### 测试



## **原理**

## POM

### spring-boot-starter-web

```xml
<!--        springboot-web -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```



