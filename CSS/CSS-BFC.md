[toc]

# CSS-BFC

## 什么是 BFC

在一个Web页面的CSS渲染中，块级格式化上下文 (Block Fromatting Context)是按照块级盒子布局的。W3C对BFC的定义如下：

> 浮动元素和绝对定位元素，非块级盒子的块级容器（例如 inline-blocks, table-cells, 和 table-captions），以及overflow值不为“visiable”的块级盒子，都会为他们的内容创建新的BFC（块级格式上下文）。即：
>
>    1、浮动元素，float 除 none 以外的值； 
>    2、定位元素，position（absolute，fixed）； 
>    3、display 为以下其中之一的值 inline-block，table-cell，table-caption； 
>    4、overflow 除了 visible 以外的值（hidden，auto，scroll）；

BFC是一个独立的布局环境，其中的元素布局是不受外界的影响，并且在一个 BFC 中，块盒与行盒（行盒由一行中所有的内联元素所组成）都会垂直的沿着其父元素的边框排列。

块格式化上下文(BFC)的行为通过一个简单的`float`示例很容易理解。在下面的示例中，我有一个框，其中包含向左浮动的图像和一些文本。如果我们有足够多的文本，它会环绕浮动的图像和边框，然后环绕整个区域。

**BFC的特性：**

   1.内部的Box会在垂直方向上一个接一个的放置。
   2.垂直方向上的距离由margin决定
   3.bfc的区域不会与float的元素区域重叠。
   4.计算bfc的高度时，浮动元素也参与计算
   5.bfc就是页面上的一个独立容器，容器里面的子元素不会影响外面元素。

**例子：**

```html
<!--html-->
<div class="outer">
  <div class="float">I am a floated element.</div>
  I am text inside the outer box. * N
</div>
```

```css
/*css*/
.outer {
  border: 5px dotted rgb(214,129,137);
  border-radius: 5px;
  width: 450px;
  padding: 10px;
  margin-bottom: 40px;
}

.float {
  padding: 10px;
  border: 5px solid rgba(214,129,137,.4);
  border-radius: 5px;
  background-color: rgba(233,78,119,.4);
  color: #fff;
  float: left;  
  width: 200px;
  margin: 0 20px 0 0;
}
```

**效果：**

<img src="https://segmentfault.com/img/bVbquvb"/>

如果我删除了一些文本，那么就没有足够的内容来包围图像，而且由于浮动被从文档流中脱离，所以边框会上升，并在图像下方，直到文本的高度:

<img src="https://segmentfault.com/img/bVbquv2"/>

这是因为float元素已经脱离了文档流。

我们通常有两种方法来解决这个布局问题。一种方法是使用 `clearfix hack`，它的作用是在文本和图像下面插入一个元素，并将其设置为 `clear:both`。另一种方法是使用 `overflow` 属性，其值不是缺省值 `visible`。

```css
/*clearfix*/
.outer:before,.outer:after{
  content: '';
  display: block;
  clear:both;
}

.outer {
  overflow: auto;
}
```

`overflow` 以这种方式工作的原因是，使用 `visible` 的初值以外的任何值都会创建一个块格式化上下文，而 BFC 的一个特性是它包含浮动。

## BFC 是布局中的一个迷你布局

你可以将 BFC 看作是页面内的一个迷你布局。一旦一个元素创建了一个 BFC，它就包含了所有的内容。正如我们所看到的，这包括浮动的元素，它们不再从盒子底部伸出来。BFC 还会导致一些其他有用的行为。

> BFC 可以防止 margin 折叠

了解边距合并是另一个被低估的 CSS 技能。在下一个示例中，假设有一个背景颜色为灰色的 `div`。

这个 `div` 包含两个标签 `p`。外部 div 元素的 `margin-bottom` 为 40 像素，标签 `p` 的顶部和底部 `margin` 都是 20 像素。

```css
// html
<div class="outer">
  <p>I am paragraph one and I have a margin top and bottom of 20px;</p>
  <p>I am paragraph one and I have a margin top and bottom of 20px;</p>
</div>
  
// css
.outer {
   background-color: #ccc;
  margin: 0 0 40px 0;
}

p {
  padding: 0;
  margin: 20px 0 20px 0;
  background-color: rgb(233,78,119);
  color: #fff;
}
```

因为 `p` 元素的 margin 和外部 `div` 上的 margin 之间没有任何东西，所以两个会折叠，因此 `p` 最终与 `div` 的顶部和底部齐平。 我们在 `p` 的上方和下方看不到任何灰色。

![图片描述](https://segmentfault.com/img/bVbqwQT)

在CSS当中，相邻的两个盒子（可能是兄弟关系也可能是祖先关系）的外边距可以结合成一个单独的外边距。这种合并外边距的方式被称为折叠，并且因而所结合成的外边距称为折叠外边距。折叠的结果按照如下规则计算：

1. 两个相邻的外边距都是正数时，折叠结果是它们两者之间较大的值。
2. 两个相邻的外边距都是负数时，折叠结果是两者绝对值的较大值。
3. 两个外边距一正一负时，折叠结果是两者的相加的和。

**产生折叠的必备条件：margin必须是邻接的!**

如果我们把盒子设为 **BFC**，它现在包含了标签 `p` 和它们的边距，这样它们就不会折叠，我们可以看到边距后面容器的灰色背景。

```css
.outer {
  background-color: #ccc;
  margin: 0 0 40px 0;
  overflow: auto;
}
```

![图片描述](https://segmentfault.com/img/bVbqvHO)

再一次，BFC 的工作是把东西装在盒子里，防止它们从盒子里跑出来。

> BFC 可以阻止元素被浮动元素覆盖

你将熟悉 BFC 的这种行为，因为使用浮动的任何列类型布局都是这样工作的。如果一个项目创建了一个 BFC，那么该项目将不会包裹任何浮动元素。在下面的例子中，有如下 html 结构：

```html
<div class="outer">
  <div class="float">I am a floated element.</div>
  <div class="text">I am text</div>
</div>
```

带有 float 类的项被向左浮动，因此 `div` 中的文本在它环绕 `float` 之后。

![图片描述](https://segmentfault.com/img/bVbqvLl)

我可以通过将包裹文本的 `div` 设置为 **BFC** 来防止这种包裹行为。

```css
.text {
  overflow: auto;
}
```

![图片描述](https://segmentfault.com/img/bVbqvLX)

这实际上是我们创建具有多个列的浮动布局的方法。浮动项还为该项创建了一个 BFC，因此，如果右边的列比左边的列高，那么我们的列就不会相互环绕。

## 在多列布局中使用 BFC

如果我们创建一个占满整个容器宽度的多列布局，在某些浏览器中最后一列有时候会掉到下一行。这可能是因为浏览器四舍五入了列宽从而所有列的总宽度会超出容器。但如果我们在多列布局中的最后一列里创建一个新的BFC，它将总是占据其他列先占位完毕后剩下的空间。

例如：

```
<div class="container">
    <div class="column">column 1</div>
    <div class="column">column 2</div>
    <div class="column">column 3</div>
</div>
```

对应的CSS：

```
.column {
    width: 31.33%;
    background-color: green;
    float: left;
    margin: 0 1%;
}
.column:last-child {
  float: none;
}
```

未创建 BFC 之前：

![clipboard.png](https://segmentfault.com/img/bVbqwTv)

添加以下样式创建一个 BFC:

```
.column:last-child {
  float: none;
  overflow: hidden; 
}
```

![clipboard.png](https://segmentfault.com/img/bVbqwTE)

现在尽管盒子的宽度稍有改变，但布局不会打破。当然，对多列布局来说这不一定是个好办法，但能避免最后一列下掉。这个问题上弹性盒或许是个更好的解决方案，但这个办法可以用来说明元素在这些环境下的行为。

## 还有什么能创建 BFC?

除了使用 `overflow` 创建 BFC 外，其他一些 CSS 属性还创建 BFC。正如我们所看到的，浮动元素创建了 BFC。你的浮动项将包含它里面的任何东西。

使用以下方式都能创建 BFC

- float 的值不是 none。
- position 的值不是 static 或者 relative。
- display 的值是 inline-block、table-cell、flex、table-caption 或者inline-flex
- overflow 的值不是 visible

## 创建 BFC 的新方式

使用overflow或其他的方法创建BFC时会有两个问题。首先，这些方法本身是有自身的设计目的，所以在使用它们创建BFC时可能会产生副作用。例如，使用overflow创建BFC后在某些情况下可能会看到出现一个滚动条或者元素内容被裁切。

这是由于overflow属性的设计是用来让你告诉浏览器如何定义元素的溢出状态的。浏览器执行了它最基本的定义。

即使在没有任何不想要的副作用的情况下，使用 `overflow` 也可能会让其他开发人员感到困惑。为什么 `overflow` 设置为 `auto` 或 `scroll`?最初的开发者的意图是什么?他们想要这个组件上的滚动条吗?

最安全的做法应该是创建一个 BFC 时并不会带来任何副作用，它内部的元素都安全的呆在这个迷你布局中，这种方法不会引起任何意想不到的问题，也可以理解开发者的意图。CSS 工作组也十分认同这种想法，所以他们定制了一个新的属性值：`display:flow-root`。

**flow-root 浏览器支持情况**

你可以使用display:flow-root安全的创建BFC，来解决上文中提到的各种问题：包裹浮动元素、阻止外边距叠加和阻止围绕浮动元素。

![图片描述](https://segmentfault.com/img/bVbqwLJ)

浏览器对该属性的支持目前还是有限的，如果你觉得这个属性值很方便，请投票去让Edge也支持它。不过无论如何，你现在应该已经理解了什么是 BFC，以及如何使用 overflow 或其他方法来包裹浮动，以及知道了 BFC 可以阻止元素去环绕浮动元素，如果你想使用弹性或网格布局可以在一些不支持他们的浏览器中使用 BFC 的这些特性做降级处理。

理解浏览器如何布置网页是非常基础的。 虽然有时看起来无关紧要，但是这些小知识可以加快创建和调试 CSS 布局所需的时间。

## 参考

[segmentfault](https://segmentfault.com/)-[前端小智](https://segmentfault.com/u/minnanitkong)-[理解 CSS 布局和 BFC](https://segmentfault.com/a/1190000018677177)