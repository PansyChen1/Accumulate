### 1. JavaScript 执行三部曲

1) 语法分析

* 任何变量未经声明，则该变量归 window 所有；通过 var 操作符所得出的属性，叫做不可配置的属性；不可配置的属性不能通过 delete 删除掉；
* 一切声明的全局变量全是 window 的属性。

``` javascript
a = 10; // window 是全局
console.log(window.a)
```

2) 预编译：发生在函数执行的前一刻

* 函数声明整体提升，变量 声明提升；
* 若同时存在函数提升和变量提升，则函数提升是在变量提升前面的。

``` javascript
console.log(f); // f() {}
var f = 'hello';

function f() {};
```

> 预编译环节步骤：
> 1）创建 AO 对象：Activation Object（活动对象 执行期上下文） 函数执行产生的存储空间库；
> 2）找函数中的形参和变量声明，将变量和形参作为 AO 属性名，值为 undefined；
> 3）将实参值和形参统一；
> 4）在函数体里面找函数声明，值赋予函数体。

3）解释执行

* 函数声明整体提升
* 变量 声明提升

##### 发生在函数体内的预编译

``` javascript
function fn1(a) {
    console.log(a);

    var a = 123;
    console.log(a);

    function a() {};
    console.log(a);

    var b = function() {};
    console.log(b);

    function d() {};

    fn1(1);
}

// AO {
//     a: undefined, // 1 // function a() {}
//     b: undefined,
//     arguments: {} 类数组
//     this: window
// }
```

> AO {
> a: undefined, // 1 // function a() {}
> b: undefined, 
> arguments: {} 类数组
> this: window
> }

``` javascript
function fn2(a, b) {
    console.log(a); // 1

    c = 0;
    var c;
    a = 3;
    b = 2;

    console.log(b); // 2

    function b() {};

    function d() {};
    console.log(b); // 2
}
fn2(1)

// AO {
//     a: undefined, // 1 // 3
//     b: undefined, // function b() {} // 2
//     c: undefined, // 0
// }
```

``` javascript
function fn3(a, b) {
    console.log(a); //function a() {} 函数声明 整体提升
    console.log(b); // undefined

    var b = 234;

    console.log(b); // 234 上一行赋值

    a = 123;

    console.log(a); // 123 上一行赋值

    function a() {};
    var a;
    b = 234;
    var b = function() {};

    console.log(a); // 123 
    console.log(b); // function() {} 第149行赋值
}
fn3(1)

//   AO {
//       a: undefined, // 1 //function
//       b: undefined // 
//   }
```

##### 发生在全局的预编译

生成的是 GO （window）对象，与发生在函数体内的预编译是一样的。

``` javascript
function bar() {
    return foo; // 11
    foo = 10;

    function foo() {}
    var foo = 11;
}
console.log(bar());

//  AO {
//      foo: undefined // 11
//  }
```

``` javascript
console.log(bar())

function bar() {
    foo = 10;

    function foo() {}
    var foo = 11;
    return foo; // 11
}
//  AO {
//      foo: undefined // 11
//  }
```

``` javascript
// 面试题
var str = false + 1;
document.write(str); // 1
var demo = false == 1;
document.write(demo); // false
// "undefined" && "NAN"
if (typeof(a) && -true + (+undefined) + "") {
    document.write('基础扎实');
}
// 11 + 22 == 33
if (11 + "11" * 2 == 33) {
    document.write('基础扎实');
}
// true + false - false 1 + 0 - 0 = 1
!!" " + !!"" - !!false || document.write('can it console')
```

### 2. 作用域

``` javascript

```

``` javascript

```
