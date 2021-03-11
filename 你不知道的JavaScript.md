# 你不知道的 JavaScript


### 手写eventHub:
```js
class EventHub {
  constructor(){
    this.dep = {}
  }
  on(eventName, fn){
    if (!this.dep[eventName]) {
      this.dep[eventName] = []
    }
    this.dep[eventName].push(fn)
  }
  emit(eventName, data) {
    if (!this.dep[eventName]) return
    for(const fn of this.dep[eventName]) {
      fn(data)
    }
  }
  off(eventName, fn){
    const index = this.dep[eventName].findIndex((value) => value === fn)
    if(index === -1) return
    this.dep[eventName].splice(index, 1)
  }
}
```

### 手写promise:
```js
//promise有三个状态：pending fulfilled rejected；
const PENDING = 'PENDING';
const FULFILLED = 'FULFILLED';
const REJECTED = 'REJECTED';

const resolvePromise = (promise2, x, resolve, reject) => {
  // 自己等待自己完成是错误的实现，用一个类型错误，结束掉 promise  Promise/A+ 2.3.1
  if (promise2 === x) { 
    return reject(new TypeError('Chaining cycle detected for promise #<Promise>'))
  }
  // Promise/A+ 2.3.3.3.3 只能调用一次
  let called;
  // 后续的条件要严格判断 保证代码能和别的库一起使用
  if ((typeof x === 'object' && x != null) || typeof x === 'function') { 
    try {
      // 为了判断 resolve 过的就不用再 reject 了（比如 reject 和 resolve 同时调用的时候）  Promise/A+ 2.3.3.1
      let then = x.then;
      if (typeof then === 'function') { 
        // 不要写成 x.then，直接 then.call 就可以了 因为 x.then 会再次取值，Object.defineProperty  Promise/A+ 2.3.3.3
        then.call(x, y => { // 根据 promise 的状态决定是成功还是失败
          if (called) return;
          called = true;
          // 递归解析的过程（因为可能 promise 中还有 promise） Promise/A+ 2.3.3.3.1
          resolvePromise(promise2, y, resolve, reject); 
        }, r => {
          // 只要失败就失败 Promise/A+ 2.3.3.3.2
          if (called) return;
          called = true;
          reject(r);
        });
      } else {
        // 如果 x.then 是个普通值就直接返回 resolve 作为结果  Promise/A+ 2.3.3.4
        resolve(x);
      }
    } catch (e) {
      // Promise/A+ 2.3.3.2
      if (called) return;
      called = true;
      reject(e)
    }
  } else {
    // 如果 x 是个普通值就直接返回 resolve 作为结果  Promise/A+ 2.3.4  
    resolve(x)
  }
}

class Promise {
  constructor(executor) {
    this.status = PENDING;
    this.value = undefined;
    this.reason = undefined;
    this.onResolvedCallbacks = [];
    this.onRejectedCallbacks= [];

    let resolve = (value) => {
      if(this.status ===  PENDING) {
        this.status = FULFILLED;
        this.value = value;
        this.onResolvedCallbacks.forEach(fn=>fn());
      }
    } 

    let reject = (reason) => {
      if(this.status ===  PENDING) {
        this.status = REJECTED;
        this.reason = reason;
        this.onRejectedCallbacks.forEach(fn=>fn());
      }
    }

    try {
      executor(resolve,reject)
    } catch (error) {
      reject(error)
    }
  }

  then(onFulfilled, onRejected) {
    //解决 onFufilled，onRejected 没有传值的问题
    //Promise/A+ 2.2.1 / Promise/A+ 2.2.5 / Promise/A+ 2.2.7.3 / Promise/A+ 2.2.7.4
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v;
    //因为错误的值要让后面访问到，所以这里也要跑出个错误，不然会在之后 then 的 resolve 中捕获
    onRejected = typeof onRejected === 'function' ? onRejected : err => { throw err };
    // 每次调用 then 都返回一个新的 promise  Promise/A+ 2.2.7
    let promise2 = new Promise((resolve, reject) => {
      if (this.status === FULFILLED) {
        //Promise/A+ 2.2.2
        //Promise/A+ 2.2.4 --- setTimeout
        setTimeout(() => {
          try {
            //Promise/A+ 2.2.7.1
            let x = onFulfilled(this.value);
            // x可能是一个proimise
            resolvePromise(promise2, x, resolve, reject);
          } catch (e) {
            //Promise/A+ 2.2.7.2
            reject(e)
          }
        }, 0);
      }

      if (this.status === REJECTED) {
        //Promise/A+ 2.2.3
        setTimeout(() => {
          try {
            let x = onRejected(this.reason);
            resolvePromise(promise2, x, resolve, reject);
          } catch (e) {
            reject(e)
          }
        }, 0);
      }

      if (this.status === PENDING) {
        this.onResolvedCallbacks.push(() => {
          setTimeout(() => {
            try {
              let x = onFulfilled(this.value);
              resolvePromise(promise2, x, resolve, reject);
            } catch (e) {
              reject(e)
            }
          }, 0);
        });

        this.onRejectedCallbacks.push(()=> {
          setTimeout(() => {
            try {
              let x = onRejected(this.reason);
              resolvePromise(promise2, x, resolve, reject)
            } catch (e) {
              reject(e)
            }
          }, 0);
        });
      }
    });
  
    return promise2;
  }
}
```

### 数组扁平化:
```

```
### 图片懒加载的伪代码实现:
```js
// onload是等所有的资源文件加载完毕以后再绑定事件
window.onload = function(){
  var imgs = document.querySelectorAll('img')  // 获取图片列表，即img标签列表
  function getTop(e) { return e.offsetTop }    // 获取到浏览器顶部的距离
  function lazyload(imgs){
    var h = window.innerHeight                 // 可视区域高度
    var s = document.documentElement.scrollTop || document.body.scrollTop // 滚动区域高度
    for(var i=0;i<imgs.length;i++){
      if ((h+s)>getTop(imgs[i])) { // 图片距离顶部的距离大于可视区域和滚动区域之和时懒加载
        (function(i){            // 真实情况是页面开始有2秒空白，所以使用setTimeout定时2s
          setTimeout(function(){
            // 不加立即执行函数i会等于9 隐形加载图片或其他资源， 创建一个临时图片，这个图片在内存中不会到页面上去。实现隐形加载
            var temp = new Image();
            temp.src = imgs[i].getAttribute('data-src');//只会请求一次
            temp.onload = function(){ // onload判断图片加载完毕，真是图片加载完毕，再赋值给dom节点
              imgs[i].src = imgs[i].getAttribute('data-src') // 获取自定义属性data-src，用真图片替换假图片
            }
          },2000)
        })(i)
      }
    }
  }
  lazyload(imgs)
  window.onscroll =function(){ lazyload(imgs) } // 滚屏函数
}
```

### js为什么 0.1+0.2 != 0.3
```
在JavaScript中数字是以IEEE 754双精度64位浮点数来存储的，编程中的浮点数的精度往往都是有限的
解决方法：
  同时乘以10相加后在除以10
```

### 重绘和回流的解释:
```
回流（reflow）/ 重排: 元素的几何属性发生变化（宽和高等）, 导致浏览器渲染树部分失效需重新构建渲染树
重绘（repaint）：元素外貌发生变化 如背景色字体等，导致浏览器重新绘制
重排必定会引发重绘，但重绘不一定会引发重排
如何避免触发回流和重绘：
  合并对Dom的多次修改，减少Dom操作
  避免频繁使用 style，而是采用修改class的方式
  也可以先为元素设置display:none，操作结束后再把它显示出来。因为display在属性为none的元素上进行的DOM操作不会引发回流和重绘
  使用createDocumentFragment进行批量的 DOM 操作。
  对于 resize、scroll 等进行防抖/节流处理
  避免频繁读取会引发回流/重绘的属性，如果确实需要多次使用，就用一个变量缓存起来。
  利用 CSS3 的transform、opacity、filter这些属性可以实现合成的效果，也就是GPU加速。
```

### es6的新特性:
```
1.新的变量声明let和const
2.模板字符串
3.箭头函数
4.函数的参数默认值可以在括号里面直接设置默认值了
5.Spread / Rest 操作符指的是 ...
6.对象和数组解构
7.for...of用于遍历一个迭代器，如数组 , for...in 用来遍历对象中的属性
8.ES6中的类:ES6 中支持 class 语法，不过，ES6的class不是新的对象继承模型，它只是原型链的语法糖表现形式。(在类中定义函数不需要使用 function 关键词)
9.async await
10.promise

```
### 32. 浏览器缓存：
```
通常浏览器缓存策略分为两种："强缓存" 和 "协商缓存"，并且缓存策略都是通过设置 HTTP 请求头 来实现的。
1.	强缓存:
    强缓存不会向服务器发送请求，直接从缓存中读取资源。强缓存可以通过设置两种 HTTP Header 实现：Expires 和 Cache-Control。
    Expires 和 Cache-Control区别：Cache-Control是http1.1的产物，两者同时存在的话，Cache-Control优先级高于Expires；在某些不支持HTTP1.1的环境下，Expires就会发挥用处
2.  协商缓存:
    协商缓存就是强制缓存失效后，浏览器携带缓存标识向服务器发起请求，由服务器根据缓存标识决定是否使用缓存的过程，主要有以下两种情况：
    Last-Modified 和 If-Modified-Since
```

### 31. async 和 await 、promise 的区别:
```
async/await是写异步代码的新方式，以前的方法有回调函数和Promise。　　
async/await是基于Promise实现的，它不能用于普通的回调函数。　　
async/await与Promise一样，是非阻塞的。　　
async/await使得异步代码看起来像同步代码，这正是它的魔力所在。
为什么Async/Await好：
使用async函数可以让代码简洁很多，不需要像Promise一样需要些then，不需要写匿名函数处理Promise的resolve值，也不需要定义多余的data变量，还避免了嵌套代码。　　
错误处理：Async/Await 让 try/catch 可以同时处理同步和异步错误。
```
### 30. 深度克隆：
```js
function deepClone(target) {
  if (typeof target === 'object') {
    const cloneTarget = Array.isArray(target) ? [] : {}
    for (const key in target) {
      cloneTarget[key] = deepClone(target[key])
    }
    return cloneTarget
  } else {
    return target
  }
}
```

### 29. axios请求并发控制逻辑：
```js
// formDatas: 请求数组， 并发请求数：limit
async sendRequest(formDatas, limit = 3) {
  return new Promise((reslove, reject) => {
    const len = formDatas.length
    let counter = 0, isStop = false // counter:总请求数 isStop:请求停止
    function async start() {
      if (isStop) retrun
      const task = formDatas.shift()
      if (task) {
        try {
          await this.$http.post('/***')
          if (counter == len - 1) {
            resolve() // 全部请求成功
          } else {
            counter++
            start() // 一个请求结束继续下一个请求开始
          }
        } catch(error) {
          if (task.error < 3) { 
            task.error++
            formDatas.unshift(task) // 请求失败后将其放回原位
            start() // 重新开始请求
          } else { // 重复请求失败3次终止所有请求
            isStop = true
          }
        }
      }
    }
    while (limit > 0) { // 同时并发limit个请求
      start()
      limit--
    }
  })
}
```

### 28. 函数柯里化：
```js
function curry(fn) {
  let arr = [].slice.call(arguments, 1)
  return function _curry() {
    arr = [...arr, ...arguments]
    if (arr.length >= fn.length) {
      return  fn(...arr)
     } else {
      return _curry
    }
  }
}

function add(a, b, c) {
  return a + b + c
}

const addCurry = curry(add)
addCurry(1)(2)(3)  //6
```
### 27. 取大于17位的Number类型解决精度丢失: (转字符串)
```js
var text = '{ "id":18014398509481985 }';
const id= text.match(/\d{17,}/)[0]; // 正则获取大于17位数字的值
text = text.replace(id,`"${id}"`); // 补上双引号
const data = JSON.parse(text);
```
### 26. defineReactive与porxy原理：
```js
/************ vue 2.x ************/
function observer(value) {
  if (typeof value === 'object' && typeof value !== null) {
    for (key in value) {
      defineReactive(value, key, value[key])
    }
  }
}

function defineReactive(obj, key, value) {
  observer(value)
  Object.defineProperty(obj, key, {
    get(){
      // watcher监听
      return value
    }
    set(newValue) {
      if (value !== newValue) {
        observer(newValue)
        value = value
        //触发watcher更新组件
      }
    }
  })
}
let obj = { aa: { bbb:'b', ccc: 333} }
observer(obj)
console.log(obj)

/************ vue 3 ************/
let handler = {
  get(target, key) {
    if (typeof target[key] == 'object' && target[key] !== null) {
      return new Proxy(target[key], handler)
    }
    return Reflect.get(target, key)
  },
  set(target, key) {
    return Reflect.set(target, key, value)
  }
}
let obj2 = { aa: { bbb:'b', ccc: 333} }
let proxy = new Proxy(obj2, handler)

```
### 25. 跨域解决⽅案：
```js
1. JSONP(JSON with Padding)，前端+后端⽅案，绕过跨域
  前端构造script标签请求指定URL（由script标签发出的GET请求不受同源策略限制），服务器返
  回⼀个函数执⾏语句，该函数名称通常由查询参callback的值决定，函数的参数为服务器返回的
  json数据。该函数在前端执⾏后即可获取数据 

2. 代理服务器
请求同源服务器，通过该服务器转发请求⾄⽬标服务器，得到结果再转发给前端。
前端开发中测试服务器的代理功能就是采⽤的该解决⽅案，但是最终发布上线时如果web应⽤和
接⼝服务器不在⼀起仍会跨域。

3. CORS(Cross Origin Resource Share) - 跨域资源共享，后端⽅案，解决跨域
  cors是w3c规范，真正意义上解决跨域问题。它需要服务器对请求进⾏检查并对响应头做相应处理，
从⽽允许跨域请求。
```

### 24. 事件捕获与冒泡：
```js
事件流程：先捕获 后冒泡
点击标签：捕获/冒泡事件谁先注册谁先触发
```

### 23. webpack中的bundle、module、chunk分别是什么
```js
Bundle:
Bundle是由多个不同的模块生成，bundles 包含了早已经过加载和编译的最终源文件版本。
Bundle 分离（Bundle Splitting）: 这个流程提供了一个优化 build 的方法，允许 webpack 为应用程序生成多个 bundle。最终效果是，当其他某些 bundle 的改动时，彼此独立的另一些 bundle 都可以不受到影响，减少需要重新发布的代码量，因此由客户端重新下载并利用浏览器缓存。

Module: 
相比于一个完整的项目，项目中分散的一个个功能性模块能够提供一个对于程序员来说更加专注的视角。一个编写良好的模块能够形成一个很清晰的抽象结构，保证之后的维护基于此能够变得规范化和开发具有明确性。
Module Resolution（模块解析）: 一个模块可以作为另一个模块的依赖项，即在另一个模块中通过import或者require的方式进行引入。模块解析器是一个代码库，通过这个代码库对引入的模块进行解析。我们可以在resolve.modules自定义设置自己的解析路径，以更好地方便个人脚本在项目中的引入，比如如下代码：

Chunk: 
这个webpack中专用的术语用于管理webpack内部的打包进程。bundle由许多chunk组成，chunk有几种类型，比如说"入口"和"子块"。
通常chunk和输出的bundle一一对应，但是，有些是一对多的关系。
Code Splitting:  它表示将你的代码拆分成多个bundle或chunk，之后你可以按需加载它们，而不是简单地加载一个单独的文件。
Configration:    webpack的配置文件是一段非常普通的javascript代码，它会输出一个对象，然后webpack将会基于对象中的每个属性开始运行。

Asset: 
这个术语主要是用来描述我们通常在web应用或者其他应用中用到的图片、字体文件、音视频，以及其他类型的一些文件。这些资源通常为一个个单独的文件，但在webpack中，我们借助style-loader或者css-loader也能进行适当的管理。

Dependency Graph: 
只要一个文件依赖另一个文件才能有所作为，webpack把这个文件定义为依赖项。webpack会从一个入口点开始，通过递归的方式构建出一个依赖关系图，这里面包括每一个被拆分的小模块，每一个asset。

Entry Point: 
入口点告诉webpack从哪里开始解析，根据构建出来的依赖关系图，从而知道哪些部分将会输出为bundle。

Hot Module Replacement (HMR): 
即热更新，当项目在运行时发生变更、文件新增、文件删除时，整个项目无需全部全局加载。

Loaders: 
应用于项目模块源代码的转换，它将按需对文件进行预处理或“加载”。它类似于一个“task-runner”。

Plugin: 
一个具有apply属性的javascript对象。apply属性通过webpack编译器被调用，以访问整个整个编译生命周期。这些Plugins通常以一种或另一种方式扩展webpack的编译功能。

Target: 
该配置用于指定项目的运行环境（browser、nodejs、electron等），以使webpack编译器以不同的方式进行编译。
```

### 22. 实现一个函数，运算结果可以满足如下预期结果 (函数柯里化)
`Add(1)(2)             //3`
`Add(1，2，3)(10)      //16`
`Add(1)(2)(3)(4))(5)  //15 `
```js
function add(){
	let args = [...arguments]
	let addfun = function(){
		args.push(...arguments)
		return addfun;
	}
	addfun.toString = function(){
		return args.reduce((a,b)=> a + b)
	}
	return addfun
}
```

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
for(let i = 0;i < 5;i++){
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
forEach: 遍历 for可await但forEach不能 |  map:     修改数据 返回数组
slice:   返回截取数组 不改变原始数组   |  splice:  返回截取数组  改变原始数组 可插入值
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

