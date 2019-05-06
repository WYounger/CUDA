```mysql
create table test(
`id` int auto_increment primary key,
`date` date,
`dateTime` datetime
);

/*
datetime       8 bytes   YYYY-MM-DD HH:MM:SS   
date           3 bytes   YYYY-MM-DD 
2019-04-15 19:39:17
2019-04-15
*/

insert into test(`date`,`dateTime`)
 values('2019-04-15','2019-04-15 19:39:17');
```

model

```
public class DateTimeTest {
    private String date;//对应Mysql中date,存和取一致
    private String dateTime;//对应Mysql中dateTime,存和取一致
    private int id;
    //constructor,getter and setter
}
```

mapper

```java
public interface DateTimeMapper {
    List<DateTimeTest> getDateTime();
    void insertDateTime(DateTimeTest timeTest);
}
```



mapper.xml

```xml
<mapper namespace="dao.DateTimeMapper">
    <select id="getDateTime" resultType="model.DateTimeTest">
        select `date`,`dateTime` from test
    </select>

    <insert id="insertDateTime" parameterType="model.DateTimeTest">
        insert into test(`date`,`dateTime`)
        values(#{date},#{dateTime})
        <selectKey resultType="int" keyProperty="id" order="AFTER">
            select last_insert_id();
        </selectKey>
    </insert>
<!--mysql中的date和datetime类型数据直接与Java中String对应-->
</mapper>
```

test

```java
//格式对应Mysql中date的格式
SimpleDateFormat formatDate = new SimpleDateFormat("yyyy-MM-dd");
String date = formatDate.format(new Date());

//格式对应Mysql中datetime的格式
SimpleDateFormat formatDateTime = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
String dateTime = formatDateTime.format(new Date());


DateTimeTest dateTimeTest = new DateTimeTest(date,dateTime);
mapper.insertDateTime(dateTimeTest);
				
System.out.println("--------------------------"+dateTimeTest.getId());

List<DateTimeTest> list = mapper.getDateTime();
for (DateTimeTest e :
         list) {
      String date2 = e.getDate();
      String dateTime2 = e.getDateTime();
      System.out.println(date2);
			System.out.println(dateTime2);
}
```

总结：

date_String_Java <==>date_Mysql

dateTime_string_Java <==>datetime_Mysql