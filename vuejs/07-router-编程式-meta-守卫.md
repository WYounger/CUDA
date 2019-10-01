##### 编程式路由
在vue实例内部可以用`this.$router`访问路由实例
```javascript
//router.push(...)
//等价于<router-link to=".."/>，向history添加一个记录
//几种实现方式
//字符串
this.$router.push('/login')
//命名路由
this.$router.push({name:'home',params:{userName:'young'}})
//url:/login?userName=young
this.$router.push({path:'/login',query:{userName:'young'}})
//注意:如果提供了path,params会被忽略，而query不同

//router.replace(...) 不会向history添加新纪录，而是替换掉当前history记录
//<router-link :to="..." replace>	

//router.go() 前进和后退
this.$router.go(-1) //返回上一步记录
this.$router.go(1) //前进一步
```
##### 全局前置首守卫

```javascript
const router = new VueRouter({...})
router.beforeEach((to,from,next) => {
  /* 
  to:route 目的路由对象，一条路由记录
  from:route 当前导航要离开的路由的对象
  next: function 
        next() 允许通过
        next() 不允许通过，停留在当前路由
        next('path')/next({name:'link'})  重定向到指定的路由
  */ 
})

有一条路由记录: {path:'/t1',component:T1,name:'t1',meta:{title:'t1'}}
to(路由记录)详细信息:
{
    "name":"t1",
    "meta":{
        "login_require":false
    },
    "path":"/t1",
    "hash":"",
    "query":{

    },
    "params":{

    },
    "fullPath":"/t1",
    "matched":[
        {
            "path":"/t1",
            "regex":{
                "keys":[

                ]
            },
            "components":{
                "default":{
                    "staticRenderFns":[

                    ],
                    "_compiled":true,
                    "_scopeId":"data-v-1b44d9ed",
                    "beforeCreate":[
                        null,
                        null
                    ],
                    "__file":"src/components/T1.vue",
                    "beforeDestroy":[
                        null
                    ]
                }
            },
            "instances":{

            },
            "name":"t1",
            "meta":{
                "login_require":false
            },
            "props":{

            }
        }
    ]
}
```
##### 全局后置，钩子

```javascript
router.afterEach((to,from) => {
  /*
  钩子不接受next function
  */
})


```
##### 路由独享守卫

```javascript
const router = new VueRouter({
  routes:[
   {path:'/home',
    component:Home,
    beforeEnter:(to,from,next) => {
      //...
    },
    afterEnter:(to,from,next) => {
      //...
    }
   }
  ]
})
```
##### 组建内守卫

```javascript
const Foo = {
  template:'...'
  beforeRouteEnter(tp,from,next){
    /*
   	 当前守卫执行前，组件实例还未实例化，所以不能获取组件this
    */
  },
  beforeRouteUpdate(to,from,next){
    /* 
    	在当前路由改变，但是该组件被复用时调用
      举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
      由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
      可以访问组件实例 `this`
   */
  },
  beforeRouteLeave(to,from,next){
    /*
    在组件离开该组件对应的路由时调用，当然可以访问组件实例this
    */
    const answer = window.confirm('确认要离开吗?')
    answer ? next() : next(false)
  }
}
```
##### 完整导航解析流程

1. 导航被触发
2. 在失活的组件里调用离开守卫
3. 调用全局的`beforeEach`守卫
4. 在重用组件里调用`beforeRouteUpdate`守卫
5. 在路由配置中调用`beforeEnter`
6. 解析异步路由组件
7. 在被激活的组件里调用`beforeRouteEnter`
8. 调用全局的 `beforeResolve` 守卫 
9. 导航被确认
10. 调用全局的`afterEach`钩子
11. 触发`DOM`更新
12. 用创建好的实例调用 `beforeRouteEnter` 守卫中传给 `next`的回调函数

##### 路由元信息

在定义路由时，给每一个路由添加一个自定义的meta对象，在meta对象中定义一些状态，例如window的titile，权限等

```javascript
{
  path:'/a',
  component:A,
  meta:{
    login_require:false
  }
},
{
  path:'/B',
  component:B,
  meta:{
    login_require:true
  }
}

/* 依据login_require来限定访问 */

router.beforeEach((to,from,next) => {
  if(to.mathed.some(item => item.meta.login_require)){//范文该组件需要登录
    //判断用户是否处于登录状态
    //next()/next('/login'),
  }else{//不需要处于登录状态，直接放行
    next()
  }
})
```

参考链接：[官方文档](<https://router.vuejs.org/zh/>)