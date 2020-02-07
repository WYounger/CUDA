​        处理器方法也可以返回Obejct对象。但这个对象不是作为逻辑视图出现的，而是作为数据直接在页面显示的出现的。

​       返回Object对象需要使用@ResponseBody注解，转换后的JSON数据放入到响应体。

​	减少数据的传输,提高响应速度

###### 依赖

```xml
     <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.5</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>2.9.5</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-annotations -->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-annotations</artifactId>
      <version>2.9.5</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/net.sf.json-lib/json-lib -->
    <dependency>
      <groupId>net.sf.json-lib</groupId>
      <artifactId>json-lib</artifactId>
      <version>2.4</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.codehaus.jackson/jackson-mapper-asl -->
    <dependency>
      <groupId>org.codehaus.jackson</groupId>
      <artifactId>jackson-mapper-asl</artifactId>
      <version>1.9.13</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.codehaus.jackson/jackson-core-asl -->
    <dependency>
      <groupId>org.codehaus.jackson</groupId>
      <artifactId>jackson-core-asl</artifactId>
      <version>1.9.13</version>
    </dependency>
```

##### 开启注解驱动

spring-web.xml

```xml
<--! Obejct数据转换为JSON数据，需要Http消息转换器HttpMessageConverter完成。而开启转换器需要
    <mvc:annotation-driven/>  完成 -->
    <!-- 开启SpringMVC注解模式 -->
    <mvc:annotation-driven/>
```

##### model

```java
package model;

public class Student {
    private String name;
		private int age;
   //getter.setter.toString
}
```

##### Controller

```java
package controller;

import model.Student;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;


@Controller
public class JsonController {

    //produces = "text/html;charset=utf-8"防止返回字符串的中文乱码问题，只能用于返回字符串
    @RequestMapping(value="/doJson.do")//,produces = "text/html;charset=utf-8")
    @ResponseBody
    public Object doJson(Student student){

        Student student1 =new Student("young",9);
        //返回单个对象
        //return student;

        //返回list
//        List<Student> list = new ArrayList<>();
//        list.add(student);
//        list.add(student1);
//        return list;

        //返回map
        Map<String,Student> map = new HashMap<>();
        map.put("s",student);
        map.put("s1",student1);
        return map;
    }
}
```

##### page.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <!--引入jQuery库，不用idea的自带的jQuery-->
    <script type="text/javascript" src="jqeury-3.1.js"></script>
    <script>
        $(function () {
            $("button").click(function () {
                var o = {'name':'wy','age':10};
               $.ajax({
                   url:'doJson.do',
                   type:'post',//提交方式,method的别名
                   data:o,//传递给服务器端的数据
                   dataType:'json',//从服务器端返回数据的格式
                   success:function (data) {//访问成功的回调函数
                       var temp = '';
                       
                       //data:接收单个对象
                       //temp += data.name +' ' + data.age;

                       //data:接收list/array集合
                       // for(var i = 0;i<data.length;i++){
                       //     temp +=data[i].name + ' ' + data[i].age+'\n';
                       // }
                       
                       //data:接收map map.key取值
                       temp += data.s.name + ' ' + data.s.age + '\n';
                       temp += data.s1.name + ' ' + data.s1.age;
                       alert(temp);
                   }
               });
            });
        })
    </script>
    <title>返回Json对象</title>
</head>
<body>
<div>
    <button>提交Ajax请求</button>
</div>
</body>
</html>
```

##### 总结

注意的点:以上使用ajax默认传数据方式:json格式

1. 页面Ajax-->controller

   将数据封装为json格式数据对象，从而转换为请求参数

   ​                                              var obj = {"name":value,...,

   ​							    "name":value}

   controller接收数据: 

   1. 若obj正好为一个实体类(参数名与model的属性名一致)，可以用直接使用该实体类型接收数据
   2. 单个数据类型接收(type name,...,type name)

2. Controller-->页面Ajax

   返回Object，并以`.`访问Object的属性