##### 安装

```sh
npm install vuex --save
```

##### 状态管理

![](https://vuex.vuejs.org/vuex.png)

**子组件之间共享状态**

store 看成一个容器，包含大部分状态

与全局变量的不同

1. 响应式
2. 不直接修改store中的状态，必须通过mutations显示提交

1. ##### 基本使用

   在根实例中引入`store`,自动注入到所有子组件中，然后子组件中通过计算属性来访问

```javascript
//通过计算属性返回
computed:{
  //当store.state.count变化时，都会重新求取计算属性，并触发更新DOM操作
	count(){
  	return this.$store.state.count;
  }
}
```

##### 2.mutation

```javascript
//store.js
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)
export default new Vuex.Store({
    state:{//定义state
        count:0
    },
    mutations:{//定义mutations，改变state
        increment(state){
            state.count++;
        }
    }
})

//main.js
//引入store
import store from '@/store/store.js'
new Vue({
  //配置store,其他配置省略...
  store
})

// A.vue
<template>
    <div id="A">
        <h1>{{count}}</h1>
        <button @click="clickMe" >加一</button>
    </div>
</template>
<script>
export default {
		//将计算属性与store.state绑定起来
    computed:{
        count(){
          //返回store.state
          return this.$store.state.count;
        }
    },
    methods:{
        clickMe(){
           //通过commit调用mutation中的increment方法，来改变store.state
            this.$store.commit('increment');
        }
    }
}
</script>
```

```javascript
/* 给mutation中的函数添加其他参数 */
// 定义
mutations: {
  increment (state, payload) {//payload可以是对象
    state.count += payload.amount
  }
}
//调用
store.commit('increment', {
  amount: 10
})
```

**Mutation 必须是同步函数**

##### 3.action

由于mutations只能包含同步函数，所以诞生了action来解决异步函数的问题

#### 4.模块化

mapState
```javascript
import { mapState } from 'vuex'


computed:{
	localComputed(){/* ... */},
  ...mapState([
    'count' // this.count ===> this.$store.state.count
  ])
}

//以上代码转换为
computed:{
 localComputed(){/* .. */},
  count(){
    return this.$store.state.count
  }
}
```

mapMutations
```javascript
import { mapMutations } from 'vuex'

methods:{
  ...mapMutations([
    'increment', //this.increment() ===> this.$store.commit('increment')
    // playload
    'incrementBy' //this.incrementBy(amount) ===> this.$store.commit('incrementBy',amount)
  ])
}
```



参考链接: [官方文档](<https://vuex.vuejs.org/zh/>)



