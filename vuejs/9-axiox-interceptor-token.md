##### 安装

```sh
# 安装 axios
npm install axios
# 安装axios-mock-adapter 模拟请求后端数据
npm install axios-mock-adapter --save-dev
```
##### 引入

```javascript
import axios from 'axios'
import mockAdapter from 'axios-mock-adapter'

Vue.use(axios)
Vue.use(mockAdapter)
```
##### 使用

```javascript
//创建mock实例
let mock = new mockAdapter(axios)

```

##### axios基本语法

```javascript
//get请求，通过params传递请求参数
axios.get('url',{
  params:{
    param1:value1,
    param2:value2
    //...
  }
}).then( response => {  
  //...
}).catch( error => {
  //...
})

//post请求，用第二个参数传递请求参数
axios.post('url',{
  param1:value1,
  param2:value2
  //...
}).then( response => {
  //...
}).catch( error => {
  //...
})

//通过向axios传递相关配置来创建请求 axios(config)

// GET request for remote image
axios({
  method: 'get',
  url: 'http://bit.ly/2mTM3nY',
  responseType: 'stream'
}).then(function (response) {
    response.data.pipe(fs.createWriteStream('ada_lovelace.jpg'))
  });
  
//发送post请求
axios({
  method:'post',
  url: 'url',
  data:{
    param1:value1,
  	param2:value2
  }
}).then( response => {
  //...
})
```

##### response schema

```javascript
{
  data:{},
  status:200,
  statusText:'OK',
  headers:{},
  config:{}
  request:{}
}

//how to use
response.data
response.status
```

##### 拦截请求
```javascript
//拦截请求
axios.interceptors.request.use(
config => {
  //在发送请求之前对请求数据config操作
  return config
},
error => {
	//对错误请求做些什么
  return Promise.reject(error)
}
)

//拦截响应
axios.interceptors.response.use(
resposne => {
  //对响应数据做些什么
  return response
},
error => {
  //对响应错误做些什么
  return Promise.reject(error)
}
)
```
##### token以及路由权限验证
###### token

token主要用来让服务端对客户端进行认认证

1. 客户端登陆成功后，在localStorage或sessionStorage中保存token
2. 在以后的每一次发送请求中，在请求头中设定token，服务端收到请求后，验证token
3. 在接收服务端响应时，要验证Token是否过期，从而判断否要跳转到login路由
```javascript
//1. 发送登录请求,获取Token
this.axios.post('/login',{userName:'young',password:'123'}).then(
response => {
	localStorage.setItem('token',response.data.token)
},
error => {
  console.log('登录失败')
}
).catch(error => {
  console.log('登录失败')
})

//2. 成功获取Token后，将其放在以后每一请求的请求头中，让服务端验证
axios.interceptors.request.use(
 config => {
 	 //首先判断客户端是否有token,若有，则放入请求头;如果没有，则什么也不做
   if(localStorage.token){
      config.headers.Authorization = localStorage.token
   }
   return config;
 },
 err => {
    return Promise.reject(err);
  })
  
  //3. 验证响应中Token是否过期
  axios.interceptors.response.use(
  response => {
  	//此处依据服务端设定token过期的方式来在客户端判断token是否过期
  	if(response.data.tokenTimeOut){ 
    		//路由到login
  			router.replace('/login')
  			console.log('token 已经过期')
  	}
  	return response;
  },
   error => {
    return Promise.reject(error);
  }
  )
```

###### 路由验证
由于路由是在客户端页面跳转，并不涉及对服务器的请求，所以路由验证只是本地验证，限制对路由的访问
1. 首先在需要认证的路由是上，添加权限认证meta
2. 在beforeEach()中做权限校验
```javascript
//1. 设置路由
{
  path:'/login',
  component:Login,
  name:'login'
}，
{
  path:'/home',
  component:Home,
  name:'home',
  meta:{
    requireAuth:true
  }
}
//2.在beforeEach中校验

router.beforeEach({
  (to,from,next) => {
  	//判断是否需要认证
    if(to.meta.requireAuth){
    	//判断是否有token
      if(localStorage.token){
        next()
      }else{
        next({name:'login'})
      }
    }
    //不需要认证，直接放行
    next()
  }
})

```

ps:`spring mvc`在接收`axios`传递的参数问题

1. `axios.get()`，`spring mvc`正常接收参数

2. `axios.post()`,`spring mvc`要使用`@RequestBody`注解。

    	1. 当axios.post()封装的参数可以封装成后台的一个`pojo`,那么就用`@ReqeustBody Pojo pojo`接收
        	2. 当a`xios.post()`封装的参数不可以封装成后台的一个`pojo`，那么就用`@RequestBody Map map`接收，然你后从`map`中强转得到相应的类型值

3. 原因

   1. `axios.get()`是发起`GET`请求，请求参数在`ReqeustHeaders`中，直接拼接到`url`后

   2. axios.post()是发起`POST`请求，请求参数在`ReqeustBody`,

      且`Content-Type:application/json;charset=UTF-8`

参考链接
[Axios中文说明](https://www.kancloud.cn/yunye/axios/234845)
[Mock js中文说明](https://www.kancloud.cn/sophie_u/mockjs/532225)
[Token拦截认证](https://juejin.im/post/5bab739af265da0aa3593177)

