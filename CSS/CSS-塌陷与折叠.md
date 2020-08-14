[toc]

# CSS-塌陷与折叠

## 折叠

### 兄弟元素

在相邻兄弟元素的**垂直外边距**中当发生重叠时：

+ 如果两个外边距都为正值，会取两者之间的较大值。
+ 如果兄弟元素的垂直外边距一正一负，则取两者之和。
+ 如果均为负值，则取两者绝对值较大的值

> 该现象可以避免两个元素间的距离过大，在实际开发中通常是有利的，无需处理。

```html
<--html-->
<div></div>
<div></div>
```



```css
/*css*/
/*为正 两个div上下边距只有10px*/
div{
  width: 100px;
  height: 100px;
  background-color: red;
  margin: 10px;
}

/*一正一负 两个div上下边距为0*/
div{
  width: 100px;
  height: 100px;
  background-color: red;
  margin: 10px 0 -10px;
}
/*都为负数。两个div上下边距为-10px*/
div{
  width: 100px;
  height: 100px;
  background-color: red;
  margin: -10px 0 -20px;   
}
```

### 父子元素

在父子元素中的相邻外边距中，**如果父元素没有设置任何内边距/边框值**，那么设置子元素的垂直上外边距时会传递至父元素。

**处理方案：**

1. 不设置子元素的外边距；
2. 设置父元素的**内边距/边框值**，使子元素和父元素的上外边距不相邻（在标准页面布局中需注意按要求修改对应内容区width/height值）；
3. 设置父元素的overflow属性，属性值非visible；—开启*BFC*
4. 通过给父元素设置::before伪元素； `div::before{content:'';display:table;}`



## 塌陷

在默认情况下，当父元素未设置高度height时，会自动被子元素的内容撑开；但当设置子元素为浮动后，由于浮动会使子元素完全脱离文档流，导致无法撑起父元素，父元素高度丢失，即CSS高度塌陷问题。

> 该现象会导致父元素下的元素自动上移，影响页面布局，所以需要进行处理。

```css
/*eg: div为父元素，p为div的子元素*/
div {
  background-color: yellow;
  border:1px solid red;
}
p {
  float:left;
}
```

<img src="https://img-blog.csdnimg.cn/20200530161039771.png"/>

**处理方案：**

- 设置父元素高度;
- 设置父元素的overflow属性，属性值非visible，使其可以包含浮动的子元素；—开启BFC
- 通过给父元素设置::after伪元素；
  `div::after{content:'';display:block;clear:both;}`

## clearfix样式

既解决CSS父子元素的外边距折叠问题，又解决了CSS高度塌陷问题。

```css
.clearfix::before,.clearfix::after{
content:’’;
display:table;
clear:both;
}
```

