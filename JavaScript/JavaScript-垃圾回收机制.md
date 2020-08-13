[toc]

# JavaScript-垃圾回收机制

Javascript 会找出不再使用的变量，不再使用意味着这个变量生命周期的结束。Javascript 中存在两种变量——全局变量和局部变量，全部变量的声明周期会一直持续，直到页面卸载

而局部变量声明在函数中，它的声明周期从执行函数开始，直到函数执行结束。在这个过程中，局部变量会在堆或栈上被分配相应的空间以存储它们的值，函数执行结束，这些局部变量也不再被使用，它们所占用的空间也就被释放

但是有一种情况的局部变量不会随着函数的结束而被回收，那就是局部变量被函数外部的变量所使用，其中一种情况就是**闭包**，因为在函数执行结束后，函数外部的变量依然指向函数内的局部变量，此时的局部变量依然在被使用，所以也就不能够被回收。

```javascript
function func1 () {
  const obj = {}
}

function func2 () {
  const obj = {}
  return obj
}

const a = func1()
const b = func2()

```

上面这个例子中，`func1` 执行时为 `obj` 分配了一块内存，但是随着函数执行结束，`obj`占用的空间也就被释放了；而 `func2` 执行时，也为 `obj` 分配了内存，但是由于 `obj` 最终被返回赋值给了 `b` 导致其依然被使用，所以 `func2` 中的 `obj` 占用的内存不会被释放。

## 垃圾回收的两种实现方式

垃圾回收有两种实现方式，分别是**标记清除**和**引用计数**。JS多用`标记清除`。

### 标记清除

**这是javascript中最常用的垃圾回收方式**。当变量进入执行环境是，就标记这个变量为“进入环境”。从逻辑上讲，永远不能释放进入环境的变量所占用的内存，因为只要执行流进入相应的环境，就可能会用到他们。当变量离开环境时，则将其标记为“离开环境”。

垃圾收集器在运行的时候会给存储在内存中的所有变量都加上标记。然后，它会去掉环境中的变量以及被环境中的变量引用的标记。而在此之后再被加上标记的变量将被视为准备删除的变量，原因是环境中的变量已经无法访问到这些变量了。最后。垃圾收集器完成内存清除工作，销毁那些带标记的值，并回收他们所占用的内存空间。

```javascript
function func3 () {
  const a = 1
  const b = 2
  // 函数执行时，a b 分别被标记 进入环境
}

func3() // 函数执行结束，a b 被标记 离开环境，被回收
```

```javascript
var m = 0,n = 19 // 把 m,n,add() 标记为进入环境。
add(m, n) // 把 a, b, c标记为进入环境。
console.log(n) // a,b,c标记为离开环境，等待垃圾回收。
function add(a, b) {
  a++
  var c = a + b
  return c
}
```

### 引用计数

所谓”引用计数”是指语言引擎有一张”引用表”，保存了内存里面所有的资源（通常是各种值）的引用次数。如果一个值的引用次数是0，就表示这个值不再用到了，因此可以将这块内存释放。

<img src="https://image.fundebug.com/2019-0430-02.png"/>

上图中，左下角的两个值，没有任何引用，所以可以释放。

如果一个值不再需要了，引用数却不为0，垃圾回收机制无法释放这块内存，从而导致内存泄漏。

上面代码中，数组[1, 2, 3, 4]是一个值，会占用内存。变量arr是仅有的对这个值的引用，因此引用次数为1。尽管后面的代码没有用到arr，它还是会持续占用内存。至于如何释放内存，我们下文介绍。

第三行代码中，数组[1, 2, 3, 4]引用的变量arr又取得了另外一个值，则数组[1, 2, 3, 4]的引用次数就减1，此时它引用次数变成0，则说明没有办法再访问这个值了，因而就可以将其所占的内存空间给收回来。

但是引用计数有个最大的问题： 循环引用。

```javascript
function func() {
    let obj1 = {};
    let obj2 = {};

    obj1.a = obj2; // obj1 引用 obj2
    obj2.a = obj1; // obj2 引用 obj1
}
```

当函数 func 执行结束后，返回值为 undefined，所以整个函数以及内部的变量都应该被回收，但根据引用计数方法，obj1 和 obj2 的引用次数都不为 0，所以他们不会被回收。

要解决循环引用的问题，最好是在不使用它们的时候手工将它们设为空。上面的例子可以这么做：

```javascript
obj1 = null;
obj2 = null;
```

## 哪些情况会引起内存泄漏

### 意外的全局变量

```javascript
function foo(arg) {
  bar = "this is a hidden global variable";
}
```

bar没被声明,会变成一个全局变量,在页面关闭之前不会被释放。

另一种意外的全局变量可能由 `this` 创建:

```javascript
function foo() {
    this.variable = "potential accidental global";
}
// foo 调用自己，this 指向了全局对象（window）
foo();
```

在 JavaScript 文件头部加上 ‘use strict’，可以避免此类错误发生。启用严格模式解析 JavaScript ，避免意外的全局变量。

### 被遗忘的计时器或回调函数

```javascript
var someResource = getData();
setInterval(function() {
    var node = document.getElementById('Node');
    if(node) {
        // 处理 node 和 someResource
        node.innerHTML = JSON.stringify(someResource));
    }
}, 1000);
```

这样的代码很常见，如果id为Node的元素从DOM中移除，该定时器仍会存在，同时，因为回调函数中包含对someResource的引用，定时器外面的someResource也不会被释放。

### 闭包

```js
function bindEvent(){
  var obj=document.createElement('xxx')
  obj.onclick=function(){
    // Even if it is a empty function
  }
}
```

闭包可以维持函数内局部变量，使其得不到释放。上例定义事件回调时，由于是函数内定义函数，并且内部函数–事件回调引用外部函数，形成了闭包。

```javascript
// 将事件处理函数定义在外面
function bindEvent() {
  var obj = document.createElement('xxx')
  obj.onclick = onclickHandler
}
// 或者在定义事件处理函数的外部函数中，删除对dom的引用
function bindEvent() {
  var obj = document.createElement('xxx')
  obj.onclick = function() {
    // Even if it is a empty function
  }
  obj = null
}
```

解决之道，将事件处理函数定义在外部，解除闭包，或者在定义事件处理函数的外部函数中，删除对dom的引用。

### 没有清理的DOM元素引用

有时，保存 DOM 节点内部数据结构很有用。假如你想快速更新表格的几行内容，把每一行 DOM 存成字典（JSON 键值对）或者数组很有意义。此时，同样的 DOM 元素存在两个引用：一个在 DOM 树中，另一个在字典中。将来你决定删除这些行时，需要把两个引用都清除。

```javascript
var elements = {
    button: document.getElementById('button'),
    image: document.getElementById('image'),
    text: document.getElementById('text')
};
function doStuff() {
    image.src = 'http://some.url/image';
    button.click();
    console.log(text.innerHTML);
}
function removeButton() {
    document.body.removeChild(document.getElementById('button'));
    // 此时，仍旧存在一个全局的 #button 的引用
    // elements 字典。button 元素仍旧在内存中，不能被 GC 回收。
}
```

虽然我们用removeChild移除了button，但是还在elements对象里保存着#button的引用，换言之，DOM元素还在内存里面。

## 内存泄漏的识别方法

新版本的chrome在 performance 中查看：

<img src="https://image.fundebug.com/2019-0430-03.png"/>

步骤:

- 打开开发者工具 Performance
- 勾选 Screenshots 和 memory
- 左上角小圆点开始录制(record)
- 停止录制

图中 Heap 对应的部分就可以看到内存在周期性的回落也可以看到垃圾回收的周期,如果垃圾回收之后的最低值(我们称为min),min在不断上涨,那么肯定是有较为严重的内存泄漏问题。

避免内存泄漏的一些方式：

- 减少不必要的全局变量，或者生命周期较长的对象，及时对无用的数据进行垃圾回收
- 注意程序逻辑，避免“死循环”之类的
- 避免创建过多的对象
- 总而言之需要遵循一条**原则：不用了的东西要及时归还**

## 垃圾回收的使用场景优化

###  数组array优化

将[]赋值给一个数组对象，是清空数组的捷径(例如： arr = [];),但是需要注意的是，这种方式又创建了一个新的空对象，并且将原来的数组对象变成了一小片内存垃圾！实际上，将数组长度赋值为0（arr.length = 0）也能达到清空数组的目的，并且同时能实现数组重用，减少内存垃圾的产生。

```js
const arr = [1, 2, 3, 4];
console.log('浪里行舟');
arr.length = 0  // 可以直接让数字清空，而且数组类型不变。
// arr = []; 虽然让a变量成一个空数组,但是在堆上重新申请了一个空数组对象。
```

### 对象尽量复用

对象尽量复用，尤其是在循环等地方出现创建新对象，能复用就复用。不用的对象，尽可能设置为null，尽快被垃圾回收掉。

```javascript
var t = {} // 每次循环都会创建一个新对象。
for (var i = 0; i < 10; i++) {
  // var t = {};// 每次循环都会创建一个新对象。
  t.age = 19
  t.name = '123'
  t.index = i
  console.log(t)
}
t = null //对象如果已经不用了，那就立即设置为null；等待垃圾回收。
```

### 在循环中的函数表达式，能复用最好放到循环外面

```js
// 在循环中最好也别使用函数表达式。
for (var k = 0; k < 10; k++) {
  var t = function(a) {
    // 创建了10次  函数对象。
    console.log(a)
  }
  t(k)
}
```

```js
// 推荐用法
function t(a) {
  console.log(a)
}
for (var k = 0; k < 10; k++) {
  t(k)
}
t = null
```





## 参考

浪里行舟-[JavaScript中的垃圾回收和内存泄漏](https://blog.fundebug.com/2019/04/30/javascript-memory-management/)

李赫feixuan-[javascript 垃圾回收机制](https://juejin.im/post/6844903652331618312)