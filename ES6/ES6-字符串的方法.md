[toc]

# ES6-字符串的方法

## charAt()

> 返回在指定位置的字符。

```js
var str="abc"
console.log(str.charAt(0))//a

let str = '\u0061\u0062\u0063' //abc
str.charAt(0); //a

let str = '\u{1F680}' //"🚀"
str.charAt(0); //"�"
str.charAt(1); //"�" charAt是每两个字节一个字符判断 则：'\u{1F680}' === '\uD83D\uDE80' \uD83D和\uDE80用chrome解析都是"�"
```

## charCodeAt()

> 返回在指定的位置的字符的 Unicode 编码（10进制）。

```js
var str="abc"
console.log(str.charCodeAt(1))//98

let str = '\u0061\u0062\u0063' //abc
str.charCodeAt(0); //97

let str = '\u{1F680}' //"🚀"
str.charCodeAt(0); //55357
str.charCodeAt(1); //56960  
//跟charAt()一样 '\u{1F680}' === '\uD83D\uDE80'
```

## concat()

> 连接字符串。

```js
var a = "abc";  
var b = "def";  
var c = a.concat(b); //abcdef

a = '\u0061' //'a'
b = '\u0062' // 'b'
a.concat(b); //'ab'


a = '\u{1f680}'
b = '\u0062'
a.concat(b); //"🚀b"


a = '\uD83D'
b = '\uDE80'
a.concat(b);  //"🚀"
```

## indexOf()

> 检索字符串。返回某个指定的字符串值在字符串中首次出现的位置，如果没有找到返回-1。indexOf() 方法对大小写敏感！

语法：`stringObject.indexOf(searchvalue,fromindex)`

<img src="https://segmentfault.com/img/bVbdZh0?w=829&h=134"/>

```js
var str="Hello world!"
console.log(str.indexOf("Hello"))//0
console.log(str.indexOf("World"))//-1
console.log(str.indexOf("world"))///6

let str = '\u0061\u0062\u0063';
console.log(str.indexOf('b')); //1

let str2 = '\u{17680}\u0062'
console.log(str2.indexOf('b')); //2
console.log(str2.indexOf('\u0062')); //2
```

## match()

> match() 方法可在字符串内检索指定的值，或找到一个或多个正则表达式的匹配。该方法类似 indexOf() 和 lastIndexOf()，但是它返回指定的值，而不是字符串的位置。

```js
var str="1 abc 2 def 3"
console.log(str.match(/\d+/g))// ['1','2','3']

var str="1 abc 2 def 3"
console.log(str.match(/\d+/))// ["1", index: 0, input: "1 abc 2 def 3", groups: undefined]
```

## replace()

> replace() 方法用于在字符串中用一些字符替换另一些字符，或替换一个与正则表达式匹配的子串。

```js
var str="abc Def!"
console.log(str.replace(/abc/, "CBA"))//CBA Def!

var str="abc Def!"
console.log(str.replace(/\u0061bc/, "CBA"))//CBA Def!
```

## search()

> search() 方法用于检索字符串中指定的子字符串，或检索与正则表达式相匹配的子字符串。要执行忽略大小写的检索，请追加标志 i。如果没有找到任何匹配的子串，则返回 -1。

语法：

<img src="https://segmentfault.com/img/bVbdZm9?w=836&h=198"/>

**说明**
search() 方法不执行全局匹配，它将忽略标志 g。它同时忽略 regexp 的 lastIndex 属性，并且总是从字符串的开始进行检索，这意味着它总是返回 stringObject 的第一个匹配的位置。

```js
var str="abc DEF!"
console.log(str.search(/DEF/))//4
console.log(str.search(/def/i)); //4
```

## indexOf与search的区别

抛砖引玉：search()方法也是同样返回目标自字符串索引值的。indexOf()和search()有什么区别呢？为什么时候该使用它，什么时候该使用search()这个方法呢？

首先要明确search()的参数必须是正则表达式,而indexOf()的参数只是普通的字符串。indexOf()是比search()更加底层的方法。

如果只是兑一个具体字符串来茶渣检索，那么使用indexOf()的系统资源消耗更小，效率更高；如果查找具有某些特征的字符串（例如查找以a开头，后面是数字的字符串），那么indexOf()就无能为力，必须要使用正则表达式和search()方法了。

大多是时候用indexOf()不是为了真的想知道子字符串的位置，而是想知道长字符串中有没有包含这个子字符串。若果返回索引为-1，那么说明没有，反之则有。

## slice()

> 提取字符串的片断，并在新的字符串中返回被提取的部分。

> stringObject.slice(**start,end**);
> **start** :要抽取的片断的起始下标。如果是负数，则该参数规定的是从字符串的尾部开始算起的位置。也就是说，-1 指字符串的最后一个字符，-2 指倒数第二个字符，以此类推。
> **end**：紧接着要抽取的片段的结尾的下标。若未指定此参数，则要提取的子串包括 start 到原字符串结尾的字符串。如果该参数是负数，那么它规定的是从字符串的尾部开始算起的位置。

```js
var str="abc def ghk"
console.log(str.slice(6))//f ghk

var str="abc de\u0062f ghk"
console.log(str.slice(6))//f ghk
```

## split()

> 把字符串分割为字符串数组。

```js
var str="abc def ghi jkl"    
console.log(str.split(" "))//["abc", "def", "ghi", "jkl"]
console.log(str.split("") )//["a", "b", "c", " ", "d", "e", "f", " ", "g", "h", "i", " ", "j", "k", "l"]
console.log(str.split(" ",3))//["abc", "def", "ghi"]
```

## toLocaleLowerCase()

> 把字符串转换为小写。

```js
var str="ABC def!"
console.log(str.toLocaleLowerCase())//abc def!
```

## toLocaleUpperCase()

> 把字符串转换为大写。

```js
var str="ABC def!"
console.log(str.toLocaleUpperCase())//ABC DEF!
```

## toLowerCase()

> 把字符串转换为小写。

```js
var str="ABC def!"
console.log(str.toLowerCase())//abc def!
```

## toUpperCase()

> 把字符串转换为大写。

```js
var str="ABC def!"
console.log(str.toUpperCase())//ABC DEF!
```

## toLocaleLowerCase和toLowerCase的区别

> toLocaleUpperCase和toUpperCase同理。

而toLocaleLowerCase()和toLocaleUpperCase()方法则是针对特定地区的实现。

对有些地区来说，针对地区的方法与其通用方法得到的结果相同，但少数语言(如土耳其语言)会为Unicode大小写转换应用特殊的规则，这时候就必须使用针对地区的方法来保证实现正确的转换。以下是几个例子：

```js
var stringValue = "hello world";
alert(stringValue.toLocaleUpperCase());    //"HELLO WORLD"
alert(stringValue.toUpperCase());          //"HELLO WORLD"
alert(stringValue.toLocaleLowerCase());    //"hello world"
alert(stringValue.toLowerCase());          //"hello world"
```

以上代码调用的toLocaleUpperCase()和toUpperCase()都返回了“HELLO WORLD”，就像调用toLocaleLowerCase()和toLowerCase()都返回“hello world”一样。一般来说，在不知道自己的代码将在那种语言环境中运行的情况下，还是使用针对地区的方法更稳妥一些。

## substr()

> 从起始索引号提取字符串中指定数目的字符。

stringObject.substr(**start,length**)。
**start：**必需。要抽取的子串的起始下标。必须是数值。如果是负数，那么该参数声明从字符串的尾部开始算起的位置。也就是说，-1 指字符串中最后一个字符，-2 指倒数第二个字符，以此类推。
**length：**可选。子串中的字符数。必须是数值。如果省略了该参数，那么返回从 stringObject 的开始位置到结尾的字串。

```js
var str="abc def"
console.log(str.substr(2))//c def
console.log(str.substr(2,4))// c de 
```

## substring()

> 提取字符串中两个指定的索引号之间的字符。

提取字符串中两个指定的索引号之间的字符。
stringObject.substring(**start,stop**)。
**start ：**必需。一个非负的整数，规定要提取的子串的第一个字符在 stringObject 中的位置。
**stop ：**可选。一个非负的整数，比要提取的子串的最后一个字符在 stringObject 中的位置多 1。如果省略该参数，那么返回的子串会一直到字符串的结尾。

```js
var str="abc def"
console.log(str.substring(2))//c def
console.log(str.substring(2,4))// c 
```

## substr和substring的区别

**相同点：**如果只是写一个参数，两者的作用都一样：都是是截取字符串从当前下标以后直到字符串最后的字符串片段。
`substr(startIndex);` `substring(startIndex);`

```js
var str = '123456789';
console.log(str.substr(2));    //  "3456789"
console.log(str.substring(2)) ;//  "3456789"
```

**不同点：**第二个参数
`substr（startIndex,lenth）`： 第二个参数是截取字符串的长度（从起始点截取某个长度的字符串）；
`substring（startIndex, endIndex）`： 第二个参数是截取字符串最终的下标 （截取2个位置之间的字符串,‘含头不含尾’）。

```js
console.log("123456789".substr(2,5));    //  "34567"
console.log("123456789".substring(2,5)) ;//  "345"
```

## ES6新增

### codePointAt()

JavaScript 内部，字符以 UTF-16 的格式储存，每个字符固定为`2`个字节。对于那些需要`4`个字节储存的字符（Unicode 码点大于`0xFFFF`的字符），JavaScript 会认为它们是两个字符。

```javascript
var s = "𠮷";

s.length // 2
s.charAt(0) // ''
s.charAt(1) // ''
s.charCodeAt(0) // 55362
s.charCodeAt(1) // 57271
```

上面代码中，汉字“𠮷”（注意，这个字不是“吉祥”的“吉”）的码点是`0x20BB7`，UTF-16 编码为`0xD842 0xDFB7`（十进制为`55362 57271`），需要`4`个字节储存。对于这种`4`个字节的字符，JavaScript 不能正确处理，字符串长度会误判为`2`，而且`charAt()`方法无法读取整个字符，`charCodeAt()`方法只能分别返回前两个字节和后两个字节的值。

ES6 提供了`codePointAt()`方法，能够正确处理 4 个字节储存的字符，返回一个字符的码点。

```javascript
let s = '𠮷a';

s.codePointAt(0) // 134071
s.codePointAt(1) // 57271

s.codePointAt(2) // 97
```

`codePointAt()`方法的参数，是字符在字符串中的位置（从 0 开始）。上面代码中，JavaScript 将“𠮷a”视为三个字符，codePointAt 方法在第一个字符上，正确地识别了“𠮷”，返回了它的十进制码点 134071（即十六进制的`20BB7`）。在第二个字符（即“𠮷”的后两个字节）和第三个字符“a”上，`codePointAt()`方法的结果与`charCodeAt()`方法相同。

总之，`codePointAt()`方法会正确返回 32 位的 UTF-16 字符的码点。对于那些两个字节储存的常规字符，它的返回结果与`charCodeAt()`方法相同。

`codePointAt()`方法返回的是码点的十进制值，如果想要十六进制的值，可以使用`toString()`方法转换一下。

```javascript
let s = '𠮷a';

s.codePointAt(0).toString(16) // "20bb7"
s.codePointAt(2).toString(16) // "61"
```

你可能注意到了，`codePointAt()`方法的参数，仍然是不正确的。比如，上面代码中，字符`a`在字符串`s`的正确位置序号应该是 1，但是必须向`codePointAt()`方法传入 2。解决这个问题的一个办法是使用`for...of`循环，因为它会正确识别 32 位的 UTF-16 字符。

```javascript
let s = '𠮷a';
for (let ch of s) {
  console.log(ch.codePointAt(0).toString(16));
}
// 20bb7
// 61
```

另一种方法也可以，使用扩展运算符（`...`）进行展开运算。

```javascript
let arr = [...'𠮷a']; // arr.length === 2
arr.forEach(
  ch => console.log(ch.codePointAt(0).toString(16))
);
// 20bb7
// 61
```

`codePointAt()`方法是测试一个字符由两个字节还是由四个字节组成的最简单方法。

```javascript
function is32Bit(c) {
  return c.codePointAt(0) > 0xFFFF;
}

is32Bit("𠮷") // true
is32Bit("a") // false
```

### String.fromCodePoint()

ES5 提供`String.fromCharCode()`方法，用于从 Unicode 码点返回对应字符，但是这个方法不能识别码点大于`0xFFFF`的字符。

```javascript
String.fromCharCode(0x20BB7)
// "ஷ"
```

上面代码中，`String.fromCharCode()`不能识别大于`0xFFFF`的码点，所以`0x20BB7`就发生了溢出，最高位`2`被舍弃了，最后返回码点`U+0BB7`对应的字符，而不是码点`U+20BB7`对应的字符。

ES6 提供了`String.fromCodePoint()`方法，可以识别大于`0xFFFF`的字符，弥补了`String.fromCharCode()`方法的不足。在作用上，正好与下面的`codePointAt()`方法相反。

```javascript
String.fromCodePoint(0x20BB7)
// "𠮷"
String.fromCodePoint(0x78, 0x1f680, 0x79) === 'x\uD83D\uDE80y'
// true
```

上面代码中，如果`String.fromCodePoint`方法有多个参数，则它们会被合并成一个字符串返回。

注意，`fromCodePoint`方法定义在`String`对象上，而`codePointAt`方法定义在字符串的实例对象上。

### 字符串的遍历器接口 for of

```js
for (let codePoint of 'abc') {
  console.log(codePoint)
}
// "a"
// "b"
// "c"
```

除了遍历字符串，这个遍历器最大的优点是可以识别大于0xFFFF的码点，传统的for循环无法识别这样的码点。

### normalize()

许多欧洲语言有语调符号和重音符号。为了表示它们，Unicode 提供了两种方法。一种是直接提供带重音符号的字符，比如`Ǒ`（\u01D1）。另一种是提供合成符号（combining character），即原字符与重音符号的合成，两个字符合成一个字符，比如`O`（\u004F）和`ˇ`（\u030C）合成`Ǒ`（\u004F\u030C）。

这两种表示方法，在视觉和语义上都等价，但是 JavaScript 不能识别。

```javascript
'\u01D1'==='\u004F\u030C' //false

'\u01D1'.length // 1
'\u004F\u030C'.length // 2
```

ES6 提供字符串实例的`normalize()`方法，用来将字符的不同表示方法统一为同样的形式，这称为 Unicode 正规化。

```javascript
'\u01D1'.normalize() === '\u004F\u030C'.normalize()
// true
```

`normalize`方法可以接受一个参数来指定`normalize`的方式，参数的四个可选值如下。

- `NFC`，默认参数，表示“标准等价合成”（Normalization Form Canonical Composition），返回多个简单字符的合成字符。所谓“标准等价”指的是视觉和语义上的等价。
- `NFD`，表示“标准等价分解”（Normalization Form Canonical Decomposition），即在标准等价的前提下，返回合成字符分解的多个简单字符。
- `NFKC`，表示“兼容等价合成”（Normalization Form Compatibility Composition），返回合成字符。所谓“兼容等价”指的是语义上存在等价，但视觉上不等价，比如“囍”和“喜喜”。（这只是用来举例，`normalize`方法不能识别中文。）
- `NFKD`，表示“兼容等价分解”（Normalization Form Compatibility Decomposition），即在兼容等价的前提下，返回合成字符分解的多个简单字符。

```javascript
'\u004F\u030C'.normalize('NFC').length // 1
'\u004F\u030C'.normalize('NFD').length // 2
```

上面代码表示，`NFC`参数返回字符的合成形式，`NFD`参数返回字符的分解形式。

不过，`normalize`方法目前不能识别三个或三个以上字符的合成。这种情况下，还是只能使用正则表达式，通过 Unicode 编号区间判断。

## includes(), startsWith(), endsWith()

传统上，JavaScript 只有`indexOf`方法，可以用来确定一个字符串是否包含在另一个字符串中。ES6 又提供了三种新方法。

- **includes()**：返回布尔值，表示是否找到了参数字符串。
- **startsWith()**：返回布尔值，表示参数字符串是否在原字符串的头部。
- **endsWith()**：返回布尔值，表示参数字符串是否在原字符串的尾部。

```javascript
let s = 'Hello world!';

s.startsWith('Hello') // true
s.endsWith('!') // true
s.includes('o') // true
```

这三个方法都支持第二个参数，表示开始搜索的位置。

```javascript
let s = 'Hello world!';

s.startsWith('world', 6) // true
s.endsWith('Hello', 5) // true
s.includes('Hello', 6) // false
```

上面代码表示，使用第二个参数`n`时，`endsWith`的行为与其他两个方法有所不同。它针对前`n`个字符，而其他两个方法针对从第`n`个位置直到字符串结束。

### repeat()

`repeat`方法返回一个新字符串，表示将原字符串重复`n`次。

```javascript
'x'.repeat(3) // "xxx"
'hello'.repeat(2) // "hellohello"
'na'.repeat(0) // ""
```

参数如果是小数，会被取整。

```javascript
'na'.repeat(2.9) // "nana"
```

如果`repeat`的参数是负数或者`Infinity`，会报错。

```javascript
'na'.repeat(Infinity)
// RangeError
'na'.repeat(-1)
// RangeError
```

但是，如果参数是 0 到-1 之间的小数，则等同于 0，这是因为会先进行取整运算。0 到-1 之间的小数，取整以后等于`-0`，`repeat`视同为 0。

```javascript
'na'.repeat(-0.9) // ""
```

参数`NaN`等同于 0。

```javascript
'na'.repeat(NaN) // ""
```

如果`repeat`的参数是字符串，则会先转换成数字。

```javascript
'na'.repeat('na') // ""
'na'.repeat('3') // "nanana"
```

### padStart()，padEnd()

ES2017 引入了字符串补全长度的功能。如果某个字符串不够指定长度，会在头部或尾部补全。`padStart()`用于头部补全，`padEnd()`用于尾部补全。

```javascript
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'

'x'.padEnd(5, 'ab') // 'xabab'
'x'.padEnd(4, 'ab') // 'xaba'
```

上面代码中，`padStart()`和`padEnd()`一共接受两个参数，第一个参数是字符串补全生效的最大长度，第二个参数是用来补全的字符串。

如果原字符串的长度，等于或大于最大长度，则字符串补全不生效，返回原字符串。

```javascript
'xxx'.padStart(2, 'ab') // 'xxx'
'xxx'.padEnd(2, 'ab') // 'xxx'
```

如果用来补全的字符串与原字符串，两者的长度之和超过了最大长度，则会截去超出位数的补全字符串。

```javascript
'abc'.padStart(10, '0123456789')
// '0123456abc'
```

如果省略第二个参数，默认使用空格补全长度。

```javascript
'x'.padStart(4) // '   x'
'x'.padEnd(4) // 'x   '
```

`padStart()`的常见用途是为数值补全指定位数。下面代码生成 10 位的数值字符串。

```javascript
'1'.padStart(10, '0') // "0000000001"
'12'.padStart(10, '0') // "0000000012"
'123456'.padStart(10, '0') // "0000123456"
```

另一个用途是提示字符串格式。

```javascript
'12'.padStart(10, 'YYYY-MM-DD') // "YYYY-MM-12"
'09-12'.padStart(10, 'YYYY-MM-DD') // "YYYY-09-12"
```

### trimStart()，trimEnd()

[ES2019](https://github.com/tc39/proposal-string-left-right-trim) 对字符串实例新增了`trimStart()`和`trimEnd()`这两个方法。它们的行为与`trim()`一致，`trimStart()`消除字符串头部的空格，`trimEnd()`消除尾部的空格。它们返回的都是新字符串，不会修改原始字符串。

```javascript
const s = '  abc  ';

s.trim() // "abc"
s.trimStart() // "abc  "
s.trimEnd() // "  abc"
```

上面代码中，`trimStart()`只消除头部的空格，保留尾部的空格。`trimEnd()`也是类似行为。

除了空格键，这两个方法对字符串头部（或尾部）的 tab 键、换行符等不可见的空白符号也有效。

浏览器还部署了额外的两个方法，`trimLeft()`是`trimStart()`的别名，`trimRight()`是`trimEnd()`的别名。

### matchAll()

`match`加g标志、`match`不加g标志、`matchAll`加g标志、`matchAll`不加g标志，这四种组合情况分别返回什么结果。

> match`和`matchAll`的区别

1、`match`返回值是一个数组，如果没有任何匹配项则返回`null`；`matchAll`返回迭代器，要想查看其结果需要遍历迭代器。

2、`match`匹配到第一个匹配项后即停止匹配；`matchAll`会匹配出字符串中所有的匹配项。

> 正则表达式中g标志的作用

g标志表示全局搜索，如果定义正则表达式时没有g修饰，则只返回首个匹配的结果。否则返回所有匹配结果。

> 示例

<img src="https://imgedu.lagou.com/3084e094fcf44519b0de584a8695ed3b.jpg"/>



**说明：**

  1.对于`match`来说，如果正则表达式中有g修饰，结果返回所有与正则表达式匹配的字符串的列表。捕获项会被忽略！

  2.对于`match`来说，如果正则表达式中没有g修饰，结果除了返回第一个匹配外，还会列出其所有捕获项！

  3.对于`matchAll`来说，如果正则表达式有g修饰，其返回的迭代项是一个个的数组，数组中除了包含匹配的完整字符串和所有捕获项外，还包含有`index`、`input`、`groups`这三个属性，`index`表示当前匹配项在原字符串中的索引位置，`input`表示输入的原始字符串，`groups`是一个包含所以分组的对象。

  4.对于`matchAll`来说，如果正则表达式没有g修饰，其结果信息与第3点中描述的完全一致。但其迭代项只有一项，即首个匹配项。

## 参考

阮一峰-[字符串的扩展](https://es6.ruanyifeng.com/#docs/string)

沉静地闪光-[JavaScript字符串操作方法大全，包含ES6方法](https://segmentfault.com/a/1190000016603159)