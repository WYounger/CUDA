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