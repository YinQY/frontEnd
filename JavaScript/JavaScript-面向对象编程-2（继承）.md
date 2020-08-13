[toc]

# JavaScript-面向对象编程-2（继承）

## 构造函数的继承

### 构造函数绑定

```js
function Person(){
  this.species = "人";
}
Person.prototype.getType = function(){return this.species;}
//如果是 Person.prototype.getType = ()=>{return this.species;} 则返回undefined 因为=>函数中的this是在定义的时候指定的。此时this为window
function Man(name,age){
  this.name = name;
  this.age = age;
 	Person.apply(this,arguments);
}
Man.prototype.getName = function(){return this.name;}

let man = new Man('yqy',10);
console.log(man.getName()); //yqy
console.log(man.species); //人
console.log(man.age); //10
console.log(man.getType()); //not a function
```

> 可以看到，上面使用 构造函数绑定继承，只能继承父对象，而父原型对象无法继承。

### prototype模式

```js
function Person(){
  this.species = "人";
}
Person.prototype.getType = function(){return this.species;}

function Man(name,age){
  this.name = name;
  this.age = age;
}

function extend(Child,Parent){
  Child.prototype = new Parent();
  Child.prototype.constructor = Child;
}
//继承
extend(Man,Person);
Man.prototype.getName = function(){return this.name;}

let man = new Man('yqy',10);
console.log(man.getName()); //yqy
console.log(man.species); // 人
console.log(man.age); //10
console.log(man.getType()); //人
```

> 使用 prototype指向父对象的实例，来实现继承，可以继承父对象的所有内容，包括prototype。

### 直接继承prototype

因为一般都是继承父对象的不变的方法或者变量，所以可以直接把这些写在`prototype`,这样直接继承prototype即可。

```js
function Person(){
  this.species = "人";
}
Person.prototype.getType = function(){return "人";}// 这里作了修改

function Man(name,age){
  this.name = name;
  this.age = age;
}

function extend(Child,Parent){
  Child.prototype = Parent.prototype;
  Child.prototype.constructor = Child;
  
}
//继承
extend(Man,Person);
Man.prototype.getName = function(){return this.name;}

let man = new Man('yqy',10);
console.log(man.getName()); //yqy
console.log(man.species); // undefined
console.log(man.age); //10
console.log(man.getType()); //人
```

但是直接指定prototype继承有个缺点:

> 因为 `Child.prototype = Parent.prototype`,这是浅拷贝，所以会导致这两个prototype指向同一个堆内存地址，接下来执行`Child.prototype.construtor = Child`,而修改了同一个堆内存，所以`Parent.prototype.constructor`也会变成`Child`。

### 利用空对象作为中介

由于"直接继承prototype"存在上述的缺点，所以就有第四种方法，利用一个空对象作为中介。

```js
function Person(){
  this.species = "人";
}
Person.prototype.getType = function(){return "人";}// 这里作了修改

function Man(name,age){
  this.name = name;
  this.age = age;
}

function extend(Child,Parent){
  let F = function(){}
  F.prototype = Parent.Prototype;
  Child.prototype = F();
  Child.prototype.constructor = Child;
  Child.uber = Parent;
}
//继承
extend(Man,Person);
Man.prototype.getName = function(){return this.name;}

let man = new Man('yqy',10);
console.log(man.getName()); //yqy
console.log(man.species); // undefined
console.log(man.age); //10
console.log(man.getType()); //人
```

>F是空对象，所以几乎不占内存。这时，修改Cat的prototype对象，就不会影响到Animal的prototype对象。

另外，说明一点，函数体最后一行

> 　　Child.uber = Parent.prototype;

意思是为子对象设一个uber属性，这个属性直接指向父对象的prototype属性。（uber是一个德语词，意思是"向上"、"上一层"。）这等于在子对象上打开一条通道，可以直接调用父对象的方法。这一行放在这里，只是为了实现继承的完备性，纯属备用性质。

因为当请求一个方法时，先会从本地对象搜索，没有的话，就遵从原型链寻找，直到最后对象是Object时还找不到就会返回null。 当知道属性是继承而来的，用uber方法调用比普通调用效率高。（少了一次遍历过程）

### 拷贝继承

```js
function Person(){
  this.species = "人";
}
Person.prototype.getType = function(){return "人";}// 这里作了修改

function Man(name,age){
  this.name = name;
  this.age = age;
}

function extend(Child,Parent){
	var p = Parent.prototype;
	var c = Child.prototype;
	for (var i in p) {
		c[i] = p[i];
	}
	c.uber = p;
}
//继承
extend(Man,Person);
Man.prototype.getName = function(){return this.name;}

let man = new Man('yqy',10);
console.log(man.getName()); //yqy
console.log(man.species); // undefined
console.log(man.age); //10
console.log(man.getType()); //人
```



## 非构造函数的继承

### 什么是"非构造函数"的继承

比如，现在有一个对象，叫做"中国人":

```js
var Chinese = {
  nation:'中国'
};
```

还有一个对象，叫做"医生":

```js
var Doctor ={
  career:'医生'
}
```

请问怎样才能让"医生"去继承"中国人"，也就是说，我怎样才能生成一个"中国医生"的对象？

这里要注意，这两个对象都是普通对象，不是构造函数，无法使用构造函数方法实现"继承"。

### object()方法

json格式的发明人Douglas Crockford，提出了一个object()函数，可以做到这一点。

```js
function object(o) {

  function F() {}

  F.prototype = o;

  return new F();

}
```

这个object()函数，其实只做一件事，就是把子对象的prototype属性，指向父对象，从而使得子对象与父对象连在一起。

使用的时候，第一步先在父对象的基础上，生成子对象：

`var Doctor = object(Chinese);`

然后，再加上子对象本身的属性：

`Doctor.career = '医生';`

这时，子对象已经继承了父对象的属性了。

> 　　alert(Doctor.nation); //中国

### 浅拷贝

除了使用"prototype链"以外，还有另一种思路：把父对象的属性，全部拷贝给子对象，也能实现继承。

下面这个函数，就是在做拷贝：

```js
function extendCopy(p) {

  var c = {};

  for (var i in p) {
    c[i] = p[i];
  }

  c.uber = p;

  return c;
}
```

使用的时候，这样写：

```js
var Doctor = extendCopy(Chinese);

Doctor.career = '医生';

alert(Doctor.nation); // 中国
```

但是，这样的拷贝有一个问题。那就是，如果父对象的属性等于数组或另一个对象，那么实际上，子对象获得的只是一个内存地址，而不是真正拷贝，因此存在父对象被篡改的可能。

请看，现在给Chinese添加一个"出生地"属性，它的值是一个数组。

`Chinese.birthPlaces = ['北京','上海','香港'];`

通过extendCopy()函数，Doctor继承了Chinese。

`var Doctor = extendCopy(Chinese);`

然后，我们为Doctor的"出生地"添加一个城市：

`Doctor.birthPlaces.push('厦门');`

发生了什么事？Chinese的"出生地"也被改掉了！

> 　　alert(Doctor.birthPlaces); //北京, 上海, 香港, 厦门
>
> 　　alert(Chinese.birthPlaces); //北京, 上海, 香港, 厦门

所以，extendCopy()只是拷贝基本类型的数据，我们把这种拷贝叫做"浅拷贝"。这是早期jQuery实现继承的方式。

### 深拷贝

所谓"深拷贝"，就是能够实现真正意义上的数组和对象的拷贝。它的实现并不难，只要递归调用"浅拷贝"就行了。

```js
function deepCopy(p, c) {

  var c = c || {};

  for (var i in p) {

    if (typeof p[i] === 'object') {

      c[i] = (p[i].constructor === Array) ? [] : {};

      deepCopy(p[i], c[i]);

    } else {

      c[i] = p[i];

    }
  }

  return c;
}
```



使用的时候这样写：

`var Doctor = deepCopy(Chinese);`

现在，给父对象加一个属性，值为数组。然后，在子对象上修改这个属性：

```js
Chinese.birthPlaces = ['北京','上海','香港'];

Doctor.birthPlaces.push('厦门');
```

这时，父对象就不会受到影响了。

> 　　alert(Doctor.birthPlaces); //北京, 上海, 香港, 厦门
>
> 　　alert(Chinese.birthPlaces); //北京, 上海, 香港

目前，jQuery库使用的就是这种继承方法。