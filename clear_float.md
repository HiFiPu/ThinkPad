# 清除浮动

## Ⅰ额外添加新标签

> 在浮动元素的末尾添加空标签`<div style="clear: left;"></div>`

```ht
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .outer {
            width: 300px;
            border: 1px solid #ccc;
            background: red;
        }

        .inner {
            width: 100px;
            height: 100px;
            background: blue;
            float: left;
        }
    </style>
</head>

<body>
    <div class="outer">
        <div class="inner"></div>
        <div style="clear: left;"></div>
    </div>
</body>

</html>
```

**原理分析**：clear：both属性会防止指定了clear的元素两边存在浮动元素。由于.new左右均不能有浮动元素，所以它必须在.inner下面找到一个位置，所以.outer就被撑开了。注意：新添加的.new一定要为块级元素，不然.new就不会换行在.inner下面找位置，而是在inner右边找位置了。

**优点**：易理解，不容易受其他属性设置影响

**缺点**：增加了dom节点数

## Ⅱ给父级标签添加`overflow:hidden`;可以触发BFC，而BFC具有清除浮动的效果

> 缺点：缺点是内容多的时候容易造成不会自动换行导致内容被隐藏

```htm
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .outer {
            width: 300px;
            border: 1px solid #ccc;
            background: red;
            overflow: hidden;
        }

        .inner {
            width: 100px;
            height: 100px;
            background: blue;
            float: left;
        }
    </style>
</head>

<body>
    <div class="outer">
        <div class="inner"></div>
    </div>
</body>

</html>
```

> 注释：`overflow:hidden/auto/scroll`都可以触发BFC清楚浮动

> BFC
>
> 块格式化上下文（Block Formatting Context，BFC） 是Web页面的可视化CSS渲染的一部分，是块盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域。
>
> 下列方式会创建块格式化上下文：

> + 根元素或包含根元素的元素
>
> + 浮动元素（元素的 `float`不是 `none`）
>
> + 绝对定位元素（元素的 `position为`absolute`或`fixed`）
>
> + 行内块元素（元素的 `display`为 `inline-block`）
>
> + 表格单元格（元素的 `display`为 `table-cell`，HTML表格单元格默认为该值）
>
> + 表格标题（元素的 `display`为 `table-caption`，HTML表格标题默认为该值）
>
> + 匿名表格单元格元素（元素的 `display`为 `table、``table-row`、 `table-row-group、``table-header-group、``table-footer-group`（分别是HTML table、row、tbody、thead、tfoot的默认属性）或 `inline-table`）
>
> + `overflow`值不为 `visible` 的块元素
>
> + `display`值为 `flow-root` 的元素
>
> + `contain`值为 `layout`、`content`或 `strict` 的元素
>
> + 弹性元素（`display`为 `flex` 或 `inline-flex`元素的直接子元素）
>
> + 网格元素（`display`为 `grid` 或 `inline-grid` 元素的直接子元素）
>
> + 多列容器（元素的 `column-count`或 `column-width`不为 `auto`，包括 `column-count`为 `1`）
>
> + `column-span` 为 `all` 的元素始终会创建一个新的BFC，即使该元素没有包裹在一个多列容器中.

> + 块格式化上下文包含创建它的元素内部的所有内容.
>
> + 块格式化上下文对浮动定位与清除浮动都很重要。浮动定位和清除浮动时只会应用于同一个BFC内的元素。浮动不会影响其它BFC中元素的布局，而清除浮动只能清除同一BFC中在它前面的元素的浮动。外边距折叠（`Margin collapsing`）也只会发生在属于同一BFC的块级元素之间。
>
> + 设置 overflow: auto 创建一个新的BFC来包含这个浮动。我们的 <div> 元素现在变成布局中的迷你布局。任何子元素都会被包含进去。
>
> + 使用 overflow 来创建一个新的BFC，是因为 overflow 属性告诉浏览器你想要怎样处理溢出的内容。当你使用这个属性只是为了创建BFC的时候，你可能会发现一些不想要的问题，比如滚动条或者一些剪切的阴影，需要注意。另外，对于后续的开发，可能不是很清楚当时为什么使用overflow。所以你最好添加一些注释来解释为什么这样做。

## Ⅲ使用after伪元素清楚浮动

> 使用after伪元素清楚浮动，优点不用单独添加标签，符合闭合浮动思想，结构语义正确；缺点：由于IE6-7不支持:after,使用zoom:1触发hasLayout

> 注意：content:"." 里面尽量跟一个小点，或者其他，尽量不要为空，否则再firefox 7.0前的版本会有生成空格。

```jsx
结合 :after 伪元素（注意这不是伪类，而是伪元素，代表一个元素之后最近的元素）和 IEhack ，可以完美兼容当前主流的各大浏览器，这里的 IEhack 指的是触发 hasLayout。
给浮动元素的容器添加一个clearfix的class，然后给这个class添加一个:after伪元素实现元素末尾添加一个看不见的块元素（Block element）清理浮动。

```

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .box1 {
            /* height: 200px; 很多情况下，我们父级盒子，不方便给高度 考虑孩子高度会变  */
            width: 600px;
            background-color: orange;
        }

        .box2 {
            width: 600px;
            height: 240px;
            background-color: purple;
        }

        .son1 {
            /*  son1 和 son2 是标准流，会撑开盒子 */
            width: 150px;
            height: 100px;
            background-color: skyblue;
            float: left;
        }

        .son2 {
            width: 300px;
            height: 100px;
            background-color: yellow;
            float: left;
        }
        
        
        .clearfix:after {
            content: ".";
            /* 内容为小点， 尽量加不要空， 否则旧版本浏览器有空隙 */
            display: block;
            /* 转换为块级元素 */
            height: 0;
            /* 高度为0 */
            visibility: hidden;
            /* 隐藏盒子 */
            clear: both;
            /* 清除浮动 */
        }

        .clearfix {
            /* ie6.7浏览器的处理方式 */
            *zoom: 1;
            /*  * 代表ie6.7能识别的特殊符号  带有这个*的属性 只有ie6.7才执行  
             zoom 就是ie6.7 清除浮动的方法 */
        }

    </style>
</head>

<body>
    <h1>浮动影响</h1>
    <div class="box1 clearfix">
        <div class="son1"></div>
        <div class="son2"></div>
    </div>
    <div class="box2"></div>

</body>

</html>
```

> 通过CSS伪元素在容器的内部元素最后添加了一个看不见的空格"020"或点"."，并且赋予clear属性来清除浮动。需要注意的是为了IE6和IE7浏览器，要给clearfix这个class添加一条*zoom:1;触发haslayout。

## Ⅳ使用双伪类元素清除浮动

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .box1 {
            /* height: 200px; 很多情况下，我们父级盒子，不方便给高度 考虑孩子高度会变  */
            width: 600px;
            background-color: pink;
        }

        .box2 {
            width: 600px;
            height: 240px;
            background-color: purple;
        }

        .son1 {
            /*  son1 和 son2 是标准流，会撑开盒子 */
            width: 150px;
            height: 100px;
            background-color: skyblue;
            float: left;
        }

        .son2 {
            width: 300px;
            height: 100px;
            background-color: hotpink;
            float: left;
        }

        .clearfix:before,
        .clearfix:after {
            /*下面这三个写法是双伪元素解决清除浮动的完整写法*/
            content: "";
            display: table;
            /* 触发bfc 还可以防止外边距合并 */
        }

        .clearfix:after {
            clear: both;
        }

        .clearfix {
            *zoom: 1;
        }
    </style>
</head>

<body>
    <div class="box1 clearfix">
        <div class="son1"></div>
        <div class="son2"></div>
    </div>
    <div class="box2"></div>

</body>

</html>
```







