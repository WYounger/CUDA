##### 1.控制器内异常处理器

`@ExceptionHandler(Class<? extends Throwable>[] value)`

```java
@Controller
public class HelloController{

    @RequestMapping(value="/hello.do",method = RequestMethod.POST)
    public String hello(@Validated Student student, BindingResult br, Model model) throws MyException {//控制器方法抛出异常
        if(br.hasErrors()){
            throw new MyException();
        }else{
            model.addAttribute("student",student);
            return  "success";
        }
    }

    @ExceptionHandler(MyException.class)//处理异常
    public String err(){
        return "err";
    }

}
```

异常处理方法和控制器方法一样，可以返回响应信息.

如果定义了多个异常处理器方法，那么会使用最接近异常类的异常处理方法。例如一个控制器方法抛出IOExcepiton，如果有IOExcption和Excption的两个异常处理器方法，那么声明IOExption的异常处理器方法会收到异常消息，并处理异常.

##### 2.全局异常处理器

`@ControllerAdvice`  + `@ExceptionHandler`

```java
@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(MyException.class)
    public String err(){
        return "err";
    }
}

```

全局异常处理器方法与控制器内异常处理器方法一样,在控制器内异常处理方法不能处理异常时会被调用.