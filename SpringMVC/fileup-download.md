###### 依赖

在pom.xml添加依赖

```xml
<!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
    <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.3.3</version>
    </dependency>
```

######  springmvc配置

springmvc.xml

```xml
<!--启动springmvc注解驱动-->
    <mvc:annotation-driven/>

    <!--设置文件解析器,id必须为="multipartResolver"-->
    <bean id="multipartResolver"
         class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!--设置编码、上传总文件大小(字节为单位)-->
        <property name="defaultEncoding" value="utf-8"/>
        <property name="maxUploadSize" value="17367648787"/>
    </bean>
```

###### 页面

fileUpLoad.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>文件上传</title>
</head>
<body>
    <!--注意 enctype="multipart/form-data"-->
<form action="${pageContext.request.contextPath}/upLoadFile.do"
      method="post" enctype="multipart/form-data">
    单文件上传文件:<input type="file" name="upFile"></br>
    <!--多文件上传
        <input name="files" type="file" multiple="multiple">
    -->
    上传:<input type="submit" value="上传">
</form>
</body>
</html>

```

fileDownLoad.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>文件下载</title>
</head>
<body>
    <!--通过request的参数形式传递fileName文件名[全名称]-->
    <a href="${pageContext.request.contextPath}/doFileDown.do?fileName=基础知识笔记.docx">文件下载</a>
</body>
</html>
```

###### 上传、下载控制器

FileUpAndDownLoad.java

```java
@Controller
public class FileUploadAndDownController {

    @RequestMapping(value="/upLoadFile.do")
    public void doFileUpload(MultipartFile upFile, HttpSession session){
      //多文件接收 （List<MultipartFile> files,...）
      //单文件接收
        //FileDirectory文件夹先要建好
        //C:\MyFile\JavaWebProject\SpringMVCAnnotation\target\SpringMVCAnnotation-1.0-SNAPSHOT\FileDirectory
        String path = session.getServletContext().getRealPath("/FileDirectory");
        //获取原始文件名
        String  fileName = upFile.getOriginalFilename();
          if (!upFile.isEmpty()){//判断上传文件是否为空
            //创建文件对象，保存服务器
            File file = new File(path,fileName);
            try {
                upFile.transferTo(file);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    @RequestMapping("/doFileDown.do")
    public ResponseEntity<byte[]> foFileDown(String fileName, HttpServletRequest request) throws IOException {
        //下载文件路径
        String path = request.getServletContext().getRealPath("/FileDirectory");
        File file = new File(path,fileName);
        HttpHeaders headers = new HttpHeaders();
        //下载显示的文件名，解决中文名称乱码问题
        String downloadFielName = new String(fileName.getBytes("UTF-8"),"iso-8859-1");
        //通知浏览器以attachment（下载方式）打开图片
        headers.setContentDispositionFormData("attachment", downloadFielName);
        //application/octet-stream ： 二进制流数据（最常见的文件下载）
        headers.setContentType(MediaType.APPLICATION_OCTET_STREAM);
        return new ResponseEntity<byte[]>(FileUtils.readFileToByteArray(file),
                headers, HttpStatus.CREATED);
    }
}

```

