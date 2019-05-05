##### 1.xml中使用

1. `<context:property-placeholder location="properties1,properties2"/>`

只允许有一个这样的节点**，多个属性文件用**`,`**隔开**.然后属性值(key/value)可以任意在xml文件中使用。

```xml
定义两个propperty文件
properties1.properties
name=Cadillac
properties2.properties
price=666666

引入到applicationContext.xml
<context:property-placeholder location="properties/properties1.properties,
                                            properties/properties2.properties"/>

在xml中使用
<bean id="car" class="model.Car">
  <property name="name" value="${name}"/>
  <property name="price" value="${price}"/>
</bean>
```

2. `<bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">`

```xml
定义两个property文件
properties1.properties
name=Cadillac
properties2.properties
price=666666

引入到xml
<bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="locations">
            <array>
                <value>properties/properties1.properties</value>
                <value>properties/properties2.properties</value>
            </array>
        </property>
    </bean>
在xml中使用
<bean id="car" class="model.Car">
  <property name="name" value="${name}"/>
  <property name="price" value="${price}"/>
</bean>
```

在xml使用时，要注意`value="${name}"`中不能有空格，否则空格会连接到value中，导致数据不一致。

但是properties文件中`name=Cadillac的`等号两端可以有空格，不影响值的读取

##### 2.注解中使用

`<bean id="properties" class="org.springframework.beans.factory.config.PropertiesFactoryBean">`

```xml
定义两个propperty文件
properties1.properties
name=Cadillac
properties2.properties
price=666666

xml都取出来
<bean id="properties" 
      class="org.springframework.beans.factory.config.PropertiesFactoryBean">
        <property name="locations">
            <array>
                <value>properties/properties1.properties</value>
                <value>properties/properties2.properties</value>
            </array>
        </property>
</bean>

注入到bean中
注意前缀=PropertiesFactoryBean的id
@Value("#{properties.name}")
private String name;

@Value("#{properties.price}")
private double price;
//setter，getter略

```



