# document.cookie
4k
> **HTTP Cookie**（也叫 **Web Cookie** 或**浏览器 Cookie**）是服务器发送到用户浏览器并保存在本地的一小块数据。浏览器会存储 cookie 并在下次向同一服务器再发起请求时携带并发送到服务器上。通常，它用于告知服务端两个请求是否来自同一浏览器——如保持用户的登录状态。Cookie 使基于**无状态的 HTTP 协议**记录稳定的状态信息成为了可能。
### Cookie应用
**cookie主要用于以下三个方面：**

* **会话状态管理**  
如用户登录状态、购物车、游戏分数或其他需要记录的信息

* **个性化设置**  
如用户自定义设置、主题和其他设置

* **浏览器行为跟踪**  
如跟踪分析用户行为等

### 创建 Cookie `set-cookie`

> 服务器收到 HTTP 请求后，服务器可以在响应标头里面添加一个或多个 Set-Cookie 选项。浏览器收到响应后通常会保存下 Cookie，并将其放在 HTTP Cookie 标头内，向同一服务器发出请求时一起发送

```
Set-Cookie: <cookie-name>=<cookie-value>
```

```js
docCookies.setItem(name, value[, end[, path[, domain[, secure]]]])
```

> 每条 cookie 以"分号和空格 (; )"分隔 (即， key=value 键值对);用这个方法一次只能对一个 cookie 进行设置或更新

```js
let key = 'yourname';
let value = encodeURIComponent(username.value); // encodeURLComponent:保证它不包含任何逗号、分号或空格
let twoDays = 2*24*60*60;
document.cookie = `${key}=${value}; max-age=${twoDays}`;
```



* 以下可选的 cookie 属性值可以跟在键值对后，用来具体化对 cookie 的设定/更新，使用分号以作分隔：
    - `;path=path` (例如 '/', '/mydir') 如果没有定义，默认为当前文档位置的路径。
    - `;domain=domain` (例如 'example.com'， 'subdomain.example.com') 如果没有定义，默认为当前文档位置的路径的域名部分。与早期规范相反的是，在域名前面加 . 符将会被忽视，因为浏览器也许会拒绝设置这样的 cookie。如果指定了一个域，那么子域也包含在内。
    - `;max-age=max-age-in-seconds` (例如一年为 60\*60\*24\*365)
    - `;expires=date-in-GMTString-format` 如果没有定义，cookie 会在对话结束时过期
    - `;secure` (cookie 只通过 https 协议传输)
* cookie 的值字符串可以用**encodeURIComponent()**来保证它不包含任何逗号、分号或空格 (cookie 值中禁止使用这些值).


### 获得cookie
```js
docCookies.getItem(name)
```

### 移除 cookie
```js
docCookies.removeItem(name[, path,domain])
```

### 检测 cookie
```js
docCookies.hasItem(name) //检查一个 cookie 是否存在
```
### 得到所有 cookie 的列表
```js
docCookies.keys()
```

### 定义 Cookie 的生命周期  
Cookie 的生命周期可以通过两种方式定义：

* **会话期 Cookie** 会在当前的会话结束之后删除。浏览器定义了“当前会话”结束的时间，一些浏览器重启时会使用会话恢复。这可能导致会话 cookie 无限延长。
* **持久性 Cookie** 在过期时间（**Expires**）指定的日期或有效期（**Max-Age**）指定的一段时间后被删除。

# Web Storage API

* sessionStorage 为每一个给定的源（given origin）维持一个独立的存储区域，该存储区域在页面会话期间可用（即只要浏览器处于打开状态，包括页面重新加载和恢复）。
* localStorage 同样的功能，但是在浏览器关闭，然后重新打开后数据仍然存在。

## Window.localStorage

5M

>localStorage 中的键值对总是以字符串的形式存储。 (需要注意，和 js 对象相比，键值对总是以**字符串**的形式存储意味着数值类型会自动转化为字符串类型)

**注：** 如果值要存储object类型，要通过`JSON.stringfy`转换成string类型；使用时通过`JSON.parse`转化成object类型

### 增
```js
localStorage.setItem("key", "value");
localStorage.key="value";
localStorage["key"]="value";
```

### 删
```js
localStorage.removeItem("key");
delete localStorage.key;
// 移除所有
localStorage.clear();
```

### 查
```js
localStorage.getItem("key")
localStorage["key"]
localStorage.key
```



## Window.sessionStorage
>sessionStorage 属性允许你访问一个，对应当前源的 session Storage 对象。它与 localStorage 相似，不同之处在于 localStorage 里面存储的数据没有过期时间设置，而存储在 sessionStorage 里面的数据在页面会话结束时会被清除。

* 页面会话在浏览器打开期间一直保持，并且重新加载或恢复页面仍会保持原来的页面会话。
* 在新标签或窗口打开一个页面时会复制顶级浏览会话的上下文作为新会话的上下文，这点和 session cookie 的运行方式不同。
* 打开多个相同的 URL 的 Tabs 页面，会创建各自的 sessionStorage。
* 关闭对应浏览器标签或窗口，会清除对应的 sessionStorage。



## 同源窗口

### 什么是同源窗口？
>同源窗口是指具有相同 协议（Protocol）、域名（Domain） 和 端口（Port） 的窗口或标签页。同源策略（Same-Origin Policy）是浏览器的一种安全机制，用于限制不同源的窗口或页面之间的交互

### 为什么不同浏览器不算同源窗口？
1. **浏览器隔离：**  
    * 不同浏览器（如 Chrome、Firefox、Edge）是完全独立的应用程序，它们之间的数据（如localStorage、sessionStorage、cookies 等）是隔离的。
即使在同一台电脑上，不同浏览器之间无法共享数据或直接通信。
2. **同源策略的限制：**  
    * 同源策略仅适用于同一浏览器内的不同窗口或标签页。即使两个窗口打开的是同一个网站，如果它们位于不同的浏览器中，它们也不被视为同源窗口。
3. **数据隔离：**  
    * 不同浏览器的存储机制是独立的。例如：
        - Chrome 的 localStorage 和 Firefox 的 localStorage 是完全隔离的。
        - Chrome 的 cookies 和 Edge 的 cookies 也是独立的。
        
**总结**
* **同一电脑的不同浏览器不算同源窗口**，因为它们是独立的应用程序，数据完全隔离。
* **同源窗口** 是指 同一浏览器 中具有相同协议、域名和端口的窗口或标签页。

如果需要在不同浏览器之间共享数据，通常需要通过服务器（如后端 API）或外部存储（如数据库）来实现