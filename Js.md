#### 数组与类数组的区别
1. 类数组拥有length属性，其它属性（索引）为非负整数（对象中的索引会被当做字符串来处理）。
2. 不具有数组所具有的方法。



#### 如何将类数组转为数组
```
// 第一种
Array.prototype.slice.call(arrayLike, start)
// 第二种
[...arrayLike]
// 第三种
Array.from(arrayLike)
```








#### Event Loop（事件循环机制）
定义：<br>
宏任务：script(整体代码)、setTimeout、setInterval、I/O、事件、postMessage、 MessageChannel、setImmediate <br>
微任务：Promise.then、 MutaionObserver、process.nextTick (Node.js)

##### JS的执行流程：
同步代码执行 <br>
👇<br>
遇到`微任务`则将其放入`微任务队列`<br>
遇到`宏任务`则将其放入`宏任务队列`<br>
👇<br>
同步代码执行完毕后，会`优先`执行`微任务队列`中的代码<br>
👇<br>
之后才会去执行`宏任务队列`中的代码<br>
👇<br>
End

##### 对于async/await的情况，我们将其当做Promise看待
```
async function a() {
  console.log(1)
  await b()
  console.log(2)
}
async function b() {
  console.log(3)
}
a() // 结果 1 3 2
```
我们将上面的代码当做Promise
```
function a() {
  console.log(1)
  new Promise((resolve, reject) =>{
    resolve(b())
  })
  .then(() => {
    console.log(2)
  })
}
function b() {
  console.log(3)
}
a() // 结果同样为 1 3 2

```

##### 我们以下面这道题目为例，来分析
```
async function async1() {
  console.log('async1 start')
  await async2()
  console.log('async1 end')
}
async function async2() {
  console.log('async2')
}
console.log('script start')
setTimeout(function() {
  console.log('setTimeout')
}, 0)
async1()
new Promise(function(resolve) {
  console.log('promise1')
  resolve()
}).then(function() {
  console.log('promise2')
})
console.log('script end')

```
1. 首先打印 `script start`
2. 接着执行到setTimeout定时器，它内部的log被加入宏队列，`此时的宏队列['setTimeout']`
3. 执行函数async1，直接会打印 `async1 start`，然后我们会执行到 await async2(),其内部的同步代码会直接执行，故打印 `async2`，之后await async2()后面的 async1 end 被加入微队列，`此时的微队列['async1 end']`
4. 继续，我们到了Promise，内部同步代码直接执行，打印 `promise1`,then中的代码，被加入微队列，`此时的微队列['async1 end', promise2]`
5. 最后一行，打印 `script end`
6. 回过头来执行微任务，打印`async1 end`和`promise2`
7. 最后执行宏队列，打印`setTimeout`

script start、async1 start、async2、promise1、script end、async1 end、promise2、setTimeout

> 这里不同的浏览器、相同浏览器的不同版本、Node.js中可能会有不同的结果，主要集中在微任务`async1 end`和`promise2`的顺序上，我们不需要管这些，只需要记住这一种流程即可。

#### 浏览器 与 Node.js 环境下 Event Loop 的区别
可以参靠这篇文章：https://www.jianshu.com/p/b221e6e36dcb<br>
需要注意的是，`Node.js 11版本之后， Event Loop 与浏览器已没有区别`。

#### 函数柯里化
柯里化（Currying），又称部分求值（Partial Evaluation），是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术.

通用实现
```
function currying(fn, ...rest1) {
  return function(...rest2) {
    return fn.apply(null, rest1.concat(rest2))
  }
}
function sayHello(name, age, fruit) {
  console.log(console.log(`我叫 ${name},我 ${age} 岁了, 我喜欢吃 ${fruit}`))
}

const curryingShowMsg1 = currying(sayHello, '小明')
curryingShowMsg1(22, '苹果')            // 我叫 小明,我 22 岁了, 我喜欢吃 苹果
const curryingShowMsg2 = currying(sayHello, '小衰', 20)
curryingShowMsg2('西瓜')               // 我叫 小衰,我 20 岁了, 我喜欢吃 西瓜

参考： https://cloud.tencent.com/developer/article/1356699

```


#### 实现add(1)(2)(3) 输出6，add(1)(2)(3)(4)输出10 
隐式转换会触发function的toString方法与valueOf方法,且两个方法都存在的话只会触发valueOf
```
  function add(x) {
    var sum = x
    var callback = function(y) {
      sum += y
      return callback
    }
    callback.toString = function() {
      return sum
    }
    return callback // 隐式转换
  }
  console.log(add(1)(2)(3))
  console.log(add(1)(2)(3)(4))
```


#### 如果要实现一个fn，使得其可以在这些：fn(1)(2)(3)(4)，fn(1, 2)(3, 4)，fn(1)(2, 3)(4)等情况下都可以处理[1, 2, 3, 4]
这里不太好描述，就是要讲不管怎么传参，都要拿到统一的处理结果：[1, 2, 3, 4]

这里就要用到函数柯里化
```
function curry(fn, args = []) {
  return function () {
    let mergeArgs = [...args, ...arguments]
    // 以函数的形参个数为判断标准，传入的参数够数了，则执行fn
    if (mergeArgs.length < fn.length) {
      return curry.call(this, fn, mergeArgs)
    } else {
      return fn.apply(this, mergeArgs)
    }
  }
} 
function fnc(a, b, c, d) {
  console.log('参数', [...arguments])
}
var fn = curry(fnc)

console.log(fn(1)(2)(3)(4)) // 参数 (4) [1, 2, 3, 4]
console.log(fn(1, 2)(3, 4)) // 参数 (4) [1, 2, 3, 4]
console.log(fn(1)(2, 3)(4)) // 参数 (4) [1, 2, 3, 4]
```


#### 如何获得函数形参与实参的个数
```
function test(a, b ,c) {
  console.log('实参的个数', arguments.length) // 2
}
test(1, 2)
console.log('形参的个数', test.length) // 3
console.log('获取函数名称', test.name) // 'test'
```


#### 为什么 0.1 + 0.2 != 0.3
因为在进制转换和进阶运算的过程中出现精度损失，不详细列了，可自行查找。
如何处理精度问题？这里有这样一个值： Number.EPSILON 它代表的是Js的最小精度值，
以此我们封装一个函数即可解决：
```
function isNumEqual(num1, num2) {
  return (Math.abs(num1 - num2) <= Number.EPSILON)
}
isNumEqual(0.1 + 0.2, 0.3) // true
isNumEqual(0.3 + 0.6, 0.9) // true
```

#### 如何理解函数也是对象这句话？直接定义具名函数与将一个匿名函数赋值给一个变量有什么区别？
函数也是一个对象，通常的函数的写法只是一种语法糖，实际都会被转换为 new Function() 形式。[MDN Function](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function)

具名函数在js运行之前就会整个被提升到顶部，故即使函数写在代码最后，也可以在第一行直接调用；
而将一个匿名函数赋值给一个变量只会讲变量名提升，函数并不会提升，在匿名函数定义之前调用则会报错。

#### 对ES6中async函数的简单理解
`async函数` 是 `Generator函数` 的语法糖。<br>
async函数的返回值是 Promise 对象，这比 Generator 函数的返回值是 Iterator 对象方便多了。可以用then方法指定下一步的操作。<br>
async函数完全可以看作多个异步操作，包装成的一个 Promise 对象，而await命令就是内部then命令的语法糖。<br>
有这样一段代码（取自阮一峰ES6入门）：
```
const fs = require('fs')
const readFile = function (fileName) {
  return new Promise(function (resolve, reject) {
    fs.readFile(fileName, function(error, data) {
      if (error) return reject(error)
      resolve(data)
    })
  })
}

const gen = function* () {
  const f1 = yield readFile('/etc/fstab')
  const f2 = yield readFile('/etc/shells')
  console.log(f1.toString())
  console.log(f2.toString())
}
```
这段Generator函数写成async函数就是这样：
```
const asyncReadFile = async function () {
  const f1 = await readFile('/etc/fstab')
  const f2 = await readFile('/etc/shells')
  console.log(f1.toString())
  console.log(f2.toString())
};
```



#### V8垃圾回收机制
[V8垃圾回收机制](https://hbxywdk.github.io/2019/04/24/V8%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E6%9C%BA%E5%88%B6/)

#### 判断两个数组是否相等
这里给出方式仅适用于数组的元素不是对象的情况

想要考虑全面可以看下 `Underscore.js`中`isEqual`方法的源码
```
function arrayEqual(arr1, arr2) {
  for (let i = 0; i < arr1.length; i++) {
    if (arr1[i] !== arr2[i]) {
      return false
    }
  }
  return true
}
var arr1 = [1, '1', 2]
var arr2 = [1, '1', 2]

arrayEqual(arr1, arr2)
```

#### 如何对比两个对象相等
这里给出方式仅适用于对象的属性不是对象的情况

想要考虑全面可以看下 `Underscore.js`中`isEqual`方法的源码
```
function objectEqual(a, b) {
  // 获取两个对象所有属性
  var aProps = Object.getOwnPropertyNames(a)
  var bProps = Object.getOwnPropertyNames(b)
  // 如果属性个数都不相同，直接返回false
  if (aProps.length != bProps.length) {
    return false
  }

  // 遍历第一个对象的属性名，对比第二个对象相应的属性，如果不一样则返回false
  for (var i = 0; i < aProps.length; i++) {
    var propName = aProps[i]
    if (a[propName] !== b[propName]) {
      return false
    }
  }

  return true
}

var obj1 = { a: 1, b: 2 }
var obj2 = { a: 1, b: 2 }

console.log(objectEqual(obj1, obj2)) // true
```


#### Object.prototype.toString.call([])的结果为什么是"[object Array]"？
先说原因：
1. 获取this对象的[[Class]]属性值
2. 拼接字符串并返回，即：`"[object " + Class名 + "]"` 

`Object.prototype.toString.call()只能用于判断原生对象哦，自定义Class请使用instanceof判断`

详细可看：https://www.jianshu.com/p/e4237ebb1cf0


#### js继承
[JS实现继承的几种方式](https://www.cnblogs.com/humin/p/4556820.html)


#### js原型&原型链
```
概念：
1.__proto__和constructor属性是对象所独有的。
2.prototype属性是函数所独有的。
3.但在Js中函数也是一种对象，所以函数也拥有__proto__和constructor属性。
4.对象（object, function）的__proto__指向 ‘构造该对象的构造函数的原型’。
5.__proto__属性的作用是当访问一个对象的属性时，如果该对象内部不存在这个属性，那么就会去它（指当前__proto__）的__proto__属性所指向的那个对象（父对象）里找，一路向上，直到__proto__属性的终点null，这条链路叫做 ‘原型链’。
6.函数的prototype属性指向一个对象，这个对象的用途就是包含所有实例共享的属性和方法，我们把这个对象叫做原型对象。
7.原型对象也有一个属性，叫constructor，这个属性指回原构造函数。

例题：
	// 正常
	function A() {}
	console.log(A.prototype) // Object
	var a = new A()
	console.log(a.__proto__) // Object
	console.log(a.__proto__ === A.prototype) // true

	// 将构造函数的prototype置为null，实例b的__proto__找不到构造函数B的原型，所以顺着原型链找到了Object的prototype
	function B() {}
	console.log(B.prototype) // Object
	B.prototype = null
	console.log(B.prototype) // null
	var b = new B()
	console.log(b.__proto__ === B.prototype) // false
	console.log(b.__proto__ === Object.prototype) // true
```


#### ajax
```
// GET
//步骤一:创建异步对象
var ajax = new XMLHttpRequest();
//步骤二:设置请求的url参数,参数一是请求的类型,参数二是请求的url,可以带参数,动态的传递参数starName到服务端
ajax.open('get','getStar.php?starName='+name);
//步骤三:发送请求
ajax.send();
//步骤四:注册事件 onreadystatechange 状态改变就会调用
ajax.onreadystatechange = function () {
   if (ajax.readyState==4 &&ajax.status==200) {
    //步骤五 如果能够进到这个判断 说明 数据 完美的回来了,并且请求的页面是存在的
　　　　console.log(xml.responseText);//输入相应的内容
  　　}
}
// POST
//创建异步对象  
var xhr = new XMLHttpRequest();
//设置请求的类型及url
//post请求一定要添加请求头才行不然会报错
xhr.setRequestHeader("Content-type","application/x-www-form-urlencoded");
 xhr.open('post', '02.post.php' );
//发送请求
xhr.send('name=fox&age=18');
xhr.onreadystatechange = function () {
    // 这步为判断服务器是否正确响应
  if (xhr.readyState == 4 && xhr.status == 200) {
    console.log(xhr.responseText);
  } 
};
```

#### new操作符具体干了什么
```
 1、创建一个空对象，并且 this 变量引用该对象，同时还继承了该函数的原型。
 2、属性和方法被加入到 this 引用的对象中。
 3、新创建的对象由 this 所引用，并且最后隐式的返回 this 。
 var obj  = {};
 obj.__proto__ = Base.prototype;
 Base.call(obj);
```

#### 实现一个 new 操作符
```
function New(func) {
    var res = {};
    if (func.prototype !== null) {
        res.__proto__ = func.prototype;
    }
    var ret = func.apply(res, Array.prototype.slice.call(arguments, 1));
    if ((typeof ret === "object" || typeof ret === "function") && ret !== null) {
        return ret;
    }
    return res;
}
var obj = New(A, 1, 2);
// equals to
var obj = new A(1, 2);
```

#### 实现call、apply 与 bind
```
// call
Function.prototype.myCall = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  context = context || window // 执行上下文，没传默认给window
  context.fn = this // this是要调用的函数 这一步很重要 必须将要执行的函数赋为context的方法
  const args = [...arguments].slice(1) // 去掉以一个入参
  const result = context.fn(...args) // 执行
  delete context.fn // 删掉添加的fn
  return result
}

// apply，apply与call的区别在于对参数的处理
Function.prototype.myApply = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  context = context || window
  context.fn = this
  let result
  // 处理参数和 call 有区别
  if (arguments[1]) {
    result = context.fn(...arguments[1])
  } else {
    result = context.fn()
  }
  delete context.fn
  return result
}

// bind
Function.prototype.myBind = function (context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  const _this = this
  const args = [...arguments].slice(1)
  // 返回一个函数
  return function F() {
    // 因为返回了一个函数，我们可以 new F()，所以需要判断
    if (this instanceof F) {
      return new _this(...args, ...arguments)
    }
    return _this.apply(context, args.concat(...arguments))
  }
}
```

#### 实现 instanceOf
```
function instanceOf(left,right) {
    let proto = left.__proto__
    let prototype = right.prototype
    while(true) {
        if(proto == null) return false
        if(proto == prototype) return true
        proto = proto.__proto__
    }
}
```

#### 实现 Promise
这里列出的是简易版
```
    // 定义三个状态
    const PENDING = 'pending'
    const RESOLVED = 'resolved'
    const REJECT = 'rejected'
    // 定义构造函数
    function MyPromise(fn){
        // that不解释了
        var that = this
        // 初始状态为pending
        this.state = PENDING
        this.value = null

        // 两个回调会在then方法里向里push
        // 成功回调
        this.resolveCallback = []
        // 失败回调
        this.recjectCallback = []

        // 定义成功方法
        const resolve = function (value) {
            if(that.state === PENDING){
                // 修改为对应状态
                that.state = RESOLVED
                // 修改对应状态的 value
                that.value = value
                // 执行所有回调
                that.resolveCallback.map(cb => cb(that.value))
            }
        }

        // 定义失败方法
        const reject = function (value) {
            if(that.state === PENDING){
                that.state = REJECT
                that.value = value
                that.recjectCallback.map(cb => cb(that.value))
            }
        }
        fn(resolve, reject)
    }
    MyPromise.prototype.then = function (success, fail) {
        // 处理参数校验
        success = typeof success === 'function' ? success : val => val
        fail = typeof success === 'function' ? success : err => { throw err }
        // Promise处于不同状态时做不同处理
        // 处于pending则向callback添加
        if(this.state === PENDING){
            this.resolveCallback.push(success)
            this.recjectCallback.push(fail)
        }
        // 非pending状态,直接调用传入的函数
        if(this.state === RESOLVED){
            success(this.value)
        }
        if(this.state === REJECT){
            fail(this.value)
        }
    }

    new MyPromise(function(resolve, reject) {
        setTimeout(() => {
            resolve('啦啦啦')
        }, 2000)
    })
    .then(function(val) {
        alert(val)
    })
```
