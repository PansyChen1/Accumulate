### 1. let 与 const

##### 1.1 const 常量本质

> 本质：保证的不是变量的值不能改动，而是变量指向的那个内存地址不得改动。

> 对于简单类型的数据，值就是保存在变量指向的内存地址中，因此等同于常量；对于复合数据类型来说，变量指向的内存地址只是一个指针，const 只能保证指针是固定的，至于它指向的数据结构是不是可变的这是完全不能控制的。

##### 1.2 let 与 const 特性

* 不存在变量提升。变量必须在声明语句之后才可以使用；

* 暂时性死区（TDZ）：在代码块内，使用 let 命令声明变量之前，该变量都是不可用的。

* 不允许重复声明。

##### 1.3 实现一个 const 常量

``` javascript
// const 特点：
// 临时性死区
// 在定义的时候完成初始化
// 不能重新定义
// 不能重新赋值
// 语义化标识，表示声明后不可更改的变量

// 原理：
// 将 const 声明的变量绑定在全局对象上，借助 Object.defineProperty() 劫持该对象，配置相关属性
// 关键字和数字不能作为对象属性

var const_customer = function(param, value) {
    // 在浏览器端测试全局对象 window
    var _gobal = window;
    // 关键字列表
    var KEY_WORD = ['const', 'let', 'var', 'class', 'return'];
    // 数字正则表达式 
    var REG_NUMBER = '^[1-9][0-9]*([.][0-9]+)?$|0';
    var _reg = new RegExp(REG_NUMBER);

    // 检测键值是否存在且命名规则被支持（只能以数字、字母、下划线和$进行命名，但是数字不能作为变量的开头）
    if (!param || _reg.test(parseFloat(param)) || KEY_WORD.indexOf(param) > -1) {
        throw new Error( `Unexpected token: ${param}` );
    }

    // 检测是否被定义
    if (_gobal.hasOwnProperty(param)) {
        throw new Error( `${param} had already been declared!` );
    }

    _gobal[param] = value;
    // 访问器属性
    Object.defineProperty(_gobal, param, {
        configurable: false,
        enumrable: false,
        get: function() {
            return value;
        },
        set: {
            function(data) {
                // 检测赋值异常
                if (_gobal.hasOwnProperty(param)) {
                    throw new Error( `${param} is read-only!` );
                } else {
                    return value;
                }
            }
        }
    })
}
```

##### 1.4 ES6 声明变量的6种方法

* var、function、let、const、import、class。

### 2. 解构赋值

> 数组、对象、字符串、数值、布尔值都是有解构赋值的。模式匹配。
> 数组的解构元素是按照次序进行排列的，变量的取值是由它的位置决定的。而对象的属性没有次序，变量必须与属性同名才能取到正确的值。

### 3. 模板字符串

> 是增强版的字符串，用反引号标识。可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。
> 定义多行字符串，所有的空格和缩进都会被保留在输出中。

### 4. 箭头函数

* 函数体内的 this 对象就是定义时所在的对象，而不是使用时所在的对象；

* 不可以当作构造函数，也就是说不能使用 new 命令；

* 不可以受用 arguments 对象，该对象在函数体内不存在；

* 不可以使用 yield 命令，因此箭头函数不能用作 Generator 函数。

### 5. 异步编程的解决方案

> 同步：连续的执行。
> 异步：将一个任务分成两段，先执行一段，然后转为执行其他任务，等做好了准备，再回头准备执行第二段。

##### 5.1 回调函数（Callback）

> * 优点：简单、容易理解和实现。
> * 缺点：不利于代码的维护和理解，各个部分高度耦合，使得程序结构混乱，流程难以追踪，且每个任务只能指定一个回调函数，容易写出地狱回调。不能使用 try···catch 来捕获错误，不能直接 return。

##### 5.2 事件监听

> 异步任务的执行不取决于代码的顺序，而是取决于某个事件是否发生。

``` javascript
f1.on('done', f2);

function f1() {
    setTimeout(function() {
        f1.trigger('done'); // f1 执行完后，触发 done 事件，执行 f2
    }, 1000);
}
```

##### 5.3 发布订阅

> 假设存在一个“信号中心”，某个任务执行完成，向信号中心发布（publish）一个信号，其他任务可以向信号中心订阅（subscribe）这个信号，从而知道自己什么时候可以开始执行。这种模式叫做发布/订阅模式（publish-subscribe pattern），也叫做“观察者模式”（observer pattern）。

``` javascript
// jQuery 信号中心
jQuery.subscribe('done', f2);

function f1() {
    setTimeout(function() {
        jQuery.publish('done');
    }, 1000);
}

// 取消订阅
jquery.unsubscribe('done', f2);
```

##### 5.4 Promise

###### 5.4.1 Promise 基础

> Promise 是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。
> 特点：
> * 对象的状态不受外界影响。代表一个异步操作，有三种状态：Pending（进行中）、Fulfilled（已成功）、Rejected（已失败）。只有异步操作的结果能决定当前是哪一种状态。
> * 一旦状态改变就不会再变。任何时候都可以得到这个结果。

``` javascript
// resolve reject 是两个函数
// resolve 函数的作用是将 Promise 的状态从“未完成“变成“成功”，在异步操作成功时调用，并将异步操作的结果作为参数传递出去。
// reject 函数的作用是将 Promise 的状态从“未完成”变成“失败”，在异步操作失败时调用，并将异步操作报出的错误作为参数传递出去。
var promise1 = new Promise(function(resolve, reject) {
    // if(/* success */) {
    //     resolve(value);
    // }else {
    //     reject(value)
    // }
});

// Promise 实例生成之后，可以通过 then 方法指定 Resolved 状态和 Rejected 状态的回调函数。
promise1.then(function(value) {
    // success
}, function(value) {
    // failure
});

// 例子 🌰
var promise2 = new Promise(function(resolve) {
    console.log('Promise');
    resolve('resolve function');
})

promise2.then(function(value) {
    console.log('then ' + `${value}` )
})

console.log('hi')

// Promise
// hi
// then resolve function
// Promise 在创建之后就会立即执行，首先输出的是 Promise，然后 then 方法指定的回调函数将在当前脚本所有同步任务执行完成后才执行，所以最后执行。
```

###### 5.4.2 Promise.prototype.then()

> 作用：为 Promise 实例添加状态改变时的回调函数。
> 返回的结果是一个新的 Promise 实例，不是原来的那个 Promise 实例。

```javascript
// then 方法依次指定了两个回调函数，第一个回调函数完成之后，会将返回结果作为参数传递给第二个回调函数。

getJSON("/post/1.json").then(
    post => console.log(post.commentURL);
).then(
    comments => console.log("Resolved: ", comments);
    error => console.log("Rejected: ", error)
)
``` 

###### 5.4.3 Promise.prototype.catch()

> 作用：用于指定发生错误时的回调函数。是 .then(null, rejection) 的别名。then 方法指定的回调函数如果在运行中抛出错误，也会被 catch 方法捕获。

```javascript
getJSON('/posts.json').then(function(posts) {
    // ...
}).catch(function(error) {
    // 处理 getJSON 和前一个回调函数运行时发生的错误
    console.log('发生错误', error)
})

// 建议使用 catch 方法，而不是第二个 then 方法，因为可以捕获到上一个 then 方法中出现的错误。
var promise = new Promise(resolve, rejected) {};
promise.then(function(data) {
    //success
}).catch(function(error) {
    console.log(error)
})
``` 

###### 5.4.4 Promise.all()

> 用于将多个 Promise 实例包装成一个新的 Promise 实例。

```javascript
// Promise.all 方法接受一个数组作为参数，p1, p2, p3 都是 Promise 对象的实例；如果不是，就会先调用下面讲到的 Promise.resolve 方法，将参数转为 Promise 实例，再进一步处理
// p 的状态分两种情况：
// 1 p1, p2, p3 的状态都变为 Fulfilled 时，p 的状态才变为 Fulfilled ，此时 p1, p2, p3 的返回值作为一个数组传递给 p 的回调函数。
// 2 p1, p2, p3 的状态只要有一个被 Rejected，则 p 的状态就变为 Rejected，此时会将第一个被 Rejected 的实例的返回值传递给 p 的回调函数。

var p = Promise.all([p1, p2, p3]);

var promises = [2, 3, 5, 7].map(function(id) {
    return getJSON('/post' + id + '.json');
});

Promise.all(promises).then(function(posts) {
    // ...
}).catch(function(err) {
    // ...
});


// 实现

// 1.
// 核心思路
// ① 接收一个 Promise 实例的数组或具有 Iterator 接口的对象作为参数
// ② 这个方法返回一个新的 promise 对象，
// ③ 遍历传入的参数，用Promise.resolve()将参数"包一层"，使其变成一个promise对象
// ④ 参数所有回调成功才是成功，返回值数组与参数顺序一致
// ⑤ 参数数组其中一个失败，则触发失败状态，第一个触发失败的 Promise 错误信息作为 Promise.all 的错误信息。
function promiseAll(promises) {
  return new Promise(function(resolve, reject) {
    if(!Array.isArray(promises)){
        throw new TypeError(`argument must be a array`)
    }
    var resolvedCounter = 0;
    var promiseNum = promises.length;
    var resolvedResult = [];
    for (let i = 0; i < promiseNum; i++) {
      Promise.resolve(promises[i]).then(value => {
        resolvedCounter++;
        resolvedResult[i] = value;
        if (resolvedCounter == promiseNum) {
            return resolve(resolvedResult)
          }
      }, error => {
        return reject(error)
      })
    }
  })
}

// 2.
// * Promise.all
// * @description 当这个数组里的所有promise对象全部变为resolve状态的时候，才会resolve, 当有一个promise对象变为reject状态时，就不再执行直接 reject
// * @param {*} values promise对象组成的数组作为参数

Promise.prototype.all = (values) => {
  return new Promise((resolve,reject) => {
      let resultArr = [];
      let count = 0;
      let resultByKey = (value,index) => {
           resultArr[index] = value;
           if(++count === values.length){
                resolve(resultArr);
           }
      }
      values.forEach((promise, index) => {
         promise.then((value)=>{
            resultByKey(value, index);
         },reject)
      })
  })
}
``` 

###### 5.4.5 Promise.race()

> 也是将多个 Promise 实例包装成一个新的 Promise 实例。
> 只要多个实例中的其中一个率先改变状态，则 p 的状态就跟着改变。

###### 5.4.6 Promise.resolve()

> 将现有对象转为 Promise 对象。

```javascript
Promise.resolve('foo');
//  等价于
new Promise(resolve => resolve('foo'));

// 其参数分为 4 种情况：
// 1 参数是一个 Promise 实例，则不做任何修改；

// 2 参数是一个 thenable 对象（指具有 then 方法的对象）。
let thenable = {
    then: function(resolve, rejected) {
        resolve(42);
    }
};
// 将对象转为 Promise 对象，然后立即执行 thenable 对象的 then 方法。
let p1 = Promise.resolve(thenable);
p1.then(function(value) {
    console.log(value)
})

// 3 参数不是具有 then 方法的对象或者根本不是对象，则返回一个新的 Promise 对象，状态为 resolved。

// 4 不带任何参数
``` 

###### 5.4.7 Promise.reject()

###### 5.4.8 Promise.done()

> 总是在回调链的尾端，保证抛出任何可能出现的错误。

```javascript
asyncFunc()
.then(f1)
.catch(r1)
.then(f2)
.done();

// 实现原理
Promise.prototype.done = function(onFulfilled, onRejected) {
    this.then(onFulfilled, onRejected)
    .catch(function(reason) {
        // 抛出一个全局错误
        setTimeout(() => {throw reason}, 0);
    })
}
``` 

###### 5.4.9 Promise.finally()

###### 5.4.10 应用

```javascript
// 加载图片，一旦完成加载，Promise 的状态就发生变化。
const preloadImage = function(path) {
    return new Promise(resolve, reject) {
        var image = new Image();
        image.onload = resolve;
        image.onerror = reject;
        image.src = path;
    }
}
``` 

###### 5.4.11 自己实现一个 Promise

```javascript
function myPromise(constructor){
    let self = this;
    self.status = "pending" //定义状态改变前的初始状态
    self.value = undefined;//定义状态为resolved的时候的状态
    self.reason = undefined;//定义状态为rejected的时候的状态
    function resolve(value){
        //两个==="pending"，保证了状态的改变是不可逆的
       if(self.status === "pending"){
          self.value = value;
          self.status = "resolved";
       }
    }
    function reject(reason){
        //两个==="pending"，保证了状态的改变是不可逆的
       if(self.status === "pending"){
          self.reason = reason;
          self.status = "rejected";
       }
    }
    //捕获构造异常
    try{
       constructor(resolve, reject);
    }catch(e){
       reject(e);
    }
}

// 定义链式调用的then方法
myPromise.prototype.then = function(onFullfilled, onRejected){
   let self = this;
   switch(self.status){
      case "resolved":
        onFullfilled(self.value);
        break;
      case "rejected":
        onRejected(self.reason);
        break;
      default:
   }
}
``` 

##### 5.6 Generator 函数

> ES6 提供的一种异步编程解决方案。最大的特点是可以控制函数的执行。

> * 语法上，可以理解成一个状态机，封装多个内部状态；

> * 执行 Generator 函数会返回一个遍历器对象，是一个遍历器对象生成函数。返回的遍历器对象可以依次遍历 Generator 函数内部的每一个状态；
> * 使用 yield 语句定义不同的内部状态；

###### 5.6.1 yield 表达式

> yield 语句是暂停标志，在此处暂停，只会在 next 方法将指针移到这一句时才求值，且返回的是 yield 后表达式的结果。

###### 5.6.1 next 方法的参数

> yield 语句本身没有返回值，或者说总是返回 undefined。next 方法可以带一个参数，该参数会被当作上一条 yield 语句的返回值。

```javascript
function *foo(x) {
    var y = yield (x + 2);
    var z = 3 * (yield(y - 1));
    return (x + y + z);
}
var a = foo(5);

console.log(a.next()); // {value: 7, done: false}
console.log(a.next(12)); // {value: 11, done: false}
// x = 5, y = 12, z = 24
console.log(a.next(8)); // {value: 41, done: true}
```

###### 5.6.1 yield 表达式

##### 5.7 async / await

> 是 Generator 的语法糖。

> async 表示函数里有异步操作，await 表示紧跟在后面的表达式需要等待结果。async 函数完全可以看作是由多个异步操作包装成的一个 Promise 对象，而 await 命令就是内部 then 命令的语法糖。

```javascript
function timeout(ms) {
    return new Promise((resolve) => {
        setTimeout(resolve, ms);
    })
}

async function asyncPrint(value, ms) {
    await timeout(ms);
    console.log(value);
}
asyncPrint('hello', 5000);
```

### 6. 剩余参数

### 7. set、map结构

### 8. class类

### 9. symbol

### 10. Iterator 和 for…of 循环

### 11. 数值的扩展方法

### 12. 数组的扩展方法

### 13. 正则的扩展方法

### 14. 对象的扩展方法