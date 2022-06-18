# kafka的入门使用

# kafka的入门使用

## kafka入门

- 启动zookeeper

  ```shell
  bin\windows\zookeeper-server-start.bat config\zookeeper.properties
  ```

- 启动kafka

  ```she
  bin\windows\kafka-server-start.bat config\server.properties
  ```

- 创建主题

  ```shell
  kafka-topics.bat --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic test
  ```

## springboot整合kafka

- 引入依赖，spring-kafka

  ```xml
  <dependency>
      <groupId>org.springframework.kafka</groupId>
      <artifactId>spring-kafka</artifactId>
  </dependency>
  ```

- 配置kafka，配置server、consumer

  ```yaml
  spring:
      kafka:
          bootstrap-servers: localhost:9092
          consumer:
            group-id: test-consumer-group
            enable-auto-commit: true
            auto-commit-interval: 3000
  ```

- 访问kafka

  - 生产者使用```  kafkaTemplate.send(topic,content);```发送消息
  - 消费者使用@KafkaListener(topics = {"test"})注解监听某些主题，消息封装成ConsumerRecord类型```handleMessage(ConsumerRecord consumerRecord)```

  ```java
  @SpringBootTest(classes = NowcoderCommunityApplication.class)
  public class KafkaTest {
  
      @Autowired
      private KafkaProducer kafkaProducer;
  
  
      @Test
      public void testKafka(){
          kafkaProducer.sendMessage("test","这是第一条kafkaProducer");
          kafkaProducer.sendMessage("test","hah");
          kafkaProducer.sendMessage("test","你好");
          try {
              Thread.sleep(1000*5);
          } catch (InterruptedException e) {
              e.printStackTrace();
          }
  
      }
  
  }
  
  //生产者
  @Component
  class KafkaProducer{
  
      @Autowired
      private KafkaTemplate kafkaTemplate;
  
      public void sendMessage(String topic,String content){
          kafkaTemplate.send(topic,content);
      }
  
  }
  
  //消费者
  @Component
  class KafkaConsumer{
  
      @Autowired
      private KafkaTemplate kafkaTemplate;
  
      //监听某些主题
      @KafkaListener(topics = {"test"})
      public void handleMessage(ConsumerRecord consumerRecord){
          System.out.println(consumerRecord.value());
      }
  
  }
  ```

  

  

