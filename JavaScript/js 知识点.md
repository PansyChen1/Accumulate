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

### 2. 作用域与作用域链

* 作用域 [[scope]]：存储了运行去上下文的集合。
* 作用域链：[[scope]] 中所存储的执行期上下文对象的集合，这个集合呈链式链接，我们把这种链式链接叫做作用域链。
* 执行期上下文：当函数执行时，会创建一个称为执行期上下文的内部环境。一个执行期上下文定义了一个函数执行时的环境，函数每次执行时的执行上下文丢失独一无二的，所以多次调用一个函数会导致创建多个执行期上下文，函数每次执行时，都会把新生成的执行期上下文填充到作用域链的最顶端。当函数执行完毕，它所产生的执行期上下文被销毁。

``` javascript
function a() {
    function b() {
        function c() {

        }
        c();
    }
    b();
}
a();

// 执行时产生的作用域链才是真正的作用域链

// a defined a.[[scope]] --> 0: GO
// a doing   a.[[scope]] --> 0: aAO
//                           1: GO
// b defined b.[[scope]] --> 0: aAO
//                           1: GO
// b doing   b.[[scope]] --> 0: bAO
//                           1: aAO
//                           2: GO
// c defined c.[[scope]] --> 0: bAO
//                           1: aAO
//                           2: GO
// c doing   c.[[scope]] --> 0: cAO
//                           1: bAO
//                           2: aAO
//                           3: GO
```

* 立即执行函数：针对初始化功能的函数，只执行一次之后就被销毁。

``` javascript
(function() {}()); // 建议这一种。被括号包起来之后算作函数表达式，因此能够被执行
(function() {})();
// 只有表达式才能被执行符号执行，且执行完之后会销毁，比如：
var fn6 = function(param) {
        console.log('fn6')
    }()

    +
    function fn7() {
        console.log('fn7')
    }() // + 正 负 ！ 也是立即执行函数

function fn8(a, b, c, d) {
    console.log(a + b + c + d);
}(1, 2, 3, 4) //不会报错，会识别成单独的 (1, 2, 3, 4)
```

### 3 闭包

> 内部的函数被保存在外部，一定会生成闭包。
> 形成闭包的真正原理是里面的函数比外面的函数生存的时间还长，正常来讲的话应该是一起销毁的。
> 闭包会导致原有作用域链不释放，造成内存泄漏。

``` javascript
function fn4() {
    function fn5() {
        var bb = 234;
        document.write(aa);
    }
    var aa = 123;
    return fn5; // 将 fn5 保存出来
}

var glob = 100;
var demo = fn4();
demo(); //  执行 fn5
```

##### 经典闭包例题

``` javascript
function fn9() {
    var arr = [];
    for (var i = 0; i < 10; i++) {
        // arr[i] = function () { // 在此处定义，但是在这句代码 myArr[j]() 才执行，循环的时候没有输出
        //     document.write(i + " ");
        //  }

        // 通过立即执行函数来解决，for 循环形成十个立即执行函数
        (function(j) {
            arr[j] = function() {
                document.write(j + " ");
            }
        }(i));
    }
    return arr; // 被统一保存在外部，访问的是同一个引用，访问的是 fn9 的 AO
}
var myArr = fn9();
for (var j = 0; j < 10; j++) {
    myArr[j](); // document.write(i + "")
} // 10 10 10 10 10 10 10 10 10 10
```

### 4 对象

* 对象的增删改查

``` javascript
var missChen = {
    name: "Champion",
    age: 25,
    sex: "female",
    health: 100,
    running: function() {
        console.log("I am running");
        this.health++;
    },
    vice: function() {
        console.log("I like sleeping too late");
        this.health--;
    }
}
delete missChen.age
```

* 对象的创建方法

``` javascript
// 1 
var obj = {}
plainObject
// 2 构造函数
// 1）系统自带的构造函数，相当于工厂，可以批量的生产对象，生产出来的对象长的完全一样，但是彼此独立。（工艺品生产流程）
var obj = new Object()
// 2）自定义对象（自定义构造函数）
// 构造函数命名规则遵循大驼峰。
```

* 构造函数的内部原理（执行 new 之后）

> 1 在函数体最前面隐式的加上 this = {}；
> 2 执行 this.xxx = xxx；
> 3 隐式的返回 this。

``` javascript
 function Student(name, age, sex) {
     // var this = {
     //     name: "",
     //     age: ""
     // };
     this.name = name;
     this.age = age;
     this.sex = sex
     this.grade = 2019
     // return 123; //如果显示的返回一个原始值，则默认还是会隐式的返回 this，new 的话不能返回原始值
     // return this;
 }
 var student = new Student('Champion', 25, 'female');
```

* 基本包装类型

> 每次读取一个基本包装类型值的时候，后台就会创建一个对应的基本包装类型的对象。
> Boolean()
> Number()
> String()

``` javascript
var str = "abcd";
str.lenght = 2;
// 内部隐式发生
// 1 创建 String() 类型的一个实例；
// 2 在实例上调用指定的方法；
// 3 销毁这个实例。
// new String('abcd').length = 2; delete
console.log(str, str.length) // 'abcd' 4
```

``` javascript
var str = "abc";
str += 1;
var test = typeof(str);
if (str.length == 6) {
    this.sign = "typeof 的返回结果可能是 string"
}
console.log(test.sign); // undefined
```

### 5 原型

``` javascript

```

``` javascript

```

``` javascript

```
