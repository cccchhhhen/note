## 一、简单请求 vs 非简单请求

### 1、简单请求（如登录接口）

满足以下所有条件时，浏览器不会发送预检请求：

* 请求方法为` GET`、`POST`、`HEAD`

* 请求头仅包含以下字段：
  `Accept`
  `Accept-Language`
  `Content-Language`
  `Content-Type`（且值为 `text/plain`、`multipart/form-data`、`application/x-www-form-urlencoded`）
  未使用自定义头（如 `token`）

* 登录接口示例：

  ```js
  fetch('/login', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/x-www-form-urlencoded' // 符合简单请求
    },
    body: 'username=admin&password=123456'
  });
  ```


  此时浏览器直接发送POST请求，后端只需设置：

  ```js
  res.setHeader('Access-Control-Allow-Origin', '*');
  ```

### 2、非简单请求（如account-list接口）

当请求包含以下特征时，浏览器会先发送OPTIONS预检请求：

* 使用 `PUT`、`DELETE` 等方法
* 设置自定义头（如 `token`）
* `Content-Type` 为 `application/json` 等非简单值
  `account-list`接口示例：

```js
// 前端请求（携带自定义头）
xhr.setRequestHeader('token', 'your_token'); // 触发 options 预检
```


此时浏览器会先发送`OPTIONS`请求，后端必须响应：

```js
HTTP/1.1 204 No Content
Access-Control-Allow-Origin: http://127.0.0.1:5501
Access-Control-Allow-Methods: GET
Access-Control-Allow-Headers: token
Access-Control-Allow-Credentials: true
```

若未正确处理`OPTIONS`请求或头信息错误，实际请求会被浏览器拦截。

### 3、登录接口为何能工作？

#### 3.1 未触发预检请求

假设你的登录请求代码为：

```js
// 前端登录请求（未设置自定义头）
xhr.open('POST', '/login');
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
xhr.send('username=admin&password=123456');
```


此时：

* 方法为 `POST`
* `Content-Type` 为 `application/x-www-form-urlencoded`（简单值）
* 未设置自定义头

浏览器判定为简单请求，直接发送POST请求，后端只需设置：

```js
res.setHeader('Access-Control-Allow-Origin', '*');
```



#### 3.2参数是自定义的，但头是标准的

虽然请求体携带了自定义参数（如 `username`、`password`），但这些参数属于请求体内容，不影响`CORS`规则。`CORS`仅关注**请求方法和头信息**。

### 4.为什么account-list接口失败？

4. #### 1自定义头触发预检

   代码中设置了自定义头：

```js
xhr.setRequestHeader('token', `${sessionStorage.getItem('chen_token')}`);
```


这会触发浏览器发送`OPTIONS`预检请求。

#### 4.2后端未正确处理预检


未提供`OPTIONS`路由

#### 4.3解决方案

* 统一使用`CORS`中间件
  强烈建议使用标准库处理`CORS`，避免手动设置头的错误：

* 安装`cors`包
  `npm install cors`

* ```js
  // 后端配置 位置 app.js
  const express = require('express');
  const cors = require('cors');
  const app = express();
  
  app.use(cors({
    origin: 'http://127.0.0.1:5501', // 指定允许的域名
    methods: ['GET', 'POST', 'OPTIONS'],
    allowedHeaders: ['Content-Type', 'token'], // 明确列出允许的头
    credentials: true // 若需要携带Cookie
  }));
  
  // 显式处理OPTIONS请求
  app.options('*', cors());
  
  // 前端请求（示例）
  const xhr = new XMLHttpRequest();
  xhr.open('GET', 'http://127.0.0.1:3000/account-list');
  xhr.setRequestHeader('token', 'your_token'); // 确保后端允许此头
  xhr.send();
  
  // 后端获取前端的token 
  let token = req.get('token');
  ```



## 二、token传递

在中间件中，req.userId = data._id;

