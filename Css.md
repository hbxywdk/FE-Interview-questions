##### BFC
```
块级格式化上下文
具有 BFC 特性的元素可以看作是隔离了的独立容器，'容器里面的元素不会在布局上影响到外面的元素'，并且 BFC 具有普通容器所没有的一些特性。

触发BFC: 
浮动元素：float 除 none 以外的值
绝对定位元素：position (absolute、fixed)
display 为 inline-block、table-cells、flex
overflow 除了 visible 以外的值 (hidden、auto、scroll)
```

##### CSS的盒子模型
```
IE 盒模型、标准盒模型
盒模型： 内容(content)、填充(padding)、边界(margin)、 边框(border)
区  别： IE的content部分把 border 和 padding计算了进去
```