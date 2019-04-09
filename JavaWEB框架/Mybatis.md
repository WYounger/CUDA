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

##### [官方文档](http://www.mybatis.org/mybatis-3/zh/getting-started.html)

SqlSessionFactory:相当于数据库连接池，单例，生命周期在整个应用

SqlSession:相当于Connection对象，要及时关闭

Mapper：生命周期<=SqlSession

**单例子构建SqlSessionSession**

```java
public class SqlSessionFactoryUtils {
  
	//实例静态化，保证堆中只有一个实例
	private static SqlSessionFactory sqlSessionFactory = null;
	//构造器私有化，防止外界直接new出对象
	private SqlSessionFactoryUtils() {
	}
	
	public static SqlSessionFactory getSqlSessionFactory() {
		//懒汉式+同步创建单例
		if (sqlSessionFactory != null) {
				return sqlSessionFactory;
		}
    //静态方法中开启同步，锁住Class
    synchronized (SqlSessionFactoryUtils.class) {
			String resource = "mybatis-config.xml";
			InputStream inputStream;
			try {
				inputStream = Resources.getResourceAsStream(resource);
				sqlSessionFactory = new       
                   SqlSessionFactoryBuilder().build(inputStream);
			} catch (IOException e) {
				e.printStackTrace();
				return null;
			}
			return sqlSessionFactory;
		}
	}
  //获取SqlSession对象
  public static SqlSession openSqlSession() {
		if (sqlSessionFactory == null) {
			getSqlSessionFactory();
		}
		return sqlSessionFactory.openSession();
	}
}
```

