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

##### 左右布局，高度自适应
HTML结构：
```
<div class="container">
    <div class="left">
        left
    </div>
    <div class="right">
        right
    </div>
</div>
```
- 第一种 float
```
  *{ padding: 0; margin: 0; }
  html, body{ height: 100%;}
  .container{ height: 100%; }
  .left{ float: left; height: 100%; width: 300px; background: gray; }
  .right{ height: 100%; margin-left: 300px; background: #bfb9ba; }
```
- 第二种 flex
```
  *{ padding: 0; margin: 0; }
  html, body{ height: 100%;}
  .container{ height: 100%; display: flex; }
  .left{ width: 300px; background: gray; }
  .right{ background: #bfb9ba; width: 100%; flex: 1; }
```
- 第三种 定位布局
```
  *{ padding: 0; margin: 0; }
  html, body{ height: 100%;}
  .container{ height: 100%; width: 100%;position: relative; }
  .left{ background: gray;  width: 300px; height: 100%; }
  .right{ background: #bfb9ba; position: absolute; top: 0; bottom: 0; left: 300px; right: 0; }
```

##### div水平、垂直居中
HTML结构：
```
  <div class="container">
    <div class="box">
      BOX
    </div>
  </div>
```
- 第一种 上、左50%定位，负上、左margin（未知父元素尺寸）
```
  *{ padding: 0; margin: 0; }
  html, body{ height: 100%;}
  .container{ height: 100%; width: 100%;position: relative; }
  .box{ background: gray;  width: 200px; height: 200px; position: absolute; top: 50%; left: 50%; margin-top: -100px; margin-left: -100px; }
```
- 第二种 定位，上下左右全为0，margin: auto;
```
  *{ padding: 0; margin: 0; }
  html, body{ height: 100%;}
  .container{ height: 100%; width: 100%;position: relative; }
  .box{ background: gray;  width: 200px; height: 200px; position: absolute; top: 0; left: 0; right: 0; bottom: 0; margin: auto; }
```
- 第三种 display: flex;
```
  *{ padding: 0; margin: 0; }
  html, body{ height: 100%;}
  .container{ height: 100%; width: 100%;display: flex; display: -webkit-flex; align-items: center; justify-content: center; }
  .box{ background: gray;  width: 200px; height: 200px;  }
```
- 第四种 display: table;
- 第五种 在第一种基础上使用 translate
- 第六种 在第一种基础上，如果知道父元素尺寸，如果知道，直接给子元素固定的top、left即可
- ...太多了，不列了。