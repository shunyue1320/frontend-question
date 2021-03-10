### 你不知道的 Css

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
  justify-content: center;
  align-items: center;
}
```