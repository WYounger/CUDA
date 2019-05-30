#### 1.选择器

##### (1) 元素选择器

```javascript
$("#sid") //选择id=sid的元素
$(".red") //选择class=red的元素
$("p") //选择<p>元素
$("p.red") //选择<p class="red">元素
$("p#name") //选择<p id="name">
```

##### (2)属性选择器

```javascript
$("[href]") //选择所有带有属性href的元素
$("[href='link']") //选择所有带有属性href="link"的元素
$("[href!='link']")//选择所有带有属性不是href="link"的元素
```

##### (3)css选择器

```javascript
$("p").css("background-color","red");
```

#### 2.获取和设置

##### 1.获取

```javascript
$("#").text();//获取文本内容
$("#").html();//获取内容和html
$("#").val();//获取表单元素的值
```

##### 2.设置

```javascript
$("#").text(newText);//获取文本内容
$("#").html();//获取内容和html
$("#").val(newValue);//获取表单元素的值
$("#").attr("attribue",value);//修改元素属性
```

#### 3.操作元素

##### 1.创建元素

```javascript
//html
var a = "<a href='https://www.baidu.com'>百度</a>";
//jquery
var a = var a = $("<a></a>").text("百度").attr("href",'https://www.baidu.com'); 

//dom
var a = document.createElement("a");
a.innerText = "百度";
a.setAttribute("href","https://www.baidu.com");
```

##### 2.添加元素

```javascript
p.append(a + i)//p 1 2 3 4 5  
p.after(a + i) //p 5 4 3 2 1
p.before(a＋ｉ)//1 2 3 4 5 p
p.prepend(a + i)//5 4 3 2 1 p
```

##### 3.删除元素

```javascript
$("#").remove();//删除该元素以及子元素
$("#").empty();//删除元素的子元素
```

##### 4.CSS

```javascript
<style>
  .myStyle{
    ...
  }
</style>
$("#").addClass('myStyle');
$("#").removeClass(['myStyle']);
```

