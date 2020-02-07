#### 1.let,var,const

1. 用let声明的变量的作用域仅在他的定义块中

2. 而用var声明的变量为作用域为全局

3. const定义的变量分为两种情况

对于用const定义的基本数据类型变量，该变量存储的的是基本类型的值，不能被改变。

对于用const定义的对象变量，该变量存储的是对象的地址，始终不能被改变，而该地址指向的对象是可以改变的

#### 2.变量解析结构

1. 对象的解构

   ```javascript
   //两边的属性必须一致而顺序随意，没有赋值成功的变量的值为undefined
   let {a,b} = {a:'a',b:'b'}
   //设置默认值
   let {a='a',b,c} = {b='b',c='c'}  //a='a',b='b',c='c'
   ```

#### 3.字符串模板语法

```javascript
let a = 'a'
`${a}bc` //输出abc
```

#### 4.promise

```javascript
//promise
const promise = new Promise((resolve,reject) => {
    //some code here
    if(异步操作成功){
        resolve(reuslt) //将Promise的状态由pending改变为fulfiled,并将result传递出去
    }else{
        reject(error)//将Promise的状态由pending改变为rejected，并将error传递出去
    }
})

//then
promise.then(
    result => {  //promise状态为fulfiled的回调函数,接受resolve传递过来的值
        //some code here
    },
    error => { //promise状态为rejected的回调函数,接受reject传递过来的值;通常省略,让catch函数捕获
         //some code here
    }
)

//catch
promise.then(
    result=>{},
    error=>{})
    s.catch(error => {})//捕获promise异常和promise状态rejected

//promise通常写法
promise.then(result => {/**/})
	  .catch(error => {/**/})
	  .finally(() => {/**/})//finally的回调函数没有参数,与promise状态无关
```

#### 5.遍历对象

```javascript
for(let attr in object){
    console.log(object[attr])
}
```

#### 6.手动触发一个标签的事件

```javascript
document.querySelector('input').click()
```

[参考文档](https://www.runoob.com/jsref/met-document-queryselector.html)









