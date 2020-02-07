##### 1.环境搭建

```java
//启动类
@SpringBootApplication
@MapperScan("com.mp.dao")//使用MP的注解扫描Mapper接口
public class Application{
  public static void main(String[] args){
    SpringApplication.run(Application.class,args);
  }
}

//user 表:(id,name,age,email,manager_id)
//实体类
@Data
public class User{
  private Long id;
  private String name;
  private Integer age;
  private String email;
  private Long managerId;
}

//Mapper接口
//继承 BaseMapper<T>
package com.mp.dao;
public interface UserMapper extends BaseMapper<User>{
  
}

//测试
@RunWith(SpringRunner.class)
@SpingBootTest
public class SimpleTest{
  @Autowired
  private UserMapper uesrMapper;
  
  @Test
  public void select(){
    //查询user表中所有记录
    List<User> list = userMapper.selectList(null);
    list.forEach(System.out::println);
  }
}
```

##### 2.isnert、表名和字段名映射

```java
@Test
public void insert(){
  User user = new User();
  user.setName("young");
  user.setAge(20);
  user.setManagerId(1);
  //email=null，默认不会出现在sql语句中
  int rows = userMapper.insert(user);
  
  
  //orm默认按照下划线与驼峰对应
  //当主键名称为id，插入时缺省则默认填充；若不是id,则报错,可以使用@TableId注解实体类主键字段
}

//---------对应插入操作的问题------------

//1. 表名与实体类名称不一致
  // 表名:mp_user 实体类: User
@TableName("mp_user")
public class User{
  //...
}

//2.主键不是id
//table: user_id class: userId
public class User{
  @TableId
  private Long uerId;
  //...
}

//3.字段(非主键)和列名不对应
//table: name class: realName
public class User{
  @TableField("name")
  private String realName;
  //...
}

//4.table中没有对应的列
//table:没有remark字段 class:多了一个remark字段
public class User{
  @TableField(exist = false)
  private Sting remark;
  //...
}
```

##### 3.查询

```java
@Test
public void select(){
  //id单个查询
  Uesr user = userMapper.selectById(1L);
  
  //ids多个查询
  List<User> users = userMapper.selectBatchIds(Arrays.asList(1L,2L));
 
  //多列查询
  Map<String> columnMap = new HashMap<>();
  //列-参数值，这里的列没有转换操作，必须使用表中的列名
  columnMap.put("name","young");
  column.put("age",20);
  List<User> users = userMapper.selectByMap(columnMap);
 
  //查询构造器 ==> 构建where子句
  QueryWrapper<Uesr> queryWrapper = new QueryWrapper<>();
  
  // name like '%you%' and age < 24 and email is not null
  queryWrapper.like("name","you").lt("age",24).isNotNull("email");
   //name like 'y%' or age > 25 order by age desc,id asc
  queryWrapper.likeRight("name","y").or().gt("age",25).orderByDesc("age").orderByAsc("id");
  //name like 'y%' and (age < 25 or email is not null)
  queryWrapper.likeRight("name","y").and(wq -> wq.lt("age",25).or().isNotNull("email"));
  //name like 'y%' or (age < 25 and age > 18 and email is not null)
  queryWrapper.likeRight("name","y").or(wq -> wq.lt("age",25).gt("age",18).isNotNull("email"));
  //age in (18,19,20)
  queryWrapper.in("age",Arrays.asList(18,19,20));
  
  //执行QueryWrapper，得到结果
  //使用select返回特定列，默认返回所有列
  //select id,name from user where name like '%young%'
  queryWrapper.select("id","name").like("name","young");
  //如果没有返回实体类对应的列，那么该列对应的字段为null
  List<User> userList = userMapper.selectList(queryWrapper);
  //结果不分装为对象
  List<Map<String,Object>> map = userMapper.selectMaps(queryWrapper);
  
  //lambda条件构造器.
  //可以在编译期及时发现问题
  LambdaQueryWrapper<User> lambdaQuery = Wrappers.<User>lambdaQuery();
  //name like 'y%' and (age < 25 or email is not null)
  lambdaQuery.likeRight(User::getName,"y").and(lqw ->                           
                        lqw.lt(User::getAge,25).or().isNotNull(User::getEmail));
  userMapper.selectList(lambdaQuery);
}
```

##### 4.分页查询

```yaml
# mapper.xml文件路径配置
mybatis-plus:
	mapper-locations:
	- classpath: mapper/*.xml
	- classpath: dao/*.xml
```

```java
//物理分页插件配置类
@Configuration
public class MybatisPlusConfig{
  @Bean
  public PaginationInterceptor paginationInterceptorMySql(){
    PaginationInterceptor pageInterceptor = new PaginationInterceptor();
    pageInterceptor.setDialectType("mysql");
    return pageInterceptor;
  }
}

@Test
public void page(){
  LambdaQueryWrapper<User> lambdaQuery = Wrappers.<User>lambdaQuery();
  lambdaQuery.ge(User::getAge,18);
  Page<User> page = new Page<>(1,2);//当前页，每页多少条
  IPage<User> iPage = userMapper.selectPage(page,lambdaQuery);
  iPage.getPages();//总页数
  iPgae.getTotal();//总条数
  List<User> userList = iPage.getRecords(); //记录数据
}
```

##### 5.Update

```java
//updateById
User user = new User();
user.setId(1L);
user.setAge(18);
int rows = userMapper.updateById(user); //user实例中除id字段外其他不为null的字段出现在set中

//updateByWrapper
UpdateWrapper<User> updateWrapper = new UpdateWrapper<>()
updateWrapper.eq("age",18);//where条件

//1.多个字段更新，封装为对象
User user = new User(); 
user.setAge(19);
user.setEmail("young@qq.com");
int rows = userMapper.update(user,updateWrapper);//实例中，不为null的字段将出现在set中
//2.少量字段更新,updateWrapper直接set
updateWrapper.eq("name","young").eq("age",18).set("age",19);
int rows = userMapper.update(null,updateWrapper);

//LambdaUpdateWrapper
LambdaUpdateWrapper<User> lambdaUpdate = Wrappers.<User>lambdaUpdate();
lambdaUpdate.eq(User::getName,"young").eq(User::getAge,18).set(User::getAge,19);
int rows = userMapper.update(null,lambdaUpdate);
```

##### 6.delete

```java
@Test
public void delete(){
  //deleteById
  int rows = uesrMapper.deleteById(1L);
  
  //deleteBatchIds
  int rows = userMapper.deleteBatchIds(Arrays.asList(1L,2L,3L));
  
  //deleteByMap
  Map<String,Object> columnMap = new HashMap<>();
  columnMap.put("name","young");
  columnMap.put("age",20);
  int rows = userMapper.deleteByMap(columnMap);
  
  //delete
  LambdaQueryWrapper<User> lambdaQuery = Wrappers.<User>lambadQuery();
  lambdaQuery.eq(User::getName,"young").or().gt(User::getAge,20);
  int rows = userMapper.delete(lambdaQuery);
}
```





