#### Vue和React的区别、优缺点
1. Vue使用支持双向绑定，相对于React要方便许多。
2. React首拍卖行JSX，Vue默认使用template，不过后面也加入的JSX语法。
3. 学习成本Vue要比React低很多。


#### Vue wtach 与 computed 怎么实现的？
详见[这里](https://hbxywdk.github.io/2019/05/14/%E5%AD%A6%E4%B9%A0Vue%E6%BA%90%E7%A0%8112-Vue%E4%B8%AD%E7%9A%84watch%E4%B8%8Ecomputed/)


#### Vue会劫持所有的数据类型吗？
如果数据已经被定义为不可枚举，则不能劫持。


#### Vue3使用Proxy解决了哪些问题
Vue1、2使用的都是Object.defineProperty，它有如下缺点：
1. 无法监听es6的Set、WeakSet、Map、WeakMap的变化；
2. 无法监听Class类型的数据；
3. 无法监听对象属性的新加或者删除；
4. 无法监听数组元素的增加和删除；

Proxy就是解决了Object.defineProperty的一系列缺点。

还有一点：

`Object.defineProperty监听数据是要递归遍历所有的属性的`，如果数据庞大，那么遍历的时间就会长很多。同样的，`如果监听数据过大，那么Object.defineProperty用于监听数据变化的内存就会很大`。

Vue3使用Proxy后，将会对Vue的性能带来巨大的提升！


#### Vue 数据改变就会更新吗？ Vue会在何时触发更新？
不会，默认是异步更新的，这里放Watcher的update部分代码：
```
  update () {
    /* istanbul ignore else */
    if (this.lazy) {
      this.dirty = true
    } else if (this.sync) {
      // 同步更新的直接更新
      this.run()
    } else {
      // 默认异步更新，使用 queueWatcher 将wather推入队列中，下一个tick时调用
      queueWatcher(this)
    }
  }
```
然后看 queueWatcher 方法：
```
export function queueWatcher (watcher: Watcher) {
  const id = watcher.id
  // 这里判断同一个Watcher只会被推入一次
  if (has[id] == null) {
    has[id] = true
    if (!flushing) {
      queue.push(watcher)
    } else {
      // if already flushing, splice the watcher based on its id
      // if already past its id, it will be run next immediately.
      let i = queue.length - 1
      while (i > index && queue[i].id > watcher.id) {
        i--
      }
      queue.splice(i + 1, 0, watcher)
    }
    // queue the flush
    if (!waiting) {
      waiting = true

      if (process.env.NODE_ENV !== 'production' && !config.async) {
        flushSchedulerQueue()
        return
      }
      // 下一次Tick更新
      nextTick(flushSchedulerQueue)
    }
  }
}
```


#### Vue的nextTick作用与原理
作用：Vue的DOM更新是异步的，nextTick可以让我们在下次DOM更新后，拿到更新后的DOM。

原理（Vue版本2.6.8）： 

四套方案：
1. Promise
2. MutationObserver
3. setImmediate
4. setTimeout
依据运行环境的支持度，层层降级，最后的方案是都支持的 setTimeout

源码如下：
```
import { noop } from 'shared/util'
import { handleError } from './error'
import { isIE, isIOS, isNative } from './env'

export let isUsingMicroTask = false

// 用于存放要执行的callback lists
const callbacks = []
// 状态flag
let pending = false

// 这个函数会将所有点饿callback拿出来执行
function flushCallbacks () {
  pending = false
  const copies = callbacks.slice(0)
  callbacks.length = 0
  for (let i = 0; i < copies.length; i++) {
    copies[i]()
  }
}

// 定义一个定时器方法变量，在下面的代码中会根据运行环境的不同赋予其不同的值
let timerFunc

if (typeof Promise !== 'undefined' && isNative(Promise)) {
  // 1.优先使用 Promise
  const p = Promise.resolve()
  timerFunc = () => {
    p.then(flushCallbacks)
    if (isIOS) setTimeout(noop)
  }
  isUsingMicroTask = true
} else if (!isIE && typeof MutationObserver !== 'undefined' && (
  isNative(MutationObserver) ||
  // PhantomJS and iOS 7.x
  MutationObserver.toString() === '[object MutationObserverConstructor]'
)) {
  // 2.次选方案 MutationObserver，其提供了监视对DOM树所做更改的能力
  let counter = 1
  const observer = new MutationObserver(flushCallbacks)
  const textNode = document.createTextNode(String(counter))
  observer.observe(textNode, {
    characterData: true
  })
  timerFunc = () => {
    counter = (counter + 1) % 2
    textNode.data = String(counter)
  }
  isUsingMicroTask = true
} else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  // 3.降级方案 setImmediate，其优于 setTimeout
  timerFunc = () => {
    setImmediate(flushCallbacks)
  }
} else {
  // 4.如果以上都不支持，则最终会使用 setTimeout
  timerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}

export function nextTick (cb?: Function, ctx?: Object) {
  let _resolve
  
  callbacks.push(() => {
    if (cb) {
      try {
        cb.call(ctx)
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })
  // pending为false就直接执行timerFunc
  if (!pending) {
    pending = true
    timerFunc()
  }
  // $flow-disable-line
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
      _resolve = resolve
    })
  }
}

```
