##### 1.Javascript操作DOM

1.document对象的就是整个DOM树 的根节点

2.选择Element

```javascript
document.getElementById("id");
document.getElementByTagName("tagName");
document.getElementByClassName("className");
//注意三种方式的组合使用
```

3.Element三种属性

innerHTML:节点的文本内容或子节点

innerText:节点的文本（除隐藏的元素外）

textContent:节点的文本（包括隐藏的元素）

DOM的style对应所有的CSS  

```javascript
var p = document.getElementBiyId("p-id");
p.style.color="red";
```

4.插入DOM

appendChild(child);

```javascript
var list = document.getElementById("list");
var child = document.createElement("p");
child.id = "childID";
child.innerText="text";
list.append(child);
```

parentElemnet.insertBefore(newElement,referenceElement);

5.删除一个节点

首先找到该节点的父节点，然后调用父节点的removeChild把自己删除掉

```javascript
var self = document.getElementById("to-be-removed");
var parent = self.parentElement;
var removed = parent.removeChild(self);
removed == self;//true
```

6.表单操作

```html
<input type="text">
<input type="password">
<input type="radio">
<input type="checkbox">
<select><option></option></select>
<input type="hidden">
```

**取值**

```javascript
//<input type="text" id="email>
var input = document.getElementById("email");
var value = input.value;//取值
```

这种获取值的方法对应于`text`,`passwprd`,`hidden`,`select`.但是对于`checkbox`和`radio`是不能那么取值

```javascript
//男:<input type="radio" name="gender" value="male" id="male">
//女:<input type="radio" name="gender" value="female" id="female">
var male = document.getElementById("male");
var female = document.getElementById("female");
female.checked;//true or false
male.checked;//false or true
```

**设值**

```javascript
input.value = newValue;
```

```javascript
female.checked=true;
```

提交表单

```html
<form action="" method="GET/POST" onsubmit="return checkForm()">
  <input type="text">
  <input type="submit">
</form>
<script>
  function checkForm(){
    //...check
  }
</script>
```

提交文件

```html
<form action=""  method="POST" enctype="multipart/form-data">
  <input type="file">
  <input type="submit">
</form>
```

##### 2.jQuery操作DOM

1.选择器

```javascript
var div = $("#div-id");//按ID查找
var p = $("p");//按标签查找
var a = $(".className1.className2..");//按class查找，可以同时使用几个class
var email=$('[name=email]');//按照属性查找
//组合查找
var emailInput=$('input[name=email]');//tag+属性
var tr=$('tr.red');//tag+class
```

2.操作DOM

1. 修改Text和HTML

````javascript
$("#id").text();//获取节点文本
$("#id").html();//获取原始HTML
$("#id").text('text');//设置节点文本
$("#id").html('html');//设置HTML
````

2. 修改CSS

```javascript
$('#id').css('name','value');
```

3. 修改class

```javascript
var div = $('#div');
div.hasClass('class');
div.addClass('class');
div.remove('class');
```

4. 显示和隐藏DOM

```javascript
var div = $('#div');
div.hide();//隐藏
div.show();//显示
```

5. 修改DOM属性

```javascript
var div = $('#div');
div.attr('name');//获取name的属性值
div.attr('name','email');//设置name的属性值
```

6. 操作表单

```javascript
var input = $('#input');
var value = input.val();//获取值
input.val(newValue);//设置新值
```

7. 添加DOM

```javascript
dom.append(newDom)
```

8. 删除DOM

```javascript
dom.remove()；//先获取到dom，然后直接remove()
```

9. 事件

```javascript
dom.on('event',function(){...});
dom.event(function(){...});
```

10. Ajax

```javascript
//$.ajax(url[,settings]);
$.ajax({
  url:'发送URL',
  async:true or false,//默认true
  contentType:application/x-www-form-urlencoded //发送到server的数据类型，默认值可以处理多数情况 false不设置
  		|multipart/form-data|text/plain
   data: o,//传输到server的数据，格式key/value pairs
   dataType:'json',//期望server返回数据的格式
   method: post|get|put,//请求方式<==>type
   processData:true|false,//对于contentType为默认类型，需要将processData设置为true,因为data将会转换为query String,而contentType为其他类型，processData为false
  statusCode:{
  	404:function(){},
  	...
	},
    success:function(anything data,String textStatus){}
});
```

