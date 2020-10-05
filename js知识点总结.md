# JavaScript 题库

### 21. 用setTimeout和clearTimeout实现setInterval与clearInterval
```js
//1. setTimeout 实现一个简单版本的 setInterval
let timeMap = {}
let id = 0                               // 简单实现id唯一
const mySetInterval = (cb, time) => {
  let timeId = id                        // 将当前 mySetInterval 的 timeId 赋予 id
  id++                                   // id 自增实现唯一id 可实现多个 mySetInterval 同时使用
  let fn = () => {
    cb()
    timeMap[timeId] = setTimeout(() => {
      fn()
    }, time)
  }
  timeMap[timeId] = setTimeout(fn, time)
  return timeId                          // 返回timeId 清楚该定时器时可通过 timeMap[timeId] 清楚该 setTimeout
}


//2. 使用 clearTimeout 实现 clearInterval (id 为 mySetInterval 返回的 timeId)
const myClearInterval = (id) => {
  clearTimeout(timeMap[id]) // 通过timeMap[id]获取真正的id
  delete timeMap[id]
}
```

### 20. 请简述浏览器解析，加载页面的过程:
```js
1. DNS 查询
2. TCP 连接
3. HTTP 请求即响应
4. 服务器响应
5. 客户端渲染

客户端渲染：
  1. 处理 HTML 标记并构建 DOM 树。
  2. 处理 CSS 标记并构建 CSSOM 树。
  3. 将 DOM 与 CSSOM 合并成一个渲染树。
  4. 根据渲染树来布局，以计算每个节点的几何信息。
  5. 将各个节点绘制到屏幕上。

详情： https://juejin.im/entry/6844903503609987080
```

### 18. document load 和 document ready 的区别：
```js
1.load是当页面所有资源全部加载完成后（包括DOM文档树，css文件，js文件，图片资源等），执行一个函数

问题：如果图片资源较多，加载时间较长，onload后等待执行的函数需要等待较长时间，所以一些效果可能受到影响

2.$(document).ready()是当DOM文档树加载完成后执行一个函数 （不包含图片，css等）所以会比load较快执行

在原生的js中不包括ready()这个方法，只有load方法也就是onload事件

详情： https://juejin.im/post/6844903976568094727
```

### 17. js如何自定义事件：
```js
js如何自定义事件: Event() 与 CustomEvent()
区别:
Event() 适合创建简单的自定义事件，而 CustomEvent() 支持参数传递的自定义事件，它支持 detail 参数，作为事件中需要被传递的数据，并在 EventListener 获取。

//Event:
// 创建一个支持冒泡且不能被取消的 pingan 事件
let myEvent = new Event("pingan", {"bubbles":true, "cancelable":false});
//添加适当的事件监听器
window.addEventListener('build', function (e) { ... }, false);
//派发事件
document.dispatchEvent(myEvent);
// 事件可以在任何元素触发，不仅仅是document
testDOM.dispatchEvent(myEvent);


//CustomEvent:
// 创建事件
let myEvent = new CustomEvent("pingan", {
	detail: { name: "wangpingan" }
});
// 添加适当的事件监听器
window.addEventListener("pingan", e => {
	alert(`pingan事件触发，是 ${e.detail.name} 触发。`)
})
document.getElementById("app").addEventListener("click", function () {
    // 派发事件
		window.dispatchEvent(myEvent);
  }
)
```

### 16. `<div><p>123</p><p>456</p></div>` 匹配出所有 p 中的内容 输出[123, 456]：
```js
let html = '<div><p>123</p><p>456</p></div>'
html.match(/[^>]+(?=<\/p>)/img)          //[123, 456]
```

### 15. call bind apply区别：
```js
相同点：都是改变函数体内部 this 的指向
不同点：
  1.call( thisValue , arg1, arg2, ... )   //如果call方法没有参数，或者参数为null或undefined，则等同于指向全局对象。
  //手写call:
  Function.prototype.myCall = function(that) {
    that = that || window
    that.fn = this
    const args = [...arguments].slice(1)
    const result = that.fn(...args)
    delete that.fn
    return result
  }

  2.apply( thisValue , [arg1, arg2, ...] ) //与call唯一的区别是接收的第二个参数是数组
  //手写apply:
  Function.prototype.myApply = function(that) {
    that = that || window
    that.fn = this
    let restult
    if (arguments[1]) {
      restult = that.fn(arguments[1])
    } else {
      restult = that.fn()
    }
    delete that.fn
    return restult
  }
  示例：
  var a = [10, 2, 4, 15, 9]
	Math.max.apply(Math, a)    //15
	Math.min.apply(null, a)    //2

  3.bind( thisArg[, arg1[, arg2[, ...]]])   //传参结合call与apply两种方法，bind改变this后并不直接执行，而是返回对函数的引用
  //手写bind:
  Function.prototype.myBind = function() {
    let _this = this
    let that = [].shift.call(arguments)
    let args = [...arguments]
    return function () {
      return _this.apply(that, args.concat([...arguments]))
    }
  }

提示：arguments是类数组对象,存在length与callee属性
作用：arguments可以[...arguments]解构, 参数不定长, 函数柯里化, 递归调用, 函数重载
详情：https://github.com/mqyqingfeng/Blog/issues/14
```

### 14. var let const 的区别：
```js
/*** 1. 变量提升 ***/
console.log(a); //正常运行，控制台输出 undefined
var a = 1;

console.log(b); //报错，Uncaught ReferenceError: b is not defined
let b = 1;

console.log(c); //报错，Uncaught ReferenceError: c is not defined
const c = 1;

/*** 2. 暂时性死区 ***/
//原因： 存在全局变量 tmp，但是块级作用域内 let 又声明了一个 tmp变量，导致后者被绑定在这个块级作用域中，所以在 let 声明变量前，对 tmp 赋值就报错了
var tmp = 123;
if (true) {
	tmp = 'abc';  //报错，Uncaught ReferenceError: tmp is not defined
	let tmp;
}


/*** 3.特性 ***/
let const 不可重复   |  var 可以重复声明
let const 块级作用域 |  var 全局作用域
const 必须设置初始值
//案例一：
for(var i = 0;i < 5;i++){
  setTimeout(()=>{
    console.log(i)  //输出5个5      原因：var变量提升至全局作用域，for循环后全局作用域i = 5
  },i * 1000)
}
//案例二：
for(var i = 0;i < 5;i++){
  setTimeout(()=>{
    console.log(i)  //输出0 1 2 3 4 原因：let变量没有提升每个块级作用域里面的 i 独立存在
  },i * 1000)
}
```

### 13. 没有 prototype 的两种函数：
```js
//1.箭头函数：
let a = () => {}
console.log(a.prototype) //undefined

//2.bind执行函数：
let a = function() {}
let b = a.bind(this)
console.log(b.prototype) //undefined
```

### 12. Array.forEach()与 Array.map()的区别，Array.slice()与 Array.splice()的区别
```js
forEach: 遍历 并发                   |  map:     修改数据 返回数组
slice:   返回截取数组 不改变原始数组  |  splice:  返回截取数组  改变原始数组 可插入值
```

### 11. 写出以下代码运行后的输出
```js
 setTimeout(function(){
 console.log(1)
 })
 new Promise(function(resolve,reject){
 console.log(2);
 resolve(3)
 }).then(function(val){
 console.log(val)
 })
 console.log(4)

 // 2 4 3 1
```

### 10. 用多种方法 JAVAScript 实现继承。 
```js
//1. 原型链继承       (缺点：多个实例对引用类型的操作会被篡改)
SubType.prototype = new SuperType()
new SubType()


//2. 借用构造函数继承 (缺点：只能继承父类的实例属性和方法，不能继承原型属性/方法, 无法实现复用，每个子类都有父类实例函数的副本，影响性能)
function A() {
    this.name='小明'
}
function B() {
    A.call(this)                //继承自A
}
var AB = new B()


//3. 组合继承        (缺点：原型存在两份属性/方法)
(借用构造函数继承 与 原型链继承 的结合)


//4. 原型式继承      (缺点：浅拷贝 易篡改)
function object(obj){
  function F(){}
  F.prototype = obj
  return new F()
}


//5. 寄生式继承      (缺点：无法传递参数 易篡改)
function createAnother(original){
  var clone = object(original)  // 通过调用 object() 函数创建一个新对象
  clone.sayHi = function(){     // 以某种方式来增强对象
    alert("hi")
  };
  return clone;                 // 返回这个对象
}


//6. 寄生组合式继承 (结合借用构造函数传递参数和寄生模式实现继承, 这是最成熟的方法，也是现在库实现的方法)
function inheritPrototype(subType, superType){
  var prototype = Object.create(superType.prototype) // 创建对象，创建父类原型的一个副本
  prototype.constructor = subType                    // 增强对象，弥补因重写原型而失去的默认的constructor 属性
  subType.prototype = prototype                      // 指定对象，将新创建的对象赋值给子类的原型
}

function SuperType(name){                            //父类初始化实例属性和原型属性
  this.name = name
  this.colors = ["red", "blue", "green"]
}
SuperType.prototype.sayName = function(){
  alert(this.name)
}

function SubType(name, age){                         //借用构造函数传递增强子类实例属性（支持传参和避免篡改）
  SuperType.call(this, name)
  this.age = age
}

inheritPrototype(SubType, SuperType)                 //将父类原型指向子类

SubType.prototype.sayAge = function(){               //新增子类原型属性
  alert(this.age)
}

var instance1 = new SubType("xyc", 23)
var instance2 = new SubType("lxy", 23)

instance1.colors.push("2")                           // ["red", "blue", "green", "2"]
instance1.colors.push("3")                           // ["red", "blue", "green", "3"]


//7. 混入方式继承多个对象
function MyClass() {
     SuperClass.call(this)
     OtherSuperClass.call(this)
}

MyClass.prototype = Object.create(SuperClass.prototype)     //继承一个类

Object.assign(MyClass.prototype, OtherSuperClass.prototype) //混合其它

MyClass.prototype.constructor = MyClass                     //重新指定constructor

MyClass.prototype.myMethod = function() {
  // do something
}

//8. ES6类继承extends
class Rectangle {
    constructor(height, width) {
        this.height = height
        this.width = width
    }
    get area() {
        return this.calcArea()
    }
    
    calcArea() {
        return this.height * this.width
    }
}

const rectangle = new Rectangle(10, 20)
console.log(rectangle.area)   //200
/**********继承*********/
class Square extends Rectangle {

  constructor(length) {
    super(length, length)
    this.name = 'Square'      //如果子类中存在构造函数，则需要在使用 this 之前首先调用 super()。
  }

  get area() {
    return this.height * this.width
  }
}

const square = new Square(10)
console.log(square.area)      //100
```

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

