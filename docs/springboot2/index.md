# Springboot笔记

# Springboot笔记

## 基础

### 集成junit

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

## 运维实用

## 开发实用

## 原理


