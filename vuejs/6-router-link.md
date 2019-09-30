​		安装

```sh
npm install vue-router
```

src/router/index.js

```javascript
//引入vue,vue-vouter
import Vue from 'vue'
import VueRouter from 'vue-router'

//引入路由到的其他组件
import Hello from './../components/Hello'
import Home from './../components/Home'

//注册router
Vue.use(VueRouter)

//配置router
export default new VueRouter({//必须要导出,以便在main.js使用,从而能够模块化
    routes:[
        {path:'/',component:Home},//路径-->component
        {path:'/hello',component:Hello}
    ],
    mode:'history'//消除'#'
})

/**
 * 注意export和export defalut区别
 * 1. export可以导出多个对象，而export default只能导出一个对象
 * 2. 在导入时,export用{obj1,obj2...},export default用{obj}接收
 * 参考连接:https://www.jianshu.com/p/c359b7d35107
 */
```

main.js

```javascript
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import App from './App'
import router from './router'//引入路由配置index.js,名字必须为router,以便在Vue中使用

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  router,//必须使用router,或完整:router:router
  el: '#app',
  components: { App },
  template: '<App/>'
})
```

App.vue

```vue
<template>
  <div id="app">
    <p>{{name}}</p>
    <li><router-link to="/">home</router-link></li><!--组件链接-->
    <li><router-link to="/hello">Hello</router-link></li>
    <router-view></router-view><!--显示组件类容-->
  </div>
<!--
router-link两种常用方法
<router-link to="/path">home</router-link>
<router-link :to="{name:'pathName'}">home</router-link> //绑定一个路由记录
-->
</template>


```

Home.vue

```vue
<template>
  <div id="app">
    <p>{{name}}</p>
  </div>
</template>

<script>
export default {
  data(){
    return {
       name:'app.vue',
       
    }
  },
  components:{
     
  }
}
</script>

<style>

</style>
```

Hello.vue

```vue
<template>
    <div id="hello">
        <p>{{name}}</p>
    </div>
</template>

<script>
export default {
    data(){
        return {
            name :'hello.vue'
        }
    }
}
</script>

<style>

</style>
```

