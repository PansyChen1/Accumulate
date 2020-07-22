### 一、选择器

### 二、盒模型

### 三、多列布局

### 四、水平垂直居中

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
