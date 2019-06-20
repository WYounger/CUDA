### 一、Mybatis基本组件

#### 1.基本组件概要

1. SqlSessionFactoryBuilder

   会依据配置或者代码来生成SqlSessionFacitory，采用的是分布构建的Builder模式

2. SqlSessonFactory

   使用它来生成SqlSession,使用工厂模式

3. SqlSession

   可以直接发送SQL执行返回结果，也可获取Mapper接口

4. SQL Mapper

   Java接口+XML文件/注解。负责发送SQL去执行，并返回结果

#### 2.SqlSessionFactory

Configuration-->SqlSessionFactoryBuilder-->SqlSessionFactory

1. 使用XML构建SqlSessionFactoty

   [XML 映射文件](http://www.mybatis.org/mybatis-3/zh/sqlmap-xml.html#)

   通过xml文件来构建SqlsessionFactory

   ```java
   InputStream inputStream = Resources.getResouceAdStream("xmlFile");
   SqlsessionFactory sqlSessionFactory = 
                   new SqlSessionFatoryBulilder().builder(inputStream);
   ```

2. 使用代码创建(略)

#### 3.映射器

1. 用xml实现

   接口+xml

   [官方文档](http://www.mybatis.org/mybatis-3/zh/sqlmap-xml.html)

2. 注解实现

   [官方文档](http://www.mybatis.org/mybatis-3/zh/sqlmap-xml.html)

##### 4.生命周期

[官方文档](http://www.mybatis.org/mybatis-3/zh/getting-started.html)

SqlSessionFactory:相当于数据库连接池，单例，生命周期在整个应用

SqlSession:相当于Connection对象，要及时关闭

Mapper：生命周期<=SqlSession

**单例构建SqlSessionSession**

```java
public class SqlSessionFactoryUtils {

    // 实例静态化，保证堆中只有一个实例
    private static SqlSessionFactory sqlSessionFactory = null;

    // 构造器私有化，防止外界直接new出对象
    private SqlSessionFactoryUtils() {
    }
  	public static SqlSessionFactory getSqlSessionFactory() {
    // 懒汉式+同步创建单例。双重锁
    	if (sqlSessionFactory == null) {//第一次判定
        //静态方法中开启同步，锁住Class对象
        synchronized (SqlSessionFactoryUtils.class) {
            if (sqlSessionFactory == null){//第二次判定
                    String resource = "mybatis-config.xml";
                    InputStream inputStream=null;
                try {
                        inputStream = Resources.getResourceAsStream(resource);
                        sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
                } catch (IOException e) {
                    e.printStackTrace();
                    return null;
                }
            }
        }
    }
    return sqlSessionFactory;
}
    // 获取SqlSession对象
    public static SqlSession openSqlSession() {
        if (sqlSessionFactory == null) {
            getSqlSessionFactory();
        }
        return sqlSessionFactory.openSession();
    }
}
```

### 二、XML配置

[官方文档](http://www.mybatis.org/mybatis-3/zh/configuration.html)

### 三、映射器

[官方文档](http://www.mybatis.org/mybatis-3/zh/sqlmap-xml.html#)

#### 1.传递多个参数

1. 使用Map接口传递

   Mapper接口中方法参数列表: Map<String,Object>

   xml-Sql中: parameterType="map" #{key1},#{key2}

2. 使用注解**@Param**

   Mapper接口中方法参数列表:`@Param("name1") JavaType name1`,

   ​							`@Param("name2") JavaType name2`,

   ​							...

   xml-Sql中:不需要指定`parameterType`  #{name1},#{name2},...

3. 使用Bean

   Mapper接口中方法参数列表: BeanName object

   xml-Sql中: parameterType="BeanName" #{property1},#{property2}

4. 混合使用

   Mapper接口中方法参数列表:@Param("object1") BeanName1 object1,

   ​						 @Param("object2")  BeanName2 object2,

   ​							...

   xml-Sql中: 不用指定parameterType  #{object1.property1},#{object1.property2}

   ​							        #{object2.property1},#{object2.property2}
5. 总结

     1. 使用map传递参数，可读性较差
     2. @Param传递参数个数不宜太多

#### 2.ResultMap

1. 作用

   **重新映射结果集**

2. 当表column和实体property不同，可使用ResutltMap重新映射

3. 一对一、一对多

    **一对一**(association javaTypy)

   ```xml
   <resultMap  id="resultMap" tepy="Pojo">
     <id property="property_id" column="id"/>
     <result property="property2" column="column2"/>
     <result property="property3" column="column3"/>
     ...
     <association property="propertyn" javaType="Pojo2">
       <id property="property_id" column="id"/>
       <result property="property2" column="column2"/>
       <result property="property3" column="column3"/>
   		  ...
     </association>
   </resultMap> 
   ```

   **一对多**(collection ofType)

   ```xml
   <resultMap  id="resultMap" tepy="Pojo">
     <id property="property_id" column="id"/>
     <result property="property2" column="column2"/>
     <result property="property3" column="column3"/>
     ...
     <collection property="propertyn" ofType="Pojo2">
       <id property="property_id" column="id"/>
       <result property="property2" column="column2"/>
       <result property="property3" column="column3"/>
   		  ...
     </collection>
   </resultMap> 
   ```

注意关联的Bean最好具有`无参构造函数`,谨防出现Bean创建问题

#### 3.主键回填

1.第一种方法

​	**useGeneratedkeys="true" keyProperty="id"**

useGeneratedHeys: 使用JDBC的statement对象中的getGeneratedKeys方法返回主键

keyProperty: 实体中代表主键的属性
2.第二种方法
<insert>
<selectKey keyProperty="id">
	select last_insert_id();
</selectKey>
</insert>

#### 4.动态SQL

[官方文档](http://www.mybatis.org/mybatis-3/zh/dynamic-sql.html)
##### .动态Sql

1. **choose when otherwise**

三者的组合相当于**switch case**语句的功能

```xml
<choose>
	<when test="condition1">
        and ...
    </when>
    <when test="conditionn">
        and ...
    </when>
    <otherwise>
    	and ...
    </otherwise>
</choose>
```

2. **where if**

**where子句功能**，自动去除语句开头多余的and、or

```xml
<where>
	<if test="condition1">
     	and ...
    </if>
    <if test="condition2">
        or ...
    </if>
</where>
```

3. **set**

**set子句功能**,自动去掉多余的"**,**"

```xml
update student
<set>
	  <if test="username != null">username=#{username},</if>
    <if test="password != null">password=#{password},</if>
    <if test="email != null">email=#{email}</if>
</set>
```

4. **foreach**

**遍历集合**,通常用于in子句中

```xml
<foreach item="item"  collection="list" open="(" separator=,"" close=")" index>
	#{item}
</foreach>
```

### 四、Mybatis底层原理

待续
