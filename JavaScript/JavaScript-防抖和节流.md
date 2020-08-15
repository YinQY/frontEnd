[toc]

# JavaScript-防抖和节流

## 概念

防抖和节流最主要用于js一些运行频率很高的事件比如:

`resize` `scroll` `mousemove` `mouseover` `keypress`等短时间内会触发很多次的事件，因为短时间内触发很多次没有必要，而且浪费内存，可能会让网页运行缓慢，所以要进行防抖或者节流。

**下面以一个事例来说明防抖和节流**

```html
<input type="text">
<div></div>
```

```js
let ipt = document.querySelector('input');
let dv = document.querySelector('div');
let index = 0;
ipt.addEventListener('keyup',function(){
  console.log(++index);
  dv.innerHTML = index;
});
```

则运行效果如下：

<img src="https://user-gold-cdn.xitu.io/2018/9/4/165a252be5c94d6b?imageslim"/>

## 防抖

> 在事件触发N秒后再执行，如果在N秒内又被触发则重新计算。

```js
ipt.addEventListener('keyup',dobounce(function(){
  console.log(++index);
  dv.innerHTML = index;
}));

function dobounce(fn,delay = 300){
  let timer = null;
  return function(...args){
    let that = this;
    clearTimeout(timer);
    timer = setTimeout(function(){
      fn.call(that,...args);
    },delay);
  }
}
```

运行效果如下：

<img src="https://user-gold-cdn.xitu.io/2018/9/4/165a252b4b429b56?imageslim"/>

## 节流

> 规定在一个单位时间内，只能触发一次函数。如果这个单位时间内触发多次函数，只有一次生效。

```js
ipt.addEventListener('keyup',throttle(function(){
  console.log(++index);
  dv.innerHTML = index;
}));

function throttle(fn,delay = 1000){
  let timer = null;
  return function(...args){
    let that = this;
    if(timer){return ;}

    timer = setTimeout(function(){
      fn.call(that,...args);
      timer = null;
    },delay);
  }
}
```

效果如下：

<img src="https://user-gold-cdn.xitu.io/2018/9/4/165a252b4c1a9686?imageslim"/>

可以看到，我们在不断输入时，ajax会按照我们设定的时间，每1s执行一次。

## 总结

- 函数防抖和函数节流都是防止某一时间频繁触发，但是这两兄弟之间的原理却不一样。
- 函数防抖是某一段时间内只执行一次，而函数节流是间隔时间执行。

**结合应用场景**

- debounce
  - search搜索联想，用户在不断输入值时，用防抖来节约请求资源。
  - window触发resize的时候，不断的调整浏览器窗口大小会不断的触发这个事件，用防抖来让其只触发一次
- throttle
  - 鼠标不断点击触发，mousedown(单位时间内只触发一次)
  - 监听滚动事件，比如是否滑到底部自动加载更多，用throttle来判断

## 参考

[薄荷前端](https://juejin.im/user/3949101498252824)-[7分钟理解JS的节流、防抖及使用场景](https://juejin.im/post/6844903669389885453)