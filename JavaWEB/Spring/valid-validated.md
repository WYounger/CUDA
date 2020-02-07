看个例子

```java
// 依赖 springboot + lombok
@Data
public class Prop {
    @NotNull
    @Size(min = 1,max=5,message = "长度在1-5个字符")
    private String propName;
}

@Data
public class StudentDto {

    @NotNull
    @Max(20)
    @Min(30)
    private int age;

    @NotNull
    @Size(min = 2,max = 5,message = "长度在2-5个字符")
    private String name;
    /**
     * 嵌套验证
     * 加上@Valid,@Validated不能添加在成员变量上
     */
    @Valid
    @NotNull
    @Size(min = 1,message = "至少有一个prop")
    private List<Prop> props;
}

//ExceptionHandler
@Slf4j
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler({MethodArgumentNotValidException.class})
    public Object methodArgumentValidExceptionHandler(HttpServletRequest request,
                                                      MethodArgumentNotValidException ex){
        List<FieldError> errors = ex.getBindingResult().getFieldErrors();
        Map<String,String> map = new HashMap<>(10);
        for (FieldError error : errors){
            map.put(error.getField(),error.getDefaultMessage());
        }
        return map;
    }

    @ExceptionHandler({ConstraintViolationException.class})
    public Object constranViolationExceptionHandle(HttpServletRequest request,
                                                   ConstraintViolationException ex){
        return ex.getMessage();
    }

    @ExceptionHandler({Exception.class})
    public Object exceptionHandler(Exception ex){
        return ex.getMessage();
    }
}

//测试
@RestController
public class TestExceptionController {

    @PostMapping(value = "/testException")
    public Object test(){
        if(true)
            throw new MyException("my exception!!!");
        return "success";
    }

    @PostMapping(value = "/testDto")
    public Object test(@Valid  @RequestBody StudentDto studentDto){
        return studentDto;
    }
}
```

##### 参考资料

[spring boot 数据验证](https://blog.csdn.net/leilecoffee/article/details/80225548)

[@Valid和@Validated的区别](https://blog.csdn.net/qq_27680317/article/details/79970590)

[JSR303-Bean Validation](https://www.ibm.com/developerworks/cn/java/j-lo-jsr303/index.html)



