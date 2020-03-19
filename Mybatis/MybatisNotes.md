##### 1.Mapper接口中参数是Bean

​	在sql语句中直接`#{Bean的属性}`

##### 2.@Param

​	当有多个参数，但又不想封装成Bean，可以使用@Param注解

```java
(@Param("param1") Type param1,..,@Param("paramn") Type paramn)

//如果,当多个参数均为Object对象
//可以使用parami.addr取值
```

##### 3.返回List<Type>

​	当返回值是对象集合，resultType="Type",而不是resultType="list"

##### 4.参数Java基本类型

​	当参数是Java基本类型，最好将其改为包装类型，便于请求参数为空值时传null,然后改null值传递到sql中，便于用于动态SQL中判空。

##### 5.模糊查询

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

##### 6.受影响行行数

通常需要依据更新数据库后受影响的行数来判断操作是否成功.

其实在mytais返回首受影响的行数的值非常简单，只需将dao接口函数返回值类型为int，在sql语句执行后，受影响的行数自动返回。

需要注意的一点是 :这里返回受影响的行数只是match的数据库中记录数，并不是真正将记录修改的条数。

如果真的需要返回真实受影响的行数，需要在数据库连接`url`上添加`useAffectedRows=true`

##### 7.树结构查询

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Cate {
    private Integer id;
    private String cateName;
    private Integer parentId;
    private List<Cate> cates;
}
```

```xml
<resultMap id="cateMap" type="com.young.mybatisplustest.model.Cate">
  <id column="id" property="id"/>
  <result column="cate_name" property="cateName"/>
  <result column="parent_id" property="parentId"/>
  <collection property="cates" ofType="com.young.mybatisplustest.model.Cate"
              select="selectSonByParentId" column="{parentId = id}"/>
</resultMap>

<select id="selectCatById" resultMap="cateMap">
  select * from cates where id = #{id}
</select>

<select id="selectSonByParentId" parameterType="java.util.Map" resultMap="cateMap">
  select * from cates where parent_id = #{parentId}
</select>
```

重点分析collection中的内容

- select: 子查询
- column: 定义父查询给子查询的变量

该方法可以很简单实现树形结构查询,但是效率很低. 因为Mybatis在执行查询时,对每一个子查询都发起一次查询,可想而知,当数据结构深度很大,查询的次数就越多,效率越低. 实例如下

```
id	cate_name	parent_id
1	  数码产品	 -1
3	  笔记本	    1
4	  智能手机	 1
15	小米	     4
```

```verilog
==>  Preparing: select * from cates where id = ? 
==> Parameters: 1(Integer)
<==    Columns: id, cate_name, parent_id
<==        Row: 1, 数码产品, -1
====>  Preparing: select * from cates where parent_id = ? 
====> Parameters: 1(Integer)
<====    Columns: id, cate_name, parent_id
<====        Row: 3, 笔记本, 1
======>  Preparing: select * from cates where parent_id = ? 
======> Parameters: 3(Integer)
<======      Total: 0
<====        Row: 4, 智能手机, 1
======>  Preparing: select * from cates where parent_id = ? 
======> Parameters: 4(Integer)
<======    Columns: id, cate_name, parent_id
<======        Row: 15, 小米, 4
========>  Preparing: select * from cates where parent_id = ? 
========> Parameters: 15(Integer)
<========      Total: 0
<======      Total: 1
<====      Total: 2
<==      Total: 1
```

```json
[
    {
        "id":1,
        "cateName":"数码产品",
        "parentId":-1,
        "cates":[
            {
                "id":3,
                "cateName":"笔记本",
                "parentId":1,
                "cates":[
                ]
            },
            {
                "id":4,
                "cateName":"智能手机",
                "parentId":1,
                "cates":[
                    {
                        "id":15,
                        "cateName":"小米",
                        "parentId":4,
                        "cates":[
                        ]
                    }
                ]
            }
        ]
    }
]
```

