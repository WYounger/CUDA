##### 基础组件

`RabbitAdmin`: 对exchange,queue,binding的管理

SpringAMQP声明: 对exchange.queue,binding使用注解形式定义

`RabbitTemplate`

`SimpleMessageListenerContainer`

`MessageListenerAdapter`

`MessageConverter`

依赖

```xml
<dependency>
  <groupId>com.rabbitmq</groupId>
  <artifactId>amqp-client</artifactId>
  <version>5.9.0</version>
</dependency>
<dependency>
  <groupId>org.springframework.amqp</groupId>
  <artifactId>spring-rabbit</artifactId>
  <version>2.2.6.RELEASE</version>
</dependency>

<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-databind</artifactId>
  <version>2.11.0</version>
</dependency>
```



###### RabbitAdmin

```java
/**
     * 创建连接工厂
     *
     * @return
     */
@Bean
public ConnectionFactory connectionFactory() {
  CachingConnectionFactory cachingConnectionFactory = new CachingConnectionFactory();
  cachingConnectionFactory.setHost("127.0.0.1");
  cachingConnectionFactory.setPort(5672);
  cachingConnectionFactory.setUsername("guest");
  cachingConnectionFactory.setPassword("guest");
  cachingConnectionFactory.setVirtualHost("/");
  return cachingConnectionFactory;
}

@Bean
public RabbitAdmin amqpAdmin(ConnectionFactory connectionFactory) {
  RabbitAdmin rabbitAdmin = new RabbitAdmin(connectionFactory);
  rabbitAdmin.setAutoStartup(true);
  return rabbitAdmin;
}

//定义交换机
rabbitAdmin.declareExchange(new DirectExchange("test.direct", false, false));
//定义队列
rabbitAdmin.declareQueue(new Queue("test.queue", false));
//绑定
rabbitAdmin.declareBinding(new Binding("test.queue",
                                       Binding.DestinationType.QUEUE,
                                       "test.direct",
                                       "test-direct", new HashMap<String, Object>(0)));

```

1. `autoStartup`:必须设置为`true`,否则Spring容器不会加载`RabbtAdmin`类
2. `RabbitAdmin`底层就是从Spring容器中获取Exchange，Binding，RoutintKey以及Queue的`@Bean`声明，并定义的这些对象
3. 使用`rabbtiTemplate.execute()`执行对应的声明，修改删除一系列RabbitMQ的基础操作

###### SpringAMQP声明

使用`@Bean`的定义queue,exchange,binding。

```java
/**
     * 定义一个队列
     *
     * @return
     */
@Bean
public Queue queue1() {
  Queue queue = new Queue("queue1", false);
  return queue;
}

/**
     * 定义一个exchange
     *
     * @return
     */
@Bean
public Exchange topicExchage() {
  return new TopicExchange("topic_exchange", false, false);
}

/**
     * 定义绑定
     *
     * @return
     */
@Bean
public Binding binding() {
  //        Binding binding = new Binding("queue1",
  ////                Binding.DestinationType.QUEUE,
  ////                "topic_exchange",
  ////                "topic.#",
  ////                null);


  return BindingBuilder.bind(queue1()).to(topicExchage()).with("topic.#").and(null);
}
```

###### RabbitTemplate

1. 发送消息的关键类
2. 包含发送消息，回调监听消息接口ConfirmCallback,返回值确定接口ReturnCallback

````java
MessageProperties messageProperties = new MessageProperties();
messageProperties.setContentType("text/plain");
messageProperties.getHeaders().put("desc","描述");
Message message = new Message("hello rabbit mq".getBytes(),messageProperties);
//通过send发送
rabbitTemplate.send("topic_exchange","topic.test",message);
//通过convertAndSend发送
rabbitTemplate.convertAndSend("topic_exchange","topic.test","通过convertAndSend发送过来的消息");
````

###### SimpleMessageListenerContainer

消息监听

```java
@Bean
public SimpleMessageListenerContainer simpleMessageListenerContainer(ConnectionFactory connectionFactory) {
  SimpleMessageListenerContainer listenerContainer = new SimpleMessageListenerContainer(connectionFactory);
  //设置监听的队列
  listenerContainer.setQueues(queue1(), queue2(), queue3());
  //设置当前消费者数量
  listenerContainer.setConcurrentConsumers(1);
  //设置最大消费者数量
  listenerContainer.setMaxConcurrentConsumers(3);
  //自动签收
  listenerContainer.setAcknowledgeMode(AcknowledgeMode.AUTO);
  //消费端标签策略
  listenerContainer.setConsumerTagStrategy(new ConsumerTagStrategy() {
    public String createConsumerTag(String s) {
      return s + "_" + UUID.randomUUID().toString();
    }
  });

  //定义消费者
  listenerContainer.setMessageListener(new MessageListener() {
    public void onMessage(Message message) {
      System.out.println("消费: " + new String(message.getBody()));
    }
  });
  return listenerContainer;
}
```

###### MessageListenerAdapter

使用默认方法`handleMessage`处理消息

```java
MessageListenerAdapter messageListenerAdapter = new MessageListenerAdapter(new MessageDelegate());
listenerContainer.setMessageListener(messageListenerAdapter);

public class MessageDelegate {    
    public void handleMessage(byte[] messageBody) {
        System.out.println("消费: " + new String(messageBody));
    }
}

```

自定义方法处理消息

```java
MessageListenerAdapter messageListenerAdapter = new MessageListenerAdapter(new MessageDelegate());
//定义自定义消费方法
messageListenerAdapter.setDefaultListenerMethod("consumerMessage");
listenerContainer.setMessageListener(messageListenerAdapter);

public class MessageDelegate {
    
    public void consumerMessage(byte[] messageBody) {
        System.out.println("消费: " + new String(messageBody));
    }
}
```

简单消息转换器

```java
MessageListenerAdapter messageListenerAdapter = new MessageListenerAdapter(new MessageDelegate());
messageListenerAdapter.setDefaultListenerMethod("consumerMessage");
//设置消息转换器
messageListenerAdapter.setMessageConverter(new TextMessageConverter());
listenerContainer.setMessageListener(messageListenerAdapter);

public class TextMessageConverter implements MessageConverter {

    //Java对象转换为消息
    public Message toMessage(Object o, MessageProperties messageProperties) throws MessageConversionException {
        return new Message(o.toString().getBytes(), messageProperties);
    }

    //消息转换为Java对象
    public Object fromMessage(Message message) throws MessageConversionException {
        String contentType = message.getMessageProperties().getContentType();
        if (contentType != null && contentType.contains("text")) {
            return new String(message.getBody());
        }
        return message.getBody();
    }
}

public class MessageDelegate {
		//使用String接收
    public void consumerMessage(String mesage) {
        System.out.println("消费: " + new String(mesage));
    }
}
```

可以将队列消息指定处理方法

```java
MessageListenerAdapter messageListenerAdapter = new MessageListenerAdapter(new MessageDelegate());
messageListenerAdapter.setDefaultListenerMethod("consumerMessage");
messageListenerAdapter.setMessageConverter(new TextMessageConverter());
Map<String,String> queueOrTagToMathodName = new HashMap<String, String>(2);
//queue1的消息让method1处理
queueOrTagToMathodName.put("queue1","method1");
//queue2的消息让method2处理
queueOrTagToMathodName.put("queue2","method2");
messageListenerAdapter.setQueueOrTagToMethodName(queueOrTagToMathodName);
listenerContainer.setMessageListener(messageListenerAdapter);

public class MessageDelegate {

    public void consumerMessage(String mesage) {
        System.out.println("消费: " + mesage);
    }

    public void method1(String mesage) {
        System.out.println("method1-消费: " + mesage);
    }

    public void method2(String mesage) {
        System.out.println("method2-消费: " + mesage);
    }
}
```

###### MessageConverter

`Jackson2JsonMessageConverter`: 将数据转换为`json`

```
MessageListenerAdapter adapter = new MessageListenerAdapter(new MessageDelegate());
adapter.setDefaultListenerMethod("consumerMessage");
//设置json转换器
Jackson2JsonMessageConverter jsonMessageConverter = new Jackson2JsonMessageConverter();
adapter.setMessageConverter(jsonMessageConverter);

listenerContainer.setMessageListener(adapter);


public class MessageDelegate {
		//使用map接收
    public void consumerMessage(Map messageBody) {
        System.out.println("map-消费: "+ messageBody);
    }
}

Student student = new Student("younger",23);
MessageProperties messageProperties = new MessageProperties();
//contentType一定要设置为application/json
messageProperties.setContentType("application/json");
Message message = new Message(new ObjectMapper().writeValueAsString(student).getBytes(),messageProperties);
rabbitTemplate.send("topic_exchange","topic.test",message);
```

`Jackson2JsonMessageConverter` + `JavaTypeMapper`

```java
MessageListenerAdapter adapter = new MessageListenerAdapter(new MessageDelegate());
adapter.setDefaultListenerMethod("consumerMessage");

Jackson2JsonMessageConverter jsonMessageConverter = new Jackson2JsonMessageConverter();
//定义javaMapper
DefaultJackson2JavaTypeMapper javaTypeMapper = new DefaultJackson2JavaTypeMapper();
Map<String,Class<?>> idClassMapping = new HashMap<>(2);
//定义类型映射
idClassMapping.put("student", Student.class);
idClassMapping.put("car", Car.class);
javaTypeMapper.setIdClassMapping(idClassMapping);
//设置JavaTypeMapper
jsonMessageConverter.setJavaTypeMapper(javaTypeMapper);

adapter.setMessageConverter(jsonMessageConverter);
listenerContainer.setMessageListener(adapter);


public class MessageDelegate {
    //接收Car类型的body
    public void consumerMessage(Car car) {
        System.out.println("car-消费: "+ car);
    }
    //接收Student类型的body
    public void consumerMessage(Student student) {
        System.out.println("student-消费: "+ student);
    }
}

test(rabbitTemplate,Student.builder().name("younger").age(23).build(),"student" );
test(rabbitTemplate, Car.builder().name("benz").price(1000000D).build(),"car" );
void test(RabbitTemplate rabbitTemplate,Object o,String typeId) throws JsonProcessingException {
  MessageProperties messageProperties = new MessageProperties();
  //必须设置为application/json
  messageProperties.setContentType("application/json");
  //声明实体的类型 与消费者定义的idClassMapping对应
  messageProperties.getHeaders().put("__TypeId__",typeId);
  Message message = new Message(new ObjectMapper().writeValueAsString(o).getBytes(),messageProperties);
  rabbitTemplate.send("topic_exchange","topic.test",message);
}
```

`ContentTypeDelegatingMessageConverter`

```java
//监听转换器
MessageListenerAdapter adapter = new MessageListenerAdapter(new MessageDelegate());
adapter.setDefaultListenerMethod("consumerMessage");
//全局转换器，可以包含多个转换器。依据消息的contentType来调用指定的转换器
ContentTypeDelegatingMessageConverter converter = new ContentTypeDelegatingMessageConverter();
//文本转换器
TextMessageConverter textConverter = new TextMessageConverter();
//将contentType为text,html/text,xml/text,text/plaind消息交给textConverter
converter.addDelegate("text", textConverter);
converter.addDelegate("html/text", textConverter);
converter.addDelegate("xml/text", textConverter);
converter.addDelegate("text/plain", textConverter);
//json转换器
Jackson2JsonMessageConverter jsonConverter = new Jackson2JsonMessageConverter();
converter.addDelegate("json", jsonConverter);
converter.addDelegate("application/json", jsonConverter);
//文件流转换器
FileMessageConverter fileConverter = new FileMessageConverter();
converter.addDelegate("application/pdf", fileConverter);
converter.addDelegate("img/jpg", fileConverter);
converter.addDelegate("img/png", fileConverter);
//设置MessageConverter
adapter.setMessageConverter(converter);
listenerContainer.setMessageListener(adapter);

public class FileMessageConverter implements MessageConverter {
    @Override
    public Message toMessage(Object o, MessageProperties messageProperties) throws MessageConversionException {
        throw new MessageConversionException("转换错误");
    }

    @Override
    public Object fromMessage(Message message) throws MessageConversionException {
        System.out.println("file message converter");
        String extName = (String) message.getMessageProperties().getHeaders().get("extName");

        byte[] body = message.getBody();

        String fileName = UUID.randomUUID().toString();
        String filePath = "C:\\Users\\Young\\Desktop\\" + fileName + "." + extName;
        File file = new File(filePath);
        try {
            Files.copy(new ByteArrayInputStream(body),file.toPath());
        } catch (IOException e) {
            e.printStackTrace();
        }
        return file;
    }
}

public class MessageDelegate {

    public void consumerMessage(String mesage) {
        System.out.println("消费: " + mesage);
    }
  
    public void method1(String mesage) {
        System.out.println("method1-消费: " + mesage);
    }

    public void method2(String mesage) {
        System.out.println("method2-消费: " + mesage);
    }

    public void consumerMessage(Map messageBody) {
        System.out.println("map-消费: " + messageBody);
    }

    public void consumerMessage(Car car) {
        System.out.println("car-消费: " + car);
    }
    //，对应jsonConverter+javaTypeMapper
    public void consumerMessage(Student student) {
        System.out.println("student-消费: " + student);
    }

  	//使用File接口，对应FileMessageConverter
    public void consumerMessage(File file) {
        System.out.println("file-消费: " + file.getName());
    }
}

MessageProperties messageProperties = new MessageProperties();
//设置文件扩展名
messageProperties.getHeaders().put("extName", "jpg");
//设置contentType
messageProperties.setContentType("img/jpg");
byte[] body = Files.readAllBytes(Paths.get("C:\\Users\\Young\\Desktop", "2020-09-05_182942.jpg"));
Message message = new Message(body, messageProperties);
rabbitTemplate.send("topic_exchange", "topic.test", message);
```

