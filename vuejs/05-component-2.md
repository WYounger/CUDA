index.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>test</title>
  </head>
  <body>
    <div id="app"></div>
    <!-- built files will be auto injected -->
    <h1>index.html</h1>
  </body>
</html>

```

main.js

```javascript
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'//引入vue库
import App from './App'//引入App.vue

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({//实例化Vue
  el: '#app',
  components: { App },//注入组件
  template: '<App/>'
})

```

App.vue

```vue
<template>
  <div id="app">
    <h1>App.vue</h1>
    <users v-bind:users="users"/><!--使用user组件,通过属性实现父组件向子组件传值-->
    <users v-bind:users="users"/><!--使用user组件,通过属性实现父组件向子组件传值-->
  </div>
</template>

<script>
import Users from './components/Users'//引入组件
export default {
  name: 'App',
  components: {
    'users':Users//注册组件:'customerName':Component
  },
  data(){
    return{
      users:[//在父组件内定义数据，使得可以在子组件共享数据
        {name:'Bob1',age:201,position:'武汉1'},
        {name:'Bob2',age:202,position:'武汉2'},
        {name:'Bob3',age:203,position:'武汉3'}
        ]
    }
  }
}
</script>
//样式
<style scoped>
h1{
  color: red;
}
</style>

```

Uers.vue

```vue
<template>
    <div class="users">
        <h1>Users.vue</h1>
        <ul>
            <li v-for="user in users" v-bind:key="user.name">
                <h3>{{user.name}}</h3>
                <h4>{{user.age}}</h4>
                <h4>{{user.position}}</h4>
            </li>
        </ul>
        <button v-on:click="deleteUser">删除一个user</button>
    </div>
</template>

<script>
export default {
    name:'users',
    props:{
        users:{//定义一个属性，来接收父组件的数据
            type:Array,
            required:true
        }
    },
    data(){
        return{
            //users:['Bob','Cindy','Henry']//组件数据
        }
    },
    methods:{
        deleteUser:function(){
            this.users.pop();
        }
    }
}
</script>

<!--scoped:让该样式仅用于本组件内,不对其他组件和元素产生影响,不做标注的话会组建嵌套时间会发生覆盖-->
<style scoped>
h1{
    color:purple;
}
</style>
```

**组件的使用步骤** 

```在</sript>中引入--->在components中注册--->在模板使用```

```vue
<script>
  import Component from 'path/Component'//引入
  export default{
    components:{
      'component':Component//注册
    }
  }
</script>

<template>
	<component/><!--使用-->
</template>
```



**样式的使用**

为了让本组件的样式仅用于本组件，需要在`<style>`中是使用`scoped`标注。不标注的话，本组件的样式有可能覆盖其他组件的样式

**父组件向子组件传数据**

只要通过子组件的属性来向子组件来传递值

1. 在父组件中使用子组件

`<subComponent v-bind:subProperty="fatherData"/>`

2. 子组件中定义属性

```json
props:{
  property:{
    type:Type,
    required:true/false
	}
}
```

3. 在子组件使用`pros`

   组件中的`pros`可以像`new Vue(data:...)`中`data`一样在组件的`<template>`和`<script>`使用

**值传递和引用传递**

要注意传递值过程中传递的是`值`还是`引用`,传递`值`则组件之间的数据不是共享的，各个组件之间支持有`值的副本`；而传递`引用`,各个组件之间的共享一个实例对象，一个组件把对象状态改变了，其他组件的也会收到影响