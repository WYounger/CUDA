## 1、在web.xml文件下，配置中央调度器DispatcherServlet

#### 1）在默认条件下[在web-inf下]

##### A、默认，不添加参数,springmvc.xml的文件名必须在[servlet-name]的基础上加

​          **-servlet**

![1](https://github.com/WYounger/computer-science/blob/master/images/1.png)

##### B、修改DispatcherServlet的初始化参数，去掉 添加部分-**servlet**

![2](https://github.com/WYounger/computer-science/blob/master/images/2.png)

web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
  <!--配置DispatcherServlet
      如果当前不修改springmvc配置文件默认位置，
      那么springmvc会去web-inf下面找一个[servlet-name]-servlet.xml的文件
  -->
  <servlet>
    <servlet-name>myspringmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>namespace</param-name>
      <param-value>springmvc</param-value>
    </init-param>
  </servlet>
  <servlet-mapping>
    <servlet-name>myspringmvc</servlet-name>
    <url-pattern>*.do</url-pattern>
  </servlet-mapping>
</web-app>
```

springmvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!--配置一个视图解析器
    常用内部资源视图解析器
    -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/jsp/" />
        <property name="suffix" value=".jsp"/>
    </bean>
    <!--注册Controller--    <bean id="/helloController.do" class="com.young.controller.HelloController"></bean>
</beans>
```

#### 2）在maven下配置springmvc.xml

> 在maven项目中,springmvc.xml放在resources目录下

![3](https://github.com/WYounger/computer-science/blob/master/images/1.png)

web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
 <!--
  在类路径下寻找springmvc.xml
 -->
  <servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
  </servlet>
  <servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>*.do</url-pattern>
  </servlet-mapping>
</web-app>
```

