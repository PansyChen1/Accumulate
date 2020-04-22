### JavaScript 引擎
> 使用的是谷歌 V8 引擎，V8 引擎使用在 Chrome 以及 Node 中。
      
引擎由两部分组成：
- 内存堆：内存分配发生的地方；
- 调用栈：代码执行时的地方。 

### 调用栈
> 单线程语言，只有一个调用栈，同一时间只能做一件事。

### JavaScript 的事件循环
> 执行在单线程中的任务分为同步任务和异步任务。
     
事件循环过程：
- 同步和异步任务分别进入不同的执行场所，同步任务进入主线程；异步任务进入Event Table，并注册函数；
- 当指定的事情完成时，Event Table 会将函数移入 Event Queue（事件队列）；
- 主线程内的任务执行完毕为空，会去 Event Queue 读取对应的函数，进入主线程执行；
- 上述过程会不断重复，也就是常说的 EVent Loop（事件循环）。

### setTimeout 函数
> 经过指定的时间，把要执行的任务加入到 Event Queue 中，又因为单线程任务是一个个执行，如果前面的任务需要的时间太久，那么就只能等着，等执行完成之后，才从任务队列到主线程。

setTimeout(fn, 0) 的含义是，不是会立即执行，而是指定某个任务在主线程最早可得的空闲时间执行，只要主线程执行栈内的同步任务全部执行完成，栈为空就立马执行。

> 注：即使主线程是空的，0ms 实际上也是达不到的，根据 HTML 的标准，最低是 4ms。

### setInterval 函数
> 与 setTimeout 一样，只是是循环执行。

### 微任务与宏任务
- macro-task（宏任务）：包括整体代码 script、setTimeout、setInterval；
- micro-task（微任务）：Promise、process.nextTick。
> 不同类型的任务会进入对应的事件队列。
 
事件循环的顺序，决定 js 代码的执行顺序。进入整体代码（宏任务）后，开始第一次循环。接着执行所有的微任务，然后再次从宏任务开始，找到其中一个任务队列执行完毕，再执行所有的微任务。

```javascript
setTimeout(function() {
  console.log('setTimeout');
})

new Promise(function(resolve) {
  console.log('promise');
}).then(function() {
  console.log('then');
})

console.log('console');
```
- 代码作为宏任务，进入主线程；
- 先遇到 setTimeout，将其回调函数注册后分发到宏任务任务队列；
- 接着遇到 Promise， new Promise 立即执行，then 函数被分发到微任务事件队列；
- 遇到 console.log() 立即执行；
- 至此，整体代码 script 作为第一个宏任务执行结束，然后执行微任务事件队列中的 then 函数；
- 第一轮事件循环结束，开始第二轮事件循环，从宏任务事件队列开始，发现 setTimeout 对应的回调函数，立即执行。

> 思考题（答案： 1 7 6 8 2 4 3 5 9 11 10 12）
```javascript
console.log('1');

setTimeout(function() {
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    })
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})
process.nextTick(function() {
    console.log('6');
})
new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})

setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    })
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})
```

### 总结
> JavaScript 是一门单线程语言。
> Event Loop 是 JavaScript 的执行机制。








