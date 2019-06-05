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

