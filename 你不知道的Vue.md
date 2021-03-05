# 你不知道的 Vue

## vue 3.0

### vue3.0的新特性：
```
1. 虚拟dom重写 更详细，编译时更好的警告报错反馈
2. 优化slots的生成，减少不必要的重绘
3. 静态树 静态属性的提升，把静态属性标记出来，大大降低静态重复渲染与patch
4. 使用proxy代替defineProperty
5. 体积小：通过摇树优化核心库体机
6. 更容易维护： TypeScript + 模块化
7. 跨平台更遍历：编译器核心和运行时核心与平台无关
8. 更易使用：对 TypeScript 支持，更好的调试，独立的响应化模块，Composition API
```


## vue 2.x

### nextTick作用及原理：
```
1. vue中的异步更新策略导致数据更改后不会立刻改变dom，此时需要第一时间获取更新后的dom则使用nextTick
2. vue中dom更新是利用微任务异步执行的。避免了 wathcher 多次触发带来的重复渲染，
   nextTick 方法在队列末尾添加回调函数确保dom操作完成后调用
3. 在操作dom后想立刻获取或执行dom相关操作就需用到 nextTick 方法
```

### 22. 全局守卫、路由独享守卫、组件内守卫区别：
```
全局守卫：全局性的每次导航都会执行
路由独享守卫：导航与路由相相关时触发
组件内守卫：可获取组件实例组件
```

### 21. vue-router如何保护指定路由安全：
```
1. 通过导航守卫保护路由安全，设置守卫钩子函数判断用户登入状态和权限
2. 有3个层级：全局前置守卫beforeEach，路由独享守卫beforeEach，组件内守卫beforeRouteEnter
   全局守卫：router.beforeEach((to, from, next) => {}) 每次路由导航时执行，判断用户是否可以继续导航 禁止：next(false)，正常：next()，重定向：next(path)
```

### 20. vue组件通信方式：
```
props、$emit/$on、$children/$parent、$attrs/$listeners、provide/inject、ref、$root、eventBus、vuex 
1. 父子组件通信：props、$emit/$on、$children/$parent、$attrs/$listeners、ref
2. 兄弟组件通信：$parent、$root、eventBus、vuex
3. 跨层级关系: provide/inject、eventBus、vuex
```

### 19. vuex使用理解：
```
1. vuex是一个专用的状态管理库
2. 解决了多组件之间的状态共享问题，保证简洁的单向数据流
3. 业务足够大需要维护大量状态时使用vuex，否则可使用一个简单的store模式
4. 数据响应式原理：通过new vue实例把状态设置到data里面，通过vue来实现数据响应式
5. 工作流程：dispatch -> actions -> commit -> mutations -> state -> render -> Vue components
```

### 18. vue响应式为什么重写数组push、pop、shift、unshift、splice、sort、reverse方法：
```
因为 Object.defineProperty 无法监听数组变化?
其实 Object.defineProperty 本身是可以监控到数组下标的变化的，只是在 Vue 的实现中，从性能/体验的性价比考虑，放弃了这个特性
Object.defineProperty 在数组中的表现和在对象中的表现是一致的，数组的索引就可以看做是对象中的 key。
重点：当使用 unshift、shift 等方法时会改变数组数据中原来的key顺序会牵连其它数据触发 getter 和 setter 方法来改变 key
操作数组既然会带来很多副作用的数据响应式这显然是不对的，所以vue干脆就舍弃了通过 defineProperty 监听数组
proxy则原生的解决了数据响应式的问题
(参考文献：https://www.infoq.cn/article/spcmacrdazqfmlbgjegr)
```

### 17. vue性能优化：
```
1. 路由组件懒加载：component: () => import('Foo.vue')
2. keep-alive 缓存页面
3. v-show 代替 v-if 复用频繁切换的页面
4. 冻结不需要响应式的数据 created() { ... this.pageData = Object.freeze(pageData) }
5. 链表长采用 vue-virtual-scroller、vue-virtual-scroll-list 虚拟滚动渲染部分区域节点
6. 启用定时器时在 beforeDestroy 内手动移除，防止异常关闭页面带来的内存泄露
7. 图片懒加载 vue-lazyload: <img v-lazy="/static/img/1.png">
8. 无状态组件标记为函数式组件：<template functional>
9. 子组件分割避免因为一小部分数据变化导致整个组件重新渲染
10. ssr服务端渲染SEO优化
```

### 16. vue的设计原理：
```
1. 渐进式JavaScript框架，可以由简单到复杂的去使用它
2. 易用性：提供数据响应式，声明式模板语法和基于配置的组件系统等，使用户只需关注核心业务逻辑
3. 灵活性：业务足够小，仅使用vue核心特性即可完成功能，规模扩大时引入路由、状态管理、vue-cli等
4. 高效性：虚拟DOM和diff算法轻松解决频繁操作dom带来的性能损失，vue3中引入proxy对数据响应式与对静态内容标记的编译方式使页面渲染更高效
```

### 15. vue渲染过程解析：
```
1. 把模板编译为render函数 (参考源码34行：https://github.com/vuejs/vue/blob/dev/src/platforms/web/entry-runtime-with-compiler.js) 可见 render > template > el
2. 实例进行挂载, 根据根节点render函数的调用，递归的生成虚拟dom
3. 对比虚拟dom，渲染到真实dom
4. 组件内部data发生变化，组件和子组件引用data作为props重新调用render函数，生成虚拟dom, 返回到步骤3
```
### 14. vue中diff算法原理：
```
1. diff算法是虚拟dom技术必然的产物，通过对新旧虚拟dom比较（diff），将变化的地方更新在真实dom上，diff算法则让比较过程更高效，时间复杂度降至O(n)
2. vue2.x 中为了降低watcher粒度，每个组件只有一个wathcher，只有引入diff算法才能精确找到发生变化的位置
3. vue中diff执行的时刻是组件实例执行其更新函数时，它会比对上一次渲染结果oldVnode和新的渲染结果newVnode, 此过程称为patch
4. diff算法遵循深度优先、同层比较的策略：两个节点之间比较会根据它们是否拥有子节点或者文本节点做不同操作  
   比较两组子节点是算法的重点，首先比较头尾节点可能相同 共4种比较，如果没有找到相同节点才遍历查找，查找结束再处理剩下节点
   借助key可以精确高效的找到相同节点，让patch过程更高效
```

### 13. vue中key的作用和工作原理：
```
key能够高效更新虚拟DOM，其原理是vue在patch过程中通过key精确判断两个节点是否相同，从而避免相同节点再次渲染，减少DOM操纵提高性能
(原因见vue源码：src/core/vdom/patch.js 424 行)
```

### 12.  Vue组件data选项为什么必须是个函数而Vue的根实例则没有此限制：
```
1. vue组件可能存在多个实例，所以每次组件复用时 data 都会指向同一个地址，当data选项是函数时每次获取data都返回一个全新对象
 (原因见vue源码：src/core/instance/state.js 114 行)

2. 根实例只存在一个，所以规避了这种情况
```

### 11. v-if和v-for谁的优先级高，如果同时出现，应该怎么优化性能：
```
v-for解析优先级高于v-if (原因见vue源码：src/compiler/codegen/index.js 64行)
如果同时出现，每次渲染都会先执行循环再判断条件 因为v-if在v-for后面执行的作用是为了获取v-for遍历出来的item
优化：如果v-if与item无关则在外层嵌套一个template进行v-if判断
```

### 10. 继承 mixins 指向问题：
```js
import mixinModel from '~/plugins/mixins/mixinModel' //配置打包只引入一次
//无论for生成了多少组件， 所有组件mixins内继承的方法始终指向同一个内存地址
export default {
  mixins: [ mixinModel ]  //每个组件mixinModel内的方法都指向同一个方法
}  
```

### 9. 在vue的组件中，data用function返回对象原因：
```js
当一个组件被定义， data 必须声明为返回一个初始数据对象的函数，因为组件可能被用来创建多个实例。
如果 data 仍然是一个纯粹的对象，则所有的实例将共享引用同一个数据对象！
通过提供 data 函数，每次创建一个新实例后，我们能够调用 data 函数，从而返回初始数据的一个全新副本数据对象。
```

### 8. 生命周期：
```js
//1. 页面生命周期:
beforeCreate  //this.$el = undefined this.$data = undefined
created       //this.$el = undefined this.$data = 已被初始化
beforeMount   //this.$el = undefined this.$data = 已被初始化
mounted       //this.$el = 已被初始化 this.$data = 已被初始化
/*** 页面更新生命周期 start ***/
beforeUpdate
updated
/*** 页面更新生命周期 end ***/
beforeDestroy
destroyed

//2. 混入生命周期执行顺序: (每个生命周期都是mixin先执行)
mixin beforeCreate
page  beforeCreate
mixin created
page  created
mixin beforeMount
page  beforeMount
mixin mounted
page  mounted
/*** 页面更新生命周期 start ***/
mixin beforeUpdate
page  beforeUpdate
mixin updated
page  updated
/*** 页面更新生命周期 end ***/
mixin beforeDestroy
page  beforeDestroy
mixin destroyed
page  destroyed

//3. 混入覆盖问题
mixins: [ mixin1, mixin2 ]
//(1) page内同名属性与方法 覆盖所有 mixin同名属性与方法
//(2) mixin2同名属性与方法 覆盖所有 mixin1同名属性与方法
//(3) 生命周期函数合并成数组执行 执行顺序：mixin1 -> mixin2 -> page
```

### 7. <keep-alive> 作用：
```html
<!-- include 包含的组件(可以为字符串，数组，以及正则表达式,只有匹配的组件会被缓存) -->
<!-- exclude 排除的组件(以为字符串，数组，以及正则表达式,任何匹配的组件都不会被缓存) -->
<!-- max缓存组件的最大值(类型为字符或者数字,可以控制缓存组件的个数) -->
<keep-alive :include="[home]" exclude="error" max=10>
    <router-view />
</keep-alive>
<!-- activated: 组件显示触发   |   deactivated：组件隐藏触发  -->
<!-- 包含在 keep-alive 中，但符合 exclude ，不会调用activated和 deactivated。 -->
```

### 6. addRoutes 作用：
```js
//设置用户可访问的路由
//当用户登入后， 或者拥有某些特定权限 可要手动添加路由便于用户访问
this.$router.addRoutes([
    {
        path: '/vip',
        name: 'vip',
        component: () => import('../views/vip.vue')   //动态引入
    }
])
```

### 5. 组件复用后再次显示无法执行created如何重新请求数据：
```js
/* 组件内方法 */
//1.方法
export default {
    created() {
        this.fatchData()
    },
    watch: {
        $route: {                //监听路由变化
            handler() {
                this.fatchData() //请求数据
            }
        }
    }
}

//2.方法
export default {
    watch: {
        $route: {            //监听路由变化
            immediate: true, //初始发起请求
            handler() {
                console.log("在此请求数据")
            }
        }
    }
}

/* 路由实现方法 */
//3.方法 （组件为被渲染）
beforeRouteEnter(to, from, next) {
    fatchData(to.params.id, post => {
        next(vm => vm.setData(post))    //请求数据后通过next内回调设置到组件内
    })
}

//4. 方法 （组件已渲染）
beforeRouteUpdate(to, from, next) {
    this.post = null                    //将原理组件内的数据清空
    fatchData(to.params.id, post => {   //重新获取数据并渲染
        this.setData(post)
        next()
    })
}
```

### 4. <style scoped> 深度作用选择器
```css
<style scoped>
.parent >>> .children { /* 只实用与css */ }
</style>

<style lang="scss" scoped>
.parent /deep/ .children { /* 实用与scss */ }
/* or */
.parent ::v-deep .children { /* 实用与scss */ }
</style>
```
### 3. vue组件化的作用：
```js
优点： 提高代码可复用性，降低代码的复杂性，可维护性 提高开发效率
使用场景：
    通用组件： （提高代码复用性：按钮组件，输入框组件，布局组件）
    业务组件： （具体谋部分业务逻辑： 登入注册组件，通用头尾组件，轮播组件）
    页面组件： （个页面区分独立模块：列表卡片，详情页）
组件使用：
    通信：    props, $emit/$on, provide/inject, $children/$parent, $root, $attrs/$listeners
    内容分发：<slot>, <template>, v-slot
    优化：    is, keep-alive, 异步组件，遵循单向数据流原则
组件本质：
    组件配置:
        vue组件是基于配置的，我们通常写的是组件配置而并非组件本身，框架会自动生成构造函数，目的产生虚拟DOM
        -> VueComponent实例 -> render() -> Virtual DOM -> DOM
    
```

### 2. vue的 activated 和 deactivated 生命周期作用:
```js
<keep-alive>包裹的动态组件会被缓存，它是一个抽象组件，它自身不会渲染一个dom元素，也不会出现在父组件链中。
当组件在 <keep-alive> 内被切换时执行 activated 和 deactivated 这两个钩子函数
activated：   激活组件触发
deactivated： 停用组件触发
```


### 1. Vue 中的 computed 和 watch 的区别是什么？

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


## 尤雨溪都没全对的题目：
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

1. BCD   2. ABCD   3. BD   4. B   5. D   6. B   7. C   8. C   9. C   10. D
```
