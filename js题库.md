# JavaScript 题库

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

//2. for of 方法
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
```

