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

#### 深拷贝
1. JSON.parse(JSON.stringify(obj))。
这中方式有局限性，比如如果obj中有值为 undefined、function、symbol，那么这些值就会被忽略，不会被转换。
2. 递归深拷贝
```
function deepCopy(obj) {
    var result = Array.isArray(obj) ? [] : {}
    for (var key in obj) {
        if (obj.hasOwnProperty(key)) {
            if (typeof obj[key] === 'object') {
                result[key] = deepCopy(obj[key]) // 递归复制
            } else {
                result[key] = obj[key]
            }
        }
    }
    return result
}
```

##### Tips: ES6 中的 ... 展开运算符算是浅拷贝哦，它只会拷贝一层，层级多了就不行：
一层：
```
var a = { a: 1 }
var b = { ...a }
b.a = 2
console.log(a, b) // { a: 1 } , { a: 2 }
```

多层：
```
var a = { a: 1, b: { b1: 1 } }
var b = { ...a }
b.b.b1 = 2
console.log(a,b) //  { a: 1, b: { b1: 2 } } , { a: 1, b: { b1: 2 } }
```

#### 编写代码，将以下一维数组转化为树形结构
pid 为其父亲 id
```
var arr = [
    { id: '0', pid: null, val: 'A' },
    { id: '1', pid: '0', val: 'B' },
    { id: '2', pid: '0', value: 'C' },
    { id: '3', pid: '1', value: 'D' },
    { id: '4', pid: '1', value: 'E' },
    { id: '5', pid: '2', value: 'F' },
    { id: '6', pid: '3', value: 'G' },
]
```
答案：
这种输出结果是数组形式，如果要对象形式可稍作修改。
```
function transform(arr) {
    let root = arr.filter(val => val.pid === null ) // 找出根节点
    let children = arr.filter(val => val.pid !== null ) // 找出子节点
    console.log(root, children)

    function recursive(parent, child) {
        parent.forEach(p => {
            child.forEach((c, i) => {
                if (c.pid === p.id) {
                    // 当前父节点没有children则赋初始值[]，否则将当前子节点push进去
                    typeof p.children !== 'undefined' ? p.children.push(c) : p.children = [c]
                    // 以当前节点为根节点递归查找
                    recursive([c], child)
                }
            })
        })
    }
    recursive(root, children)
    return root
}
console.log( transform(arr) )
```