##### js继承
[JS实现继承的几种方式](https://www.cnblogs.com/humin/p/4556820.html)

##### js原型&原型链
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

##### 实现add(1)(2)(3) 输出6，add(1)(2)(3)(4)输出10 
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
    return callback
  }
  console.log(add(1)(2)(3))
  console.log(add(1)(2)(3)(4))
```

##### ajax
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

##### new操作符具体干了什么
```
 1、创建一个空对象，并且 this 变量引用该对象，同时还继承了该函数的原型。
 2、属性和方法被加入到 this 引用的对象中。
 3、新创建的对象由 this 所引用，并且最后隐式的返回 this 。
 var obj  = {};
 obj.__proto__ = Base.prototype;
 Base.call(obj);
```

##### 实现一个 new 操作符
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

##### 实现call、apply 与 bind
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

##### 实现 instanceOf
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
##### 实现 Promise
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
