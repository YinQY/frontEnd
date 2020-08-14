[toc]

# CSS-元素

## 块级元素

- 总是独占一行，表现为另起一行开始，而且其后的元素也必须另起一行显示;
- 宽度(width)、高度(height)、内边距(padding)和外边距(margin)都可控制;

### address

> <address> 标签定义文档或文章的作者/拥有者的联系信息。
>
> 如果 <address> 元素位于 <body> 元素内，则它表示文档联系信息。
>
> 如果 <address> 元素位于 <article> 元素内，则它表示文章的联系信息。
>
> \<address> 元素中的文本通常呈现为斜体。大多数浏览器会在 address 元素前后添加折行。

**提示和注释：**

**提示：**<address> 标签不应该用于描述通讯地址，除非它是联系信息的一部分。

**提示：**<address> 元素通常连同其他信息被包含在 <footer> 元素中。

[例子](https://www.w3school.com.cn/tiy/t.asp?f=html_address)

### blockquote

> \<blockquote> 标签定义块引用。
>
> \<blockquote> 与 </blockquote> 之间的所有文本都会从常规文本中分离出来，经常会在左、右两边进行缩进（增加外边距），而且有时会使用斜体。也就是说，块引用拥有它们自己的空间。

**提示和注释：**

**提示：**请使用 [q 元素](https://www.w3school.com.cn/tags/tag_q.asp)来标记短的引用。

**注释：**如需把页面作为 strict XHTML 进行验证，那么 <blockquote> 元素必须包含块级元素，比如这样：

```
<blockquote>
<p>here is a long quotation here is a long quotation</p>
</blockquote>
```

**可选的属性:**

| 属性                                                         | 值    | 描述             |
| :----------------------------------------------------------- | :---- | :--------------- |
| [cite](https://www.w3school.com.cn/tags/att_blockquote_cite.asp) | *URL* | 规定引用的来源。 |

[例子](https://www.w3school.com.cn/tiy/t.asp?f=html_blockquote)

不想写了... 

其他块级元素：

+ address - 地址 

* blockquote - 块引用 
* center - 举中对齐块 
* dir - 目录列表 
* div - 常用块级容易，也是css layout的主要标签 
* dl - 定义列表 
* fieldset - form控制组 
* form - 交互表单 
* h1 - 大标题 
* h2 - 副标题 
* h3 - 3级标题 
* h4 - 4级标题 
* h5 - 5级标题 
* h6 - 6级标题 
* hr - 水平分隔线 
* isindex - input prompt 
* menu - 菜单列表 
* noframes - frames可选内容，（对于不支持frame的浏览器显示此区块内容 * noscript - ）可选脚本内容（对于不支持script的浏览器显示此内容）
* ol - 排序表单
* p - 段落 
* pre - 格式化文本 
* table - 表格 
* ul - 非排序列表

## 内联元素

- 和相邻的内联元素在同一行;
- 宽度(width)、高度(height)、内边距的top/bottom(padding-top/padding-bottom)和外边距的top/bottom(margin-top/margin-bottom)都不可改变，就是里面文字或图片的大小;

* a - 锚点 
* abbr - 缩写 
* acronym - 首字 
* b - 粗体(不推荐) 
* bdo - bidi override 
* big - 大字体 
* br - 换行 
* cite - 引用 
* code - 计算机代码(在引用源码的时候需要) 
* dfn - 定义字段 
* em - 强调 
* font - 字体设定(不推荐) 
* i - 斜体 
* img - 图片 
* input - 输入框 
* kbd - 定义键盘文本 
* label - 表格标签 
* q - 短引用 
* s - 中划线(不推荐) 
* samp - 定义范例计算机代码 
* select - 项目选择 
* small - 小字体文本 
* span - 常用内联容器，定义文本内区块 
* strike - 中划线 
* strong - 粗体强调 
* sub - 下标 
* sup - 上标 
* textarea - 多行文本输入框 
* tt - 电传文本 
* u - 下划线 
* var - 定义变量

## 可变元素

* applet - java applet 
* button - 按钮 
* del - 删除文本 
* iframe - inline frame 
* ins - 插入的文本 
* map - 图片区块(map) 
* object - object对象 
* script - 客户端脚本