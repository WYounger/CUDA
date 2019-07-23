##### 1、整体图

**JDBC Driver: **Java类的集合，它实现了JDBC接口。不同数据库提供商都需要提自己的JDBC Driver。它可以让你连接到特定的数据库

**Connection**:一旦JDBC Driver载入和初始化后，可以通过JDBC API  和 JDBC Driver来获取一个Connection,它可以然你连接到数据库。对数据库的所有操作都是经过Connection来完成的。当然，一个数据库可以有多个Connection实例

**Statement**:操作数据库的语句

**ResultSet**:数据库查询结果集

![](http://tutorials.jenkov.com/images/java-jdbc/jdbc.png)

![](http://tutorials.jenkov.com/images/java-jdbc/overview.png)

##### 2、Connection

1. 载入JDBC Driver

```java
 Class.forName("driverClassName");
 java6可以不用手动载入JDBC Driver;
没有必要在每一次获取Connection之前载入driver，只需在第一次获取之前加载一次即可
```

1. 获取连接

​     调用java.sql.DriverManager中getConnection()来获取

```java
//总共有三种方法
//第一种...
//第二种 常用
String url      = "jdbc:h2:~/test";   //database specific url.
String user     = "sa";
String password = "";
Connection connection = DriverManager.getConnection(url, user, password);
//第三种 不推荐，因为属性名应数据库不同而可能不同
String url      = "jdbc:h2:~/test";   //database specific url.
Properties properties = new Properties( );
properties.put( "user", "sa" );
properties.put( "password", "" );
Connection connection = DriverManager.getConnection(url, properties);
```

1. 关闭Connection

   由于Connection在打开状态会占用系统资源，所以要在它不需要的时候将其关闭

   1. 直接关闭

      ```java
      connection.close();
      ```

   2. try(){...}

      JDK7以后自动关闭机制

      ```java
      try(Connection connection =
          DriverManager.getConnection(url, user, password)) {
          //use the JDBC Connection inhere
      }
      ```

2. setAutoCommit()

   ```java
   //自动提交 每条更新语句执行完后立即提交，而无需等待事务结束commit
   //如果不处于自动提交模式，则必须通过调用Connection commit()方法显式提交每个数据库事务。
   connection.setAutoCommit(true);//connection默认状态
   //后面transation细谈
   connection.setAutoCommit(false);
   ```

##### 3、查询数据库

==**statement.executeQuery(sql);**==

```java
Statement statement = null;

try{
  	//用Connection来创建SQL语句
    statement = connection.createStatement();
    ResultSet result = null;
    try{
      	//SQL语句
        String sql = "select * from people";
      	//用Statemente执行SQL语句，并将结果返回给ResultSet
        ResultSet result = statement.executeQuery(sql);

      	//遍历ResultSet
        while(result.next()) {//如果还有下一行，result.next()返回true
          	//result指向一行row
          	//result.getXXX("columnName);获取当前行列名为colnmnName的值
            String name = result.getString("name");
            long   age  = result.getLong("age");
            System.out.println(name);
            System.out.println(age);
        }
    } finally {
        if(result != null) result.close();//注意关闭result
    }
} finally {
    if(statement != null) statement.close();//注意关闭statement
}

				//sty-with-resource
        try(Connection connection = ConnectionUtil.getConnection()) {

            PreparedStatement ps = connection.prepareStatement("select * from `student` where id = ?");
            ps.setInt(1,10000);
            ResultSet rs = ps.executeQuery();
            ResultSetMetaData md = rs.getMetaData();
            while(rs.next()){
                for(int i = 1; i <= md.getColumnCount(); i++){
                    //resultSet获取值，index从1开始
                    System.out.print(rs.getObject(i)+" ");
                }
                System.out.println();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
```

##### 4、更新数据库

包括：update、delete 

==**statement.executeUpdate(sql);**==

```java
//update操作
Statement statement = connection.createStatement();
String    sql       = "update people set name='John' where id=123";
int rowsAffected    = statement.executeUpdate(sql);
//delete操作
Statement statement = connection.createStatement();
String    sql       = "delete from people where id=123";
int rowsAffected    = statement.executeUpdate(sql);
```

##### 5、PreparedStatement

1. 创建PreparedStatement  ==**connection.prepareStatement(sql);**==

```java
String sql = "select * from people where id=?";

PreparedStatement preparedStatement =
        connection.prepareStatement(sql);
```

1. 插入参数到PreparedStatement  ==**preparedStatement.setXXX(index, value);**==

```java
String sql = "select * from people where firstname=? and lastname=?";

PreparedStatement preparedStatement =
        connection.prepareStatement(sql);

preparedStatement.setString(1, "John");
preparedStatement.setString(2, "Smith");
```

1. 执行PreparedStatement  ==**preparedStatement.executeQuery()/executeUpdate()**==
2. 重用PreparedStatement

```java
String sql = "update people set firstname=? , lastname=? where id=?";

PreparedStatement preparedStatement =
        connection.prepareStatement(sql);
//设置查询参数
preparedStatement.setString(1, "Gary");
preparedStatement.setString(2, "Larson");
preparedStatement.setLong  (3, 123);
//执行更新，返回受影响的记录条数
int rowsAffected = preparedStatement.executeUpdate();
//重新给preparedStatement传递参数
preparedStatement.setString(1, "Stan");
preparedStatement.setString(2, "Lee");
preparedStatement.setLong  (3, 456);
//再次执行preparedStatement
int rowsAffected = preparedStatement.executeUpdate();
```

**不要使用Statement,而要使用性能更高的PreparedStatement**

##### 6、批量更新

==**preparedStatement.addBatch();**==

==**preparedStatement.executeBatch();**==

```java
String sql = "update people set firstname=? , lastname=? where id=?";

PreparedStatement preparedStatement = null;
try{
    preparedStatement =
            connection.prepareStatement(sql);

    preparedStatement.setString(1, "Gary");
    preparedStatement.setString(2, "Larson");
    preparedStatement.setLong  (3, 123);
		//添加到batch
    preparedStatement.addBatch();

    preparedStatement.setString(1, "Stan");
    preparedStatement.setString(2, "Lee");
    preparedStatement.setLong  (3, 456);
		//添加到batch
    preparedStatement.addBatch();

  	//批量执行
    int[] affectedRecords = preparedStatement.executeBatch();

}finally {
    if(preparedStatement != null) {
        preparedStatement.close();
    }
}
```

注意各个语句是**分开执行**的，所以可能会执行成功，有的可能执行失败，这就破坏了原子性。可以将他们放在一个事务中执行，从而保证原子性

##### 7、Transaction

简单介绍一下事务：一系列数据库操作的集合，他们要么全做，要么全不做，即满足**原子性**。

1. 开启一个事务

   ```java
   connection.setAutoCommit(false);//设置手动提交
   ```

2. 回滚

   开启事务后，后面的查询和更新就都属于一个事务中，一旦有一个操作执行失败，就要回滚事务

   ```java
   connection.rollback();
   ```

3. 提交

​          事务中所有操作都执行成功了，就可提交事务了。一旦提交事务，所有所有操纵执行的结果都保存到数据库中，永久不会改变，即满足**永久性**

```java
connection.commit();
```

**模板**

```java
Connection connection = ...
try{
    connection.setAutoCommit(false);
    // create and execute statements etc...
    connection.commit();
} catch(Exception e) {
    connection.rollback();//发生异常就回滚
} finally {
    if(connection != null) {
        connection.close();
    }
}
```

