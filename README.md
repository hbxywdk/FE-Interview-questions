# FE-Interview-questions
自己收集的一些前端面试题，部分来自网络，部分自己写的，不保证绝对正确，如有错误可以issues告知
##### 前端性能优化有哪些?
```
1.减少HTTP请求数
2.合理设置 HTTP缓存,很少变化的资源可以直接通过 HTTP Header中的Expires设置
一个很长的过期头 ;变化不频繁而又可能会变的资源可以使用 Last-Modifed来做请求验证
3.html,css,js,图片等资源的合并/压缩
4.CSS Sprites(精灵图)
5.Inline Images(base64图片)
6.懒加载
7.代码按需加载(webpack)
8.'将js放于页面的底部,css放于顶部',让浏览器先加载css与html结构,以便更早的渲染出
页面结构,脚本的运行会阻碍资源的加载.
9.'为script增加async 或 defer来异步加载脚本'
10.避免过多的操作DOM节点
11.使用CDN(根据用户ip,获取就进服务上的静态资源,加快访问速度)
12.添加 link dns-prefetch 对于访问过的地址直接跳过dns解析
```
##### 输入url按下回车后发生了什么?
```
1.解析URL
2.DNS解析,将域名解析为IP(本地,DNS服务器,如果查找不到,页面则无法访问)
3.通过TCP协议，向服务器发送请求(三次握手,建立连接)
4.浏览器向web服务器发起HTTP请求
5.服务器接收请求,响应请求
6.浏览器接收响应，开始下载并渲染，将页面呈现在我们面前
  {
    解析HTML生成DOM树，
    解析CSS文件生成CSSOM树，
    并解析Javascript代码
    CSS和DOM组合形成渲染树，
    进行布局，在浏览器中绘制渲染树中节点的属性(位置，宽度，大小等)
    绘制元素，绘制各个节点的可视属性(颜色背景等，此时可能会形成多个图层)
    合并图层，将页面呈现给用户面前
  }
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

##### 强缓存和协商缓存
强缓存
```
强缓存是利用http的返回头中的Expires或Cache-Control两个字段来控制的，用来表示资源的缓存时间。

Expires是http1.0的规范，值为一个绝对时间的GMT格式的时间字符串，这个时间代
表着资源的失效时间，在此时间之前，即命中缓存。这种方式有一个明显的缺点，由
于失效时间是一个绝对时间，所以当服务器与客户端时间偏差较大时，就会导致缓存混乱。

Cache-Control是http1.1时出现的，主要是利用该字段的`max-age`值来进行判断，它
是一个相对时间，例如Cache-Control:max-age=3600，代表着资源的有效期是3600
秒。
注: Expires与Cache-Control同时启用的时候Cache-Control优先级高。
```
协商缓存
```      
协商缓存是由服务器来确定缓存资源是否可用，所以客户端与服务器端要通过某种标识来进行通信，从而让
服务器判断请求资源是否可以缓存访问，这主要涉及到下面两组header字段，这两组搭档都是成对出现的，
即第一次请求的响应头带上某个字段（Last-Modified或者Etag），则后续请求则会带上对应的请求字段（If-
Modified-Since或者If-None-Match），若响应头没有Last-Modified或者Etag字段，则请求头也不会有对应的
字段。

Last-Modify/If-Modify-Since

第一次请求资源，服务器会加上Last-Modify，一个时间标识该资源的最后修改时间，
再次请求时，request的请求头中会包含If-Modify-Since，该值为缓存之前返回的Last-
Modify。服务器收到If-Modify-Since后，根据资源的最后修改时间判断是否命中缓存。
如果命中缓存，则返回304，并且不会返回资源内容，并且不会返回Last-Modify。

ETag/If-None-Match

Etag/If-None-Match返回的是一个校验码。ETag可以保证每一个资源是唯一的，资源
变化都会导致ETag变化。服务器根据浏览器上送的If-None-Match值来判断是否命中缓存。
注: ETag 优先级高于 Last-Modified
```
##### iframe有那些缺点?
```
  *iframe会阻塞主页面的Onload事件；
  *爬虫无法解读这种页面，不利于SEO;
  *iframe和主页面共享连接池，而浏览器对相同域的连接有限制，所以会影响页面的并行加载。

  使用iframe之前需要考虑这两个缺点。如果需要使用iframe，最好是通过javascript
  动态给iframe添加src属性值，这样可以绕开以上两个问题。
```
##### CSS的盒子模型
```
IE 盒模型、标准盒模型
盒模型： 内容(content)、填充(padding)、边界(margin)、 边框(border)
区  别： IE的content部分把 border 和 padding计算了进去
```

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
##### new操作符具体干了什么
```
 1、创建一个空对象，并且 this 变量引用该对象，同时还继承了该函数的原型。
 2、属性和方法被加入到 this 引用的对象中。
 3、新创建的对象由 this 所引用，并且最后隐式的返回 this 。
 var obj  = {};
 obj.__proto__ = Base.prototype;
 Base.call(obj);
```
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
##### 防抖和节流
防抖：连续触发某一事件，该事件执行函数只会在最后一次触发事件的 N 秒后执行（每次触发都会清空之前的定时器，并重新创建定时器）。
节流：连续触发某一事件，在一定时间 N 内只允许执行一次函数。

##### js继承（https://www.cnblogs.com/humin/p/4556820.html）

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
##### 阻止a标签跳转
```
  <a href="javascript:;">https://www.baidu.com</a>
  <a href="javascript: void 0;">https://www.baidu.com</a>
  // 对于页面中已存在的a标签
  var a = document.querySelector('a')
  a.onclick = function(e) {
    e.preventDefault()
  }
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
##### jq大致原理
```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
</head>
<script>
	(function(window){
		function Jquery (el) {
			return new Jquery.fn.init(el)
		}
		Jquery.fn = Jquery.prototype = {
			constructor: Jquery,
			init: function (el) {
				var el = document.querySelectorAll(el)
				this.length = el.length
				for (var i = 0; i < el.length; i++) {
					this[i] = el[i]
				}
			},
			each: function (cb) {
				for (var i = 0; i < this.length; i++) {
					cb.call(this[i], this[i], i)
				}
			},
			get:  function (index) {
				return this[index]
			},
			html: function (param) {
	            if(typeof param !== "undefined"){
	                this.each(function(){
	                    this.innerHTML = param
	                })
	            }else{
	                return this[0].innerHTML
	            }
	            return this
			}
		}
		Jquery.fn.init.prototype = Jquery.fn 
		// jq的方法定义在Jquery.prototype上，
		// Jquery.prototype.init的原型上并没有方法
		// 这一句Jquery.prototype.init与Jquery共享原型

		window.$ = Jquery
	})(window)

	// 扩展一个方法
	$.fn.get1 = function (index) {
		return this[index]
	}
</script>
<body>
	<h1 class="app">1</h1>
	<h1 class="app">2</h1>
	<h1 id="app"></h1>
</body>
<script>
	console.log($('.app').get(0))
	$('.app').each(function(e, i){
		console.log(e)
		console.log(i)
	})
	$('#app').html('FakeJquery')
</script>
</html>
```
##### vue原理 
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Two-way data-binding</title>
</head>
<body>
  
  <div id="app">
    <input type="text" v-model="text">
    {{ text }}
  </div>

  <script>
    function observe (obj, vm) {
      Object.keys(obj).forEach(function (key) {
        defineReactive(vm, key, obj[key]);
      });
    }
    function defineReactive (obj, key, val) {
      var dep = new Dep();
      Object.defineProperty(obj, key, {
        get: function () {
          // 添加订阅者watcher到主题对象Dep
          if (Dep.target) dep.addSub(Dep.target);
          return val
        },
        set: function (newVal) {
          if (newVal === val) return
          val = newVal;
          // 作为发布者发出通知
          dep.notify();
        }
      });
    }
    function nodeToFragment (node, vm) {
      var flag = document.createDocumentFragment();
      var child;
      
      while (child = node.firstChild) {
        compile(child, vm);
        flag.appendChild(child); // 将子节点劫持到文档片段中
      }
      return flag;
    }
    function compile (node, vm) {
      var reg = /\{\{(.*)\}\}/;
      // 节点类型为元素
      if (node.nodeType === 1) {
        var attr = node.attributes;
        // 解析属性
        for (var i = 0; i < attr.length; i++) {
          if (attr[i].nodeName == 'v-model') {
            var name = attr[i].nodeValue; // 获取v-model绑定的属性名
            node.addEventListener('input', function (e) {
              // 给相应的data属性赋值，进而触发该属性的set方法
              vm[name] = e.target.value;
            });
            node.value = vm[name]; // 将data的值赋给该node
            node.removeAttribute('v-model');
          }
        };
        new Watcher(vm, node, name, 'input');
      }
      // 节点类型为text
      if (node.nodeType === 3) {
        if (reg.test(node.nodeValue)) {
          var name = RegExp.$1; // 获取匹配到的字符串
          name = name.trim();
          new Watcher(vm, node, name, 'text');
        }
      }
    }
    function Watcher (vm, node, name, nodeType) {
      Dep.target = this;
      this.name = name;
      this.node = node;
      this.vm = vm;
      this.nodeType = nodeType;
      this.update();
      Dep.target = null;
    }
    Watcher.prototype = {
      update: function () {
        this.get();
        if (this.nodeType == 'text') {
          this.node.nodeValue = this.value;
        }
        if (this.nodeType == 'input') {
          this.node.value = this.value;
        }
      },
      // 获取data中的属性值
      get: function () {
        this.value = this.vm[this.name]; // 触发相应属性的get
      }
    }
    function Dep () {
      this.subs = []
    }
    Dep.prototype = {
      addSub: function(sub) {
        this.subs.push(sub);
      },
      notify: function() {
        this.subs.forEach(function(sub) {
          sub.update();
        });
      }
    };
    function Vue (options) {
      this.data = options.data;
      var data = this.data;
      observe(data, this);
      var id = options.el;
      var dom = nodeToFragment(document.getElementById(id), this);
      // 编译完成后，将dom返回到app中
      document.getElementById(id).appendChild(dom); 
    }
    var vm = new Vue({
      el: 'app',
      data: {
        text: 'hello world'
      }
    });
  </script>

</body>
</html>
```
##### 算法


```
// 1.冒泡 O(n2)
// <1>.比较相邻的元素。如果第一个比第二个大，就交换它们两个；
// <2>.对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对，这样在最后的元素应该会是最大的数；
// <3>.针对所有的元素重复以上的步骤，除了最后一个；
// <4>.重复步骤1~3，直到排序完成。
var arr1 = [3,44,38,5,47,15,36,26,27,2,46,4,19,50,48]
function bubbleSort (arr) {
	var len = arr.length
	for (var i = 0; i < len; i++) {
		for (var j = 0; j < len - 1 - i; j++) {
			if (arr[j] > arr[j+1]) {
				var t = arr[j+1]
				arr[j+1] = arr[j]
				arr[j] = t
			}
		}
		console.log(arr)
	}
	return arr
}
console.log(bubbleSort(arr1))

// 2.快排 O(nlogn)
// <1>.从数列中挑出一个元素，称为 “基准”（pivot）；
// <2>.重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区退出之后，该基准就处于数列的中间位置。这个称为分区（partition）操作；
// <3>.递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序。
var arr2 = [3,44,38,5,47,15,36,26,27,2,46,4,19,50,48]
function quickSort (arr) {
	console.time('2.快速排序耗时')
	if (arr.length <= 1) return arr
	var num = arr.splice(0, 1)
	// console.log(num)
	var left = []
	var right = []
	for (var i = 0; i < arr.length; i++) {
		if (arr[i] < num) {
			left.push(arr[i])
		} else {
			right.push(arr[i])
		}
	}
	// console.log(left)
	// console.log(right)
	console.timeEnd('2.快速排序耗时')
	return quickSort(left).concat(num, quickSort(right))
}
console.log(quickSort(arr2))

// 3.选择排序 O(n2)
// <1>.初始状态：无序区为R[1..n]，有序区为空；
// <2>.第i趟排序(i=1,2,3…n-1)开始时，当前有序区和无序区分别为R[1..i-1]和R(i..n）。该趟排序从当前无序区中-选出关键字最小的记录 R[k]，将它与无序区的第1个记录R交换，使R[1..i]和R[i+1..n)分别变为记录个数增加1个的新有序区和记录个数减少1个的新无序区；
// <3>.n-1趟结束，数组有序化了。
// 每次循环选择最小的数，将其放到前面
var arr3 = [3,44,38,5,47,15,36,26,27,2,46,4,19,50,48]
function selectionSort (arr) {
	var minIndex // 当前循环最小索引
	for (var i = 0; i < arr.length - 1; i++) { // arr.length - 1 最后一个就是最大的
		minIndex = i // 默认当前最小索引为外层循环的第一个
		for (var j = i + 1; j < arr.length; j++) { // 内循环比外循环索引多1
			if( arr[j] < arr[minIndex] ){ // 内循环的没一个与当前外循环对比
				// 保存当前最小索引为 j
				minIndex = j
				// console.log(minIndex)
			}
		}
		// 交换当前最小值
		var t = arr[i]
		arr[i] = arr[minIndex]
		arr[minIndex] = t
		// console.log(arr[i])
		// console.log(arr[minIndex])
	}
	return arr
}
console.log(selectionSort(arr3))


// 4.插入排序 O(n2)
// <1>.从第一个元素开始，该元素可以认为已经被排序；
// <2>.取出下一个元素，在已经排序的元素序列中从后向前扫描；
// <3>.如果该元素（已排序）大于新元素，将该元素移到下一位置；
// <4>.重复步骤3，直到找到已排序的元素小于或者等于新元素的位置；
// <5>.将新元素插入到该位置后；
// <6>.重复步骤2~5。
var arr4 = [3,44,38,5,47,15,36,26,27,2,46,4,19,50,48]
function insertionSort (arr) {
	if (arr.length <= 1) return arr
	for (var i = 0; i < arr.length; i++) {
		var key = arr[i]
        var j = i - 1;
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;
	}
	return arr
}
console.log(insertionSort(arr4))




// 5.归并排序 O(nlogn)
// <1>.把长度为n的输入序列分成两个长度为n/2的子序列；
// <2>.对这两个子序列分别采用归并排序；
// <3>.将两个排序好的子序列合并成一个最终的排序序列。
var arr5 = [3,44,38,5,47,15,36,26,27,2,46,4,19,50,48]
function mergeSort (arr) {
	var len = arr.length
	if(len <= 1) return arr
    var middle = Math.floor(len / 2) // 中间值
	var left = arr.slice(0, middle) // 
	var right = arr.slice(middle)
	return merge(mergeSort(left), mergeSort(right))
}
function merge (left, right) {
    var result = [];
    while (left.length && right.length) {
        if (left[0] <= right[0]) {
            result.push(left.shift());
        } else {
            result.push(right.shift());
        }
    }
    while (left.length)
        result.push(left.shift());
    while (right.length)
        result.push(right.shift());
    return result;
}
console.log(mergeSort(arr))

/*  */
// 数组去重1
var arr6 = [3,2,3,5,47,15,3,26,4,2,46,4,19,5,48]
var removeReapt = new Set(arr6)
console.log([...removeReapt])

// 数组去重2
var arr7 = [3,2,3,5,47,15,3,26,4,2,46,4,19,5,48]
function rReapt (arr) {
	var array = []
	for (var i = 0; i < arr.length; i++) {
		if (array.indexOf(arr[i]) == -1) {
			array.push(arr[i])
		}
		
	}
	return array
}
console.log(rReapt(arr7))


// 数组去重3
var arr8 = [3,2,3,5,47,15,3,26,4,2,46,4,19,5,48]
function rReapt2 (arr) {
	var obj = {}
	var array = []
	for (var i = 0; i < arr.length; i++) {
		if (!arr[i] in obj) {
			obj[arr[i]] = true
		}
	}
	for (var x in obj){
		array.push(x)
	}
	return array
}
console.log(rReapt(arr7))


```

##### 参考：
```
移动端兼容：https://blog.csdn.net/hardgirls/article/details/51722519
js继承：https://www.cnblogs.com/humin/p/4556820.html
vue原理：https://www.jianshu.com/p/c2fa36835d77
算法：https://www.cnblogs.com/beli/p/6297741.html
```
