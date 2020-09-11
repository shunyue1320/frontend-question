# JavaScript 题库

### 9. Cookie 有哪些属性？其中Secure，httpOnly 分别有什么作用？如何使用原生 node.js 操作 cookie？ 
```js
cookie属性包括：name、value、expires、domain、path、secure、HttpOnly

//Expires属性：
Expires是cookie失效日期Expires必须是 GMT 格式的时间 （可以通过 new Date().toGMTString()或者 new Date().toUTCString() 来获得）

//Domain是域名，Path是路径，两者加起来就构成了 URL:
Domain和Path一起来限制 cookie 能被哪些 URL 访问。
即请求的URL是Domain或其子域、且URL的路径是Path或子路径，则都可以访问该cookie。

//Secure属性: 
设置Secure属性意味着保持Cookie通信只限于加密传输
//HttpOnly属性: 
HttpOnly属性指示浏览器不要在除HTTP（和 HTTPS)请求之外暴露Cookie
一个有HttpOnly属性的Cookie，不能通过非HTTP方式来访问，(例如，引用 document.cookie）

//原生node获取cookie：
var cookies = {};
req.headers.cookie && req.headers.cookie.split(';').forEach(function( Cookie ) {
    var parts = Cookie.split('=')
    cookies[ parts[ 0 ].trim() ] = ( parts[ 1 ] || '' ).trim()
})
console.log(cookies)

//原生node设置cookie:
 res.writeHead(200, {
  'Set-Cookie': "userId=828; userName=hulk; expire="+date.toGMTString(),
  'Content-Type': 'text/html'
});

//使用 cookie-parser 库操作 cookie:
var express=require("express")
var cookieParser = require('cookie-parser')
var app=express()
app.use(cookieParser())  //使用cookie必须引入cookieParser中间件
...
res.cookie("add",adds,{maxAge: 900000, httpOnly: true}) //设置
res.send("cookie-add:"+req.cookies.add)                 //获取
```


### 8. 下列输出结果是什么
```js
function Foo(){
 getName = function(){ console.log(1) }
 return this
}
Foo.getName = function(){ console.log(2) }
Foo.prototype.getName = function(){ console.log(3) }
var getName = function(){ console.log(4) }
function getName(){ console.log(5) }

//输出结果
Foo.getName()                              //2 (先找本身再找prototype方法)
getName()                                  //4 (预编译function getName先执行,赋值后执行)
Foo().getName()                            //1
getName()                                  //1 
new Foo.getName()                          //2
new Foo().getName()                        //3 (实例化方法继承prototype)
new new Foo().getName()                    //3
```

### 7. 求多个数组之间的交集
```js
function getArrIntersect(...args) {
  if (args.length == null) return []
  if (args.length == 1) return args[0]

  return args.reduce((IntersectArr, currentArr) => {
    // 返回交集数组内存在于当前数组内的子项
    return IntersectArr.filter(item => currentArr.includes(item))
  })
}

getArrIntersect([1,2,3], [2,3,4]) //[2,3]
```

### 6.将'10000000'形式的字符串，以每3位进行分隔展示'10.000.000'
```js
// 德国以 . 分割金钱, 转到德国当地格式化方案即可
Number('10000000').toLocaleString('de-DE')       //10.000.000
//标准以' , ' 分割
Number('10000000').toLocaleString()              //10,000,000

//字符串数字间 . 分割
'10000000abc10000000'.replace(/\B(?=(\d{3})+(?!\d))/g, '.') //"10.000.000abc10.000.000"

//纯数字间 . 分割
'10000000'.replace(/(\d)(?=(\d{3})+\b)/g, '$1.') //10,000,000
```

### 5. 用最简洁代码实现 indexOf 方法
```js
//方法一，正则
function indexOf(str, target, start = 0) {
  if (start < 0) start += str.length
  if (start >= str.length) return -1

  const reg = new RegExp(`${target}`, 'g')
  reg.lastIndex = start
  const result = reg.exec(str)
  return result ? result.index : -1
}

//方法二， for遍历
function indexOf(str, target, start = 0) {
  if (start < 0) start += str.length
  if (start >= str.length) return -1

  for (let i = start; i < str.length; i++) {
    if (str.slice(i, i + target.length) == target) return i
  }
  return -1
}

//提示：new RegExp(需要匹配的内容, 匹配的模式)
// 'g' 全局匹配 'gi' 全局匹配且忽略大小写
```

### 4.实现一个 normalize 函数，进行如下功能转化
示例一: `'abc'  ⇨  {value: 'abc'}`
示例二：`'[abc[bcd[def]]]'  ⇨  {value: 'abc', children: {value: 'bcd', children: {value: 'def'}}}`
```js
const normalize = (str) => {
  var result = {}
  str.split(/[\[\]]/g).filter(Boolean).reduce((obj, item, index, that) => {
    obj.value = item
    if(index !== that.length -1) {
      return (obj.children = {})
    }
  }, result)
  return result
}

//提示：filter(Boolean) 是" filter((v) => { return Boolean(v); }) "的缩写
//reduce(计算后的返回值, 当前元素, 当前索引, 当前元素所属的数组对象)
```

### 3. 模拟实现 Array.prototype.splice 方法
```js
Array.prototype._splice = function (start, deleteCount, ...addArray) {
  if (start < 0) {
    start += this.length
    if (start < 0) start = 0
  } else if (start > this.length) {
    start = this.length
  }

  if (typeof deleteCount === 'undefined') {
    deleteCount = this.length - start
  }

  const afterArray = this.slice(start + deleteCount)
  const removeArray = this.slice(start, start + deleteCount)

  let index = start
  addArray.concat(afterArray).forEach((vlaue) => {
    this[index] = vlaue
    index++
  })
  this.length = index

  return removeArray
}
```

### 2. 实现 Promise.retry，成功后 resolve 结果，失败后重试，尝试超过一定次数才真正的 reject
```js
//默认失败重试5次
Promise.retry = (fun, limit = 5) => {
  return new Promise((resolve, reject) => {
    let _num = 1
    let retryFn = async () => {
      try{
        const data = await fun()
        resolve(data)
      } catch (e) {
        _num++ >= limit ? reject(e) : retryFn()
      }
    }
    retryFn()
  })
}

let k = 1
function requestData() {
  return new Promise((resolve, reject) => {
    if (k++ > 2) {
      resolve()
      console.log('成功获取到数据')
    } else {
      console.log('数据获取失败！')
      reject()
    }
  })
}

Promise.retry(requestData)
// 数据获取失败！
// 数据获取失败！
// 成功获取到数据
```

#### 1. 每隔 1s 输出一个结果：
```js
let list = [1, 2, 3, 4]
let square = (num) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log(num)
      resolve()
    }, 1000)
  })
}

//1. async await 方法
async function test1() {
  for (let i = 0; i < list.length; i++) {
    let num = list[i]
    await square(num)
  }
}
test1()

//2. for of 方法  （串行执行）
async function test2() {
  for (let num of list) {
    const res = await square(num)
  }
}
test2()

//2. promise then 方法
let promise = Promise.resolve()
function test3(i = 0) {
  if (i === list.length) return
  promise = promise.then(() => square(list[i]))
  test3(i + 1)
}
test3()

//提示： forEach是并行执行，并非串行执行，所以无法使用
```

