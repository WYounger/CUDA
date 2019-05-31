##### 基本概念

拦截器是动态代理的实现[基于**反射技术**],依赖于spring

而过滤器基于**回调机制**，依赖于servlet

##### 拦截器

springmvc实现自定义拦截器需要实现**HandlerInterceptor接口**，并要在springweb中注册

###### 实现HandlerInterceptor接口

```java

public class MyInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(javax.servlet.http.HttpServletRequest httpServletRequest, javax.servlet.http.HttpServletResponse httpServletResponse, Object o) throws Exception {
        System.out.println("执行自定义拦截器1的preHandle方法");
        return true;
        //返回true:执行Controller
        //返回false:不执行Controller
    }

    @Override
    public void postHandle(javax.servlet.http.HttpServletRequest httpServletRequest, javax.servlet.http.HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {
        System.out.println("执行自定义拦截器1的postHandle方法");
    }

    @Override
    public void afterCompletion(javax.servlet.http.HttpServletRequest httpServletRequest, javax.servlet.http.HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {
        System.out.println("执行自定义拦截器1的afterHandle方法");
    }
}

```



###### 注册自定义拦截器

```xml
    <!--注册拦截器
    path:拦截的url
    class:拦截器Class
    实现对所有.do和.action的控制器的拦截
    -->
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/*.do"/>
            <mvc:mapping path="/*.action"/>
            <bean class="utils.MyInterceptor1"/>
        </mvc:interceptor>
        <mvc:interceptor>
            <mvc:mapping path="/*.do"/>
            <mvc:mapping path="/*.action"/>
            <bean class="utils.MyInterceptor2"/>
        </mvc:interceptor>
    </mvc:interceptors>
```

##### 过滤器

###### 实现javax.servlet.Filter接口

```java
import javax.servlet.*;
import java.io.IOException;

public class MyFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        System.out.println("执行MyFilter的init方法");
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("执行MyFilter的doFilter方法");
        //通过filterChain,dofilter(..)放行，不执行这句则不放行
        filterChain.doFilter(servletRequest,servletResponse);
    }

    @Override
    public void destroy() {
        System.out.println("执行MyFilter的destroy方法");
    }
}
/***
 * FilterChain维护了一个链表，链表中存放着配置对象的链条，每次用户调用一次
 * chain.doFilter(request, response)，链表就去取下一个配置对象，
 * 再通过配置对象得到下一个filter，然后调用该filter，接着在filter里写的逻辑就被执行了
 */
```

###### 配置自定义过滤器

在web.xml中配置过滤器

```xml
  <!--对所有资源过滤-->
  <filter>
    <filter-name>MyFilter</filter-name>
    <filter-class>utils.MyFilter</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>MyFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
```

##### 拦截器与过滤器的区别

1. 过滤器是基于Java函数回调,拦截器基于Java反射机制
2. 过滤器依赖于servlet容器,而spring中拦截器依赖于spring，由spring管理
3. 过滤器对所有请求几乎都起作用，而拦截器只对控制起作用
4. 拦截器可以访问action的上下文,栈里的数据，而过滤器不能
5. 拦截器可以获取IOC容器中的各种Bean，而过滤器不行



