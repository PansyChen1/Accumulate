### 1. 选择器

### 2. 盒模型

> 包含了元素内容（content）、内边距（padding）、边框（border）、外边距（margin）。

> 使用 box-sizing: content-box(标准盒模型) / border-box(IE 盒模型)

##### 2.1 标准盒模型

标准盒模型的宽度 width = content。

##### 2.2 IE盒模型

IE 盒模型的宽度 width = content + padding + border。

### 3. 多列布局

### 4. 水平垂直居中

##### 4.1 居中元素定宽高使用

* 绝对定位 + margin: auto

``` css
div {
    width: 200px;
    height: 200px;
    background: green;

    position: absolute;
    left: 0;
    top: 0;
    bottom: 0;
    right: 0;
    margin: auto;
}
```

* 绝对定位 + 负 margin

``` css
div {
    width: 200px;
    height: 200px;
    background: green;

    position: absolute;
    left: 50%;
    top: 50%;
    margin-left: -100px;
    margin-top: -100px;
}
```

##### 4.2 居中元素不定宽高使用

* 绝对定位 + transform

``` css
div {
    width: 200px;
    height: 200px;
    background: green;

    position: absolute;
    left: 50%;
    /* 定位父级的50% */
    top: 50%;
    transform: translate(-50%, -50%);
    /*自己的50% */
}
```

* flex 布局

``` css
.box {
    height: 600px;

    display: flex;
    justify-content: center; //子元素水平居中
    align-items: center; //子元素垂直居中
    /* aa只要三句话就可以实现不定宽高水平垂直居中。 */
}

.box>div {
    background: green;
    width: 200px;
    height: 200px;
}
```

* table-cell

``` css
div {
    display: table-cell;
    text-align: center;
    vertical-align: middle;
}
```

### 5. rem em px vh vw

##### 5.1 绝对单位

> px

##### 5.2 相对单位

> em：相对于父元素；
> rem：相对于根元素（HTML 标签）；
> %：相对于父元素；
> vw(viewport width)：相对于视窗宽度；
> vh(viewport height)：相对于视窗高度；

font-size = 16px
1em = 16px(1* 16)
1rem = 16px
100% = 16px

100vh = 整个视窗的高度
100vw = 整个视窗的宽度

### 6 定位

>

### 7 BFC

##### 7.1 什么是 BFC
> Block Formatting Context 块级格式化上下文。它是页面中的一块渲染区域，并且有一套渲染规则，决定了其子元素将如何定位，以及和其他元素的关系和相互作用。

> 具有 BFC 特性的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且 BFC 具有普通容器所没有的一些特性。

##### 7.2 触发 BFC 的条件

* 根元素，HTML 元素；
* 浮动元素：float 除了 none 以外的值；
* 绝对定位元素：position（absolute、fixed、sticky）；
* display 为 inline-block、table-cells、table-caption；
* overflow 除了 visible 以外的值（hidden、auto、scroll）。

> 所有产生了浮动流的元素，块级元素都看不到它们；
> 产生了 BFC 的元素和文本类属性的元素以及文本都能看到浮动元素。

##### 7.3 BFC 的作用

###### 7.3.1 自适应两栏布局

> 见布局.thml 文件。

###### 7.3.2 阻止元素被浮动元素覆盖

> 给被覆盖元素增加属性，触发 BFC。

###### 7.3.3 可以包含浮动元素 —— 清除内部浮动

> 如果不清除，子元素浮动则父元素高度塌陷。给父元素加上 overflow: hidden 属性。

###### 7.3.4 解决 margin 塌陷

> 垂直方向的距离是由 margin 决定的，属于同一个 BFC 的两个相邻 margin 会发生重叠。

> 让两个相邻块级子元素分别属于不同的 BFC，给其中一个再包裹上 div，触发外面这层 div 的 BFC（overflow: hidden）来解决。
