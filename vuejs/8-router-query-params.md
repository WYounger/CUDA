##### $route和$router

$route是一个路由记录对象实例，包括 `name` `fullPath` `meta` `query` `params`等，用来获取路由信息

$router是VueRouter对象实例，用来编程式路由

##### query

###### 路由记录

```javascript
const router = new VueRouter({
    routes:[
        {path:'/testQuery',name:'testQuery',component:TestQuery},
    ],
    mode:'history',
})
```

###### name

```vue
<router-link :to="{name:'testQuery',query:{id:'123',title:'young'}}">t2</router-link>
```

或者编程式导航

```javascript
this.$router.push({name:'testQuery',query:{id:'123',title:'young'}})
```

生成的链接

```
http://localhost:8080/testQuery?id=123&title=young
```

路由参数的获取

```javascript
this.$route.query.id
this.$route.query.title
```

##### path

上面是用name来指定路由路径的，也可以用path来指定

```vue
<router-link :to="{path:'/testQuery',query:{id:'123',title:'young'}}">t2</router-link>
```

编程式导航一样，路由参数的获取一致

##### params

params可以用与query一样的方式来传递参数，但是生成的链接不会包含由路由参数。当用户再次刷新该页面时，传递的路由参数是空的，所以有下面`路径参数绑定`的方式

###### 路由记录

```javascript
const router = new VueRouter({
    routes:[
        {path:'/testParams/:id/:title',name:'testParams',component:TestParams},
    ],
    mode:'history',
})
```

###### path

```vue
<router-link :to="{path:`/testParams/${id}/${title}`}">testParams</router-link>
```

或者

```javascript
this.$router.push({path:`/testParams/${this.id}/${this.title}`})
```

###### name

```vue
<router-link :to="{name:'testParams',params:
                  {id:this.id,title:this.title}}">testParams</router-link>
```

或者

```javascript
this.$router.push({name:"testParams",params:{id:this.id,title:this.title}})
```

###### 获取路由参数

```javascript
this.$router.params.id
this.$router.params.title
```

##### 总结

query: 参数接在路径后面

params: 参数是路径的一部分，符合`restful`风格