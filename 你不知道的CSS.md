### 你不知道的 Css


## css新特性：
```css
/*1. 动态模糊（Motion Blur）*/
.animated-layer {
  /* GPU加速动画 */
  animation: rotate .5s linear infinite;
  /* 向引擎请求动态模糊
    * motion-rendering可以接受inherit | initial | auto | none | blur 值
  */
  motion-rendering: blur; 
  /* 类似于相机的快门，指的是快门角度，用来控制模糊量或模糊强度
    * motion-shutter-angle可受任意角度值 inherit | initial | auto = 180deg | [0deg, ..., 720deg]
  */
  motion-shutter-angle: 180deg;
}

@keyframes rotate {
  to {
    transform: rotate(1turn);
  }
}
```

### box-sizing 属性定义了应该如何计算一个元素的总宽度和总高度:
```
1.	box-sizing: content-box;(默认值) 标准盒子模型:  width = 内容的宽度， height = 内容的高度
2.	box-sizing: border-box;(IE盒模型) width = border + padding + 内容的宽度
3.  box-sizing: inherit;(继承父级)
```

### css3新特性：
```
选择器, 圆角(边框半径), 文本效果, 2D/3D 转换, 动画、过渡, 多列布局, 边框图片, 盒阴影, 怪异盒, 媒体查询
```
### 元素居中：
```
1.	text-align 方式
2.	定宽 margin auto 方式
3.	flex 方式
4.	position + transform 方式
```

### flex布局垂直居中：
```css
.box {
  display: flex;
  justify-content: center;  /* 水平居中 */
  align-items: center; /* 垂直居中 */
}
```