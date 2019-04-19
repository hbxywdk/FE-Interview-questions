##### PC端常见兼容问题

```
1.IE6浮动的双被边距bug
解决方法：为其增加display：inline；属性
```

```
2.IE8的input没有placeholder
解决方法：内容为空且没有focus，为其设置默认文字，focus时去除默认文字；未focus且已输入文字，则不设置默认文字
```

```
3.不同浏览器的标签默认的margin和padding不同
解决方法：重置所有标签{margin:0;padding:0;}
```

```
4.li标签之间有空白
解决方法：1. 标签不要换行，首尾连接；2. 父元素设置 display：inline；3. 父元素设置 font-size: 0;
```

```
5.margin重叠（取最大的）
解决方法：两个元素外包一层div，为该div怎加overflow：hidden；（BFC）
```

##### 手机端兼容问题

```
1.字体、图片模糊
解决方法：由于像素比的原因，可根据设备像素比，动态设置meta的缩放比例。
```

```
2.ios fixed bug
解决方法：(1) header main footer 全部fixed，main区域overflow: hidden; mian区域的输入框focus，就没有问题。
（2）focus时，fixed改为absolute
```

```
3.点击iOS的可点击的元素时，覆盖显示的高亮颜色。
解决方法：-webkit-tap-highlight-color：transparent；
```

```
4.ios中滚动区域卡顿
解决方法：webkit-overflow-scrolling: touch; overflow-scrolling: touch;
```

```
5.Retina屏的1px边框
解决方法：设置1px的div，将其缩放0.5。
```

```
6.transition闪屏
解决方法：
/设置内嵌的元素在3D 空间如何呈现：保留3D /
-webkit-transform-style: preserve-3d;
/ 设置进行转换的元素的背面在面对用户时是否可见：隐藏 / 
-webkit-backface-visibility:hidden;
```
```
7.移动端点击300ms延迟
解决方法：产生的原因是手机要检测双击，解决的方法可以用zepto的tap事件，或者使用fastclick.js，或者自己使用touch相关事件来取代click。
```
```
8.Retina屏的1px边框
解决方法：设置1px的div，将其缩放0.5。
```