**jar包**

`log4j-core`

`log4k-api`

`slf4j-api`[spring下需要引入]

log4j2.xml 文件名固定 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
    <Appenders>
        <Console name="console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>

    <Loggers>
        <!--过滤掉spring和mybatis的一些无用的DEBUG信息-->
        <logger name="org.springframework" level="INFO"></logger>
        <logger name="org.mybatis" level="INFO"></logger>
        <root level="debug">
            <appender-ref ref="console"/>
        </root>
    </Loggers>
</Configuration>
```

在mybatis的全局配置文件中引入

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <!--固定写法-->
        <setting name="logImpl" value="LOG4J2"></setting>
    </settings>
</configuration>
```

**参考资料**

[[聊一聊log4j2配置文件log4j2.xml]](https://www.cnblogs.com/hafiz/p/6170702.html)