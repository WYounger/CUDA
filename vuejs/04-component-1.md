```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <script src="vue.js"></script>
        <title>vue.js-4</title>
    </head>
    <body>
        <div id="app">
            <!---运用定义的组件-->
            <button_component></button_component>
            <!---可以定义多个,每个组件之间相互独立-->
            <button_component></button_component>
            <button_component></button_component>
        </div>

        <div id="app2">
            <!--通过v-bind,将组件(元素)属性和data域中的属性绑定起来-->
            <h_component
            v-for="post in posts"
            v-bind:id="post.id"
            v-bind:title="post.title"
            ></h_component>
        </div>
        <script>
            //全局注册一个组件，提高复用性
            Vue.component('button_component',{//组件名称
                data:function(){//必须是一个返回对象的函数
                    return {count:0};
                },
                template:'<button v-on:click="count++">click:{{count}}</button>'
            });
            var app = new Vue({
                el:"#app"
            })

            //通过props向组件传递数据
            Vue.component('h_component',{
                props:['title','id'],//定义组件的属性组
                //template:'<h3>{{id}}-{{title}}</h3>'//属性组在component内部像data域中的属性一样用
                //当一个组件有多个元素时，必须要有一个根元素将他们包裹在其中
                template:'<div><p>{{title}}</p><p>{{id}}</p></div>'
            });
            var app2 = new Vue({
                el:"#app2",
                data:{
                    posts:[
                        {id:1,title:'title1'},{id:2,title:'title2'},{id:3,title:'title3'}
                    ]
                }
            });
        </script>
    </body>
</html>
```

