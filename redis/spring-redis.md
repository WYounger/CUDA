pom.xml

`jedis` `spring-data-redis` 

注意版本问题，可能导致异常

redis.properties

```properties
redis.host = 127.0.0.1
redis.port = 6379
# redis.password = ""
redis.maxIdle = 100
redis.maxTotal = 100
redis.maxWaitMillis = 1000
redis.blockWhenExhausted = true
redis.testOnBorrow = true
redis.timeout = 30000
```



spring-redis.xml `单主机配置`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--引入redis.properties-->
    <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="locations">
            <list>
                <value>classpath:properties/redis.properties</value>
            </list>
        </property>
    </bean>

    <!--配置jedisPool-->
    <bean id="jedisPool" class="redis.clients.jedis.JedisPoolConfig">
        <!--最大连接数-->
        <property name="maxTotal" value="${redis.maxTotal}"/>
        <!--允许最大空闲数-->
        <property name="maxIdle" value="${redis.maxIdle}"/>
        <!--资源池没有可用的连接，调用者是否等待-->
        <property name="blockWhenExhausted" value="${redis.blockWhenExhausted}"/>
        <!--资源池没有可用的连接，调用者最大等待时间-->
        <property name="maxWaitMillis" value="${redis.maxWaitMillis}"/>
        <!--向资源池借用连接时是否做连接有效性检测(ping)-->
        <property name="testOnBorrow" value="${redis.testOnBorrow}"/>
    </bean>

    <!--配置jedisConnectionFactory-->
    <bean id="jedisConnectionFactory" class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory">
        <!--连接远程服务 单主机-->
        <!--主机名host-->
        <property name="hostName" value="${redis.host}"/>
        <!--主机名port-->
        <property name="port" value="${redis.port}"/>
        <!--引入连接池配置-->
        <property name="poolConfig" ref="jedisPool"/>
        <!---->
        <property name="timeout" value="${redis.timeout}"/>
        <property name="usePool" value="true"/>
    </bean>

    <bean id="redisTemplate" class="org.springframework.data.redis.core.RedisTemplate">

        <property name="connectionFactory" ref="jedisConnectionFactory"/>
        <!--key一般用String类型,故一般使用StringRedisSerializer完成序列化和反序列化-->
        <property name="keySerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"/>
        </property>
        <!--value一般有可能为Bean，故一般使用JdkSerializationRedisSerializer完成序列化和反序列化-->
        <property name="valueSerializer">
            <bean class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer"/>
        </property>
        <!--key一般用String类型,故一般使用StringRedisSerializer完成序列化和反序列化-->
        <property name="hashKeySerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"/>
        </property>
        <!--value一般有可能为Bean，故一般使用JdkSerializationRedisSerializer完成序列化和反序列化-->
        <property name="hashValueSerializer">
            <bean class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer"/>
        </property>

        <!--开启事务-->
        <property name="enableTransactionSupport" value="true"/>
    </bean>

</beans>
```

spring-redis.xml `sentinel配置`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--引入redis.properties-->
    <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="locations">
            <list>
                <value>classpath:properties/redis.properties</value>
            </list>
        </property>
    </bean>

    <!--配置jedisPool-->
    <bean id="jedisPool" class="redis.clients.jedis.JedisPoolConfig">
        <!--最大连接数-->
        <property name="maxTotal" value="${redis.maxTotal}"/>
        <!--允许最大空闲数-->
        <property name="maxIdle" value="${redis.maxIdle}"/>
        <!--资源池没有可用的连接，调用者是否等待-->
        <property name="blockWhenExhausted" value="${redis.blockWhenExhausted}"/>
        <!--资源池没有可用的连接，调用者最大等待时间-->
        <property name="maxWaitMillis" value="${redis.maxWaitMillis}"/>
        <!--向资源池借用连接时是否做连接有效性检测(ping)-->
        <property name="testOnBorrow" value="${redis.testOnBorrow}"/>
    </bean>

    <!--配置jedisConnectionFactory-->
    <bean id="jedisConnectionFactory" class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory">
        <!--连接远程服务 单主机-->
        <!--&lt;!&ndash;主机名host&ndash;&gt;-->
        <!--<property name="hostName" value="${redis.host}"/>-->
        <!--&lt;!&ndash;主机名port&ndash;&gt;-->
        <!--<property name="port" value="${redis.port}"/>-->
        <!--&lt;!&ndash;引入连接池配置&ndash;&gt;-->

        <!--连接远程服务  sentinel-->
        <constructor-arg name="sentinelConfig" ref="sentinelConfig"/>

        <property name="poolConfig" ref="jedisPool"/>
        <property name="timeout" value="${redis.timeout}"/>
        <property name="usePool" value="true"/>
    </bean>

    <bean id="redisTemplate" class="org.springframework.data.redis.core.RedisTemplate">

        <property name="connectionFactory" ref="jedisConnectionFactory"/>
        <!--key一般用String类型,故一般使用StringRedisSerializer完成序列化和反序列化-->
        <property name="keySerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"/>
        </property>
        <!--value一般有可能为Bean，故一般使用JdkSerializationRedisSerializer完成序列化和反序列化-->
        <property name="valueSerializer">
            <bean class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer"/>
        </property>
        <!--key一般用String类型,故一般使用StringRedisSerializer完成序列化和反序列化-->
        <property name="hashKeySerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"/>
        </property>
        <!--value一般有可能为Bean，故一般使用JdkSerializationRedisSerializer完成序列化和反序列化-->
        <property name="hashValueSerializer">
            <bean class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer"/>
        </property>

        <!--开启事务-->
        <property name="enableTransactionSupport" value="true"/>
    </bean>

    <!--RedisSentinelConfiguration-->
    <bean id="sentinelConfig" class="org.springframework.data.redis.connection.RedisSentinelConfiguration">
        <property name="master" ref="master"/>
        <property name="sentinels">
            <set>
                <ref bean="RedisNode-26379"/>
                <ref bean="RedisNode-26380"/>
                <ref bean="RedisNode-26381"/>
            </set>
        </property>
    </bean>

    <!--配置sentinels-master-->
    <bean id="master" class="org.springframework.data.redis.connection.RedisNode">
        <property name="name" value="mymaster"/>
    </bean>
    <!--配置sentinel-->
    <bean id="RedisNode-26379" class="org.springframework.data.redis.connection.RedisNode">
        <constructor-arg name="host" value="127.0.0.1"/>
        <constructor-arg name="port" value="26379"/>
    </bean>
    <bean id="RedisNode-26380" class="org.springframework.data.redis.connection.RedisNode">
        <constructor-arg name="host" value="127.0.0.1"/>
        <constructor-arg name="port" value="26380"/>
    </bean>
    <bean id="RedisNode-26381" class="org.springframework.data.redis.connection.RedisNode">
        <constructor-arg name="host" value="127.0.0.1"/>
        <constructor-arg name="port" value="26381"/>
    </bean>

</beans>
```

test.java

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"classpath:spring-redis.xml"})
public class MyTest {
    @Autowired
    private RedisTemplate<Object,Object> redisTemplate;
    @Test
    public void test(){
//        Student student  = new Student(1000,"young",20);//Student要实现Serializable接口
//        redisTemplate.opsForValue().set("student",student);
//        Student studentFromRedis = (Student) redisTemplate.opsForValue().get("student");
//        System.out.println(studentFromRedis);

//        Student student1  = new Student(1000,"young1",21);
//        Student student2  = new Student(1000,"young2",22);
//        Student student3  = new Student(1000,"young3",23);
//        redisTemplate.opsForHash().put("students","student1",student1);
//        redisTemplate.opsForHash().put("students","student2",student2);
//        redisTemplate.opsForHash().put("students","student3",student3);
        Map<Object, Object> students = redisTemplate.opsForHash().entries("students");
        Iterator iterator = students.entrySet().iterator();
        while(iterator.hasNext()){
            Map.Entry<String,Student> entry = (Map.Entry<String, Student>) iterator.next();
            System.out.println(entry.getKey() + ":" + entry.getValue());
        }
    }
}
```

[有关RedisTemplate API 参考]()

**`StringRedisTemplate RedisTemplate`的区别**

```java
public class RedisTemplate<K, V> extends RedisAccessor implements RedisOperations<K, V>, BeanClassLoaderAware {
    private boolean enableTransactionSupport = false;
    private boolean exposeConnection = false;
    private boolean initialized = false;
    private boolean enableDefaultSerializer = true;
    private RedisSerializer<?> defaultSerializer;
    private ClassLoader classLoader;
    private RedisSerializer keySerializer = null;
    private RedisSerializer valueSerializer = null;
    private RedisSerializer hashKeySerializer = null;
    private RedisSerializer hashValueSerializer = null;
    private RedisSerializer<String> stringSerializer = new StringRedisSerializer();
    private ScriptExecutor<K> scriptExecutor;
    private ValueOperations<K, V> valueOps;
    private ListOperations<K, V> listOps;
    private SetOperations<K, V> setOps;
    private ZSetOperations<K, V> zSetOps;
    private GeoOperations<K, V> geoOps;
    private HyperLogLogOperations<K, V> hllOps;
}
public class StringRedisTemplate extends RedisTemplate<String, String> {
    public StringRedisTemplate() {
        RedisSerializer<String> stringSerializer = new StringRedisSerializer();
        this.setKeySerializer(stringSerializer);
        this.setValueSerializer(stringSerializer);
        this.setHashKeySerializer(stringSerializer);
        this.setHashValueSerializer(stringSerializer);
    }
}
```

1. `StringRedisTemplate`继承自`RedisTemplate`
2. 由于`StringRedisTemplate`序列化和反序列已经指定为`StringRedisSerializer`,所以存储的`key-value`都只能是`String`类型。而`RedisTemplate`可以指定序列化器，所以可以配置`JdkSerializationRedisSerializer`来存储`Object`



**浅析`StringRedisSerializer  JdkSerializationRedisSerializer`**

`String`或者`Object`都被序列化为`byte[]`

```java
/**
 * redis序列化接口
 */
public interface RedisSerializer<T> {
    //序列化
    byte[] serialize(T var1) throws SerializationException;
    //反序列化
    T deserialize(byte[] var1) throws SerializationException;
}

//举例两个常用的实现类

/**
 * StringRedisSerializers
 * 仅仅针对String类型序列化
 */
public class StringRedisSerializer implements RedisSerializer<String> {
    //字符编码
    private final Charset charset;

    public StringRedisSerializer() {
        this(Charset.forName("UTF8"));
    }

    public StringRedisSerializer(Charset charset) {
        Assert.notNull(charset, "Charset must not be null!");
        this.charset = charset;
    }
    /**
     * 反序列化
     * new String(bytes[] bytes,Charset charset);
     */
    public String deserialize(byte[] bytes) {
        return bytes == null ? null : new String(bytes, this.charset);
    }
    /**
     * 序列化
     * bytes[] string.getBytes(Charset charset);
     */
    public byte[] serialize(String string) {
        return string == null ? null : string.getBytes(this.charset);
    }
}

/**
 * JdkSerializationRedisSerializer
 * 对Object序列化
 */
public class JdkSerializationRedisSerializer implements RedisSerializer<Object> {
    //...
    public JdkSerializationRedisSerializer() {
        this(new SerializingConverter(), new DeserializingConverter());
    }
    //...
}

/**
 * 序列化转换器
 */
public class SerializingConverter implements Converter<Object, byte[]> {
    private final Serializer<Object> serializer;

    //默认序列化器
    public SerializingConverter() {
        this.serializer = new DefaultSerializer();
    }
    //传入一个序列化器
    public SerializingConverter(Serializer<Object> serializer) {
        Assert.notNull(serializer, "Serializer must not be null");
        this.serializer = serializer;
    }

    /**
     * 转换器
     * @param source 待转化的目标对象
     * @return 返回目标对象的字节数组
     */
    public byte[] convert(Object source) {
        //定义一个字节数组输出流，存放目标对象的字节
        ByteArrayOutputStream byteStream = new ByteArrayOutputStream(1024);

        try {
            this.serializer.serialize(source, byteStream);
            return byteStream.toByteArray();//获取字节数组
        } catch (Throwable var4) {
            throw new SerializationFailedException("Failed to serialize object using " + this.serializer.getClass().getSimpleName(), var4);
        }
    }
}
/**
 * 默认序列化器
 */
public class DefaultSerializer implements Serializer<Object> {
    public DefaultSerializer() {
    }
    /**
     * 序列化
     * @param object 待序列化的对象
     * @param outputStream 目标输出流
     * @throws IOException
     */
    public void serialize(Object object, OutputStream outputStream) throws IOException {
        if (!(object instanceof Serializable)) {
            throw new IllegalArgumentException(this.getClass().getSimpleName() + " requires a Serializable payload but received an object of type [" + object.getClass().getName() + "]");
        } else {
            ObjectOutputStream objectOutputStream = new ObjectOutputStream(outputStream);
            objectOutputStream.writeObject(object);
            objectOutputStream.flush();
        }
    }
}
/**
 * 反序列化
 */
public class DeserializingConverter implements Converter<byte[], Object> {
    private final Deserializer<Object> deserializer;

    public DeserializingConverter() {
        this.deserializer = new DefaultDeserializer();
    }

    public DeserializingConverter(ClassLoader classLoader) {
        this.deserializer = new DefaultDeserializer(classLoader);
    }

    public DeserializingConverter(Deserializer<Object> deserializer) {
        Assert.notNull(deserializer, "Deserializer must not be null");
        this.deserializer = deserializer;
    }

    public Object convert(byte[] source) {
        //字节输入流
        ByteArrayInputStream byteStream = new ByteArrayInputStream(source);

        try {
            return this.deserializer.deserialize(byteStream);
        } catch (Throwable var4) {
            throw new SerializationFailedException("Failed to deserialize payload. Is the byte array a result of corresponding serialization for " + this.deserializer.getClass().getSimpleName() + "?", var4);
        }
    }
}

/**
 * 默认反序列化器
 */
public class DefaultDeserializer implements Deserializer<Object> {
    @Nullable
    private final ClassLoader classLoader;

    public DefaultDeserializer() {
        this.classLoader = null;
    }

    public DefaultDeserializer(@Nullable ClassLoader classLoader) {
        this.classLoader = classLoader;
    }

    public Object deserialize(InputStream inputStream) throws IOException {
        //ConfigurableObjectInputStream比ObjectInputStream更加复杂
        ConfigurableObjectInputStream objectInputStream = new ConfigurableObjectInputStream(inputStream, this.classLoader);

        try {
            //读取对象
            return objectInputStream.readObject();
        } catch (ClassNotFoundException var4) {
            throw new NestedIOException("Failed to deserialize object type", var4);
        }
    }
}
```

