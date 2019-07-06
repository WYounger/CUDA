```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <script src="vue.js"></script>
        <title>vue.js-2</title>
    </head>
    <body>
        <div id="app">
            <a v-bind:href="bing">bing</a><br>
            <span v-if="show">文字</span>
            <button v-on:click="click">显示和隐藏</button>
            <p>ooriginal message:{{message}}</p>
            <p>after reverse:{{reverseMessage}}</p>
            <!--style绑定-->
            <p v-bind:style="styleObject">style</p>
            <!--v-if v-else-if v-else -->
            <div v-if="ok">
                <h1>h1</h1>
                <h1>h1</h1>
                <h1>h1</h1>    
            </div>
            <p v-if="type === 'A'">A</p>
            <p v-else-if="type ==='B'">B</p>
            <p v-else-if="type === 'C'">C</p>
            <p v-else>not A B C</p>
        </div>
        <br>
        <br>
        <div id="app2">
            <p>遍历一个数组</p>
            <ol>
                <li v-for="student in students">{{student.name}}-{{student.age}}</li> 
            </ol>

            <p>遍历一个对象的所有属性</p>
            <ul>
                <li v-for="attr in me">{{attr}}</li>
            </ul>
        </div>
       
        <script>
            var app = new Vue({
                el:"#app",
                data:{
                    bing:'https://cn.bing.com/',
                    show:false,
                    message:'hello',
                    styleObject:{
                        color:'red',
                        fontSize:'20px'
                    },
                    ok:false,
                    type:'D'
                },
                methods:{
                    click:function(){
                        this.show = !this.show;
                        this.ok != this.ok;
                    }
                },
                computed:{//计算属性
                    reverseMessage:function(){
                        return this.message.split('').reverse().join('');
                    }
                }
                //ps：计算属性与method的区别：计算属性是基于它们的响应式依赖进行缓存的
                
            });

            var app2 = new Vue({
                el:'#app2',
                data:{
                    students:[
                        {name:'wangyang1',age:20},
                        {name:'wangyang2',age:21},
                        {name:'wangyang3',age:22}
                    ],
                    me:{
                        name:'wangyang',
                        age:19,
                        address:'武汉'
                    }
                }
            });
        </script>
    </body>
    <!--参考: https://cn.vuejs.org/v2/guide/index.html-->
</html>


```

