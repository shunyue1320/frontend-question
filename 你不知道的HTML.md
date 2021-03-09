# 你不知道的 Html


### H5新特性:
```
用于绘画 canvas 元素
定位
websocket
拖拽
用于媒介回放的 video 和 audio 元素。
本地离线存储 localStorage 长期存储数据，浏览器关闭后数据不丢失；sessionStorage 的数据在浏览器关闭后自动删除。
语意化标签更好的内容元素，比如 article、footer、header、nav、section。
表单控件，calendar、date、time、email、url、search
```

### 1. 移动端点击事件 300ms 延迟如何去掉
```html
问题原因：
当用户一次点击屏幕之后，浏览器并不能立刻判断用户是要进行双击缩放，还是想要进行单击操作。因此，iOS Safari 就等待 300 毫秒，以判断用户是否再次点击了屏幕。

解决方案：
1. npm 库 FastClick：()
npm install FastClick                        //安装
var attachFastClick = require('fastclick');  //使用
attachFastClick(document.body);

2. 禁用缩放 (缺点：就是必须通过完全禁用缩放来达到去掉点击延迟的目的)
当HTML文档头部包含如下meta标签时：
<meta name="viewport" content="user-scalable=no">
<meta name="viewport" content="initial-scale=1,maximum-scale=1">

3. 更改默认的视口窗口 (识别出是响应式的网站，移动端浏览器就自动禁掉默认的双击缩放行为)
<meta name="viewport" content="width=device-width">

4. css 的 touch-action 属性：
div {
  touch-action: none; (表示在该元素上的操作不会触发用户代理的任何默认行为)
}

5. 使用touchstart去代替click事件 (滑动时容易误触)
```
