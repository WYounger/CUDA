##### 1.spring基础

SPring事务基于三个主要的接口

`TransactionDefinition`  `PlatformTransactionManager` `TransactionStatus`

接口定义如下

```java
public interface PlatformTransactionManager {
   TransactionStatus getTransaction(TransactionDefinition definition);
   throws TransactionException;
   
   void commit(TransactionStatus status) throws TransactionException;
   void rollback(TransactionStatus status) throws TransactionException;
}

public interface TransactionDefinition {
   int getPropagationBehavior();
   int getIsolationLevel();
   String getName();
   int getTimeout();
   boolean isReadOnly();
}

public interface TransactionStatus extends SavepointManager {
   boolean isNewTransaction();
   boolean hasSavepoint();
   void setRollbackOnly();
   boolean isRollbackOnly();
   boolean isCompleted();
}
```

[参考链接](https://www.ibm.com/developerworks/cn/education/opensource/os-cn-spring-trans/index.html)

`PlatformTransactionManager`三大主要实现类

`DataSourceTransactionManager`：适用使用JDBC和iBatis进行数据持久化操作的情况

`HibernateTransactionManager`:适用于使用Hibernate进行数据库持久化操作的情况

`JpaTransactionManager`:适用于使用JPA进行数据库持久化操作的情况



##### 2.结合Mybatis

Mybatis使用事务，要保证`SqlSessionFactoryBean`引用的数据源与`DataSourceTransactionManager`引用的数据源一致即可。

spring中事务相关jar包:`spring-jdbc`

mybatis.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">

    <!--引入db属性文件-->
    <context:property-placeholder location="properties/db.properties"/>

    <!--配置数据源-->
    <bean id="datasource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
        <!--连接池其他细节略-->
    </bean>

    <!--配置Mybatis的SqlSessionFactoryBean-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--注入数据源-->
        <property name="dataSource" ref="datasource"/>
      
        <!--配置mapper文件-->
        <property name="mapperLocations" value="classpath:mapper/*.xml"/>
    </bean>

    <!--自动扫面dao，注册到Spring容器中-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
        <property name="basePackage" value="dao"/>
    </bean>
</beans>
```

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd
	http://www.springframework.org/schema/context
	http://www.springframework.org/schema/context/spring-context.xsd
	http://www.springframework.org/schema/tx
	http://www.springframework.org/schema/tx/spring-tx.xsd">

  	<!--引入数据源-->
    <import resource="classpath:mybaties.xml"/>

    <!--给Transaction配置数据源-->
    <bean id = "transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="datasource"/>
    </bean>

    <!--开启事务注解-->
    <tx:annotation-driven/>
</beans>

```

**事务回滚规则**

1. RuntimeException回滚，CheckedExceptiont提交

**@Transactional注解**

`propagation`：默认为`Propagation.REQUIRED`,当前有事务，则加入该事务，若没有事务，则创建一个新的事务

`siolation`：默认为`Isolation.DEFAULT`,和连接的数据库的隔离级别一致

1. 该注解一般用在方法上，且只能是public方法上
2. 只有来自外部方法的调用才会被AOP代理捕获，类内部方法调用本类内部的其他方法斌不会引起事务，即使被调用的方法使用@Transaction修饰



参考资料

[spring的@Transactional注解详细用法](https://www.cnblogs.com/yepei/p/4716112.html)

[Spring事务传播行为详解](https://segmentfault.com/a/1190000013341344)