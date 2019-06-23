jar包

`hibernate-validator`

`validation-api`

bean

```java
public class Student {

    @NotNull(message = "不能为空")
    @Length(min = 2,max = 10,message = "长度2-10")
    private String name;

    @Min(value = 18,message = "年龄不能小于18")
    @Max(value = 30,message = "年龄不能大于30")
    //@Range(min = 18,max=30,message = "")等效于@Min和@Max,来自hibernate
    private int age;

    @NotNull(message = "不能为空")
    @Email(message = "邮箱格式不正确")
    private String email;

    @NotNull(message = "不能为空")
    @Pattern(regexp = "(^1[358])[1-9]{9}$",message="电话格式不正确")
    private String phone;

    @NotEmpty(message = "不能为空")
    private String address;
  	//getter和setter略
}
```

控制器方法

`@Valid Model modle` `BindingResult bindingResult`

```java
 @RequestMapping("/register.do")
    //验证model 绑定错误
    public String register(@Valid Student student, BindingResult br, Model model){
        if(br.hasErrors()){
            model.addAttribute("student",student);
            //取出所有验证失败的属性
            List<FieldError> list = br.getFieldErrors();
            for (FieldError error : list) {
                model.addAttribute(error.getField()+"Err",//属性名+Err,生成错误消息
                        error.getDefaultMessage()+//错误消息
                                "而你的的值是"+error.getRejectedValue());//属性值
            }
            return "forward:/register.jsp"; //返回到首页
        }else{
            return "success";
        }
    }
```

**jsp**

```jsp
 <form action="<%=path%>/validator/register.do" method="post">
        姓名:<input type="text" name="name" value="${student.name}"><label>${nameErr}</label><br>
        年龄:<input type="text" name="age" value="${student.age}"><label>${ageErr}</label><br>
        邮箱:<input type="text" name="email" value="${student.email}"><label>${emailErr}</label><br>
        电话:<input type="text" name="phone" value="${student.phone}"><label>${phoneErr}</label><br>
        地址:<input type="text" name="address" value="${student.address}"><label>${addressErr}</label><br>
        <input type="submit">
    </form>
```

