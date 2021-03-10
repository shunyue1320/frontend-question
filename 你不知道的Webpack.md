# 你不知道的 Webpack

### webpack的优化有哪些:
```
打包出代码的优化:
  js代码压缩优化 ("terser-webpack-plugin")
  css代码抽离 ('mini-css-extract-plugin')
  css代码压缩优化 ('optimize-css-assets-webpack-plugin')
  js代码分割 ('webpack-bundle-analyzer').BundleAnalyzerPlugin;
  tree-shaking:  es6模块化方式（去除无效的代码）
打包速度的优化:
  设置resolve.alias(别名),resolve.extensions(可省略的后缀)
  压缩js，TerserPlugin配置parallel:true
  使用多线程执行webpack: thread-loader / happypack
  预编译，设置动态链路DllPlugin

```

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



# webpack5性能优化：
# 1.项目初始化

## 1.1 安装

## 1.2 webpack.config.js

## 1.3 package.json

# 2.数据分析

## 2.1 日志美化
[friendly-errors-webpack-plugin](https://www.npmjs.com/package/friendly-errors-webpack-plugin)
可以识别某些类别的webpack错误，并清理，聚合和优先级，以提供更好的开发人员体验

### 2.1.1 安装
```js
cnpm i webpack webpack-cli html-webpack-plugin webpack-dev-server cross-env - D
```
### 2.1.2 webpack.config.js
```js
const path = require("path");
module.exports = {
mode: "development",
devtool: 'source-map',
context: process.cwd(),
entry: {
main: "./src/index.js",
},
output: {
path: path.resolve(__dirname, "dist"),
filename: "main.js"
}
};
```
```
"scripts": {
"build": "webpack",
"start": "webpack serve"
},
```

## 2.2 速度分析

```
speed-measure-webpack5-plugin可以分析打包速度
```
### 2.2.1 安装

### 2.2.2 webpack.config.js

```
cnpm i friendly-errors-webpack-plugin node-notifier - D
```
```
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
+const FriendlyErrorsWebpackPlugin = require('friendly-errors-webpack-plugin');
+const notifier = require('node-notifier');
+const ICON = path.join(__dirname, 'icon.jpg');
module.exports = {
mode: "development",
devtool: 'source-map',
context: process.cwd(),
entry: {
main: "./src/index.js",
},
output: {
path: path.resolve(__dirname, "dist"),
filename: "main.js"
},
plugins:[
new HtmlWebpackPlugin(),
+ new FriendlyErrorsWebpackPlugin({
+ onErrors: (severity, errors) => {
+ const error = errors[0];
+ notifier.notify({
+ title: "Webpack编译失败",
+ message: severity + ': ' + error.name,
+ subtitle: error.file || '',
+ icon: ICON
+ });
+ }
+ })
]
};
```
```
cnpm i speed-measure-webpack5-plugin - D
```

## 2.3 文件体积监控

```
webpack-bundle-analyzer是一个webpack的插件，需要配合webpack和webpack-cli一起使用。
这个插件的功能是生成代码分析报告，帮助提升代码质量和网站性能
它可以直观分析打包出的文件包含哪些，大小占比如何，模块包含关系，依赖项，文件是否重复，
压缩后大小如何，针对这些，我们可以进行文件分割等操作。
```
### 2.3.1 安装

### 2.3.2 编译启动

**2.3.2.1 webpack.config.js**

**2.3.2.2 package.json**

### 2.3.3 单独启动

**2.3.3.1 webpack.config.js**

**2.3.3.2 package.json**

```
+const SpeedMeasureWebpackPlugin = require('speed-measure-webpack5-plugin');
+const smw = new SpeedMeasureWebpackPlugin();
+module.exports = smw.wrap({
mode: "development",
devtool: 'source-map',
...
+});
```
```
cnpm i webpack-bundle-analyzer - D
```
```
+const {BundleAnalyzerPlugin} = require('webpack-bundle-analyzer')
module.exports={
plugins: [
+ new BundleAnalyzerPlugin()
]
}
```
```
"scripts": {
"build": "webpack",
"start": "webpack serve",
+ "dev":"webpack --progress"
},
```
```
const {BundleAnalyzerPlugin} = require('webpack-bundle-analyzer')
module.exports={
plugins: [
new BundleAnalyzerPlugin({
+ analyzerMode: 'disabled', // 不启动展示打包报告的http服务器
+ generateStatsFile: true, // 是否生成stats.json文件
}),
]
}
```

# 3.编译时间优化

#### 减少要处理的文件

#### 缩小查找的范围

## 3.1 缩小查找范围

## 3.1.1 extensions

```
指定extensions之后可以不用在require或是import的时候加文件扩展名
查找的时候会依次尝试添加扩展名进行匹配
```
## 3.1.2 alias

```
配置别名可以加快webpack查找模块的速度
每当引入bootstrap模块的时候，它会直接引入bootstrap,而不需要从node_modules文件夹中
按模块的查找规则查找
```
```
"scripts": {
"build": "webpack",
"start": "webpack serve",
"dev":"webpack --progress",
+ "analyzer": "webpack-bundle-analyzer --port 8888 ./dist/stats.json"
}
```
```
resolve: {
+ extensions: [".js",".jsx",".json"]
},
module:{
```
```
cnpm i bootstrap css-loader style-loader - S
```
```
+const bootstrap =
path.resolve(__dirname,'node_modules/bootstrap/dist/css/bootstrap.css');
module.exports = smw.wrap({
mode: "development",
devtool: 'source-map',
context: process.cwd(),
entry: {
main: "./src/index.js",
},
output: {
path: path.resolve(__dirname, "dist"),
filename: "main.js"
},
resolve: {
extensions: [".js",".jsx",".json"],
+ alias:{bootstrap}
},
+ module:{
+ rules:[
+ {
+ test: /\.css$/,
+ use: ['style-loader', 'css-loader']
```

### 3.1.3 modules

```
对于直接声明依赖名的模块webpack会使用类似Node.js一样进行路径搜索，搜索
node_modules目录
如果可以确定项目内所有的第三方依赖模块都是在项目根目录下的node_modules中的话可以直接
指定
默认配置
```
#### 直接指定

### 3.1.4 mainFields

```
默认情况下package.json 文件则按照文件中 main 字段的文件名来查找文件
```
### 3.1.5 mainFiles

```
当目录下没有 package.json 文件时，我们说会默认使用目录下的 index.js这个文件
```
### 3.1.6 oneOf

```
每个文件对于rules中的所有规则都会遍历一遍，如果使用oneOf就可以解决该问题，只要能匹配
一个即可退出
在oneOf中不能两个配置处理同一种类型文件
```
webpack.config.js

#### + }

#### + ]

#### }

#### });

```
resolve: {
extensions: [".js",".jsx",".json"],
alias:{bootstrap},
+ modules: ['node_modules']
},
```
```
resolve: {
+ modules: ['C:/node_modules','node_modules'],
}
```
```
resolve: {
// 配置 target === "web" 或者 target === "webworker" 时 mainFields 默认值是：
+ mainFields: ['browser', 'module', 'main'],
// target 的值为其他时，mainFields 默认值为：
+ mainFields: ["module", "main"],
}
```
```
resolve: {
+ mainFiles: ['index']
},
```
```
rules: [
+ {
```

### 3.1.7 external

```
如果我们想引用一个库，但是又不想让webpack打包，并且又不影响我们在程序中以CMD、AMD
或者window/global全局等方式进行使用，那就可以通过配置externals
```
**3.1.7.1 安装**

**3.1.7.2 使用jquery**

src/index.js

**3.1.7.3 index.html**

src/index.html

**3.1.7.4 webpack.config.js**

webpack.config.js

```
+ oneOf:[
{
test: /\.js$/,
include: path.resolve(__dirname, "src"),
exclude: /node_modules/,
use: [
{
loader: 'thread-loader',
options: {
workers: 3
}
},
{
loader:'babel-loader',
options: {
cacheDirectory: true
}
}]
},
{
test: /\.css$/,
use: ['cache-loader','logger-loader', 'style-loader', 'css-loader']
}
+ ]
}
]
```
```
cnpm i jquery html-webpack-externals-plugin - D
```
```
const jQuery = require("jquery");
import jQuery from 'jquery';
```
```
<script src="https://cdn.bootcss.com/jquery/3.4.1/jquery.js"></script>
```

### 3.1.8 resolveLoader

```
resolve.resolveLoader用于配置解析 loader 时的 resolve 配置,默认的配置
```
**3.1.8.1 logger-loader.js**

loaders\logger-loader.js

**3.1.8.2 webpack.config.js**

webpack.config.js

## 3.2 noParse

```
module.noParse 字段，可以用于配置哪些模块文件的内容不需要进行解析
不需要解析依赖（即无依赖） 的第三方大型类库等，可以通过这个字段来配置，以提高整体的构
建速度
使用 noParse 进行忽略的模块文件中不能使用 import、require 等语法
```
### 3.2.1 src\index.js

src\index.js

```
+ externals: {
+ jquery: 'jQuery',
+ },
module: {
```
```
function loader(source){
console.log('logger-loader');
return source;
}
module.exports = loader;
```
```
module.exports = {
resolve: {
extensions: [".js",".jsx",".json"],
alias:{bootstrap},
modules: ['node_modules'],
},
+ resolveLoader:{
+ modules: [path.resolve(__dirname, "loaders"),'node_modules'],
+ },
module:{
rules:[
{
test:/\.css$/,
use:[
+ 'logger-loader',
'style-loader',
'css-loader'
]
}
]
},
};
```

### 3.2.2 src\title.js

src\title.js

### 3.2.3 src\name.js

src\name.js

### 3.2.4 webpack.config.js

## 3.3 IgnorePlugin

```
ignore-plugin用于忽略某些特定的模块，让 webpack 不把这些指定的模块打包进去
requestRegExp 匹配(test)资源请求路径的正则表达式。
contextRegExp （可选）匹配(test)资源上下文（目录）的正则表达式。
moment会将所有本地化内容和核心功能一起打包,你可使用 IgnorePlugin 在打包时忽略本地化
内容
```
### 3.3.1 安装

### 3.3.2 src\index.js

src\index.js

### 3.3.3 webpack.config.js

webpack.config.js

```
let title = require('./title');
console.log(title);
```
```
let name = require('./name');
module.exports = `hello ${name}`;
```
```
module.exports = 'zhufeng';
```
```
module.exports = {
module: {
+ noParse: /title.js/, // 正则表达式
}
}
```
```
cnpm i moment - S
```
```
import moment from 'moment';
console.log(moment);
```

## 3.4 thread-loader(多进程)

```
把thread-loader放置在其他 loader 之前， 放置在这个 loader 之后的 loader 就会在一个单独的
worker 池(worker pool)中运行
include 表示哪些目录中的 .js 文件需要进行 babel-loader
exclude 表示哪些目录中的 .js 文件不要进行 babel-loader
exclude 的优先级高于 include,尽量避免 exclude，更倾向于使用 include
```
### 3.4.1 安装

### 3.4.2 webpack.config.js

webpack.config.js

## 3.5 利用缓存

#### 利用缓存可以提升重复构建的速度

### 3.5.1 babel-loader

```
Babel在转义js文件过程中消耗性能较高，将babel-loader执行的结果缓存起来，当重新打包构建
时会尝试读取缓存，从而提高打包构建速度、降低消耗
默认存放位置是node_modules/.cache/babel-loader
```
```
plugins:[
+ new webpack.IgnorePlugin({
+ resourceRegExp: /^\.\/locale$/,
+ contextRegExp: /moment$/
+ })
]
```
```
cnpm i thread-loader babel-loader @babel/core @babel/preset-env - D
```
```
rules: [
+ {
+ test: /\.js$/,
+ include: path.resolve(__dirname, "src"),
+ exclude: /node_modules/
+ use: [
+ {
+ loader: 'thread-loader',
+ options: {workers: 3}
+ }, 'babel-loader']
+ },
{
test: /\.css$/,
use: ['logger-loader', 'style-loader', 'css-loader']
}
]
```
#### {

```
test: /\.js$/,
include: path.resolve(__dirname, "src"),
exclude: /node_modules/,
```

### 3.5.2 cache-loader

```
在一些性能开销较大的cache-loader之前添加此 loader,可以以将结果缓存中磁盘中
默认保存在 node_modules/.cache/cache-loader 目录下
```
### 3.5.3 hard-source-webpack-plugin

```
HardSourceWebpackPlugin为模块提供了中间缓存,缓存默认的存放路径是
node_modules/.cache/hard-source
配置hard-source-webpack-plugin后，首次构建时间并不会有太大的变化，但是从第二次开始，
构建时间大约可以减少80%左右
webpack5中已经内置了模块缓存,不需要再使用此插件
```
**3.5.3.1 安装**

**3.5.3.2 webpack.config.js**

```
use: [
{
loader: 'thread-loader',
options: {
workers: 3
}
},
+ {
+ loader:'babel-loader',
+ options: {
+ cacheDirectory: true
+ }
+ }]
},
```
```
cnpm i cache-loader - D
```
#### {

```
test: /\.css$/,
use: [
+ 'cache-loader',
'logger-loader',
'style-loader',
'css-loader']
}
```
```
cnpm i hard-source-webpack-plugin - D
```
```
+let HardSourceWebpackPlugin = require('hard-source-webpack-plugin');
```
```
module.exports = {
plugins: [
+ new HardSourceWebpackPlugin()
]
}
```

# 4.编译体积优化

## 4.1 压缩JS、CSS、HTML和图片

```
optimize-css-assets-webpack-plugin是一个优化和压缩CSS资源的插件
terser-webpack-plugin是一个优化和压缩JS资源的插件
image-webpack-loader可以帮助我们对图片进行压缩和优化
```
## 4.1.1 安装

## 4.1.2 webpack.config.js

```
cnpm i terser-webpack-plugin optimize-css-assets-webpack-plugin image-webpack-
loader - D
```
```
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
+const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-
plugin');
+const TerserPlugin = require('terser-webpack-plugin');
```
```
module.exports = {
devtool: false,
entry: './src/index.js',
+ optimization: {
+ minimize: true,
+ minimizer: [
+ new TerserPlugin(),
+ ],
+ },
module:{
rules:[
{
test: /\.(png|svg|jpg|gif|jpeg|ico)$/,
use: [
'url-loader',
+ {
+ loader: 'image-webpack-loader',
+ options: {
+ mozjpeg: {
+ progressive: true,
+ quality: 65
+ },
+ optipng: {
+ enabled: false,
+ },
+ pngquant: {
+ quality: '65-90',
+ speed: 4
+ },
+ gifsicle: {
+ interlaced: false,
+ },
+ webp: {
```

## 4.2 清除无用的CSS

```
purgecss-webpack-plugin单独提取CSS并清除用不到的CSS
```
### 4.2.1 安装

### 4.2.3 src\index.js

### 4.2.4 src\index.css

src\index.css

### 4.2.5 src\index.html

src\index.html

```
+ quality: 75
+ }
+ }
+ }
]
}
]
}
plugins: [
new HtmlWebpackPlugin({
template: './src/index.html',
+ minify: {
+ collapseWhitespace: true,
+ removeComments: true
}
})
+ new OptimizeCssAssetsWebpackPlugin(),
],
};
```
```
cnpm i purgecss-webpack-plugin mini-css-extract-plugin - D
```
```
import './index.css';
```
```
body{
background-color: red;
}
#root{
color:red;
}
#other{
color:red;
}
```

### 4.2.6 package.json

package.json

### 4.2.7 webpack.config.js

## 4.3 Tree Shaking

```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Document</title>
</head>
<body>
<div id="root"></div>
</body>
</html>
```
```
+ "sideEffects":["**/*.css"],
```
```
const path = require("path");
+const MiniCssExtractPlugin = require("mini-css-extract-plugin");
+const PurgecssPlugin = require("purgecss-webpack-plugin");
+const glob = require("glob");
+const PATHS = {
+ src: path.join(__dirname, "src"),
+};
module.exports = {
module: {
rules: [
{
test: /\.css$/,
include: path.resolve(__dirname, "src"),
exclude: /node_modules/,
use: [
{
+ loader: MiniCssExtractPlugin.loader,
},
"css-loader",
],
}
]
},
plugins: [
+ new MiniCssExtractPlugin({
+ filename: "[name].css",
+ }),
+ new PurgecssPlugin({
+ paths: glob.sync(`${PATHS.src}/**/*`, { nodir: true }),
+ })
]
devServer: {},
};
```

```
一个模块可以有多个方法，只要其中某个方法使用到了，则整个文件都会被打到bundle里面去，
tree shaking就是只把用到的方法打入bundle,没用到的方法会uglify阶段擦除掉
原理是利用es6模块的特点,只能作为模块顶层语句出现,import的模块名只能是字符串常量
```
### 4.3.1 开启

```
webpack默认支持,可在production mode下默认开启
在 package.json 中配置：
"sideEffects": false 所有的代码都没有副作用（都可以进行 tree shaking）
可能会把 css和@babel/polyfill文件干掉 可以设置 "sideEffects":["*.css"]
```
### 4.3.2 没有导入和使用

functions.js

### 4.3.3 代码不会被执行，不可到达

### 4.3.4 代码执行的结果不会被用到

### 4.3.5 代码中只写不读的变量

## 4.4 Scope Hoisting

```
Scope Hoisting 可以让 Webpack 打包出来的代码文件更小、运行的更快， 它又译作 "作用域提
升"，是在 Webpack3 中新推出的功能。
scope hoisting的原理是将所有的模块按照引用顺序放在一个函数作用域里，然后适当地重命名一
些变量以防止命名冲突
```
```
function func1(){
return 'func1';
}
function func2(){
return 'func2';
}
export {
func1,
func
}
```
```
import {func2} from './functions';
var result2 = func2();
console.log(result2);
```
```
if(false){
console.log('false')
}
```
```
import {func2} from './functions';
func2();
```
```
var aabbcc='aabbcc';
aabbcc='eeffgg';
```

```
这个功能在mode为production下默认开启,开发环境要用
webpack.optimizeModuleConcatenationPlugin插件
```
hello.js

index.js

main.js

# 5.运行速度优化

## 5.1 代码分割

```
对于大的Web应用来讲，将所有的代码都放在一个文件中显然是不够有效的，特别是当你的某些
代码块是在某些特殊的时候才会被用到。
webpack有一个功能就是将你的代码库分割成chunks语块，当代码运行到需要它们的时候再进行
加载
```
## 5.1.1 入口点分割

```
Entry Points：入口文件设置的时候可以配置
这种方法的问题
如果入口 chunks 之间包含重复的模块(lodash)，那些重复模块都会被引入到各个 bundle 中
不够灵活，并且不能将核心应用程序逻辑进行动态拆分代码
```
## 5.1.2 懒加载

#### 用户当前需要用什么功能就只加载这个功能对应的代码，也就是所谓的按需加载 在给单页应用做

#### 按需加载优化时

#### 一般采用以下原则：

```
对网站功能进行划分，每一类一个chunk
对于首次打开页面需要的功能直接加载，尽快展示给用户,某些依赖大量代码的功能点可以按
需加载
被分割出去的代码需要一个按需加载的时机
```
**5.1.2.1 hello.js**

hello.js

```
export default 'Hello';
```
```
import str from './hello.js';
console.log(str);
```
```
var hello = ('hello');
console.log(hello);
```
```
entry: {
index: "./src/index.js",
login: "./src/login.js"
}
```

**5.1.2.2 index.js**

**5.1.2.3 index.html**

### 5.1.3 prefetch

#### 使用预先拉取，你表示该模块可能以后会用到。浏览器会在空闲时间下载该模块

```
prefetch的作用是告诉浏览器未来可能会使用到的某个资源，浏览器就会在闲时去加载对应的资
源，若能预测到用户的行为，比如懒加载，点击到其它页面等则相当于提前预加载了需要的资源
此导入会让<link rel="prefetch" as="script"
href="http://localhost:8080/hello.js">被添加至页面的头部。因此浏览器会在空闲时间预
先拉取该文件。
```
webpack.config.js

### 5.1.6 提取公共代码

#### 怎么配置单页应用?怎么配置多页应用?

#### 5.1.6.1 为什么需要提取公共代码

#### 大网站有多个页面，每个页面由于采用相同技术栈和样式代码，会包含很多公共代码，如果都包含

#### 进来会有问题

#### 相同的资源被重复的加载，浪费用户的流量和服务器的成本；

#### 每个页面需要加载的资源太大，导致网页首屏加载缓慢，影响用户体验。

#### 如果能把公共代码抽离成单独文件进行加载能进行优化，可以减少网络传输流量，降低服务器成本

#### 5.1.6.2 如何提取

#### 基础类库，方便长期缓存

#### 页面之间的公用代码

#### 各个页面单独生成文件

**5.1.6.3 splitChunks**

```
split-chunks-plugin
```
**5.1.6.3.1 module chunk bundle**

```
module.exports = "hello";
```
```
document.querySelector('#clickBtn').addEventListener('click',() => {
import('./hello').then(result => {
console.log(result.default);
});
});
```
```
<button id="clickBtn">点我</button>
```
```
document.querySelector('#clickBtn').addEventListener('click',() => {
import(/* webpackChunkName: 'hello', webpackPrefetch: true
*/'./hello').then(result => {
console.log(result.default);
});
});
```

```
module：就是js的模块化webpack支持commonJS、ES6等模块化规范，简单来说就是你通过
import语句引入的代码
chunk: chunk是webpack根据功能拆分出来的，包含三种情况
你的项目入口（entry）
通过import()动态引入的代码
通过splitChunks拆分出来的代码
bundle：bundle是webpack打包之后的各个文件，一般就是和chunk是一对一的关系，bundle就
是对chunk进行编译压缩打包等处理之后的产出
```
**5.1.6.3.2 默认配置**

webpack.config.js

src\page1.js

```
module.exports = {
output:{
filename:'[name].js',
chunkFilename:'[name].js'
},
entry: {
page1: "./src/page1.js",
page2: "./src/page2.js",
page3: "./src/page3.js",
},
optimization: {
splitChunks: {
chunks: 'all',
minSize: 0 ,
minRemainingSize: 0 ,
maxSize: 0 ,
minChunks: 1 ,
maxAsyncRequests: 5 ,
maxInitialRequests: 10 ,
automaticNameDelimiter: '~',
enforceSizeThreshold: 50000 ,
cacheGroups: {
defaultVendors: {
test: /[\\/]node_modules[\\/]/,
priority: - 10 ,
reuseExistingChunk: true
},
default: {
minChunks: 2 ,
priority: - 20 ,
reuseExistingChunk: true
}
}
}
},
}
```

src\page2.js

src\page3.js

src\module1.js

src\module2.js

src\module3.js

src\asyncModule1.js

```
import utils1 from "./module1";
import utils2 from "./module2";
import $ from "jquery";
console.log(utils1, utils2, $);
import(/* webpackChunkName: "asyncModule1" */ "./asyncModule1");
```
```
import utils1 from "./module1";
import utils2 from "./module2";
import $ from "jquery";
console.log(utils1, utils2, $);
```
```
import utils1 from "./module1";
import utils3 from "./module3";
import $ from "jquery";
console.log(utils1, utils3, $);
```
```
console.log("module1");
```
```
console.log("module2");
```
```
console.log("module3");
```
```
import _ from 'lodash';
console.log(_);
```

## 5.2 CDN

```
Asset Size Chunks
Chunk Names
asyncModule1.chunk.js  740 bytes asyncModule
[emitted]  asyncModule
index.html  498 bytes
[emitted]
page1.js 10.6 KiB page
[emitted]  page
page1~page2.chunk.js  302 bytes page1~page
[emitted]  page1~page
page1~page2~page3.chunk.js  308 bytes page1~page2~page
[emitted]  page1~page2~page
page2.js 7.52 KiB page
[emitted]  page
page3.js 7.72 KiB page
[emitted]  page
vendors~asyncModule1.chunk.js  532 KiB vendors~asyncModule
[emitted]  vendors~asyncModule
vendors~page1~page2~page3.chunk.js  282 KiB vendors~page1~page2~page
[emitted]  vendors~page1~page2~page
Entrypoint page1 = vendors~page1~page2~page3.chunk.js page1~page2~page3.chunk.js
page1~page2.chunk.js page1.js
Entrypoint page2 = vendors~page1~page2~page3.chunk.js page1~page2~page3.chunk.js
page1~page2.chunk.js page2.js
Entrypoint page3 = vendors~page1~page2~page3.chunk.js page1~page2~page3.chunk.js
page3.js
```

#### 最影响用户体验的是网页首次打开时的加载等待。 导致这个问题的根本是网络传输过程耗时大，

#### CDN的作用就是加速网络传输。

#### CDN 又叫内容分发网络，通过把资源部署到世界各地，用户在访问时按照就近原则从离用户最近

#### 的服务器获取资源，从而加速资源的获取速度

#### 用户使用浏览器第一次访问我们的站点时，该页面引入了各式各样的静态资源，如果我们能做到持

```
久化缓存的话，可以在 http 响应头加上 Cache-control 或 Expires 字段来设置缓存，浏览器可以
将这些资源一一缓存到本地
用户在后续访问的时候，如果需要再次请求同样的静态资源，且静态资源没有过期，那么浏览器可
以直接走本地缓存而不用再通过网络请求资源
缓存配置
HTML文件不缓存，放在自己的服务器上，关闭自己服务器的缓存，静态资源的URL变成指向
CDN服务器的地址
静态的JavaScript、CSS、图片等文件开启CDN和缓存，并且文件名带上HASH值
为了并行加载不阻塞，把不同的静态资源分配到不同的CDN服务器上
域名限制
同一时刻针对同一个域名的资源并行请求是有限制
可以把这些静态资源分散到不同的 CDN 服务上去
多个域名后会增加域名解析时间
可以通过在 HTML HEAD 标签中 加入去预解析域名，以降低域名解析带来的延迟
```
webpack.config.js

```
const path = require("path");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const PurgecssPlugin = require("purgecss-webpack-plugin");
const TerserPlugin = require("terser-webpack-plugin");
const OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const UploadPlugin = require("./plugins/UploadPlugin");
const glob = require("glob");
const PATHS = {
src: path.join(__dirname, "src"),
};
module.exports = {
mode: "development",
devtool: false,
context: process.cwd(),
entry: {
main: "./src/index.js",
},
output: {
path: path.resolve(__dirname, "dist"),
+ filename: "[name].[hash].js",
+ chunkFilename: "[name].[hash].chunk.js",
+ publicPath: "http://img.zhufengpeixun.cn/",
},
optimization: {
minimize: true,
minimizer: [
//压缩JS
/* new TerserPlugin({
sourceMap: false,
extractComments: false,
```

#### }),

#### //压缩CSS

new OptimizeCSSAssetsPlugin({}), */
],
//自动分割第三方模块和公共模块
splitChunks: {
chunks: "all", //默认作用于异步chunk，值为all/initial/async
minSize: 0, //默认值是30kb,代码块的最小尺寸
minChunks: 1, //被多少模块共享,在分割之前模块的被引用次数
maxAsyncRequests: 2, //限制异步模块内部的并行最大请求数的，说白了你可以理解为是每个
import()它里面的最大并行请求数量
maxInitialRequests: 4, //限制入口的拆分数量
name: true, //打包后的名称，默认是chunk的名字通过分隔符（默认是～）分隔开，如vendor~
automaticNameDelimiter: "~", //默认webpack将会使用入口名和代码块的名称生成命名,比
如 'vendors~main.js'
cacheGroups: {
//设置缓存组用来抽取满足不同规则的chunk,下面以生成common为例
vendors: {
chunks: "all",
test: /node_modules/, //条件
priority: -10, ///优先级，一个chunk很可能满足多个缓存组，会被抽取到优先级高的缓
存组中,为了能够让自定义缓存组有更高的优先级(默认0),默认缓存组的priority属性为负值.
},
commons: {
chunks: "all",
minSize: 0, //最小提取字节数
minChunks: 2, //最少被几个chunk引用
priority: -20,
reuseExistingChunk: true, //如果该chunk中引用了已经被抽取的chunk，直接引用该
chunk，不会重复打包代码
},
},
},
//为了长期缓存保持运行时代码块是单独的文件
/* runtimeChunk: {
name: (entrypoint) => `runtime-${entrypoint.name}`,
}, */
},
module: {
rules: [
{
test: /\.js/,
include: path.resolve(__dirname, "src"),
use: [
{
loader: "babel-loader",
options: {
presets: [
["@babel/preset-env", { modules: false }],
"@babel/preset-react",
],
},
},
],
},
{
test: /\.css$/,
include: path.resolve(__dirname, "src"),


# 6.附录

```
exclude: /node_modules/,
use: [
{
loader: MiniCssExtractPlugin.loader,
},
"css-loader",
],
},
{
test: /\.(png|svg|jpg|gif|jpeg|ico)$/,
use: [
"file-loader",
{
loader: "image-webpack-loader",
options: {
mozjpeg: {
progressive: true,
quality: 65,
},
optipng: {
enabled: false,
},
pngquant: {
quality: "65-90",
speed: 4,
},
gifsicle: {
interlaced: false,
},
webp: {
quality: 75,
},
},
},
],
},
],
},
plugins: [
new HtmlWebpackPlugin({
inject: true,
template: "./src/index.html",
}),
new MiniCssExtractPlugin({
+ filename: "[name].[hash].css",
}),
new PurgecssPlugin({
paths: glob.sync(`${PATHS.src}/**/*`, { nodir: true }),
}),
new UploadPlugin({}),
],
devServer: {},
};
```

#### 选项 描述

```
development
会将 process.env.NODE_ENV 的值设为 development。启用
NamedChunksPlugin 和 NamedModulesPlugin
```
```
production
```
```
会将 process.env.NODE_ENV 的值设为 production。启用
FlagDependencyUsagePlugin, FlagIncludedChunksPlugin,
ModuleConcatenationPlugin, NoEmitOnErrorsPlugin,
OccurrenceOrderPlugin, SideEffectsFlagPlugin 和 UglifyJsPlugin
```
## 6.1 环境

### 6.1.1 模式(mode)

#### 日常的前端开发工作中，一般都会有两套构建环境

```
一套开发时使用，构建结果用于本地开发调试，不进行代码压缩，打印 debug 信息，包含
sourcemap 文件
一套构建后的结果是直接应用于线上的，即代码都是压缩后，运行时不打印 debug 信息，静态文
件不包括 sourcemap
webpack 4.x 版本引入了 mode 的概念
当你指定使用 production mode 时，默认会启用各种性能优化的功能，包括构建结果优化以及
webpack 运行性能优化
而如果是 development mode 的话，则会开启 debug 工具，运行时打印详细的错误信息，以及
更加快速的增量编译构建
```
### 6.1.2 环境差异

#### 开发环境

```
需要生成 sourcemap 文件
需要打印 debug 信息
需要 live reload 或者 hot reload 的功能
生产环境
可能需要分离 CSS 成单独的文件，以便多个页面共享同一个 CSS 文件
需要压缩 HTML/CSS/JS 代码
需要压缩图片
其默认值为 production
```
### 6.1.3 区分环境

```
--mode用来设置模块内的process.env.NODE_ENV
--env用来设置webpack配置文件的函数参数
cross-env用来设置node环境的process.env.NODE_ENV
dotenv可以按需加载不同的环境变量文件
define-plugin用来配置在编译时候用的全局常量
```
**6.1.3.1 安装**

**6.1.3.2 mode默认值**

```
webpack的mode默认为production
```
```
cnpm i cross-env dotenv terser-webpack-plugin optimize-css-assets-webpack-plugin
```
- D


```
webpack serve的mode默认为development
可以在模块内通过process.env.NODE_ENV获取当前的环境变量,无法在webpack配置文件中获取
此变量
```
index.js

webpack.config.js

**6.1.3.3 命令行传mode**

```
同配置 1
```
**6.1.3.4 命令行配置env**

```
无法在模块内通过process.env.NODE_ENV访问
可以通过webpack 配置文件中中通过函数获取当前环境变量
```
index.js

webpack.config.js

```
"scripts": {
"build": "webpack",
"start": "webpack serve"
},
```
```
console.log(process.env.NODE_ENV);// development | production
```
```
console.log('NODE_ENV',process.env.NODE_ENV);// undefined
```
```
"scripts": {
"build": "webpack --mode=production",
"start": "webpack --mode=development serve"
},
```
```
"scripts": {
"dev": "webpack serve --env=development",
"build": "webpack --env=production",
}
```
```
console.log(process.env.NODE_ENV);// undefined
```
```
console.log('NODE_ENV',process.env.NODE_ENV);// undefined
```
```
const TerserWebpackPlugin = require('terser-webpack-plugin');
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-
plugin');
```
```
module.exports = (env) => {
console.log('env',env);// {development:true} | {production:true}
return {
+ optimization: {
```

**6.1.3.5 mode配置**

```
和命令行配置 2 一样
```
**6.1.3.6 DefinePlugin**

```
设置全局变量(不是window),所有模块都能读取到该变量的值
可以在任意模块内通过 process.env.NODE_ENV 获取当前的环境变量
但无法在node环境(webpack 配置文件中)下获取当前的环境变量
```
**6.1.3.6.1 webpack.config.js**

**6.1.3.6.2 src/index.js**

**6.1.3.6.3 src/logger.js**

**6.1.4 cross-env**

```
只能设置node环境下的变量NODE_ENV
```
package.json

```
+ minimize:env&&env.production,
+ minimizer: (env && env.production)? [
+ new TerserWebpackPlugin({
+ parallel: true//开启多进程并行压缩
+ }),
+ new OptimizeCssAssetsWebpackPlugin({})
+ ] : []
+ },
}
};
```
```
module.exports = {
mode: 'development'
}
```
```
console.log('process.env.NODE_ENV',process.env.NODE_ENV);// undefined
console.log('NODE_ENV',NODE_ENV);// error ！！！
module.exports = {
plugins:[
new webpack.DefinePlugin({
'process.env.NODE_ENV':JSON.stringify('development'),//注意用双引号引起来，
否则就成变量了
'NODE_ENV':JSON.stringify('production'),
})
]
}
```
```
console.log(NODE_ENV);// production
```
```
export default function logger(...args) {
if (process.env.NODE_ENV == 'development') {
console.log.apply(console,args);
}
}
```

webpack.config.js

### 6.1.4 env

**6.1.4.1 .env**

**6.1.4.2 webpack.config.js**

webpack.config.js

## 6.2 JavaScript兼容性

### 6.2.1 Babel

```
Babel 是一个 JavaScript 编译器
Babel 是一个工具链，主要用于将 ECMAScript 2015+ 版本的代码转换为向后兼容的 JavaScript 语
法，以便能够运行在当前和旧版本的浏览器或其他环境中
Babel 能为你做的事情
语法转换
```
**6.2.1.1 安装**

#### 6.2.1.2 命令行使用

**6.2.1.2.1 src\index.js**

**6.2.1.2.2 package.json**

### 6.2.2 插件

```
Babel 是一个编译器（输入源码 => 输出编译后的代码）。就像其他编译器一样，编译过程分为三
个阶段：解析、转换和打印输出。
```
```
"scripts": {
"build": "cross-env NODE_ENV=development webpack"
}
```
```
console.log('process.env.NODE_ENV',process.env.NODE_ENV);// development
```
```
NODE_ENV=development
```
```
+require('dotenv').config({path: path.resolve(__dirname,'.env')})
+console.log(process.env.NODE_ENV);
```
```
cnpm i @babel/core @babel/cli - D
```
```
const sum = (a,b)=>a+b;
console.log(sum( 1 , 2 ));
```
```
"scripts": {
"build": "babel src --out-dir dist --watch"
},
```

```
现在，Babel 虽然开箱即用，但是什么动作都不做。它基本上类似于 const babel = code =>
code; ，将代码解析之后再输出同样的代码。如果想要 Babel 做一些实际的工作，就需要为其添
加插件
```
**6.2.2.1 安装**

**6.2.2.2 .babelrc**

### 6.2.3 预设

```
不想自己动手组合插件？没问题！preset 可以作为 Babel 插件的组合
@babel/preset-env可以让你使用最新的JavaScript语法,而不需要去管理语法转换器(并且可选的支
持目标浏览器环境的polyfills)
```
#### 6.2.3.1 安装

**6.2.3.2 .babelrc**

**6.2.3.3 browsers**

```
@babel/preset-env 会根据你配置的目标环境，生成插件列表来编译
可以使用使用 .browserslistrc 文件来指定目标环境
browserslist详细配置
```
```
cnpm i @babel/plugin-transform-arrow-functions - D
```
#### {

```
"plugins": ["@babel/plugin-transform-arrow-functions"]
}
```
```
module.exports = function () {
return { plugins: ["pluginA", "pluginB", "pluginC"] }
}
```
```
cnpm install --save-dev @babel/preset-env
```
#### {

```
"presets": ["@babel/preset-env"]
}
```
```
//.browserslistrc
> 0.25%
not dead
```
```
last 2 Chrome versions
```
#### {

```
test: /\.js?$/,
use: {
loader: 'babel-loader',
options: {
presets: [
```

### 6.2.4 polyfill

```
@babel/preset-env默认只转换新的javascript语法，而不转换新的API，比如 Iterator,
Generator, Set, Maps, Proxy, Reflect,Symbol,Promise 等全局对象。以及一些在全局对象上的方
法(比如 Object.assign)都不会转码
比如说，ES6在Array对象上新增了Array.form方法，Babel就不会转码这个方法，如果想让这个方
法运行，必须使用 babel-polyfill来转换等
polyfill的中文意思是垫片,所谓垫片就是垫平不同浏览器或者不同环境下的差异，让新的内置
函数、实例方法等在低版本浏览器中也可以使用
官方给出@babel/polyfill和 babel-runtime 两种解决方案来解决这种全局对象或全局对象方法不
足的问题
babel-runtime适合在组件和类库项目中使用，而babel-polyfill适合在业务项目中使用。
```
**6.2.4.1 polyfill**

```
@babel/polyfill模块可以模拟完整的 ES2015+ 环境
这意味着可以使用诸如 Promise 和 WeakMap 之类的新的内置对象、 Array.from 或
Object.assign 之类的静态方法、Array.prototype.includes 之类的实例方法以及生成器函
数
它是通过向全局对象和内置对象的prototype上添加方法来实现的。比如运行环境中不支持
Array.prototype.find 方法，引入polyfill, 我们就可以使用es6方法来编写了，但是缺点就是会造成
全局空间污染
V7.4.0 版本开始，@babel/polyfill 已经被废弃
```
**6.2.4.1.1 安装**

**6.2.4.1.2 useBuiltIns:false**

```
"useBuiltIns": false 此时不对 polyfill 做操作。如果引入 @babel/polyfill，则无视配置的浏览器兼
容，引入所有的 polyfill
```
src\index.js

webpack.config.js

```
["@babel/preset-env",
{
useBuiltIns:'usage',
corejs:2,
+ targets: "> 0.25%, not dead",
}]
]
},
},
},
```
```
cnpm install --save @babel/polyfill core-js@ 3
cnpm install --save-dev webpack webpack-cli babel-loader
```
```
import "@babel/polyfill";
console.log(Array.isArray([]));
let p = new Promise();
```
```
const path = require('path');
module.exports = {
```

package.json

**6.2.4.1.3 useBuiltIns:entry**

```
"useBuiltIns": "entry" 根据配置的浏览器兼容，引入浏览器不兼容的 polyfill。需要在入口文
件手动添加 import '@babel/polyfill'，会自动根据 browserslist 替换成浏览器不兼容的
所有 polyfill
这里需要指定 core-js 的版本, 如果 "corejs": 3, 则 import '@babel/polyfill' 需要改成
import 'core-js/stable';import 'regenerator-runtime/runtime'
core-js@2 分支中已经不会再添加新特性，新特性都会添加到 core-js@3,比如
Array.prototype.flat()
```
webpack.config.js

```
mode: 'development',
entry: './src/index.js',
output: {
path: path.resolve(__dirname, 'dist'),
filename: '[name].js',
},
module: {
rules: [
{
test: /\.js?$/,
use: {
loader: 'babel-loader',
options: {
presets: [
["@babel/preset-env",
{
useBuiltIns:false
}]
]
},
},
},
]
},
plugins: []
};
```
```
"scripts": {
"build": "babel src --out-dir dist --watch",
"pack":"webpack --mode=development"
},
```
```
module: {
rules: [
{
test: /\.js?$/,
use: {
loader: 'babel-loader',
options: {
presets: [
["@babel/preset-env",
{
```

src/index.js

webpack.config.js

src/index.js

**6.2.4.1.4 useBuiltIns:usage**

```
"useBuiltIns": "usage" usage 会根据配置的浏览器兼容，以及你代码中用到的 API 来进行
polyfill，实现了按需添加
```
src\index.js

```
+ useBuiltIns:'entry',
+ corejs:2,
targets: "> 0.25%, not dead",
}]
]
},
},
},
]
},
```
```
import "@babel/polyfill";
console.log(Array.isArray([]));
let p = new Promise(resolve=>resolve('ok'));
p.then(result=>console.log(result));
```
```
module: {
rules: [
{
test: /\.js?$/,
use: {
loader: 'babel-loader',
options: {
presets: [
["@babel/preset-env",
{
useBuiltIns:'entry',
+ corejs:3,
targets: "> 0.25%, not dead",
}]
]
},
},
},
]
},
```
```
+import 'core-js/stable';
+import 'regenerator-runtime/runtime'
console.log(Array.isArray([]));
let p = new Promise(resolve=>resolve('ok'));
p.then(result=>console.log(result));
```

webpack.config.js

dist\index.js

### 6.2.5 babel-runtime

```
Babel为了解决全局空间污染的问题，提供了单独的包babel-runtime用以提供编译模块的工具函
数
简单说 babel-runtime 更像是一种按需加载的实现，比如你哪里需要使用 Promise，只要在这个
文件头部require Promise from 'babel-runtime/core-js/promise'就行了
```
**6.2.5.1 安装**

**6.2.5.2 src/index.js**

```
console.log(Array.isArray([]));
let p = new Promise(resolve=>resolve('ok'));
p.then(result=>console.log(result));
async function fn(){}
```
```
module: {
rules: [
{
test: /\.js?$/,
use: {
loader: 'babel-loader',
options: {
presets: [
["@babel/preset-env",
{
+ useBuiltIns:'usage',
+ corejs:3,
targets: "> 0.25%, not dead",
}]
]
},
},
},
]
},
```
```
"use strict";
```
```
require("core-js/stable");
```
```
require("regenerator-runtime/runtime");
```
```
console.log(Array.isArray([]));
let p = new Promise(resolve => resolve('ok'));
p.then(result => console.log(result));
async function d() {}
```
```
cnpm i babel-runtime - D
```

**6.2.5.3 dist/index.js**

### 6.2.6 babel-plugin-transform-runtime

```
启用插件babel-plugin-transform-runtime后，Babel就会使用babel-runtime下的工具函数。
babel-plugin-transform-runtime插件能够将这些工具函数的代码转换成require语句，指向
为对babel-runtime的引用
babel-plugin-transform-runtime 就是可以在我们使用新 API 时自动 import babel-runtime
里面的 polyfill
当我们使用 async/await 时，自动引入 babel-runtime/regenerator
当我们使用 ES6 的静态事件或内置对象时，自动引入 babel-runtime/core-js
移除内联babel helpers并替换使用babel-runtime/helpers 来替换
babel-plugin-transform-runtime自带的是core-js@3,如果配置corejs配置为 2 需要单独安装
@babel/runtime-corejs2
@babel/polyfill自带的core-js@2,,如果配置corejs配置为 3 需要单独安装core-js@3
@babel/plugin-transform-runtime 可以减少编译后代码的体积外，我们使用它还有一个好处，
它可以为代码创建一个沙盒环境，如果使用 @babel/polyfill及其提供的内置程序（例如
Promise ，Set 和 Map ），则它们将污染全局范围。虽然这对于应用程序或命令行工具可能是可
以的，但是如果你的代码是要发布供他人使用的库，或者无法完全控制代码运行的环境，则将成为
一个问题
```
**6.2.6.1 安装**

**6.2.6.2 webpack.config.js**

```
import Promise from 'babel-runtime/core-js/promise';
const p = new Promise((resolve)=> {
resolve('ok');
});
console.log(p);
```
```
"use strict";
```
```
var _promise = _interopRequireDefault(require("babel-runtime/core-js/promise"));
function _interopRequireDefault(obj) { return obj && obj.__esModule? obj : {
default: obj }; }
const p = new _promise.default(resolve => {
resolve('ok');
});
console.log(p);
```
```
cnpm i @babel/plugin-transform-runtime @babel/runtime-corejs2 - D
```
#### {

```
test: /\.jsx?$/,
use: {
loader: 'babel-loader',
options: {
presets: [["@babel/preset-env",{
targets: "> 0.25%, not dead",
}], '@babel/preset-react'],
plugins: [
```

**6.2.6.3 src/index.js**

### 6.2.7 执行顺序

```
插件在 Presets 前运行
插件顺序从前往后排列
presets顺序从后往
```
#### + [

```
+ "@babel/plugin-transform-runtime",
+ {
+ corejs: 2,//当我们使用 ES6 的静态事件或内置对象时自动引入 babel-
runtime/core-js
+ helpers: true,//移除内联babel helpers并替换使用babel-
runtime/helpers 来替换
+ regenerator: true,//是否开启generator函数转换成使用regenerator
runtime来避免污染全局域
+ },
+ ],
['@babel/plugin-proposal-decorators', { legacy: true }],
['@babel/plugin-proposal-class-properties', { loose: true }],
],
},
},
},
```
```
//corejs
const p = new Promise(()=> {});
console.log(p);
```
```
//helpers
class A {
```
```
}
class B extends A {
```
```
}
console.log(new B());
//regenerator
function* gen() {
```
```
}
console.log(gen());
```
```
let preset1 = { plugins: ["plugin5", "plugin6"] }
let preset2 = { plugins: ["plugin3", "plugin4"] }
```
#### {

```
"presets": ["preset1","preset"],
"plugins":['plugin1','plugin2']
}
```
```
plugin1 plugin2 plugin3 plugin4 plugin5 plugin6
```

#### 类型 含义

```
source-map 原始代码 最好的sourcemap质量有完整的结果，但是会很慢
```
```
eval-source-map 原始代码 同样道理，但是最高的质量和最低的性能
```
```
cheap-module-eval-
source-map
```
#### 原始代码（只有行内） 同样道理，但是更高的质量和更低的性能

```
cheap-eval-source-
map
```
```
转换代码（行内） 每个模块被eval执行，并且sourcemap作为eval的
一个dataurl
```
```
eval
```
```
生成代码 每个模块都被eval执行，并且存在@sourceURL,带eval的构
建模式能cache SourceMap
```
```
cheap-source-map
转换代码（行内） 生成的sourcemap没有列映射，从loaders生成的
sourcemap没有被使用
```
```
cheap-module-
source-map
```
```
原始代码（只有行内） 与上面一样除了每行特点的从loader中进行映
射
```
#### 关键字 含义

```
eval 使用eval包裹模块代码
```
```
source-
map
产生.map文件
```
```
cheap
```
```
不包含列信息（关于列信息的解释下面会有详细介绍)也不包含loader的
sourcemap
```
```
module
包含loader的sourcemap（比如jsx to js ，babel的sourcemap）,否则无法定义
源文件
```
```
inline 将.map作为DataURI嵌入，不单独生成.map文件
```
## 6.3 sourcemap

```
sourcemap是为了解决开发代码与实际运行代码不一致时帮助我们debug到原始开发代码的技术
webpack通过配置可以自动给我们source maps文件，map文件是一种对应编译文件和源文件的
方法
whyeval可以单独缓存map，重建性能更高
source-map
```
### 6.3.1 配置项

### 6.3.2 关键字

```
看似配置项很多， 其实只是五个关键字eval、source-map、cheap、module和inline的任意组合
关键字可以任意组合，但是有顺序要求
```
### 6.3.3 webpack.config.js


### 6.3.4 组合规则

```
[inline-|hidden-|eval-][nosources-][cheap-[module-]]source-map
source-map 单独在外部生成完整的sourcemap文件，并且在目标文件里建立关联,能提示错误代
码的准确原始位置
inline-source-map 以base64格式内联在打包后的文件中，内联构建速度更快,也能提示错误代码
的准确原始位置
hidden-source-map 会在外部生成sourcemap文件,但是在目标文件里没有建立关联,不能提示错
误代码的准确原始位置
eval-source-map 会为每一个模块生成一个单独的sourcemap文件进行内联，并使用eval执行
nosources-source-map 也会在外部生成sourcemap文件,能找到源始代码位置，但源代码内容为
空
cheap-source-map 外部生成sourcemap文件,不包含列和loader的map
cheap-module-source-map 外部生成sourcemap文件,不包含列的信息但包含loader的map
```
### 6.3.5 最佳实践

#### 6.3.5.1 开发环境

```
我们在开发环境对sourceMap的要求是：速度快，调试更友好
要想速度快 推荐 eval-cheap-source-map
如果想调试更友好 cheap-module-source-map
折中的选择就是 eval-source-map
```
**6.3.5.2 生产环境**

```
首先排除内联，因为一方面我们了隐藏源代码，另一方面要减少文件体积
要想调试友好 sourcemap>cheap-source-map/cheap-module-source-map>hidden-source-
map/nosources-sourcemap
要想速度快 优先选择cheap
折中的选择就是 hidden-source-map
```
### 6.3.6 调试代码

#### 6.3.6.1 测试环境调试

```
source-map-dev-tool-plugin实现了对 source map 生成，进行更细粒度的控制
filename (string)：定义生成的 source map 的名称（如果没有值将会变成 inlined）。
append (string)：在原始资源后追加给定值。通常是 #sourceMappingURL 注释。[url] 被替
换成 source map 文件的 URL
市面上流行两种形式的文件指定，分别是以 @ 和 #符号开头的,@开头的已经被废弃
```
```
module.exports = {
devtool: 'source-map',
devtool: 'eval-source-map',
devtool: 'cheap-module-eval-source-map',
devtool: 'cheap-eval-source-map',
devtool: 'eval',
devtool: 'cheap-source-map',
devtool: 'cheap-module-source-map',
}
```

webpack.config.js

```
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-
plugin');
const TerserPlugin = require('terser-webpack-plugin');
+const FileManagerPlugin = require('filemanager-webpack-plugin');
+const webpack = require('webpack');
```
```
module.exports = {
mode: 'none',
devtool: false,
entry: './src/index.js',
optimization: {
minimize: true,
minimizer: [
new TerserPlugin(),
],
},
output: {
path: path.resolve(__dirname, 'dist'),
filename: '[name].js',
publicPath: '/',
},
devServer: {
contentBase: path.resolve(__dirname, 'dist'),
compress: true,
port: 8080,
open: true,
},
module: {
```

rules: [
{
test: /\.jsx?$/,
loader: 'eslint-loader',
enforce: 'pre',
options: { fix: true },
exclude: /node_modules/,
},
{
test: /\.jsx?$/,
use: {
loader: 'babel-loader',
options: {
presets: [[
'@babel/preset-env',
{
useBuiltIns: 'usage', // 按需要加载polyfill
corejs: {
version: 3, // 指定core-js版本
},
targets: { // 指定要兼容到哪些版本的浏览器
chrome: '60',
firefox: '60',
ie: '9',
safari: '10',
edge: '17',
},
},
], '@babel/preset-react'],
plugins: [
['@babel/plugin-proposal-decorators', { legacy: true }],
['@babel/plugin-proposal-class-properties', { loose: true }],
],
},
},
include: path.join(__dirname, 'src'),
exclude: /node_modules/,
},
{ test: /\.txt$/, use: 'raw-loader' },
{ test: /\.css$/, use: [MiniCssExtractPlugin.loader, 'css-loader',
'postcss-loader'] },
{ test: /\.less$/, use: [MiniCssExtractPlugin.loader, 'css-loader',
'postcss-loader', 'less-loader'] },
{ test: /\.scss$/, use: [MiniCssExtractPlugin.loader, 'css-loader',
'postcss-loader', 'sass-loader'] },
{
test: /\.(jpg|png|bmp|gif|svg)$/,
use: [{
loader: 'url-loader',
options: {
esModule: false,
name: '[hash:10].[ext]',
limit: 8 * 1024,
outputPath: 'images',
publicPath: '/images',
},
}],
},


#### 6.3.6.2 生产环境调试

```
webpack打包仍然生成sourceMap，但是将map文件挑出放到本地服务器，将不含有map文件的
部署到服务器
```
#### {

```
test: /\.html$/,
loader: 'html-loader',
},
],
},
plugins: [
new HtmlWebpackPlugin({
template: './src/index.html',
minify: {
collapseWhitespace: true,
removeComments: true,
},
}),
new MiniCssExtractPlugin({
filename: 'css/[name].css',
}),
new OptimizeCssAssetsWebpackPlugin(),
+ new webpack.SourceMapDevToolPlugin({
+ append: '\n//# sourceMappingURL=http://127.0.0.1:8081/[url]',
+ filename: '[file].map',
+ }),
+ new FileManagerPlugin({
+ events: {
+ onEnd: {
+ copy: [{
+ source: './dist/*.map',
+ destination: 'C:/aprepare/zhufengwebpack2021/1.basic/sourcemap',
+ }],
+ delete: ['./dist/*.map'],
+ },
+ },
+ }),
],
};
```

#### 占位符名称 含义

```
ext 资源后缀名
```
```
name 文件名称
```
```
path 文件的相对路径
```
```
folder 文件所在的文件夹
```
```
hash 每次webpack构建时生成一个唯一的hash值
```
```
chunkhash 根据chunk生成hash值，来源于同一个chunk，则hash值就一样
```
```
contenthash 根据内容生成hash值，文件内容相同hash值就相同
```
## 6.4 hash、chunkhash和contenthash

#### 文件指纹是指打包后输出的文件名和后缀

```
hash一般是结合CDN缓存来使用，通过webpack构建之后，生成对应文件名自动带上对应的MD5
值。如果文件内容改变的话，那么对应文件哈希值也会改变，对应的HTML引用的URL地址也会改
变，触发CDN服务器从源服务器上拉取对应数据，进而更新本地缓存。
```
指纹占位符

### 6.4.1 hash计算

```
function createHash(){
return require('crypto').createHash('md5');
```

### 6.4.2 hash

```
Hash 是整个项目的hash值，其根据每次编译内容计算得到，每次编译之后都会生成新的hash,即
修改任何文件都会导致所有文件的hash发生改变
```
#### }

```
let entry = {
entry1:'entry1',
entry2:'entry2'
}
let entry1 = 'require depModule1';//模块entry1
let entry2 = 'require depModule2';//模块entry2
```
```
let depModule1 = 'depModule1';//模块depModule1
let depModule2 = 'depModule2';//模块depModule2
//如果都使用hash的话，因为这是工程级别的，即每次修改任何一个文件，所有文件名的hash至都将改变。
所以一旦修改了任何一个文件，整个项目的文件缓存都将失效
let hash = createHash()
.update(entry1)
.update(entry2)
.update(depModule1)
.update(depModule2)
.digest('hex');
console.log('hash',hash)
//chunkhash根据不同的入口文件(Entry)进行依赖文件解析、构建对应的chunk，生成对应的哈希值。
//在生产环境里把一些公共库和程序入口文件区分开，单独打包构建，接着我们采用chunkhash的方式生成
哈希值，那么只要我们不改动公共库的代码，就可以保证其哈希值不会受影响
let entry1ChunkHash = createHash()
.update(entry1)
.update(depModule1).digest('hex');;
console.log('entry1ChunkHash',entry1ChunkHash);
```
```
let entry2ChunkHash = createHash()
.update(entry2)
.update(depModule2).digest('hex');;
console.log('entry2ChunkHash',entry2ChunkHash);
```
```
let entry1File = entry1+depModule1;
let entry1ContentHash = createHash()
.update(entry1File).digest('hex');;
console.log('entry1ContentHash',entry1ContentHash);
```
```
let entry2File = entry2+depModule2;
let entry2ContentHash = createHash()
.update(entry2File).digest('hex');;
console.log('entry2ContentHash',entry2ContentHash);
```
```
module.exports = {
+ entry: {
+ main: './src/index.js',
+ vender:['lodash']
+ },
output:{
path:path.resolve(__dirname,'dist'),
+ filename:'[name].[hash].js'
},
```

### 6.4.2 chunkhash

```
chunkhash 采用hash计算的话，每一次构建后生成的哈希值都不一样，即使文件内容压根没有改
变。这样子是没办法实现缓存效果，我们需要换另一种哈希值计算方式，即chunkhash
chunkhash和hash不一样，它根据不同的入口文件(Entry)进行依赖文件解析、构建对应的
chunk，生成对应的哈希值。我们在生产环境里把一些公共库和程序入口文件区分开，单独打包构
建，接着我们采用chunkhash的方式生成哈希值，那么只要我们不改动公共库的代码，就可以保
证其哈希值不会受影响
```
### 6.4.3 contenthash

```
使用chunkhash存在一个问题，就是当在一个JS文件中引入CSS文件，编译后它们的hash是相同
的，而且只要js文件发生改变 ，关联的css文件hash也会改变,这个时候可以使用mini-css-
extract-plugin里的contenthash值，保证即使css文件所处的模块里就算其他文件内容改变，
只要css文件内容不变，那么不会重复构建
```
```
plugins: [
new MiniCssExtractPlugin({
+ filename: "css/[name].[hash].css"
})
]
};
```
```
module.exports = {
entry: {
main: './src/index.js',
vender:['lodash']
},
output:{
path:path.resolve(__dirname,'dist'),
+ filename:'[name].[chunkhash].js'
},
plugins: [
new MiniCssExtractPlugin({
+ filename: "css/[name].[chunkhash].css"
})
]
};
```
```
module.exports = {
plugins: [
new MiniCssExtractPlugin({
+ filename: "css/[name].[contenthash].css"
})
],
};
```



