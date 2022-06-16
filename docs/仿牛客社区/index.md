# 仿牛客社区


# 仿牛客社区

## springboot集成第三方

### 集成reids

- 导包 spring-boot-starter-data-redis

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-redis</artifactId>
  </dependency>
  ```

- 配置Redis

  - 配置数据源

    ```yaml
    spring:
    	redis:
            database: 11
            host: localhost
            port: 6379
    ```

  - 编写配置类，构造RedisTemplate

    ```java
    @Configuration
    public class RedisConfig {
    
        @Bean
        public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
            RedisTemplate<String, Object> template = new RedisTemplate<>();
            template.setConnectionFactory(factory);
    
            // 设置key的序列化方式
            template.setKeySerializer(RedisSerializer.string());
            // 设置value的序列化方式
            template.setValueSerializer(RedisSerializer.json());
            // 设置hash的key的序列化方式
            template.setHashKeySerializer(RedisSerializer.string());
            // 设置hash的value的序列化方式
            template.setHashValueSerializer(RedisSerializer.json());
    
            template.afterPropertiesSet();
            return template;
        }
    
    }
    ```

- 访问Redis

  

## 功能实现

### 动实现分页

- 分页sql语句

  ```sql
  select * from discuss_post  order by type desc, create_time desc limit #{offset}, #{limit}
  ```

- 定义分页信息对象，page类

  ```java
  /**
   * 封装分页相关的信息.
   */
  public class Page {
  
      // 当前页码
      private int current = 1;
      // 显示上限
      private int limit = 10;
      // 数据总数(用于计算总页数)
      private int rows;
      // 查询路径(用于复用分页链接)
      private String path;
  
      public int getCurrent() {
          return current;
      }
  
      public void setCurrent(int current) {
          if (current >= 1) {
              this.current = current;
          }
      }
  
      public int getLimit() {
          return limit;
      }
  
      public void setLimit(int limit) {
          if (limit >= 1 && limit <= 100) {
              this.limit = limit;
          }
      }
  
      public int getRows() {
          return rows;
      }
  
      public void setRows(int rows) {
          if (rows >= 0) {
              this.rows = rows;
          }
      }
  
      public String getPath() {
          return path;
      }
  
      public void setPath(String path) {
          this.path = path;
      }
  
      /**
       * 获取当前页的起始行
       *
       * @return
       */
      public int getOffset() {
          // current * limit - limit
          return (current - 1) * limit;
      }
  
      /**
       * 获取总页数
       *
       * @return
       */
      public int getTotal() {
          // rows / limit [+1]
          if (rows % limit == 0) {
              return rows / limit;
          } else {
              return rows / limit + 1;
          }
      }
  
      /**
       * 获取起始页码
       *
       * @return
       */
      public int getFrom() {
          int from = current - 2;
          return from < 1 ? 1 : from;
      }
  
      /**
       * 获取结束页码
       *
       * @return
       */
      public int getTo() {
          int to = current + 2;
          int total = getTotal();
          return to > total ? total : to;
      }
  
  }
  ```

  

- controller中接收分页信息并调用语句分页查找

### 发送邮件

- **邮箱设置**

  启用客户端smtp服务，可以室友163邮箱、qq邮箱等等

- **Spring boot 集成javamail**

  - 导入jar包

    ```xml
    <!--        java mail-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-mail</artifactId>
    </dependency>
    ```

  - 配置邮箱参数

    ```yaml
    spring:
        mail:
            host: smtp.163.com
            port: 465
            username: xxxx@163.com
            password: xxxxx
            protocol: smtps # 指定协议
            properties:
                mail.smtp.ssl.enable: true
    ```

  - 代码实现

    ```java
    @Component
    public class MailClient {
    
        private static final Logger logger = LoggerFactory.getLogger(MailClient.class);
    
        @Autowired
        private JavaMailSender mailSender;
    
        @Value("${spring.mail.username}")
        private String from;
    
        public void sendMail(String to, String subject, String context) {
            try {
                MimeMessage mimeMessage = mailSender.createMimeMessage();
                MimeMessageHelper mimeMessageHelper = new MimeMessageHelper(mimeMessage);
                mimeMessageHelper.setFrom(from);
                mimeMessageHelper.setTo(to);
                mimeMessageHelper.setSubject(subject);
                mimeMessageHelper.setText(context, true);
                mailSender.send(mimeMessage);
            } catch (MessagingException e) {
                logger.error("发送邮件失败：" + e.getMessage());
            }
        }
    }
    ```

  - 测试，使用thymeleaf模板发送html的邮件

    ```java
    @Test
    public void testSendHTMLMail(){
        Context context=new Context();
        context.setVariable("username","userxx");
        String content = templateEngine.process("/mail/mailexample", context);
        mailClient.sendMail("user@qq.com","suject",content);
    }
    ```

## 经验

### ajxa请求经过springmvc拦截器后response重定向问题

    - **问题**：ajxa请求经过springmvc拦截器后response重定向后，浏览器会再次请求重定向的页面，但是不会刷新显示
    
    - **解决**：可以设置ajax请求拦截后将重定向页面url作为json数据返回，在ajax回调处理函数中进行页面跳转
    
    - **代码**
    
      - java
      
      ```java
        @Override
          public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
              pass
            			{
                      //如果request.getHeader("X-Requested-With") 返回的是"XMLHttpRequest"说明就是ajax请求，需要特殊处理 否则直接重定向就可以了
                      if("XMLHttpRequest".equals(request.getHeader("X-Requested-With"))){
                          //自定义json数据返回，包含重定向的地址
                          JSONObject res=new JSONObject();
                          res.put("redirect",request.getContextPath() + "/login");
                          response.setContentType("application/json;charset=utf-8");
                          response.getWriter().write(res.toJSONString());
                      }else{
                          // 如果不是ajax 就正常重定向
                          response.sendRedirect(request.getContextPath() + "/login");
                      }
                      return false;
        
              return true;
          }
      
      ```
      
      - js
      
      ```javascript
          $.post(
              reuqest_url,
              function (data) {
                  data1 = JSON.parse(JSON.stringify(data));
                  // console.log(data);
                  if (data1.redirect != null) {
                      window.location.href = CONTEXT_PATH + data1.redirect;
                  } 
              }
          );
      ```


​      

