[toc]

# JavaScript-this指向问题

接上一篇`JavaScript-作用域和作用域链`。

这篇文章里，我们将讨论跟执行上下文直接相关的更多细节。再来回顾一下关于执行上下文的三个阶段生命周期：

<img src="https://picb.zhimg.com/80/v2-617079efb9d5aa3779b25a75abf7d56d_1440w.jpg"/>

本章将专门介绍与执行上下文创建阶段直接相关的最后一个细节——**this**是什么？以及它的指向到底是什么。

## 了解this

也许你在其他面向对象的编程语言曾经看过`this`，也知道它会指向某个构造器(constructor)所建立的对象。但事实上在JavaScript里面，`this`所代表的不仅仅是那个被建立的对象。

先来看看ECMAScript 标准规范对this 的定义：

> 「The this keyword evaluates to the value of the ThisBinding of the current execution context.」
> 「this 这个关键字代表的值为当前执行上下文的ThisBinding。」

然后再来看看MDN 对this 的定义：

> 「In most cases, the value of this is determined by how a function is called.」
> 「在大多数的情况下，this 其值取决于函数的调用方式。」

好，如果上面两行就看得懂的话那么就不用再往下看了，Congratulations！

先来看个例子吧：

```js
var getGender = function() {
    return people1.gender;
};

var people1 = {
    gender: 'female',
    getGender: getGender
};

var people2 = {
    gender: 'male',
    getGender: getGender
};

console.log(people1.getGender());    // female
console.log(people2.getGender());    // female
```

what?怎么people2变性了呢，这不是我想要的结果啊，为什么呢？

因为`getGender()`返回(return)写死了`people1.gender`的关系，结果自然是'female'。

那么，如果我们把`getGender`稍改一下：

```js
var getGender = function() {
    return this.gender;
};
```

这个时候，你应该会分别得到`female`与`male`两种结果。

所以回到前面讲的重点，从这个例子可以看出，即便`people1`与`people2`的`getGender`方法参照的都是同一个getGender function，但由于**调用的对象不同，所以执行的结果也会不同**。

现在我们知道了第一个重点，**this实际上是在函数被调用时发生的绑定，它指向什么完全取决于函数的调用方式。**如何的区分this呢？

## this到底是谁

看完上面的例子，还是有点似懂非懂吧？那接下来我们来看看不同的调用方式对 this 值的影响。

### 情况一：全局对象&调用普通函数

在全局环境中，this 指向全局对象，在浏览器中，它就是 window 对象。下面的示例中，无论是否是在严格模式下，this 都是指向全局对象。

```js
var x = 1

console.log(this.x)               // 1
console.log(this.x === x)         // true
console.log(this === window)      // true
```

如果普通函数是在全局环境中被调用，在非严格模式下，普通函数中 this 也指向全局对象；如果是在严格模式下，this 将会是 undefined。ES5 为了使 JavaScript 运行在更有限制性的环境而添加了[严格模式](https://link.zhihu.com/?target=https%3A//developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode)，严格模式为了消除安全隐患，禁止了 this 关键字指向全局对象。

```js
var x = 1

function fn() {
    console.log(this);   // Window 全局对象
    console.log(this.x);  // 1
}

fn();      
```

使用严格模式后：

```js
"use strict"     // 使用严格模式
var x = 1

function fn() {
    console.log(this);   // undefined
    console.log(this.x);  // 报错 "Cannot read property 'x' of undefined"，因为此时 this 是 undefined
}

fn();  
```

### 情况二：作为对象方法的调用

我们知道，在对象里的值如果是原生值（primitive type；例如，字符串、数值、布尔值），我们会把这个新建立的东西称为「**属性（property）**」；如果对象里面的值是函数（function）的话，我们则会把这个新建立的东西称为「**方法（method）**」。

如果函数作为对象的一个方法时，并且作为对象的一个方法被调用时，**函数中的this指向这个上一级对象**。

```js
var x = 1
var obj = {
    x: 2,
    fn: function() {
        console.log(this);    
        console.log(this.x);
    }
}

obj.fn()     

// obj.fn()结果打印出;
// Object {x: 2, fn: function}
// 2

var a = obj.fn
a()   

// a()结果打印出:   
// Window 全局对象
// 1
```

在上面的例子中，直接运行 obj.fn() ，调用该函数的上一级对象是 obj，所以 this 指向 obj，得到 this.x 的值是 2；之后我们将 fn 方法首先赋值给变量 a，a 运行在全局环境中，所以此时 this 指向全局对象Window，得到 this.x 为 1。

我们再来看一个例子，如果函数被多个对象嵌套调用，this 会指向什么。

```js
var x = 1
var obj = {
  x: 2,
  y: {
    x: 3,
    fn: function() {
      console.log(this);   // Object {x: 3, fn: function}
      console.log(this.x);   // 3
    }
  }
}

obj.y.fn();      
```

为什么结果不是 2 呢，因为在这种情况下记住一句话：**this 始终会指向直接调用函数的上一级对象**，即 y，上面例子实际执行的是下面的代码。

```js
var y = {
  x: 3,
  fn: function() {
    console.log(this);   // Object {x: 3, fn: function}
    console.log(this.x);   // 3
  }
}

var x = 1
var obj = {
  x: 2,
  y: y
}

obj.y.fn();    
```

对象可以嵌套，函数也可以，如果函数嵌套，this 会有变化吗？我们通过下面代码来探讨一下。

```js
var obj = {
    y: function() {
        console.log(this === obj);   // true
        console.log(this);   // Object {y: function}
        fn();

        function fn() {
            console.log(this === obj);   // false
            console.log(this);   // Window 全局对象
        }
    }
}

obj.y();  
```

在函数 y 中，this 指向了调用它的上一级对象 obj，这是没有问题的。但是在嵌套函数 fn 中，this 并不指向 obj。嵌套的函数不会从调用它的函数中继承 this，当嵌套函数作为函数调用时，其 this 值在非严格模式下指向全局对象，在严格模式是 undefined，所以上面例子实际执行的是下面的代码。

```js
function fn() {
    console.log(this === obj);   // false
    console.log(this);   // Window 全局对象
}

var obj = {
    y: function() {
        console.log(this === obj);   // true
        console.log(this);   // Object {y: function}
        fn();
    }
}

obj.y();  
```

### 情况三：作为构造函数调用

我们可以使用 new 关键字，通过构造函数生成一个实例对象。此时，**this 便指向这个新对象**。

```js
var x = 1;

function Fn() {
　  this.x = 2;
    console.log(this);  // Fn {x: 2}
}

var obj = new Fn();   // obj和Fn(..)调用中的this进行绑定
console.log(obj.x)   // 2
```

使用`new`来调用`Fn(..)`时，会构造一个新对象并把它（obj）绑定到`Fn(..)`调用中的this。还有值得一提的是，如果构造函数返回了非引用类型（string，number，boolean，null，undefined），this 仍然指向实例化的新对象。

```js
var x = 1

function Fn() {
  this.x = 2
 
  return {
    x: 3
  }
}

var a = new Fn()

console.log(a.x)      // 3
```

因为Fn()返回(return)的是一个对象（引用类型），this 会指向这个return的对象。如果return的是一个非引用类型的值呢？

```js
var x = 1

function Fn() {
  this.x = 2

  return 3
}

var a = new Fn()

console.log(a.x)      // 2
```

### 情况四：call 和 apply 方法调用

如果你想改变 this 的指向，可以使用 call 或 apply 方法。**它们的第一个参数都是指定函数运行时其中的`this`指向**。如果第一个参数不传（参数为空）或者传 null 、undefined，默认 this 指向全局对象（非严格模式）或 undefined（严格模式）。

```js
var x = 1;

var obj = {
  x: 2
}

function fn() {
    console.log(this);
    console.log(this.x);
}

fn.call(obj)
// Object {x: 2}
// 2

fn.apply(obj)     
// Object {x: 2}
// 2

fn.call()         
// Window 全局对象
// 1

fn.apply(null)    
// Window 全局对象
// 1

fn.call(undefined)    
// Window 全局对象
// 1
```

使用 call 和 apply 时，如果给 this 传的不是对象，JavaScript 会使用相关构造函数将其转化为对象，比如传 number 类型，会进行`new Number()`操作，如传 string 类型，会进行`new String()`操作，如传 boolean 类型，会进行new Boolean()操作。

```js
function fn() {
  console.log(Object.prototype.toString.call(this))
}

fn.call('love')      // [object String]
fn.apply(1)          // [object Number]
fn.call(true)          // [object Boolean]
```

call 和 apply 的区别在于，call 的第二个及后续参数是一个参数列表，apply 的第二个参数是数组。参数列表和参数数组都将作为函数的参数进行执行。

```js
var x = 1

var obj = {
  x: 2
}

function Sum(y, z) {
  console.log(this.x + y + z)
}

Sum.call(obj, 3, 4)       // 9
Sum.apply(obj, [3, 4])    // 9
```

### 情况五：bind 方法调用

调用 f.bind(someObject) 会创建一个与 f 具有相同函数体和作用域的函数，但是在这个新函数中，新函数的 this **会永久的指向 bind 传入的第一个参数**，无论这个函数是如何被调用的。

```js
var x = 1

var obj1 = {
    x: 2
};
var obj2 = {
    x: 3
};

function fn() {
    console.log(this);
    console.log(this.x);
};

var a = fn.bind(obj1);
var b = a.bind(obj2);

fn();
// Window 全局对象
// 1

a();
// Object {x: 2}
// 2

b();
// Object {x: 2}
// 2

a.call(obj2);
// Object {x: 2}
// 2
```

在上面的例子中，虽然我们尝试给函数 a 重新指定 this 的指向，但是它依旧指向第一次 bind 传入的对象，即使是使用 call 或 apply 方法也不能改变这一事实，即永久的指向 bind 传入的第一次参数。

### 情况六：箭头函数中this指向

值得一提的是，从ES6 开始新增了箭头函数，先来看看[MDN 上对箭头函数的说明](https://link.zhihu.com/?target=https%3A//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)：

> An arrow function expression has a shorter syntax than a function expression and does notbind its own`this`,`arguments`,`super`, or`new.target`. Arrow functions are always anonymous. These function expressions are best suited for non-method functions, and they cannot be used as constructors.

这里已经清楚了说明了，箭头函数没有自己的`this`绑定。**箭头函数中使用的`this`，其实是直接包含它的那个函数或函数表达式中的`this`**。在前面情况二中函数嵌套函数的例子中，被嵌套的函数不会继承上层函数的 this，如果使用箭头函数，会发生什么变化呢？

```js
var obj = {
  y: function() {
        console.log(this === obj);   // true
        console.log(this);           // Object {y: function}

      var fn = () => {
          console.log(this === obj);   // true
          console.log(this);           // Object {y: function}
      }
      fn();
  }
}

obj.y() 
```

和普通函数不一样，箭头函数中的 this 指向了 obj，这是因为它从上一层的函数中继承了 this，你可以理解为箭头函数修正了 this 的指向。所以**箭头函数的this不是调用的时候决定的，而是在定义的时候处在的对象就是它的this**。

换句话说，**箭头函数的this看外层的是否有函数，如果有，外层函数的this就是内部箭头函数的this，如果没有，则this是window**。

```js
var obj = {
  y: () => {
        console.log(this === obj);   // false
        console.log(this);           // Window 全局对象 
       
      var fn = () => {
          console.log(this === obj);   // false
          console.log(this);           // Window 全局对象 
      }
      fn();
  }
}

obj.y() 
```

上例中，虽然存在两个箭头函数，其实this取决于最外层的箭头函数，由于obj是个对象而非函数，所以this指向为Window全局对象。

同 bind 一样，箭头函数也很“顽固”，我们无法通过 call 和 apply 来改变 this 的指向，**即传入的第一个参数被忽略**。

```js
var x = 1
var obj = {
    x: 2
}

var a = () => {
    console.log(this.x)
    console.log(this)
}

a.call(obj)       
// 1
// Window 全局对象

a.apply(obj)      
// 1
// Window 全局对象
```

上面的文字描述过多可能有点干涩，那么就看以下的这张流程图吧，我觉得这个图总结的很好（转自掘金小册-[前端面试之道](https://link.zhihu.com/?target=https%3A//juejin.im/book/5bdc715fe51d454e755f75ef)），图中的流程只针对于单个规则。

<img src="https://pic1.zhimg.com/80/v2-7f034f6e1a8b4a137b8b1a1cba320869_1440w.jpg"/>

## 小结

本篇文章介绍了 this 指向的几种情况，不同的运行环境和调用方式都会对 this 产生影响。总的来说，函数 this 的指向取决于当前调用该函数的对象，也就是执行时的对象。在这一节中，你需要掌握：

- this 指向全局对象的情况；
- 严格模式和非严格模式下 this 的区别；
- 函数作为对象的方法调用时 this 指向的几种情况；
- 作为构造函数时 this 的指向，以及是否 return 的区别；
- 使用 call 和 apply 改变调用函数的对象；
- bind 创建的函数中 this 的指向；
- 箭头函数中的 this 指向。

参考：米淇淋的前端旅行之路-[JavaScript系列之this是什么](https://zhuanlan.zhihu.com/p/71490991)

