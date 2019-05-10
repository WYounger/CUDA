##### Model

总是将数据放到Model对象中，那么Model是什么呢？其实非常简单，他就是一个Map，保存key/value的集合。所以在Model数据类型的地方可以用Map代替掉。

既然数据放到model中，那么他是如何传递到页面中的呢？记得在学习Servlet时，将数据保存在HttpServletRequest中，然后通过转发到JSP(Servlet)，既然这样的话，也就很容易明白，其实model对象也是被放入到reuqest域中，然后通过view渲染，将数据填充到页面中。

##### 接受请求的输入

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

​          为了信息的安全性，表单提交一般使用post提交，数据会存放在请求报文体中传递到server。

通常将form提交的数据映射为一个bean



##### 重定向数据携带问题

由于重定向是发起了两次请求，第一次请求的数据不能保存到第二次请求，那么有什么办法让它携带(保存)数据呢

1. 通过URL模板

   ```java
   public String method(Model model){
     model.addAttribute("username","young");
     model.addAttribute("age",20);
     return "redirect:/{username}";
   }
   ```

   URL结果:`/young?age=20`，这种方法只能携带基本数据类型和Stringl类型的值

   不要将URL直接拼接参数，会导致sql注入问题

2. 使用flash属性

   ```java
   //存入
   public String nethod(RedirectAttributes model){
     model.addAttribute("username","young");
     model.addFlashAttribute("bean",bean);
     return "redirect:/username";
   }
   //取出
   @ReqeustMapping(value="/{username}")
   public String method(@PathVariable String username,Model model){
     Bean bean  = (Bean)model.getAttribute("bean");
   }
   ```

   RedirecAttributes接口是Model接口的子接口，添加了设置flash属性的方法。

   在执行重定向前，所有的flash书属性都会复制到会话中。在重定向后，存在会话中的flash属性会被取出，并从会话总转移到模型之中。