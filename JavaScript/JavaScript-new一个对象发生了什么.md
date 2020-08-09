[toc]

# JavaScript-new一个对象发生了什么

## call、apply、bind

在JavaScript new 对象的原理中需要用到call或者apply或者bind函数，用来绑定this。这里先学习下这三个函数。

**call apply bind三个函数都是用来重定义`this`这个对象的。**

```Javascript
var name = "abc",age = 10
var obj = {
  name: 'hello',
  objAge: this.age,
  func(){
    return `${this.name}的年龄是${this.age}`;
  }
}

obj.objAge //10 因为执行对象是windows，所以this.age指向windows的age = 10
obj.func() // hello的年龄是undefined, 因为执行对象是obj obj没有age属性
let test = obj.func;
test() //abc的年龄是10，这个可以理解为 test = function(){return `${this.name}的年龄是${this.age}`} test()的执行对象是windows 所以this.name = abc this.age = 1以this.name = abc this.age = 10
```

上面代码如果把`var name = "abc",age = 10`改成`let name = "abc",age = 10`那么结果将会是：

```javascript
obj.objAge //undefined
test() //的年龄是 undefined
/** 原因是 
(1) let声明的全局变量不是全局对象的属性。这就意味着，你不可以通过window.变量名的方式访问这些变量。它们只存在于一个不可见的块的作用域中，这个块理论上是Web页面中运行的所有JS代码的外层块。 所以this.age其实返回的是window.age 而在let声明中 window.age不存在 所以返回undefined
(2) this.name本应该与this.age一样返回undefined。但是js函数都有一个默认的name属性，默认返回函数的名字 而window的name属性默认为空 所以this.name 其实返回的是window.name*/
```

上面执行的`obj.func()`其实是`obj.func().call(obj)`,而`test()`可以理解为`test().call(undefined)`,按理说`test()`内的`this`是undefined，但是`浏览器?`里有一条规则：

```markdown
如果你传的 context 就 null 或者 undefined，那么 window 对象就是默认的 context（严格模式下默认 context 是 undefined）
```

因此上面的this绑定的就是window，它也被称为隐性绑定。

### call

call 方法第一个参数是要绑定给this的值，后面传入的是一个参数列表。当第一个参数为null、undefined的时候，默认指向window。

```javascript
var arr = [1, 2, 3, 89, 46]
var max = Math.max.call(null, arr[0], arr[1], arr[2], arr[3], arr[4])//89

//max的默认用法：
Math.max(1,2,3,4,5) //5
//在es6语法中 ...arr表示 arr中的值 所以可以写成
Math.max(...arr); //89
//在es5中如果要求最大值只能用apply
Math.max.apply(null,arr) //89
```



```Javascript
obj1.fn() 
obj1.fn.call(obj1);

fn1()
fn1.call(null)

f1(f2)
f1.call(null,f2)
```

看一个例子：

```javascript
var obj = {
  message: 'My name is: '
}

function getName(firstName, lastName) {
  console.log(this.message + firstName + ' ' + lastName)
}

getName.call(obj, 'Dot', 'Dolby') //My name is: Dot Dolby
```

### apply

apply接受两个参数，第一个参数是要绑定给this的值，第二个参数是一个参数数组。当第一个参数为null、undefined的时候，默认指向window。

```javascript
var arr = [1,2,3,89,46]
var max = Math.max.apply(null,arr)//89
```

是不是觉得和前面写的call用法很像，事实上apply 和 call 的用法几乎相同, 唯一的差别在于：当函数需要传递多个变量时, apply 可以接受一个数组作为参数输入, call 则是接受一系列的单独变量。

看一个例子：

```javascript
var obj = {
  message: 'My name is: '
}

function getName(firstName, lastName) {
  console.log(this.message + firstName + ' ' + lastName)
}

getName.apply(obj, ['Dot', 'Dolby'])// My name is: Dot Dolby
```

可以看到，obj 是作为函数上下文的对象，函数 getName 中 this 指向了 obj 这个对象。参数 firstName 和 lastName 是放在数组中传入 getName 函数。

call和apply可用来借用别的对象的方法，这里以call()为例：

```javascript
var Person1  = function () {
  this.name = 'Dot';
}
var Person2 = function () {
  this.getname = function () {
    console.log(this.name);
  }
  Person1.call(this);
}
var person = new Person2();
person.getname();       // Dot
```

从上面我们看到，Person2 实例化出来的对象 person 通过 getname 方法拿到了 Person1 中的 name。因为在 Person2 中，Person1.call(this) 的作用就是使用 Person1 对象代替 this 对象，那么 Person2 就有了 Person1 中的所有属性和方法了，相当于 Person2 继承了 Person1 的属性和方法。

对于什么时候该用什么方法，其实不用纠结。如果你的参数本来就存在一个数组中，那自然就用 apply，如果参数比较散乱相互之间没什么关联，就用 call。像上面的找一组数中最大值的例子，当然是用apply合理。

### bind

和call很相似，第一个参数是this的指向，从第二个参数开始是接收的参数列表。区别在于bind方法返回值是函数以及bind接收的参数列表的使用。

+ bind返回值是函数

```Javascript
var obj = {
    name: 'Dot'
}

function printName() {
    console.log(this.name)
}

var dot = printName.bind(obj)
console.log(dot) // function () { … }
dot()  // Dot
```

bind 方法不会立即执行，而是返回一个改变了上下文 this 后的函数。而原函数 printName 中的 this 并没有被改变，依旧指向全局对象 window。

+ 参数的使用

```javascript
function fn(a, b, c) {
  console.log(a, b, c);
}
var fn1 = fn.bind(null, 'Dot');

fn('A', 'B', 'C');            // A B C
fn1('A', 'B', 'C');           // Dot A B
fn1('B', 'C');                // Dot B C
fn.call(null, 'Dot');      // Dot undefined undefined
```

call 是把第二个及以后的参数作为 fn 方法的实参传进去，而 fn1 方法的实参实则是在 bind 中参数的基础上再往后排。

## new 对象

对于如下代码：

```javascript
function Person(name){
  this.name = name
}
var p = new Person("小明");

console.log(p.name) // 小明
console.log(p instanceof Person) // true
```

在《JavaScript高级程序设计（第三版）》P145是这么写的：

要创建Person的新实例，必须使用new操作符。以这种方式调用构造函数实际上会经历以下4个步骤：

```markdown
（1）创建一个新对象；
（2）将构造函数的作用域赋给新对象（因此this就指向了这个对象）；
（3）执行构造函数中的代码（为这个新对象添加属性）；
（4）返回新对象。(如果构造函数返回引用类型的值或者function则返回构造函数中的值)
```

用JS模拟new的过程：

```javascript
function _new(){
  // 1、创建一个新对象
  let target = {};
  let [constructor, ...args] = [...arguments];  // 第一个参数是构造函数
  // 2、原型链连接
  target.__proto__ = constructor.prototype;
  // 3、将构造函数的属性和方法添加到这个新的空对象上。
  let result = constructor.apply(target, args);
  if(result && (typeof result == "object" || typeof result == "function")){
    // 如果构造函数返回的结果是一个对象，就返回这个对象
    return result
  }
  // 如果构造函数返回的不是一个对象，就返回创建的新对象。
  return target
}
let p2 = _new(Person, "小花")
console.log(p2.name)  // 小花
console.log(p2 instanceof Person) // true
```

变一下：

```javascript
function _new(constructor){
  //创建一个新对象
  let target = {};
  target.__proto__ = constructor.prototype;
  //或者直接 let target = {__proto__: constructor.prototype}
  let result = constructor.apply(target,[].slice.call(arguments,1));//result就是构造函数返回的值
  //或者 constructor.call(target,...Array.prototype.slice.apply(arguments,[1]));
  if(result && (result instanceof Function || typeof result == 'object')){
    return result;
  }
  return target;
}
```



