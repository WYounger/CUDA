总是将数据放到Model对象中，那么Model是什么呢？其实非常简单，他就是一个Map，保存key/value的集合。所以在Model数据类型的地方可以用Map代替掉。

既然数据放到model中，那么他是如何传递到页面中的呢？记得在学习Servlet时，将数据保存在HttpServletRequest中，然后通过转发到JSP(Servlet)，既然这样的话，也就很容易明白，其实model对象也是被放入到reuqest域中，然后通过view渲染，将数据填充到页面中。

接受请求的输入

1. 请求参数

   传递: url?param1=value1&param2=value2

   控制器方法接收:(Type param1,Type param2)

2. 路径参数

   传递: webApplication/`pathVarible1`/`pathVarible2`

   控制器方法接收:

   ```java
   @RequestMapping(value="/{pathVariable1}/{pathVariable2}")//两个占位符，映射到处理方法参数上
   public String method(@PathVariable("pathVariable1") Type pathVariable1,
                        @PathVariable("pathVariable2") Type pathVariable2){
     
   }
   ```

3. 表单post提交

​    为了信息的安全性，表单提交一般使用post提交，数据会存放在请求报文体中传递到server。

通常将form提交的数据映射为一个bean