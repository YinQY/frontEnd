[toc]

# JavaScript-执行上下文和执行栈

## 执行上下文（execution context）

当 JavaScript 代码执行一段可执行代码(executable code)时，会创建对应的执行上下文(execution context)。执行上下文（可执行代码段）总共有三种类型：

- **全局执行上下文（全局代码）**：不在任何函数中的代码都位于全局执行上下文中，只有一个，浏览器中的全局对象就是 window 对象，`this` 指向这个全局对象。
- **函数执行上下文（函数体）**：只有调用函数时，才会为该函数创建一个新的执行上下文，可以存在无数个，每当一个新的执行上下文被创建，它都会按照特定的顺序执行一系列步骤。
- **Eval 函数执行上下文（eval代码）**： 指的是运行在 `eval` 函数中的代码，很少用而且不建议使用。

执行上下文又包括三个生命周期阶段：**创建阶段→执行阶段→回收阶段**，本文重点介绍创建阶段。

### 创建阶段

当函数被调用，但未执行任何其内部代码之前，会做以下三件事：

- 创建变量对象(Variable object，VO)：首先初始化函数的参数arguments，提升函数声明和变量声明。后文会详细说明。
- 创建作用域链（Scope Chain）：在执行上下文的创建阶段，作用域链是在变量对象之后创建的。作用域链本身包含变量对象。作用域链用于解析变量。当被要求解析变量时，JavaScript 始终从代码嵌套的最内层开始，如果最内层没有找到变量，就会跳转到上一层父作用域中查找，直到找到该变量。后文会详细说明。
- 确定this指向：包括多种情况，后文会详细说明。

在一段 JS 脚本执行之前，要先解析代码（所以说 JS 是解释执行的脚本语言），解析的时候会先创建一个全局执行上下文环境，先把代码中即将执行的变量、函数声明都拿出来。变量先暂时赋值为undefined，函数则先声明好可使用。这一步做完了，然后再开始正式执行程序。

另外，一个函数在被执行之前，也会创建一个函数执行上下文环境，跟全局上下文差不多，不过函数执行上下文中会多出this arguments和函数的参数。

### 执行阶段

进入执行上下文、执行代码

### 回收阶段

执行完毕后执行上下文出栈等待垃圾回收

<img src="https://pic4.zhimg.com/80/v2-941bda734e6c6679f3528e8a1343ebd7_1440w.jpg"/>

## 执行上下文栈（Execution Context Stack）

假如我们写的函数多了，每次调用函数时都创建一个新的执行上下文，如何管理创建的那么多执行上下文呢？

所以 JavaScript 引擎创建了执行上下文栈（Execution context stack，ECS）来管理执行上下文，具有 LIFO（后进先出）的栈结构，用于存储在代码执行期间创建的所有执行上下文。

首次运行JS代码时，会创建一个**全局**执行上下文并Push到当前的执行栈中。每当发生函数调用，引擎都会为该函数创建一个**新的函数**执行上下文并Push到当前执行栈的顶部，浏览器的JS执行引擎总是访问栈顶的执行上下文。

根据执行栈LIFO规则，当栈顶函数运行完成后，其对应的**函数**执行上下文将会从执行栈中Pop出，上下文控制权将移到当前执行栈的**下一个**执行上下文，最终移回到**全局**执行上下文，全局上下文只有唯一的一个，它在浏览器关闭时Pop出。

看到目前为止，是否觉得这两个概念还是有点晦涩难懂呢？那...接下来通过几小段代码和图解来详细介绍并理解吧。

### 执行上下文是如何执行的

让我们先来看一下这段简单代码：

```javascript
function b(){}
function a(){
  b();
}
a();
```

这段代码背后执行的逻辑是这样的：

首先，全局执行上下文（Global Execution Context）会被建立，这时候会一并建立this、global object (window)，在函数开始执行的过程中，function a和b由于JS提升机制的缘故会先被建立在内存中，接着才会开始逐行执行函数。

<img src="https://pic4.zhimg.com/80/v2-3498bcd6cd4b7e2194d0c7164f005e1d_1440w.jpg"/>

接着，代码会执行到a( )这个部分，这时候，会建立a的执行上下文（execution context），并且被放置到执行栈（execution stack）中。在这个execution stack中，最上面的execution context会是正在被执行的a( )。如下图：

<img src="https://pic2.zhimg.com/80/v2-266c4013a3b1b680d4531ecf437eef44_1440w.jpg"/>

function a 的execution context建立后，便会开始执行function a中的内容。由于在function a( ) 里面有去执行function b ，因此，在这个execution stack中，接下来最上面会变成function b 的execution context。如下图：

<img src="https://pic1.zhimg.com/80/v2-eb0943a691402d7b2833ed05bf6d7302_1440w.jpg"/>

当function b 执行完之后，会从execution stack中离开，继续逐行执行function a。当function a 执行完之后，一样会从execution stack中抽离，再回到Global Execution Context逐行执行。如下图：

<img src="https://pic4.zhimg.com/80/v2-c7e6f94bb793876edeee10f4da1050f8_1440w.jpg"/>

### 不同执行上下文中的变量是不同的

在了解了一般的函数其运作背后的逻辑后，让我们来看一下这段代码：

```javascript
function b(){
  var myVar;
  console.log(myVar);
}

function a(){
  var myVar = 2;
  b();
  console.log(myVar);
}

var myVar = 1;
console.log(myVar);
a();

//结果是  1 undefined 2
```

原因是：执行此段代码，全局上下文会被建立，确定this和global object(windows)，然后因为变量提升，所以会把function b和a，以及变量myVar放入内存中，然后开始执行，一开始会先执行 myVar = 1;然后`console.log(myVar)`所以打印出1。然后逐行执行，到`a()`,于是建立`function a()`的上下文环境如下：

<img src="https://pic4.zhimg.com/80/v2-11a9559d9341df0daa78c7b2fc3cf1ff_1440w.jpg"/>

在函数a的上下文环境中，依旧先是变量和函数提升，然后执行 myVar = 2,再执行函数b，hanshub进入执行栈中，进入b的上下文环境：

<img src="https://pic1.zhimg.com/80/v2-368a93dfbb75db6c5fc4c3d7425ec8d8_1440w.jpg"/>

在b中打印myVar为`undefined`。函数b执行完毕，跳出执行栈，然后重新开始执行函数a，在函数a的上下文环境中打印myVar为2。函数a执行完毕，跳出执行栈，然后又进入全局的执行上下文环境中。

<img src="https://picb.zhimg.com/80/v2-c5db32bb260bcf83728281c182a87509_1440w.jpg"/>

由上面的例子，我们可以知道，我们是在不同的execution context中分别去声明变量myVar的，**因此在不同的execution context，变量彼此之间不会影响**，所以虽然这三个变量都叫做myVar，但其实是三个不同的变量。

由于我们是在不同的execution context中去声明变量，所以这其实是位于三个不同execution context中的变量，所以即使我们是在执行完a( )后再去调用一次myVar，一样会得到" 1"的结果。

### 注意

需要注意的是，如果是在function里面直接使用myVar这个变量，而没有通过var重新声明它的话，就会得到不同的结果！因为**在函数作用域内加 var 定义的变量是局部变量，不加 var 定义的就成了全局变量**。在未声明新的变量的情况下，在该execution context中JavaScript 引擎找不到这个变量，它就会往它的外层去寻找，最后会得到，1 ,2 ,2 ,2 的结果。

```javascript
function b(){
  myVar;
  console.log(myVar);
}

function a(){
  myVar = 2;
  b();
  console.log(myVar);
}

var myVar = 1;
console.log(myVar);
a();
console.log(myVar);

/*
打印出
1
2
2
2
*/
```



参考：米淇淋的前端旅行之路-[JavaScript系列之执行上下文和执行栈](https://zhuanlan.zhihu.com/p/68799915)

