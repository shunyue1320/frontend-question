# 你不知道的 Webpack

### 1. Webpack 五个核心概念
```
1. Entry:   入口(Entry)指令 Webpack 以哪个文件为入口起点开始打包，分析构建内部依赖图
2. Output:  输出(Output)指令 Webpack 打包后的资源 bundles 输出到哪儿，以及如何命名
3. Loader:  处理非 js 文件 比如 scss, less, png, jpg (webpack本身只处理js文件)
4. Plugins: 插件(Plugins)可处理范围更广的任务，打包优化和压缩，定义环境变量
5. Mode:    模式 (development：开发模式) (production：生产模式) 生产模式插件会比较多，因为它需要将代码打包成体积更小，性能优化的更好
```