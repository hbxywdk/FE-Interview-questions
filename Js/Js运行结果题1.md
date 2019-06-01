#### 写出以下运行结果，并给出解释
```
var str = 'HAHA'
function change1(str) {
    str = 'YOYO'
}
change1(str)
console.log(str) // HAHA
// 说明此处函数参数str是对外部变量str的拷贝

var obj = {
    name: 'HAHA'
}
function change2(obj) {
    obj.name = 'YOYO'
}
change2(obj)
console.log(obj.name) // YOYO
// 但在此处obj.name怎么被改变为了 YOYO？ 难道函数的参数传递是引用传递？这和上面就出现了冲突。

/* 解释 */
// 事实上函数的参数是拷贝传递的。
// 当参数是引用类型时，函数参数确实拷贝了一份，但拷贝出的参数的值确是一个指针，指向堆内存中的地址。
```

#### try 中 return，finally 会执行吗？
```
function tryReturn() {
    try {
        return 1
    } catch (error) {
        
    } finally {
        console.log('Finally')
    }
}
console.log(tryReturn())
```
答案是 `finally 会执行`。

#### 如果 try 和 finally 中都有 return，那么返回的到底是谁？
```
function tryReturn() {
    try {
        return 1
    } catch (error) {
        
    } finally {
        console.log('Finally')
        return 2
    }
}
console.log(tryReturn()) 
```
答案是 2，finally 中的 return 会覆盖 try 中的 return。
以上两题涉及到 Js 的运行机制，这里了解即可。