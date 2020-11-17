# 你不知道的 Webpack


### 2. Webpack缓存
```
options: {
  cacheDirectory: true  //rule内开启缓存
}

//outup通过使用 build.[hash:[10]].js //浏览器加载相同名称文件缓存
hash:        每次wepack构建生成唯一的hash值， 重新打包后浏览器缓存将失效
chunkhash:   根据chunk生成hash值，若打包来源一个chunk，hash值则相等
contenthash: 根据文件的内容生成hash值， 文件相同hash值相同
```

### 1. Webpack五个核心概念
```
1. Entry:   入口(Entry)指令 Webpack 以哪个文件为入口起点开始打包，分析构建内部依赖图
2. Output:  输出(Output)指令 Webpack 打包后的资源 bundles 输出到哪儿，以及如何命名
3. Loader:  处理非 js 文件 比如 scss, less, png, jpg (webpack本身只处理js文件)
4. Plugins: 插件(Plugins)可处理范围更广的任务，打包优化和压缩，定义环境变量
5. Mode:    模式 (development：开发模式) (production：生产模式) 生产模式插件会比较多，因为它需要将代码打包成体积更小，性能优化的更好
```