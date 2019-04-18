```xml
<!-- https://mvnrepository.com/artifact/javax.mail/javax.mail-api -->
<dependency>
    <groupId>javax.mail</groupId>
    <artifactId>javax.mail-api</artifactId>
    <version>1.6.1</version>
</dependency>
```

```java
//邮箱发送验证码工具
package com.appms.email;
 
import java.util.Date;
import java.util.Properties;
 
import javax.mail.Address;
import javax.mail.Message;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;
 
import com.sun.mail.util.MailSSLSocketFactory;
 
public class JavaEmailSender {
 
 
  public static void sendEmail(String toEmailAddress,String emailTitle,String emailContent)throws Exception{
    Properties props = new Properties();
 
    // 开启debug调试
    props.setProperty("mail.debug", "true");
    // 发送服务器需要身份验证
    props.setProperty("mail.smtp.auth", "true");
    // 设置邮件服务器主机名
    props.setProperty("mail.host", "smtp.qq.com");
    // 发送邮件协议名称
    props.setProperty("mail.transport.protocol", "smtp");
 
    /**SSL认证，注意腾讯邮箱是基于SSL加密的，所有需要开启才可以使用**/
    MailSSLSocketFactory sf = new MailSSLSocketFactory();
    sf.setTrustAllHosts(true);
    props.put("mail.smtp.ssl.enable", "true");
    props.put("mail.smtp.ssl.socketFactory", sf);
 
    //创建会话
    Session session = Session.getInstance(props);
 
    //发送的消息，基于观察者模式进行设计的
    Message msg = new MimeMessage(session);
    msg.setSubject(emailTitle);
    //使用StringBuilder，因为StringBuilder加载速度会比String快，而且线程安全性也不错
    StringBuilder builder = new StringBuilder();
    builder.append("\n"+emailContent);
    builder.append("\n时间 " + new Date());
    msg.setText(builder.toString());
    msg.setFrom(new InternetAddress("你的QQ邮箱"));
 
    Transport transport = session.getTransport();
    transport.connect("smtp.qq.com", "你的QQ邮箱", "你开启SMTP服务申请的独立密码");
    //发送消息
    transport.sendMessage(msg, new Address[] { new InternetAddress(toEmailAddress) });
    transport.close();
  }
}
```

```java
/**
 * 跳转到发送邮件页面
 * @return
 * @throws Exception
 */
@RequestMapping("/goSendEmail")
public ModelAndView goSendEmail(HttpServletRequest request)throws Exception{
  ModelAndView mv = this.getModelAndView();
  String email = request.getParameter("email");
  if(email!=null&&!"".equals(email)){
    email = email.trim();
    mv.setViewName("member/send_email");
    mv.addObject("email", email);
  }
  return mv;
}
 
/**
 * 发送邮件
 * @return
 * @throws Exception
 */
@RequestMapping(value="/sendEmail",produces="application/json;charset=UTF-8")
@ResponseBody
public Object sendEmail(HttpServletRequest request)throws Exception{
  Map<String,String> map = new HashMap<String,String>();
  String msg = "ok";   //发送状态
  String toEMAIL = request.getParameter("EMAIL");         //对方邮箱
  String TITLE = request.getParameter("TITLE");          //标题
  String CONTENT = request.getParameter("CONTENT");        //内容
  JavaEmailSender.sendEmail(toEMAIL, TITLE, CONTENT);
  map.put("result", msg);
  return map;
}
```

```javascript
<script type="text/javascript" src="source/js/jquery-1.7.2.js"></script>
  <!--提示框-->
  <script type="text/javascript" src="source/js/jquery.tips.js"></script>


<!-- 发送邮件 -->
  <script type="text/javascript">
//发送
function sendEm(){
 
  if($("#TYPE").val()=="1"){
    $("#CONTENT").val(getContentTxt());
  }else{
    $("#CONTENT").val(getContent());
  }
  if($("#EMAIL").val()==""){
    $("#EMAIL").tips({
      side:3,
      msg:'请输入邮箱',
      bg:'#AE81FF',
      time:2
    });
    $("#EMAIL").focus();
    return false;
  }
  if($("#TITLE").val()==""){
    $("#TITLE").tips({
      side:3,
      msg:'请输入标题',
      bg:'#AE81FF',
      time:2
    });
    $("#TITLE").focus();
    return false;
  }
  if($("#CONTENT").val()==""){
 
    $("#nr").tips({
      side:1,
      msg:'请输入内容',
      bg:'#AE81FF',
      time:3
    });
    return false;
  }
 
  var EMAIL = $("#EMAIL").val();
  var TYPE = $("#TYPE").val();
  var TITLE = $("#TITLE").val();
  var CONTENT = $("#CONTENT").val();
 
  $("#zhongxin").hide();
  $("#zhongxin2").show();
 
  $.ajax({
    type: "POST",
    url: 'retroaction/sendEmail.do?tm='+new Date().getTime(),
    data: {EMAIL:EMAIL,TITLE:TITLE,CONTENT:CONTENT},
    dataType:'json',
    //beforeSend: validateData,
    cache: false,
    success: function(data){
      if("ok" == data.result){
        $("#msg").tips({
            side:3,
            msg:'发送成功！',
            bg:'#68B500',
            time:5
           });
        setTimeout("showdiv()",1000);  
      }else{
        $("#msg").tips({
            side:3,
            msg:'发送失败!',
            bg:'#68B500',
            time:5
           });
      }
 
    }
  });
 
}
 
</script>
```

```jsp
<!-- 编辑邮箱 -->
    <div>
    <table style="width:98%;" >
      <tr>
        <td style="margin-top:0px;">
           <div style="float: left;" style="width:81%"><textarea name="EMAIL" id="EMAIL" rows="1" cols="50" style="width:600px;height:20px;" placeholder="请选输入对方邮箱,多个请用(;)分号隔开" title="请选输入对方邮箱,多个请用(;)分号隔开">${email}</textarea></div>
           <div style="float: right;" style="width:19%"><a class='btn btn-mini btn-info' title="编辑邮箱" onclick="dialog_open();">编辑邮箱</i></a></div>
        </td>
      </tr>
      <tr>
        <td>
           <input type="text" name="TITLE" id="TITLE" value="" placeholder="请选输入邮件标题" style="width:98%"/>
        </td>
      </tr>
      <tr>
        <td id="nr">
           <script id="editor" type="text/plain" style="width:650px;height:259px;"></script>
        </td>
      </tr>
      <tr>
        <td style="text-align: center;">
          <a class="btn btn-mini btn-primary" onclick="sendEm();">发送</a>
          <a class="btn btn-mini btn-danger" onclick="top.Dialog.close();">取消</a>
        </td>
      </tr>
    </table>
    </div>
    <div id="zhongxin2" class="center" style="display:none"><br/><img src="assets/images/jzx.gif" id='msg' /><br/><h4 class="lighter block green" id='msg'>正在发送...</h4></div>    
```

```java
//生成验证码工具
public class RandomUtil {
    //随机得到6位数
    public static int getRandNum() {
        return (int)((Math.random()*9+1)*100000);
    }
}
```

参考链接:

[第一个](<https://www.jb51.net/article/101333.htm>)

[第二个](<https://www.jianshu.com/p/0991f0841b0a>)     [源码](<https://gitee.com/hengboy/spring-boot-chapter/tree/master/SpringBoot1.x/Chapter7/src/main/java/com/yuqiyu>)

注册思路:

1. 用户首先填写邮箱
2. 点击获取验证码
3. 后台检验邮箱是否已经注册过。若注册过，返回已注册消息，否则想邮箱发送验证码，同时在会话中加入邮箱和验证码，并向用户返回请接收邮件验证码的消息
4. 用户在查看邮箱验证码后，填入验证码输入框(优化:在输入验证码后，可以)立马检测掩码是否正确），接着填入用户名和密码
5. 后台在拿到验证码、用户名、密码后、**邮箱**，验证邮箱和验证码是否与会话中匹配
6. 若匹配，则添加客户到数据库，转发到客户主页；不成功重定向到注册页面

密码找回思路:

1. 用户点击找回密码
2. 跳转到密码找回页面
3. 填写邮箱，点击获取验证码，
4. 后台会话保存邮箱和验证码，并向用户发送验证码
5. 用填入验证码，后台接受验证码，并检验。返回验证码是否正确的消息
6. 若验证通过，则使修改的按钮生效，并跳到页密码修改页面；反之不生效
7. 前台校验两次密码相同后提交提交到后台，给出消息密码已经修改，请重新登录