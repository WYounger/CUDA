1.Mapper接口中参数是Bean

​	在sql语句中直接`#{Bean的属性}`

2.@Param

​	当有多个参数，但又不想封装成Bean，可以使用@Param注解

```java
(@Param("param1") Type param1,..,@Param("paramn") Type paramn)

//如果,当多个参数均为Object对象
//可以使用parami.addr取值
```

3.返回List<Type>

​	当返回值是对象集合，resultType="Type",而不是resultType="list"

4.参数Java基本类型

​	当参数是Java基本类型，最好将其改为包装类型，便于请求参数为空值时传null,然后改null值传递到sql中，便于用于动态SQL中判空。

5.模糊查询

```xml
<select id="selectStudents" resultType="com.younger.springbootstart.model.Student">
       select * from student
       <where>
         <!--对于字符串的判空,保险使用null和''判断-->
           <if test="name != null and name != ''">
              name like concat("%",#{name},"%")
           </if>
           <if test="age != null and age != 0">
              and age = #{age}
           </if>
       </where>
    </select>
```
