#### object.create()作用？
Object.create()方法创建一个新对象，新创建的对象的__proto__会被赋值为该方法的第一个参数。
如果使用 Object.create(null) 那么得到的就是一个非常干净的的空对象，连原型都没有。

#### 如何在低版本浏览器实现object.create()？
```
Object.create =  function (o) {
    var F = function () {};
    F.prototype = o;
    return new F();
};
```

#### 为什么很多框架都会在源码中大量使用 object.create(null) 定义对象呢？
1. 因为它返回的是一个非常干净的空对象，可以自己定义 hasOwnProperty、toString 等方法，不用担心会覆盖掉原型链上的同名方法。
2. for..in 循环会遍历对象原型链上的属性，使用 object.create(null) 就可以不用担心遍历到原型链上的属性了，提升了性能。