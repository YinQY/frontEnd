[toc]

# Javascript 面向对象编程（一）：封装

Javascript是一种基于对象（object-based）的语言，你遇到的所有东西几乎都是对象。但是，它又不是一种真正的面向对象编程`Object Oriented Programming`（OOP）语言，因为它的语法中没有`class`（类）。

那么，如果我们要把"属性"（property）和"方法"（method），封装成一个对象，甚至要从原型对象生成一个实例对象，我们应该怎么做呢？

## 生成实例对象的原始模式

假定我们把猫看成一个对象，它有"名字"和"颜色"两个属性。

```javascript
var Cat = {
  name : '',

  color : ''

}
```

现在，我们需要根据这个原型对象的规格（schema），生成两个实例对象。

```javascript
var cat1 = {}; // 创建一个空对象

cat1.name = "大毛"; // 按照原型对象的属性赋值

cat1.color = "黄色";

var cat2 = {};

cat2.name = "二毛";

cat2.color = "黑色";
```

好了，这就是最简单的封装了，把两个属性封装在一个对象里面。但是，这样的写法有两个缺点，一是如果多生成几个实例，写起来就非常麻烦；二是实例与原型之间，看不出任何联系。

## 原始模式的改进

我们可以写一个函数，解决代码重复的问题：

```javascript
function Cat(name,color) {

  return {

    name:name,

    color:color

  }

}
```

然后生成实例对象，就等于是在调用函数：

```javascript
var cat1 = Cat("大毛","黄色");

var cat2 = Cat("二毛","黑色");
```

这种方法的问题依然是，`cat1`和`cat2`之间没有内在的联系，不能反映出它们是同一个原型对象的实例。

## 构造函数模式

为了解决从原型对象生成实例的问题，Javascript提供了一个构造函数（Constructor）模式。

所谓"构造函数"，其实就是一个普通函数，但是内部使用了`this`变量。对构造函数使用`new`运算符，就能生成实例，并且`this`变量会绑定在实例对象上。

比如，猫的原型对象现在可以这样写，

```javascript
function Cat(name,color){

  this.name=name;

  this.color=color;

}
```

我们现在就可以生成实例对象了。

```javascript
var cat1 = new Cat("大毛","黄色");

var cat2 = new Cat("二毛","黑色");

alert(cat1.name); // 大毛

alert(cat1.color); // 黄色
```

这时`cat1`和`cat2`会自动含有一个`constructor`属性，指向它们的构造函数。

```javascript
console.log(cat1.constructor === Cat); //true
console.log(cat2.constructor === Cat); //true
```

Javascript还提供了一个`instanceof`运算符，验证原型对象与实例对象之间的关系。

```Javascript
cat1 instanceof Cat //true
cat2 instanceof Cat //true
```

## 构造函数模式的问题

构造函数方法很好用，但是存在一个浪费内存的问题。

请看，我们现在为`Cat`对象添加一个不变的属性`type`（种类），再添加一个方法`eat`（吃）。那么，原型对象`Cat`就变成了下面这样:

```javascript
function Cat(name,color){

  this.name = name;

  this.color = color;

  this.type = "猫科动物";

  this.eat = function(){alert("吃老鼠");};

}
```

还是采用同样的方法，生成实例：

```javascript
var cat1 = new Cat("大毛","黄色");

var cat2 = new Cat ("二毛","黑色");

alert(cat1.type); // 猫科动物

cat1.eat(); // 吃老鼠
```

表面上好像没什么问题，但是实际上这样做，有一个很大的弊端。那就是对于每一个实例对象，`type`属性和`eat()`方法都是一模一样的内容，每一次生成一个实例，都必须为重复的内容，多占用一些内存。这样既不环保，也缺乏效率。

```javascript
cat1.eat === cat2.eat //false
cat.eat == cat2.eat //false
```

能不能让`type`属性和`eat()`方法在内存中只生成一次，然后所有实例都指向那个内存地址呢？回答是可以的。

## Prototype模式

Javascript规定，每一个构造函数都有一个`prototype`属性，指向另一个对象。这个对象的所有属性和方法，都会被构造函数的实例继承。

这意味着，我们可以把那些不变的属性和方法，直接定义在`prototype`对象上。

```javascript
function Cat(name,color){

  this.name = name;

  this.color = color;

}

Cat.prototype.type = "猫科动物";

Cat.prototype.eat = function(){alert("吃老鼠")};
```

然后，生成实例。

```javascript
var cat1 = new Cat("大毛","黄色");

var cat2 = new Cat("二毛","黑色");

alert(cat1.type); // 猫科动物

cat1.eat(); // 吃老鼠
```

这时所有实例的`type`属性和`eat()`方法，其实都是同一个内存地址，指向`prototype`对象，因此就提高了运行效率。

```javascript
alert(cat1.eat === cat2.eat); //true
```

## Prototype模式的验证方法

 **isPrototypeOf()**

这个方法用来判断，某个`proptotype`对象和某个实例之间的关系。

```javascript
Cat.propotype.isPrototypeOf(cat1); //true
```

**hasOwnProperty()**

每个实例对象都有一个`hasOwnProperty()`方法，用来判断某一个属性到底是本地属性，还是继承自`prototype`对象的属性。

```javascript
cat1.hasOwnProperty(name); //true
cat1.hasOwnProperty(type); //false
```

**in运算符**

`in`运算符可以用来判断，某个实例是否含有某个属性，不管是不是本地属性。

```Javascript
"name" in cat1 //true
"eat" in cat1 //true
```

`in`运算符还可以用来遍历某个对象的所有属性。

```javascript
　for(var prop in cat1) { alert("cat1["+prop+"]="+cat1[prop]); }
```

## 最后

尽量不要使用`__proto__(隐式原型)`属性,因为有些浏览器不支持。尽量用`Object.getPrototypeOf(target)`来获取构造函数的原型对象`Function.prototype`。

来源：阮一峰-[Javascript 面向对象编程（一）：封装](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_encapsulation.html)