##### 1. 保存用户状态

当用户登录成功后，直接将用户对象放在session中。然后可以在拦截器或过滤器中实现拦截功能。当用户在进行页面跳转时，可以判断session中object是否为空。若为空，则重定向到登录页面；不为空，则放行。当用户退出登录时，使session失效:session.invalidate()

##### 2. 前缀问题

在springmvc中配置prefix、suffix

<bean class="InternalResoucesViewResolver">

​	<property name="prefix" value="==/==**WEB-INF/views==/=="/**>

​	<property name="suffix" value=".jsp"/>

</bean>

1. 自动添加

   1. modelAndView.setViewName("resouceName");
   2. return "resouceName";
   3. 结果：==/==WEB-INF/views/resouceName.jsp，后台路径

2. 不添加

   1. request.getRequestDispatcher("/path").forword(request,response);

      response.sendRedirect("/path")

   2. return "forword:/path";

      return  "redirect:/path";

3. 总结

   **涉及到重定向和转发的不加前缀和后缀，反之不涉及的就添加前缀和后缀**

##### 3. 在javascript和标签中使用el表达式

1. 使用方法:

   "${request.name}"

2. 注意在js中给页面节点元素赋值时，一定要在该节点之后

   例子:

   ```javasc
    <head>
    		<script>
    			 document.getElementById("err").innerHTML= "${errorMessage}";
    		</script>
    </head>
    报错: Uncaught TypeError: Cannot set property 'innerHTML' of null
    因为此时页面还没加载出来，节点元素为null
   ```

   解决办法:

   1. 将js代码放在</body>之前即可。

   2. 把原本的JS代码放入window.onload=function() { ... }中，**window.onload表示页面加载完成后执行的函数**，这样JS代码即使放在<head>中也可以完美实现效果

      ```:british_virgin_islands:
      <head>
          <script>
              window.onload=function (ev) {
                  document.getElementById("err").innerHTML= "${errorMessage}";
              }
          </script>
      </head>
      ```

##### 4.资源文件的读取

例如：读取web-inf下的/images/i-1.png

直接使用servletContext

```java
ServletContext servletContext  = this.getServletContext();
InputStream inputStream = servletContext.getResourceAsStream("images/i-1.png");
```

