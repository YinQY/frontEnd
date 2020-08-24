[toc]

# HTTP-报文

## http首部字段

HTTP请求报文：请求行(状态行)、请求头、请求体：

```http
method url version(http版本号) 
header 
body
```

其中header包括：

+ 请求首部字段
+ 通用首部字段
+ 实体首部字段

HTTP相应报文：响应行(状态行)、响应头、响应体：

```http
version status 
header
body
```

其中header包括：

+ 响应首部字段
+ 通用首部字段
+ 实体首部字段

一共有4种首部字段，下面会一一介绍

- 通用首部字段：请求报文和响应报文都使用的字段，例如Date 、 Warning
- 请求报文字段：从客户端向服务端发送请求报文时使用的首部字段,例如Host （请求资源所在服务器） User-Agent(HTTP客户端程序信息)
- 响应报文字段：从服务端想客户端发送响应报文时使用的首部字段，例如 Server
- 实体报文字段： 专门针对请求包文和响应报文的实体部分，使用的字段，例如Content-Length / Content-Type

实际的HTTP通信中，不限于RFC2616中定义的47种字段，还包括Cookie、Set-Cookie等

### 通用首部字段

1. Cache-Control : no-cache   设置 我们如何控制缓存

2. Connection: keep-alive

- 设置不再转发给代理的首部字段，比如connection: Upgrade  的意思就是，首部字段删除掉Upgrade后，再转发
- 管理持久链接。



```css
http1.1默认都是持久链接，例如 Connection: keep-alive
如果服务端想明确的断开链接，可以设置  Connection: close
```

3. Date 这当然也属于通用首部字段，表明创建HTTP报文的日期

4. Pragma 是HTTP1.1之前版本的历史遗留字段。



```css
 它的取值很单一，就是表示客户端要求，所有的中间服务器不要返给我缓存的资源
Pragma: no-cache

理想情况下，所有的中间服务器都是基于HTTP1.1协议的话，我们只用
Cache-Control: no-cache 

可是实际开发中，一般我们发送请求会2个首部字段都写上

Pragma: no-cache
Cahche-Control: no-cache
```

5. Tailer 事先说明报文主体后，记录了哪些首部字段。

6. Transfer-Encoding。 规则了传输报文主体时采用的编码方式



```http
 Transfer-Encoding: chunked
```

7. Upgrade 用于检测HTTP协议，是否可使用更高版本的协议

8. Via 该字段用于追踪传输路径，因为经过每一个代理服务器时，都有在via字段，留下自身服务器的信息

9. Warning 告知用户一些与缓存相关的警告

```css
Warning: [警告码] [警告主机： 端口号] [ 警告内容] [日期]
```

### 请求首部字段

1. Accept  客户端用该字段，指定服务器返回的文件类型



```markdown
例如： 
文本文件  text/html , text/plain , text/css
图片文件  image/jpeg    image/gif
应用程序使用的二进制文件：   application/octet-stream , application/zip

可以使用q= 可以指定优先级，默认q=1 
```

2. Accept-Charset  用于通知服务器，我们需要的字符集，以及字符集优先级
3. Accept-Encoding  告知服务器，代理支持的内容编码
4. Accept-Language:  告知服务器，我们能够处理的自然语言，用q=0.8 来设置优先级
5. Auothorization 告知服务器，用户代理的认证信息
6. Expect  告知服务器，期望服务器出现的某种行为

```kotlin
 Expect: 100-continue
等待状态码100响应的客户端在发生请求时，就需要指定这个字段
```

7. From 用来告知服务器，用户的电子邮件地址
8. Host (唯一一个必须被包含的字段 ) 告知服务器，请求资源所在的主机名和端口号
9. If-xxx 这类的请求首部字段，都可称为条件请求，满足一定条件，服务器才会执行请求。



```css
例如：
If-Match
If-Modified-Since
If-None-Match
If-Range
If--Unmodifield-Since
```

10. Range 告知服务器请求资源的范围
11. Referer 告诉服务器请求的原始资源的URI
12. User-Agent 告诉服务器，创建请求的浏览器和用户代理名称等信息

### 响应首部字段

1. Accept-Ranges  用来告诉浏览器，服务器是否可以处理范围请求



```swift
Accept-Ranges: none    // 不能处理
Accept-Ranges:bytes    // 可以处理
```

2. Age  告诉浏览器，服务器在多久之前创建了响应
3. ETag字段   告知客户端的实体标识。



```undefined
每次资源被缓存时，都会被分配一个唯一的标识。
当资源发生变化时，这个ETag也会对应的发生改变。

ETag值也分强弱，强ETag值只要发生一点变化就会改变，弱ETag只在发生较大变化才改变
```

4. Location字段 。用来做重定向的，几乎所有浏览器在接受到包含Location的响应后，都会强制性地尝试对重定向资源进行访问。
5. Proxy-Authenticate  会把代理服务器要求的认证信息发送给客户端
6. Retry-After  指定浏览器，应该在多少秒后，再次发起请求
7. Server字段  告知浏览器，当前服务器上安装的http服务器应用程序的信息
8. Vary字段。 vary字段可以对缓存进行控制



```undefined
当代理服务器接受到带有Vary字段的请求时，如果使用的Accept-Language字段相同，那么就直接从缓存中返回响应，如果不一致，就先从原服务器获取资源后，才能作为响应返回
```

9. WWW-Authenticate 用于HTTP访问认证

### 实体首部字段

在请求报文和响应报文中，都包含着实体相关信息的首部字段

1. Allow字段



```undefined
Allow: GET,HEAD
用于通知客户端，能支持请求的所有HTTP方法
```

2. Content-Encoding字段   通知客户端，服务器对实体选用的内容编码方式



```undefined
Content-Encoding: gzip
告知浏览器，我已经对实体的主体部分进行了gzip压缩，之后的解压就麻烦你了
```

3. Content-Language字段  告知浏览器，实体主体使用的自然语言
4. Content-Length 字段   告知浏览器实体主体部分的大小（单位是字节）
5. Content-MD5字段  客户端会将接受到的主体执行相同的MD5算法，然后与该字段比较
6. Content-Range
7. Content-Type   说明了实体主体内对象的媒体类型



```undefined
类似请求首部字段Accept
```

8. Expires(到期) 将资源失效的日期告知客户端。

9. Last-Modifield字段。  指明资源最终修改的时间

### 为Cookie服务的首部字段

共2个字段，一个在请求头，一个在响应头

1. Set-Cookie 字段 是响应首部字段，有一些属性

> Set-Cookie: name=value; expires=Tue, 05 Jul 2011 07:26:31 GMT; path=/; domain=.hackr.jp;

| 属性         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| NAME=VALUE   | 赋予 Cookie 的名称和其值（必需项）                           |
| expires=DATE | Cookie 的有效期（若不明确指定则默认为浏览器关闭前为止）      |
| path=PATH    | 将服务器上的文件目录作为Cookie的适用对象（若不指定则默认为文档所在的文件目录） |
| domain=域名  | 作为 Cookie 适用对象的域名 （若不指定则默认为创建 Cookie 的服务器的域名） |
| Secure       | 仅在 HTTPS 安全通信时才会发送 Cookie                         |
| HttpOnly     | 加以限制，使 Cookie 不能被 JavaScript 脚本访问               |

```css
1.expires 属性 指定浏览器可发送Cookie的有效期

如果不设置就默认浏览器关闭，Cookie就过期。

2.path属性 指定
3. domain属性
4.secure属性，用于限制web页面只有在HTTPS安全链接时，才可以发送Cookie
5.HttpOnly属性，可以时JS无法操作Cookie，所以可以用来防止跨站脚本攻击（XSS）
```

2. Cookie 字段

## 其他首部字段

HTTP的首部字段是可以自行扩展的，所以在Web服务器和浏览器的应用上，会出现各种非标准的首部字段。以下是比较常见的自己扩展的首部字段

1. X-Frame-Options字段 属于【响应首部字段】



```css
用于控制网站内，在其他网站的Frame标签内的显示问题。其主要目的是为了防止点击劫持攻击
有2个字段值

X-Frame-Options: DENY           拒绝
X-Frame-Options:SAMEORIGIN         仅同源域名下的页面匹配时许可
```

2. X-XSS-Protection 字段也属于【响应首部字段】



```undefined
X-XSS-Protection: 0       关闭XSS过滤设置
X-XSS-Protection: 1       开启XSS的过滤设置
```

3. P3P属于【响应首部字段】

4. DNT字段 属于 【请求首部字段】  Do Not Track 意思是拒绝个人信息被手机



```undefined
设置为0同意被追踪，1拒绝被追踪
由于DNT的功能具备有效性，所以Web服务器需要对DNT做好对应的支持
```

参考：[简书-吕子威-HTTP报文首部](https://www.jianshu.com/p/df5a055c4693)

