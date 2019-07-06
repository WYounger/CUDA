```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <script src="vue.js"></script><!--直接引入vue.js库-->
        <title>vue.js-1</title>
    </head>
    <body>
        <div id="app">
            <!--插值表达式-->
            <p>name:{{name}}</p><!--获取该节点内data域中属性name的值-->
            <p>age:{{age}}</p>
            <p>greep:{{greep('night')}}</p><!--使用该节点内的methods中greep()函数的返回值-->

            <!--属性值绑定，v-bind-->
            <!--将标签属性绑定到data域中的属性website-->
            <a v-bind:href="website">bing</a>

            <!--事件绑定,v-on-->
            <!--将button的click触发事件绑定到methods中的add()函数 -->
            <button v-on:click="add">age++</button>
            <!--直接实现click触发事件函数,可以直接使用data域中的属性值-->
            <button v-on:click="age --">age--</button></br>

            <!--数据的双向绑定-->
            <!--第一种通过键盘事件来实现-->
            address:<input ref="address" type="text" v-on:keyup="inputAddress"><!--绑定键盘事件-->
            <span>{{address}}</span><br>
            <!--第二种通过v-model来实现-->
            hobby:<input type="text" v-model="hobby"> <!--通过v-model,将键盘输入值绑定到data中的hobby-->
            <span>{{hobby}}</span>
        </div>

        
        <script>
            var app = new Vue({
                el:"#app",//根节点元素
                data:{//根节点内的数据
                    name:'wangyang',//自定义属性和值
                    age:20,
                    website:'https://cn.bing.com/',
                    address:'',//初始化为空
                    hobby:'null'
                },
                methods:{//节点内的方法
                    greep:function(time){
                        return 'good'+time;
                    },
                    add:function(){
                        this.age++;//直接获取data域中age的值，并操作，同时可以更新页面该的值的显示
                    },
                  
                    inputAddress:function(){
                        this.address = this.$refs.address.value;
                    }
                    
                }
            })
        </script>
    </body>
    <!--参考: 
						https://cn.vuejs.org/v2/guide/index.html
		-->
</html>


```

