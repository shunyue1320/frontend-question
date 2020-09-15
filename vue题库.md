# Vue:
## 多选题：
```js
1. Vue 实例的 data 属性，可以在哪些生命周期中获取到？
A. beforeCreate
B. created
C. beforeMount
D. mounted

2. 下列对 Vue 原理的叙述，哪些是正确的？
A. Vue 中的数组变更通知，通过拦截数组操作方法而实现
B. 编译器目标是创建渲染函数，渲染函数执行后将得到 VNode 树
C. 组件内 data 发生变化时会通知其对应 watcher，执行异步更新
D. patching 算法首先进行同层级比较，可能执行的操作是节点的增加、删除和更新


3. 对于 Vue 中响应式数据原理的说法，下列哪项是不正确的？
A. 采用数据劫持方式，即 Object.defineProperty() 劫持 data 中各属性，实现响应式数据
B. 视图中的变化会通过 watcher 更新 data 中的数据
C. 若 data 中某属性多次发生变化，watcher 仅会进入更新队列一次
D. 通过编译过程进行依赖收集

4. 下列说法不正确的是哪项？
A. key 的作用主要是为了高效地更新虚拟 DOM
B. 若指定了组件的 template 选项，render 函数不会执行
C. 使用 vm.$nextTick 可以确保获得 DOM 异步更新的结果
D. 若没有 el 选项，vm.$mount(dom) 可将 Vue 实例挂载于指定元素上

5. 下列关于 Vuex 的描述，不正确的是哪项？
A. Vuex 通过 Vue 实现响应式状态，因此只能用于 Vue
B. Vuex 是一个状态管理模式
C. Vuex 主要用于多视图间状态全局共享与管理
D. 在 Vuex 中改变状态，可以通过 mutations 和 actions

6. 关于 Vue 组件间的参数传递，下列哪项是不正确的？
A. 若子组件给父组件传值，可使用 $emit 方法
B. 祖孙组件之间可以使用 provide 和 inject 方式跨层级相互传值
C. 若子组件使用 $emit('say') 派发事件，父组件可使用 @say 监听
D. 若父组件给子组件传值，子组件可通过 props 接受数据

7. 下列关于 vue-router 的描述，不正确的是哪项？
A. vue-router 的常用模式有 hash 和 history 两种
B. 可通过 addRoutes 方法动态添加路由
C. 可通过 beforeEnter 对单个组件进行路由守卫
D. vue-router 借助 Vue 实现响应式的路由，因此只能用于 Vue

8. 下列说法不正确的是哪项？
A. 可通过 this.$parent 查找当前组件的父组件
B. 可使用 this.$refs 查找命名子组件
C. 可使用 this.$children 按顺序查找当前组件的直接子组件
D. 可使用 $root 查找根组件，并可配合 children 遍历全部组件

9. 下列关于 v-model 的说法，哪项是不正确的？
A. v-model 能实现双向绑定
B. v-model 本质上是语法糖，它负责监听用户的输入事件以更新数据
C. v-model 是内置指令，不能用在自定义组件上
D. 对 input 使用 v-model，实际上是指定其 :value 和 :input

10. 关于 Vue 的生命周期，下列哪项是不正确的？
A. DOM 渲染在 mounted 中就已经完成了
B. Vue 实例从创建到销毁的过程，就是生命周期
C. created 表示完成数据观测、属性和方法的运算和初始化事件，此时 $el 属性还未显示出来
D. 页面首次加载过程中，会依次触发 beforeCreate，created，beforeMount，mounted，beforeUpdate，updated

```

## 问答题：
**1. Vue 中的 computed 和 watch 的区别是什么？**

```js
computed：计算属性
计算属性是由data中的已知值，得到的一个新值。
这个新值只会根据已知值的变化而变化，其他不相关的数据的变化不会影响该新值。
计算属性不在data中，计算属性新值的相关已知值在data中。
别人变化影响我自己。


watch：数据监听器
监听data中数据的变化
监听的数据就是data中的已知值
我的变化影响别人

1.watch擅长处理的场景：一个数据影响多个数据

2.computed擅长处理的场景：一个数据受多个数据影响
```
