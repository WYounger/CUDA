##### 箭头函数
###### 箭头函数基本语法

```javascript
(参数1,参数2,...,参数N) => { 函数声明 }

//相当于: (参数1,参数2,...,参数N) => { return 表达式; }
(参数1,参数2,...,参数N) => 表达式 (单一)

//当只有一个参数时，圆括号是可选的
(单一参数) => { 函数声明 }
单一参数 => { 函数声明 }

// 没有参数的函数应写成一对圆括号
() => { 函数声明 }
```
###### 箭头函数与普通函数的区别

1. 首先，语法更加简洁
2. 没有this,arguments,super或new.target
3. 适合用于那些本来需要匿名函数的地方，并且不能用作构造函数

##### promise

```javascript
        var promise = new Promise( (resolve,reject) => {
        //执行异步操作
        //没有异步操作，在主线程中执行
        console.log("new promise");
        var value = 'i come from promise'
        //如果业务逻辑执行成功,value为返回值
        // resolve(value);
        //如果业务逻辑执行失败,发出错误详情
        reject(new error("出错了"));

        //resolve():使promise状态 pending => fulfilled
        //resolve():使promise状态 pending => rejected 
        });
      //异步执行回调函数,第二个回调函数可以没有
      //第一个函数是promise状态为fulfilled，执行的回调函数
      //第二个函数是promise状态为rejected，执行的回调函数
      promise.then(
        //promise状态为fulfilled传出的值
        value => {
        console.log(value)
      },//error为promise状态为rejected传出的值
      error => {
        console.log(error)
      });
      //主线程
      console.log("out");
```
##### array.filter

`array.filter(谓词)`
谓词:一个返回`true|false`的函数
多层过滤:`array.filter(谓词1).filter(谓词2)`
谓词参数:array中的`元素`和`index`，其中每个元素都被传入谓词中进行判断

```javascript      
      axios.get('/getUsers').then( response => {
        var users_temp = response.data.users;
        this.users = users_temp.filter( user => user.age >= 20)
      })
```

##### setTimeout

指定某个函数或代码，在多少**毫秒**后之后执行

```javascript
var timerId = setTimeout(function|code,delay[,args])
/*
ps: function|code: 函数或一段用'' 包裹的代码
		delay: 时延/ms
		args: functon的参数
*/
```

##### web storage

sessionStorage:	浏览器一次打开到关闭

localStorage: 浏览器关闭到打开一直存在

```javascript
// 获取
var aValue = storage.getItem(keyName)//keyName如果不存在，返回null
// 设置
storage.setItem(keyName, keyValue)
// 删除
storage.removeItem(keyName)
```
javascript正则表达式
```javascript
const reg = /pattern/flags

//flags:
//      g: 全局匹配，查找所偶匹配而非在找到一个匹配后停止
//      i:不区分大小写
```

| 方法      | 描述                                                         |
| --------- | ------------------------------------------------------------ |
| `exec`    | 一个在字符串中执行查找匹配的RegExp方法，它返回一个数组（未匹配到则返回null）。 |
| `test`    | 一个在字符串中测试是否匹配的RegExp方法，它返回true或false。  |
| `match`   | 一个在字符串中执行查找匹配的String方法，它返回一个数组或者在未匹配到时返回null。 |
| `search`  | 一个在字符串中测试匹配的String方法，它返回匹配到的位置索引，或者在失败时返回-1。 |
| `replace` | 一个在字符串中执行查找匹配的String方法，并且使用替换字符串替换掉匹配到的子字符串。 |
| `split`   | 一个使用正则表达式或者一个固定字符串分隔一个字符串，并将分隔后的子字符串存储到数组中的String方法。 |

| 字符     | 含义                                                         |
| :------- | ------------------------------------------------------------ |
| `\`      | 对特殊字符进行转义，使之变为普通字符                         |
| `^`      | 开头                                                         |
| `$`      | 结尾                                                         |
| `*`      | 匹配前一个表达式0次或多次。等价于 {0,}                       |
| `+`      | 匹配前面一个表达式1次或者多次。等价于 {1,}                   |
| `.`      | （小数点）匹配除换行符之外的任何单个字符                     |
| `{n}`    | n是一个正整数，匹配了前面一个字符刚好发生了n次               |
| `{m,n}`  | n 和 m 都是整数。匹配前面的字符至少n次，最多m次              |
| `[a-z]`  | 一个字符集合。匹配方括号中的任意字符,也可用`-`               |
| `[^a-z]` | 一个反向字符集。也就是说， 它匹配任何没有包含在方括号中的字符 |
| `\d`     | 匹配一个数字，等价于`[0-9]`                                  |
| `\D`     | 等价于`[^0-9]`                                               |
| \w       | 等价于 [A-Za-z0-9_]                                          |



参考连接](<https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API#Feature-detecting_localStorage>)

[^0-9]: 