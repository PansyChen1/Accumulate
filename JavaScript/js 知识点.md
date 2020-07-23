## 目录

* [1. JavaScript执行三部曲](#1.JavaScript执行三部曲)
* [2. 作用域与作用域链](#2.作用域与作用域链)
* [3. 闭包](#3.闭包)
* [4. 对象](#4.对象)
* [5. 原型](#5.原型)
* [6. 原型链](#6.原型链)
* [7. 继承](#7.继承)
* [8.this指向](#8.this指向)
* [9. 遍历对象 for···in](#9.遍历对象for···in)
* [10. 深浅拷贝](#10.深浅拷贝)
* [11. 类数组](#11.类数组)
* [12. 封装一个函数用来判断 js 中的数据类型](#12.封装一个函数用来判断js中的数据类型)
* [13. 数组去重](#13.数组去重)
* [14. 事件](#14.事件)
* [](#)
* [](#)
* [](#)

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

### 3. 闭包

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

### 4. 对象

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

### 5. 原型

* 定义：原型是 function 对象的一个属性，它定义了构造函数造出的对象的公有祖先。

> 通过该构造函数产生的对象，可以继承该原型的属性和方法。原型也是对象。
> Person.prototype 原型
> Person.prototype = {} 是祖先

``` javascript
Person.prototype.lastName = "Chen";
Person.prototype.say = function() {
        console.log('hi);
        }

        function Person() {
            // new 时，隐式
            // var this ={
            //    __proto__: Person.prototype
            //}
        }

        var person = new Person();
        var person1 = new Person();
        console.log(person.lastName, person1.lastName) // "Chen" "Chen"
```

* 构造函数

> 构造函数是可以手动改变的。

``` javascript
function Car() {}
var car = new Car();
car.constructor = function FalseCar() {};
console.log(car.constructor) // function Car() {}
console.log(car.prototype) // constructor __proto__
```

### 6. 原型链

> 构造函数、原型和实例的关系：
> 每个构造函数都有一个原型对象 prototype，原型的对象都包含一个指向构造函数的指针，而实例都包含一个指向原型对象的内部指针。

##### * 让原型对象等于另一个类型的实例，此时的原型的对象将包含一个指向另一个原型的指针，相应地，另一个原型中也包含着一个指向另一个构造函数的指针。假如另一个原型又是另一个类型的实例，那么上述关系依然成立，如此层层递进，就构成了实例与原型的链条，这就是所谓原型链的基本概念。

``` javascript
Grand.prototype.lastName = "Chen";

function Grand() {
    // new 时，隐式生成
    // var this = {
    //   __proto__: Grand.prototype
    //}
}
var grand = new Grand();

Father.prototype = grand;

function Father() {};
var father = new Father();

Son.protype = father;

function Son() {
    hobbit: "Running"
};
var son = new Son();
```

### 7. 继承

> 绝大多数对象的最终都会继承自 Object.prototype。
> 特例：Object.create(null) 创建出来的对象没有原型，如果手动加上原型的话，系统是不承认的。且该方法只能传入一个 Object 或者 null。

* 1）传统形式：原型链

缺点：继承了太多不想要的属性。

* 2）借用构造函数

缺点：1> 不能继承借用构造函数的原型；
2> 每次构造函数都要多走一个函数。

* 3）共享原型

缺点：不能随便改动自己的原型。

``` javascript
 Fruit.prototype.type = "hot"

 function Fruit() {}

 function Apple() {}
 Apple.prototype = Fruit.prototype; // 共享原型，多个构造函数共用一个原型
 var apple = new Apple();
 var fruit = new Fruit();
 // Son.prototype.sex = "male"; // 增加自己的属性和方法时，会改变原有的

 // 源函数和目标函数
 function inherit1(Target, Origin) {
     Target.prototype = Origin.prototype;
 }
```

* 4）圣杯模式

``` javascript
 function inherit2(Target, Origin) {
     function F() {}; // 中间函数
     F.prototype = Origin.prototype;
     Target.prototype = new F();
     Target.prototype.constructor = Target; // 将其构造函数归位
     Target.prototype.uber = Target.prototype; // 知道真正继承的是谁
 }

 // 高级写法
 // var inherit = (function() {
 //     var F = function() {}; // 保存的私有化变量，闭包的其中一个作用。通过外部访问不到，但是自己内部是存在的
 //     return function(Target, Origin) {
 //         F.prototype = Origin.prototype;
 //         Target.prototype = new F();
 //         Target.prototype.constructor = Target; // 将其构造函数归位
 //         Target.prototype.uber = Target.prototype; // 知道真正继承的是谁
 //     }
 // }());

 function Fruit2() {};

 function Pear() {};

 inherit2(Pear, Fruit2);

 var fruit2 = new Fruit2();
 var pear = new Pear();
```

### 8. this 指向

* 1）预编译环节 this 指向 window；
* 2）谁调用的 this 就指向谁；
* 3）call apply 改变 this 指向；
* 4）全局 this 指向 window。

``` javascript
// call / apply
// 作用： 改变 this 指向，this 不用的话，默认指向 window。
// 借用别人的函数，实现自己的功能。
// 两者的区别：
// 传参方式的不同：call 需要把实参按照形参的个数传递进去；apply 需要传一个 arguments 数组。

function Fn13(name, age) {
    // obj.name = name
    // obj.age = age
    // 相当于往 obj 对象中写入属性
    this.name = name;
    this.age = age;
}
var fn13 = new Fn13("Chen", 25);
var obj = {

}
// Fn13(); 直接执行的话，会隐式的调用 Fn13.call()
Fn13.call(obj, "Cheng", 24) // 让 Fn13()中所有的 this 都变成 obj
console.log("obj 对象被写入了属性", obj)
```

``` javascript
function Wheel(wheelSize, style) {
    this.wheelSize = wheelSize;
    this.style = style;
}

function Sit(c, sitColor) {
    this.c = c;
    this.sitColor = sitColor;
}

function Model(height, width, len) {
    this.height = height;
    this.width = width;
    this.len = len;
}

function Car(wheelSize, style, c, sitColor, height, width, len) {
    Wheel.call(this, wheelSize, style);
    Sit.call(this, c, sitColor);
    Model.apply(this, [height, width, len]);
}
var car = new Car(100, "colorful", "真皮", "yellow", 1800, 1000, 200);
```

### 9. 遍历对象 for...in

``` javascript
var obj = {
    name: "Champion",
    sex: "female",
    age: 25,
    height: 164,
    weight: 57,
}
for (var prop in obj) {
    // console.log(prop + " " + typeof(prop));
    // console.log(obj[prop]);

    // hasOwnProperty 用来判断属性是原型上的还是自己的，对象上都有这个方法
    if (obj.hasOwnProperty(prop)) {
        console.log(obj[prop]);
    }
}
```

``` javascript
// A instance of B 
// 看 A对象的原型链上有没有 B 的原型
// A对象 是不是 B构造函数构造出来的
```

### 10. 深浅拷贝

##### 10.1 浅拷贝
> 对基本数据类型进行了值传递，对引用数据类型进行了引用的传递，而没有真实的创建一个新的对象，则为浅拷贝。

``` javascript
var lightCloneObj = {
    name: "Clone",
    age: "123",
    sex: "female"
}

var lightCloneObj1 = {}

function clone(target, origin) {
    var target = target || {};
    for (var prop in origin) {
        target[prop] = origin[prop];
    }
    return target;
}
clone(lightCloneObj1, lightCloneObj);
```

##### 10.2 深拷贝
> 对基本数据类型进行了值传递，对引用数据类型创建了一个新对象，并复制其内容，则为深拷贝。

###### 10.2.1 深拷贝的实现方式

* Object.assign(target, ...origin)

> 是一种可以对非嵌套对象进行深拷贝的方法，如果对象中出现了嵌套的情况，则对被嵌套对象的行为就变成了浅拷贝。

* 转成 JSON

> 用 JSON.stringfy() 把对象转换成字符串，再用 JSON.parse() 把字符串转成新的对象。

```javascript
var origin = {
    'name': 'Champion'
}
var target = JSON.parse(JSON.stringfy(origin));
```

* 采用递归的方式进行深拷贝

``` javascript
var cloneObj = {
    name: "Chen",
    age: 123,
    card: ['visa', 'master'],
    husband: {
        name: "Chen",
        son: {
            name: "aaa"
        }
    }
}
var cloneObj1 = {}

// 遍历对象 for(var prop in obj)
// 1 判断是不是原始值 typeof
// 2 如果是引用值，判断是数组还是对象， instanceof() toString() constructor
// 3 建立相应的数组或对象
// 递归

function deepClone(origin, target) {
    var target = target || {},
        toString = Object.prototype.toString,
        arrStr = "[object Array]";

    for (var prop in origin) {
        if (origin.hasOwnProperty(prop)) {
            if (origin[prop] !== "null" && typeof(origin[prop]) == "object") {
                target[prop] = toString.call(origin[prop]) == arrStr ? [] : {};
                deepClone(origin[prop], target[prop]);
            } else {
                target[prop] = origin[prop];
            }
        }
    }
    return target
}
```

### 11. 类数组

``` javascript
// 1 属性要为索引（数字）属性
// 2 必须有 length 属性
// 3 最好加上 push

var similarArr = {
    "3": "a",
    "4": "b",
    "5": "c",
    "length": 3,
    "push": Array.prototype.push,
    // "splice": Array.prototype.splice 加上这个更像是一个数组
}

for (var prop in similarArr) {
    console.log(similarArr[prop]);
}

similarArr.push('d');
similarArr.push('e');
console.log("similarArr----", similarArr);

similarArr = {
    "3": "d",
    "4": "e",
    "5": "c",
    "length": 5,
    "push": Array.prototype.push
}

Array.prototype.push = function(target) {
    // similarArr[similarArr.lenght] = target;
    this[this.length] = target;
    this.length++;
}
```

### 12. 封装一个函数用来判断 js 中的数据类型

``` javascript
// 分两类 原始值 和 引用值
// 区分引用值
function type(target) {
    var ret = typeof(target);
    var template = {
        "[object Object]": "Object",
        "[object Array]": "Array",
        "[object Boolean]": "Boolean object",
        "[object String]": "String object",
        "[object Number]": "Number object"
    }
    if (target === null) {
        return "null";
    } else if (ret == "object") {
        var str = Object.prototype.toString.call(target);
        return template[str];
    } else {
        return ret;
    }
}
```

### 13. 数组去重

``` javascript
// 通过 hash 的方式：将出现的数组项作为索引存在一个对象中，依次比对
var uniqueArr = [1, 1, 1, 2, 2, 4, 5, 5];
var uniqueObj = {
    "1": "abc",
    "2": "abc",
    "4": "abc",
    "5": "abc"
}

// uniqueObj[1] undefined,
// uniqueObj[1] abc,
// uniqueObj[2] undefined,
// uniqueObj[2] abc,
// uniqueObj[4] undefined,
// uniqueObj[5] undefined,
// uniqueObj[5] abc

Array.prototype.unique = function() {
    var temp = {};
    var arrNew = [];
    var len = this.length;
    for (var i = 0; i < len; i++) {
        // this[i] 代表数组的第几位
        if (!temp[this[i]]) {
            // 此处 “abc” 必须为一个不为假的字符串
            temp[this[i]] = "abc";
            arr.push(this[i]);
        }
    }
    return arr;
}
```

### 14. 事件

* 三种绑定事件处理函数的方式：

``` javascript
// 1）ele.onXXX = function(event) {}
// 好处：兼容性很好，但是一个元素的同一个时间只能绑定一个方法。
// 基本等同于写在 html 行间上。
// 句柄的形式

div.onclick = function() {
    console.log('onclick')
    console.log(this) // 程序 this 指向的是 dom 元素本身
    // this.onclick = null; 事件执行一次之后就失效了
    this.style.backgroundColor = "green";
}

//2）obj.addEventListener(type. fn, false);
// 可以给同一个事件的同一个对象绑定多个事件处理函数

div.addEventListener('click', function() {
    console.log('addEventListener');
    console.log(this); // 程序 this 指向的是 dom 元素本身
}, false)

// 3）obj.attachEvent('on' + type, fn)
// IE 独有，一个事件同样可以绑定多个事件处理函数。

div.attachEvent('onclick', function() {
    console.log('attachEvent'); // 程序 this 指向的是 window
})
```

* 解除事件处理函数

``` javascript
// 1）
elem.onclick = false / null;
// 2)
elem.removeEventListener(type, fn, false);
// 3)
elem.detachEvent('on' + type, fn)
```

* 封装一个兼容性的方法 addEvent(elem, type, handle)，给 dom 对象添加该事件类型的处理函数。

``` javascript
function addEvent(elem, type, handle) {
    if (elem.addEventListener) {
        elem.addEventListener(type, handle, false);
    } else if (elem.attachEvent) {
        elem.attachEvent('on' + type, function() {
            handle.call(elem);
        })
    } else {
        elem['on' + type] = handle;
    }
}
```

* 事件处理模型 --- 事件冒泡和事件捕获

> 事件冒泡：结构（非视觉）上嵌套关系的元素，会存在事件冒泡的功能，即同一事件，自子元素冒泡至父元素（自底向上）。

> 事件捕获：结构（非视觉）上嵌套的关系，会存在事件捕获的功能，即同一事件，自父元素捕获至子元素（事件源元素）（自顶向下）。

> 触发顺序：先捕获，后冒泡。

> focus，blur，change，subnit，reset，select 等事件不冒泡。

``` javascript
var wrapper = document.getElementsByClassName('wrapper')[0];
var content = document.getElementsByClassName('content')[0];
var box = document.getElementsByClassName('box')[0];

wrapper.addEventListener('click', function() {
    console.log('wrapperBubble')
}, false);
content.addEventListener('click', function() {
    console.log('contentBubble')
}, false);
box.addEventListener('click', function() {
    console.log('boxBubble')
}, false);

wrapper.addEventListener('click', function() {
    console.log('wrapper')
}, true); // 改为 true，则是捕获事件
content.addEventListener('click', function() {
    console.log('content')
}, true);
box.addEventListener('click', function() {
    console.log('box')
}, true);
```

* 取消事件冒泡

``` javascript
// W3C 标准 event.stopPropagation()；但不支持 ie9 以下的版本
// IE 独有 event.cancelBubble = true; google 也有
document.onclick = function(e) {
    console.log('document')
};

wrapper.onclick = function(e) {
    // e.stopPropagation();
    // e.cancelBubble = true;
    stopBubble(e);
    console.log('box')
};
```

* 封装一个取消冒泡的方法 stopBubble(event)

``` javascript
function stopBubble(event) {
    if (event..stopPropagation) {
        event.stopPropagation()
    } else {
        event.stopBubble = true;
    }
}
```

* 取消默认事件

> 默认事件：表单提交，a标签跳转，右键菜单等。

``` javascript
// 取消右键菜单
document.oncontextmenu = function(e) {
    console.log('a');
    // return false; // 只适用于句柄
    // e.preventDefault();
    // e.returnValue = false; // 兼容 IE
    cancelHandler(e)
}
```

* 封装一个组织默认事件的函数 cancelHandler(event)

``` javascript
function cancelHandler(event) {
    if (event.preventDefault) {
        event.preventDefault();
    } else {
        e.returnValue = false
    }
}
```

* 事件源对象

> 事件对象 event，只能传一个，在 IE浏览器上会失效，IE 浏览器保存在 window.event 上。
> 火狐浏览器：event.target；IE 浏览器：event.srcElement。

``` javascript
document.onclick = function(e) {
    var event = e || window.event;
    console.log(event)
    var target = event.target || event.srcElement;
    console.log(target)
};
```

* 事件委托

``` javascript
// 利用事件冒泡和事件源对象进行处理。
// 优点：
// 1 性能提升 不需要循环所有的元素一个个绑定事件
// 2 灵活 当有新的子元素时不需要重新绑定事件

// 事件委托的例子
var eventDom = document.getElementsByClassName('event')[0];
eventDom.onclick = function(e) {
    var event = e || window.event;
    var target = event.target || event.srcElement;
    console.log(target.innerText);
}
```

### 原始数据类型
- Boolean
- Number
- String
- Null
- Undefined
- Symbol
- BigInt

### 



