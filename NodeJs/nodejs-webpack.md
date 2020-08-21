[toc]

# webpack

webpack的一些基础配置

## context

context表示上下文，即入口文件的绝对路径。

```js
module.exports = {
  context: path.resolve(__dirname,'../');
  entry: path.resolve(__dirname,'./src/main.js');
}
//其中 resolve 是拼接的意思 dirname 即 direction name，表示当前文件的目录
//上文就表示，先建立入口文件的上下文，即根目录 为 当前目录的上级目录  然后第一入口文件为 上级目录下的src/mai.js
//如果不定义 context 也是可以的  entry就要写成：path.resolve(__dirname,'../src/main.js');
```

## entry

entry 有三种形式：字符串、数组和对象。其中字符串和数组是对象的简化形式。

### entry的对象形式

```js
entry: {
  <key>: <value>
    ...
}
```

对象中的每一对属性对，都代表着一个入口文件，因此多页面配置时，肯定是要用这种形式的entry配置。

#### key

key可以是简单的字符串，比如：'app', 'main', 'entry-1'等。并且对应着`output.filename`配置中的`[name]`变量

```js
entry: {
    'app-entry': './app.js'
},
output: {
    path: './output',
    filename: '[name].js'
}
```

上面的配置打包后生成：

![img](https://segmentfault.com/img/remote/1460000008288244?w=262&h=118)

key还可以是路径字符串。此时webpack会自动生成路径目录，并将路径的最后作为`[name]`。这个特性在多页面配置下也是很有用的

```js
entry: {
    'path/of/entry': './deep-app.js',
    'app': './app.js'
},
output: {
    path: './output',
    filename: '[name].js'
}
```

上面的配置打包后生成：

![img](https://segmentfault.com/img/remote/1460000008288245?w=263&h=189)

#### value

value如果是字符串，而且必须是合理的node`require`函数参数字符串。比如**文件路径**：'./app.js'(`require('./app.js')`)；比如**安装的npm模块**：'lodash'(`require('lodash')`)

```js
entry: {
    'my-lodash': 'lodash'
},
output: {
    path: './output',
    filename: '[name].js'
}
```

上面的配置打包后生成：

![img](https://segmentfault.com/img/remote/1460000008288246?w=259&h=119)

value如果是数组，则数组中元素需要是上面描述的合理字符串值。数组中的文件一般是没有相互依赖关系的，但是又处于某些原因需要将它们打包在一起。比如：

```js
entry: {
    vendor: ['jquery', 'lodash']
}
```

上面代码就是把一些固定的，不会太改变的代码文件绑定成一个vendor.js的文件，这样减少浏览器的请求，并加快速度。

### 字符串entry

```js
entry: './app.js'
```

等价于下面的对象形式：

```js
entry: {
    main: './app.js'
}
```

### 数组entry

```js
entry: ['./app.js', 'lodash']
```

等价于下面的对象形式：

```js
entry: {
    main: ['./app.js', 'lodash']
}
```

## output

配置出口文件。

output具有很多属性，这里选几个常用的。

### filename

输出的文件名，可以是固定的文件名：

```js
output: {
  filename : 'src/bundle.js' //这样所有entry内的js都会打包成一个bundle.js文件
}
```

那么如果有多个 chunk怎么办？

根据上面的entry可以知道：

+ 如果entry是一个string或者array，就只会生成一个chunk，这个chunk的名称是main;

+ 如果entry是一个object，就可能出现多个chunk，这时chunk的名称是object键值对里键的名称

所以我们可以使用chunk来生成多个输出文件。

**使用入口名称**

```js
output: {
  filename: '[name].bundle.js'
}
```

**使用内部的chunk id**

```js
output: {
  filename: '[id].bundle.js'
}
```

**使用每次构建过程中，唯一的 hash 生成**

```javascript
output:{
filename: "[name].[hash].bundle.js"
}
```

**使用基于每个 chunk 内容的 hash**

```javascript
output:{
filename: "[chunkhash].bundle.js"
}
```

#### hash、chunkhash和contenthash三者的区别

**hash**
 我做了一个[name]_[hash].js的测试

[![img](https://www.qdtalk.com/wp-content/uploads/2018/11/hash.png)](https://www.qdtalk.com/wp-content/uploads/2018/11/hash.png)


 看到了问题了吧，hash的值是相同的，如果都使用hash的话，因为这是工程级别的，即每次修改任何一个文件，所有文件名的hash至都将改变。所以一旦修改了任何一个文件，整个项目的文件缓存都将失效。所以对于没有改变的模块而言，这样做显然不恰当，因为缓存失效了嘛。此时，chunkhash的用途随之而来。
**chunkhash**
 只有被修改了的文件的文件名，hash值修改

```javascript
filename: '[name]-[chunkhash].js'
```

当我们使用mini-css-extract-plugin拆分css的时候，就需要使用chunkhash，我一个js文件里面引入了css文件。这时要是我修改了js，但没修改css，可以通过chunkhash缓存css文件
 **contenthash**
 对css使用了chunkhash之后，我们测试会发现，如果修改了js，css文件名的hash值确实没变，但这时要是我们修改css文件的话，我们就会发现css文件名的chunkhash值居然没变化，这样就导致我们的非覆盖发布css文件失效了。所以这里需要注意就是css文件必须使用contenthash。
 上面介绍的 id、name、hash、chunkhash等都是webpack内置变量，
 id是唯一标示，不会重复，从0开始，
 name 是模块名称，是你自己起的，在配置路由懒加载的时候可以自己命名
 官网介绍的很清楚，我就不再这里啰嗦了，想要更了解contenthash和chunkhash，[点击](https://www.cnblogs.com/zhangmingzhao/p/11978606.html)

### chunkFilename

官网解释：此选项决定了非入口(non-entry) chunk 文件的名称，
 什么场景需要呢？
 在按需加载（异步）模块的时候，也就是路由懒加载，这样的文件是没有被列在entry中的，
 比如



```javascript
{
    entry: {
        "index": "pages/index.jsx"
    },
    output: {
         filename: "[name].min.js",
        chunkFilename: "[name].min.js"
    }
}
const myModel = r => require.ensure([], () => r(require('./myVue.vue')), 'myModel')
```

上面的例子，通过filename输出的是index.min.js
 异步加载的模块是要以文件形式加载哦，所以这时生成的文件名是以chunkname配置的，通过chunkFilename输出的是myModel.min.js
 所以chunkFilename也很重要哦！！！

### path

path是配置输出文件存放在本地的目录，**字符串类型**，**是绝对路径**



```javascript
output:{
    path: path.resolve(__dirname, 'dist/assets')
}
```

### publicPath

对构建出的资源进行异步加载（图片，文件），该选项的值是以 runtime(运行时) 或 loader(载入时) 所创建的每个 URL 为前缀。因此，在多数情况下，此选项的值都会以/结束。
 默认值是一个空字符串 ""，即相对路径，配置错误会导致404
 简单说，就是静态文件托管在cdn上
 举个栗子：
 如果你这么配置：



```javascript
output:{
    filename:'[name]_[chunkhash:8].js',
    publicPath:'https://www.qdtalk.com/assets/'
}
```

打包编译后，html页面就是这样的



```html
<script src="https://www.qdtalk.com/assets/a_12345678.js"></script>
```

path 和publicPath都支持字符串模板。	

## resolve

### alias

` resolve.alias` 配置项通过别名来把原导入路径映射成一个新的导入路径。例如使用以下配置：

```js
// Webpack alias 配置
resolve:{
  alias:{
    components: './src/components/'
  }
}
```

当你通过` import Button from 'components/button` 导入时，实际上被 `alias` 等价替换成了 `import Button from './src/components/button'` 。

以上 alias 配置的含义是把导入语句里的 `components` 关键字替换成 `./src/components/` 。

这样做可能会命中太多的导入语句，alias 还支持 `$` 符号来缩小范围到只命中以关键字结尾的导入语句：

```js
resolve:{
  alias:{
    'react$': '/path/to/react.min.js'
  }
}
```

 `react$` 只会命中以 `react` 结尾的导入语句，即只会把` import 'react'` 关键字替换成` import '/path/to/react.min.js' `。

### mainFields

有一些第三方模块会针对不同环境提供几分代码。 例如分别提供采用 ES5 和 ES6 的2份代码，这2份代码的位置写在 `package.json` 文件里，如下：

```js
{
  "jsnext:main": "es/index.js",// 采用 ES6 语法的代码入口文件
  "main": "lib/index.js" // 采用 ES5 语法的代码入口文件
}
```

`Webpack` 会根据 `mainFields` 的配置去决定优先采用那份代码， `mainFields` 默认如下：

```js
mainFields: ['browser', 'main']
```

`Webpack` 会按照数组里的顺序去 `package.json` 文件里寻找，只会使用找到的第一个。

假如你想优先采用 ES6 的那份代码，可以这样配置：

```js
mainFields: ['jsnext:main', 'browser', 'main']
```

### extensions

在导入语句没带文件后缀时，Webpack 会自动带上后缀后去尝试访问文件是否存在。` resolve.extensions` 用于配置在尝试过程中用到的后缀列表，默认是：

```js
extensions: ['.js', '.json']
```

也就是说当遇到 require('./data') 这样的导入语句时，Webpack 会先去寻找` ./data.js` 文件，如果该文件不存在就去寻找 `./data.json `文件， 如果还是找不到就报错。

假如你想让 Webpack 优先使用目录下的 TypeScript 文件，可以这样配置：

```js
extensions: ['.ts', '.js', '.json']
```

### modules

 `resolve.modules` 配置 Webpack 去哪些目录下寻找第三方模块，默认是只会去 node_modules 目录下寻找。 有时你的项目里会有一些模块会大量被其它模块依赖和导入，由于其它模块的位置分布不定，针对不同的文件都要去计算被导入模块文件的相对路径， 这个路径有时候会很长，就像这样 `import '../../../components/button'` 这时你可以利用 modules 配置项优化，假如那些被大量导入的模块都在 `./src/components` 目录下，把 modules 配置成

```js
modules:['./src/components','node_modules']
```

后，你可以简单通过 import 'button' 导入。

### descriptionFiles

` resolve.descriptionFiles` 配置描述第三方模块的文件名称，也就是 package.json 文件。默认如下：

```
descriptionFiles: ['package.json']
```

### enforceExtension

 `resolve.enforceExtension `如果配置为` true `所有导入语句都必须要带文件后缀， 例如开启前` import './foo' `能正常工作，开启后就必须写成` import './foo.js' `。

### enforceModuleExtension

 `enforceModuleExtension` 和 `enforceExtension` 作用类似，但 `enforceModuleExtension` 只对` node_modules` 下的模块生效。 `enforceModuleExtension` 通常搭配 `enforceExtension` 使用，在 `enforceExtension:true` 时，因为安装的第三方模块中大多数导入语句没带文件后缀， 所以这时通过配置 `enforceModuleExtension:false` 来兼容第三方模块。

## module

`module` 配置如何处理模块。

这里不做详细的叙述，每个模块都有自己的处理规则。

## plugin

> loader不能做的处理都能交给plugin来做

比如有：

```js
new CleanWebpackPlugin()  //通过clean-webpack-plugin插件删除输出目中之前旧的文件。
new VueLoaderPlugin()  //Vue-loader在15.*之后的版本都是 vue-loader的使用都是需要伴生 VueLoaderPlugin的
new HtmlWebpackPlugin({}) //配置一个html输出文件 简化了HTML文件的创建，以便为你的webpack包提供服务。
```

## 一些辅助开发的相关属性

+ devtool:打包后的代码和原始代码存在较大的差异，此选项控制是否生成以及如何生成sourcemap
+ devserver：通过配置devserver选项，可以开启一个本地服务器
+ watch：启用watch模式后，webpack将持续监听热河已经解析文件的更改，开发是开启会很方便
+ watchoption：用来定制watch模式的选项
+ performance：打包后命令行如何展示性能提示，如果超过某个大小是警告还是报错

