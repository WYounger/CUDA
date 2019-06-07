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

##### 5.路径问题

前台路径：HTML、jsp中的资源路径名，例如form中的action、图片的src等

后台路径:在java class文件和配置文件

`/`**开头的前台路径以当前服务器的ip+端口的为参照路径**

`/`**开头的后台路径以当前的项目名称ip+端口+project为参照路径**

以**路径名称开头的相对路径**以**当前访问的路径**的**资源路径**的为参照路径

注意：

**1、** 由于response重定向可以在跨项目访问，所以`/`开头的路径必须要加项目名称，eg:

在重定向到当前项目的另一个servlet：`response.sendRedirect(request.getContextpath()+”/servletName”)`

**2、** web.xml中的URL使用来的表示路径的所以不存在以路径名称开头的相对路径，故只能用`/`开头，相对当前project

**3、** 在在项目发布后，`webapp`变成了项目名,其下的资源都在`webapp/..`下。所以在`webapp`的资源的绝对路径是`http://localhost:8080/projectName/...`

在myeclipse中`webapp`为`webroot`

**4**、jsp中`${pageContext.request.contextPath}`返回的是项目的名称/projectName

等价于:

`<% String path = request.getContextPath(); %>`

`<%= path %>`

##### 6.classpath

在`maven`构建的项目中`resources`为`classpath`路径

##### 7.静态资源的访问

**web.xml**

```xml
<servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:spring/spring-*.xml</param-value>
    </init-param>
  </servlet>
  <servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern> <!--不能写成/*-->
  </servlet-mapping>
```

**1.静态资源在webapp下**

**资源目录结构**

```bash
webapp
--images
----test.jpg
--WEB-INF
----views
------test.jsp
```

**spring-web.xml**

```xml
    <mvc:default-servlet-handler/>

    <!-- 配置ViewResolver 使用内部资源视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
        <property name="prefix" value="/WEB-INF/views/"/><!--从webapp开始-->
        <property name="suffix" value=".jsp"/>
    </bean>
```

**访问test.jpg**

```jsp
src = "<%=request.getContextPath()%>/images/test.jpg"
```

**2.静态资源在WEB-INF下**

**资源目录结构**

```bash
webapp
--WEB-INF
----images
------test.jpg
----views
------test.jsp
```

**spring-web.xml**

```xml
   <!--
        mapping:url访问路径
        /**:匹配url为/images/下所有访问路径
        location:资源目录/实际路径
        此种配置可以看作像访问请求方法一样
    -->
    <mvc:resources mapping="/images/**" location="/WEB-INF/images/"/>

    <!-- 配置ViewResolver 使用内部资源视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
        <property name="prefix" value="/WEB-INF/views/"/><!--从webapp开始-->
        <property name="suffix" value=".jsp"/>
    </bean>
```

**访问test.jpg**(同上一种方案)

```jsp
src = "<%=request.getContextPath()%>/images/test.jpg"
```

**总结**

推荐将静态资源 **`图片` `js` `css`**放在**`webapp`**目录下 ,配置较少，同时官方也推荐

