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