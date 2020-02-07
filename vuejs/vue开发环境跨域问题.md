##### config/index.js

```javascript
proxyTable: {
      //代理所有以/api开头的请求到target.可以看做/api <==> target
      '/api': {
          target: 'http://localhost:10900', //后台接口地址(host:port | 域名) 
          changeOrigin: true, //改变源 
          pathRewrite: { 
            '^/api': '' //路径重写 
          } 
        } 
    }
```

##### vue中访问的链接

```vue
//以/api开头
this.axios.post('/api/test').then(...)
```

##### 后台接口

```java
/test
```

##### 代理过程

1. http://localhost:8084/api/test
2. http://localhost:10900/test

##### 打包图片后路径问题

除了将config/index.js中build节点中的修改`asstestPublicPath='./'`,还要修改build/utils.js

```javascript
 // Extract CSS when that option is specified
    // (which is the case during production build)
    if (options.extract) {
      return ExtractTextPlugin.extract({
        use: loaders,
        publicPath:'../../' //添加
        fallback: 'vue-style-loader'
      })
    } else {
      return ['vue-style-loader'].concat(loaders)
    }
```

图片.在assets目录下

```html
<img src="../assets/test.png"/>
```

```css
background: url('../assets/test.png');
```

图片在项目根目录/static下

```html
<img src="../../static/test.png"/>
```

```css
background: url('../../static/test.png')
```

以上两种方法可以这样简单理解为:路径中的第一个`..`代表项目`src`目录