1.Spring自带数据源

```xml
<bean id="dataSource"   
	  class="org.springframework.jdbc.datasource.DriverManagerDataSource">   
    <property name="driverClassName" value="oracle.jdbc.driver.OracleDriver" />
    <property name="url" value="jdbc:oracle:thin:@172.19.34.6:1521:ORCL" />
    <property name="username" value="orclight" />   
    <property name="password" value="123456" />
</bean>
```

缺点：没有数据库连接池，有请求就会创建一个来连接

2.DBCP数据源

依赖:**commons-dbcp.jar，commons-pool.jar**

```xml

<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"       
        destroy-method="close">       
    <property name="driverClassName" value="oracle.jdbc.driver.OracleDriver" />
    <property name="url" value="jdbc:oracle:thin:@172.19.34.6:1521:ORCL" />
    <property name="username" value="orclight" />   
    <property name="password" value="123456" />      
</bean> 
```

有数据库连接池，推荐使用，细节配置略

3.C3P0数据源

依赖:**c3p0.jar**

```java
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource"       
            destroy-method="close">      
        <property name="driverClass" value=" oracle.jdbc.driver.OracleDriver "/>     
        <property name="jdbcUrl" value="jdbc:oracle:thin:@172.19.34.6:1521:ORCL"/>   
        <property name="user" value="orclight"/>      
        <property name="password" value="123456"/>      
</bean> 
```

有数据库连接池，推荐使用，，配置细节略

4.JNDI数据源

在服务器端配置数据源。略