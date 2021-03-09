# 你不知道的 React


### react性能优化：
```
1. 减少不必要的渲染，如用 shouldComponentUpdate, PureComponent, React.meno
2. 数据缓存：useMemo缓存参数， useCallback缓存函数
3. 能不用就不用 context, props
4. 懒加载，对长页列表懒加载
5. 减少http请求
```

### 组件的生命周期:
```
挂载 当组件实例被创建并插入 DOM 中时，其生命周期调用顺序如下：
- [**constructor()**](https://react.docschina.org/docs/react-component.html#constructor)
- [`static getDerivedStateFromProps()`](https://react.docschina.org/docs/react-component.html#static-getderivedstatefromprops)
- [**render()**](https://react.docschina.org/docs/react-component.html#render)
- [**componentDidMount()**](https://react.docschina.org/docs/react-component.html#componentdidmount)

下述生命周期方法即将过时，在新代码中应该[避免使用它们](https://react.docschina.org/blog/2018/03/27/update-on-async-rendering.html)：
[`UNSAFE_componentWillMount()`](https://react.docschina.org/docs/react-component.html#unsafe_componentwillmount)

更新 当组件的 props 或 state 发生变化时会触发更新。组件更新的生命周期调用顺序如下：
- [`static getDerivedStateFromProps()`](https://react.docschina.org/docs/react-component.html#static-getderivedstatefromprops)
- [`shouldComponentUpdate()`](https://react.docschina.org/docs/react-component.html#shouldcomponentupdate)
- [**render()**](https://react.docschina.org/docs/react-component.html#render)
- [`getSnapshotBeforeUpdate()`](https://react.docschina.org/docs/react-component.html#getsnapshotbeforeupdate)
  [**componentDidUpdate()**](https://react.docschina.org/docs/react-component.html#componentdidupdate)

下述方法即将过时，在新代码中应该[避免使用它们](https://react.docschina.org/blog/2018/03/27/update-on-async-rendering.html)：
- [`UNSAFE_componentWillUpdate()`](https://react.docschina.org/docs/react-component.html#unsafe_componentwillupdate)
- [`UNSAFE_componentWillReceiveProps()`](https://react.docschina.org/docs/react-component.html#unsafe_componentwillreceiveprops)


卸载 当组件从 DOM 中移除时会调用如下方法：
[**componentWillUnmount()**](https://react.docschina.org/docs/react-component.html#componentwillunmount)
```

### 函数组件与class组件如何选择：
```
class组件存在的问题：组件之间复用状态逻辑很难 ，复用组件难以理解
```

### hook函数组件与以前的函数组件有什么区别:
```
以前的函数组件：无状态，无副作用，只能做单纯的展示组件
hook函数组件：可以存储和改变状态值（useState, useReducer），可执行副作用（useEffect, useLayoutEffect）， 还可复用状态逻辑
```