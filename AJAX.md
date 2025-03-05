# 一、原生 AJAX

## 1.1 AJAX 简介

AJAX 全称为 Asynchronous JavaScript And XML，就是异步的 JS 和 XML

通过 AJAX 可以在浏览器中向服务器发送异步请求，最大的优势：**无刷新获取数据**

AJAX 不是新的编程语言，而是将一种现有的标准组合在一起使用的新方式

## 1.2 XML 简介

XML 可扩展标记语言

XML 被设计用来传输和存储数据

XML 和 HYML 类似，不同的是 HTML 中都是预定义标签，而 XML 中没有预定义标签，全都是自定义标签，用来表示一些数据

## 1.3 AJAX 的特点

### 1.3.1 AJAX的有点

1. 可以无需刷新页面而与服务器端进行通信
2. 允许你根据用户事件来更新部分页面内容

### 1.3.2 AJAX的缺点

1. 没有浏览历史，不能回退
2. 存在跨域问题（同源）
3. SEO 不友好





## 1.4 AJAX 请求的基本操作

> 设置响应头 设置允许跨域
>`res.setHeader('Access-Control-Allow-Origin','*');`

GET

```html
// .html
// 需求：点击按钮后向服务端发请求，服务端放回响应数据在显示在div中（页面不刷新）
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        #box{
            width: 200px;
            height: 100px;
            border: 1px solid purple;
        }
    </style>
</head>
<body>
    <button>click</button>
    <div id="box"></div>

    <script>
        // 注意 [0]
        const btn = document.getElementsByTagName('button')[0];
        btn.onclick = function(){
            // 1. 创建对象
            const xhr = new XMLHttpRequest();
            // 2. 初始化 设置请求方法和url
            xhr.open('GET','http://127.0.0.1:8000/server');
            // 3. 发送
            xhr.send();
            //  4. 事件绑定 处理服务端返回的结果
            // readystate 是 xhr 对象中的属性，表示状态0:初始 1:open 2:send 3:数据发送中 4：数据发送完毕
            xhr.onreadystatechange = function(){
                // 判断（服务端返回了所有结果）
                if(xhr.readyState === 4){
                    // 判断响应状态码 200 404 404 500
                    // 2xx 都是正确响应
                    if(xhr.status >= 200 && xhr.status < 300){
                    // if(xhr.status === 200){
                        // 处理结果 行 头 空行 体
                        // 1. 响应行
                        // console.log(xhr.status); // 状态码
                        // console.log(xhr.statusText); // 状态字符串
                        // console.log(xhr.getAllResponseHeaders()); // 所有响应头
                        // console.log(xhr.response); // 响应体
                        document.querySelector('#box').innerHTML = xhr.response;
                    }
                }
            }
        }
    </script>
</body>
</html>
```

```js
// server.js
const express = require('express');

const app = express();

app.get('/server', (req, res) => {
    // 设置响应头 设置允许跨域
    res.setHeader('Access-Control-Allow-Origin','*');
    res.send('HELLO EXPRESS');
})

app.listen(8000, () => {
    console.log('server is running...');
})
```

POST

```html
<!--.html  需求：当鼠标移动到 div 框内时，发情请求并返回响应体 -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>post</title>
    <style>
        #box{
            width: 200px;
            height: 100px;
            border: 1px solid purple;
        }
    </style>
</head>
<body>
    <div id="box"></div>
    <script>
        // 豆包ai插件的存在导致出现了两个div框
        const div = document.querySelector('#box');
        div.addEventListener('mouseover', function(){
            const xhr = new XMLHttpRequest();
            xhr.open('POST','http://127.0.0.1:8000/server');
            xhr.send();
            xhr.onreadystatechange = function(){
                if(xhr.readyState === 4){
                if(xhr.status >= 200 && xhr.status < 300){
                    div.innerHTML = xhr.response;
                }
            }   
            }
                     
        })
    </script>
</body>
</html>
```



```js
// server.js
app.post('/server', (req, res) => {
    res.setHeader('Access-Control-Allow-Origin','*');
    res.send('HELLO AJAX POST');
})
```

## 1.5 AJAX 设置请求体信息

```js
// 发送
// AJAX 设置请求体信息
xhr.send('a=100&b=200&c=300'); // a=100&b=200&c=300
xhr.send('a:100&b:200&c:300'); // a:100&b:200&c:300
xhr.send('123abc'); // 123abc
```

## 1.6 AJAX 设置请求头信息

```js
// 设置请求头 要在send前面
xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
// 自定义请求头
xhr.setRequestHeader('hello','chen');
```

自定义请求头会报错，要添加

> res.setHeader('Access-Control-Allow-Headers', '*');
>
> 并将 post 改成 all

```js
app.all('/server', (req, res) => {
    res.setHeader('Access-Control-Allow-Origin','*');
    res.setHeader('Access-Control-Allow-Headers', '*');
    res.send('HELLO AJAX POST');
})
```

## 1.7 服务端响应 JSON 数据

``` js
<script>
        const div = document.querySelector('div');
        window.onkeydown = function(){
            const xhr = new XMLHttpRequest();
            xhr.open('GET','http://127.0.0.1:8000/json-server');
            xhr.send();
            // 1. 设置响应体数据的类型
            xhr.responseType = 'json';
            
            xhr.onreadystatechange = function(){
                if(xhr.readyState === 4){
                    if(xhr.status >= 200 && xhr.status < 300){
                        console.log(xhr.response);
                        // 2. 手动对数据进行转化
                        // const data = JSON.parse(xhr.response);
                        // div.innerHTML = data.name;

                        div.innerHTML = xhr.response.name;
                    }
                }
            }
        }
    </script>
```

server.js

```js
app.get('/json-server', (req, res) => {
    res.setHeader('Access-Control-Allow-Origin','*');
    res.setHeader('Access-Control-Allow-Headers', '*');
    const data = {
        name:'chen'
    };
    const str = JSON.stringify(data);
    res.send(str);
})
```

## 1.8 IE 缓存问题解决

后面加上时间

```js
 xhr.open('GET','http://127.0.0.1:8000/ie?t='+Date.now());
```

## 1.9 AJAX 请求超时与网络异常

```js
// 超时设置 2s
xhr.timeout = 2000;
// 超时回调
xhr.ontimeout = function(){
    alert('网络异常，请稍后重试!');
}
// 网络异常回调
xhr.onerror = function(){
    alert('您的网络似乎出了一些问题');
}
```

```js
app.get('/delay', (req, res) => {
    // 设置响应头 设置允许跨域
    res.setHeader('Access-Control-Allow-Origin','*');
    setTimeout(()=>{
        res.send('HELLO delay');
    },3000);
})
```

### 1.10 AJAX 取消请求

```js
<body>
    <button>send</button>
    <button>cancel</button>
    <script>
        const btn = document.getElementsByTagName('button');
        let xhr = null;
        btn[0].onclick = function(){
            xhr = new XMLHttpRequest();
            xhr.open('GET','http://127.0.0.1:8000/delay');
            xhr.send();
        }
        btn[1].onclick = function(){
            xhr.abort();
        }
    </script>
</body>
```

## 1.11 AJAX 请求重复发送问题

设置标识变量

```js
const btn = document.getElementsByTagName('button')[0];
let isSending  = false; // 是否正在发送AJAX请求
let xhr = null;
btn.onclick = function(){
    // 判断表示变量
    // 如果正在发送，则取消该请求，创建一个新的请求
    if(isSending) xhr.abort(); 
    xhr = new XMLHttpRequest();
    // 修改表示变量
    isSending = true;
    xhr.open('GET','http://127.0.0.1:8000/delay');
    xhr.send();
    xhr.onreadystatechange = function(){
        if(xhr.readyState === 4){
            isSending  = false;
        }
    }
}
```

# 二、jQuery 中的 AJAX

## 2.1 get 请求

$.get.(url, [ data ], [ callback ], [ type ])

* url : 请求的 URL 地址
* data：请求携带的参数
* callback：载入成功时回调函数
* type：设置返回内容格式，xml，html，script，json，text，_default

## 2.2 post 请求 

$.post(url, [ data ], [ callback ], [ type ])

* url : 请求的 URL 地址
* data：请求携带的参数
* callback：载入成功时回调函数

## 3.3 jQuery 调用 AJAX

> 格式： **$.ajax({})**
>
> 参数：
>
> * type：GET/POST
> * url：请求地址url
> * asyn：是否异步，默认是 true 表示异步
> * data：发送到服务器的数据
> * dataType：预期服务器返回的数据类型
> * contentType：设置请求头
> * success：请求成功时调用此函数
> * error：请求失败时调用此函数

```js
<body>
    <button>GET</button>
    <button>POST</button>
    <button>AJAX</button>
    <script>
        $('button').eq(0).click(function(){
            $.get('http://127.0.0.1:8000/jquery',{a:100, b:200},function(data){
                console.log(data);
            }, 'json');
        })

        $('button').eq(1).click(function(){
            $.post('http://127.0.0.1:8000/jquery',{a:100, b:200},function(data){
                console.log(data);
            });
        })

        $('button').eq(2).click(function(){
            $.ajax({
                // url
                url:'http://127.0.0.1:8000/jquery',
                // 参数
                data:{a:100, b:200},
                // 请求类型
                type: 'GET',
                // 成功的回调
                success: function(data){ console.log(data); },
                // 超时时间
                timeout:2000,
                // 失败的回调
                err:function(){ console.log('出错啦！！')},
                // 响应体结果
                dataType: 'json',
                // 头信息
                headers:{ c: 300, d: 400}
            })
        })
    </script>
</body>
```

server.js

```js
app.all('/jquery', (req, res) => {
    // 设置响应头 设置允许跨域
    res.setHeader('Access-Control-Allow-Origin','*');
    res.setHeader('Access-Control-Allow-Headers', '*');
    const data = { name: 'chen'};
    setTimeout(() => {
        res.send(JSON.stringify(data));
    }, 2000);
    
})
```

## 2.4 getJSON

要求返回的格式必须是json，不是json格式则无法获取

# 三、Axios发送AJAX请求

https://github.com/axios/axios

```js
<body>
    <button>GET</button>
    <button>POST</button>
    <button>Axios</button>
    <script>
        const btns = document.getElementsByTagName('button');
        // 配置 baseURL
        axios.defaults.baseURL = 'http://127.0.0.1:8000';
        btns[0].onclick = function(){
            // GET请求
            axios.get('/axios-server',{
                // url 参数
                params:{
                    id: 100,
                    vip: 33
                },
                // 请求头信息
                headers:{
                    name:'chen',
                    age:20
                }
            }).then(value => {
                console.log(value);
            });
        }

        btns[1].onclick = function(){
            // GET请求
            axios.post('/axios-server',{
                // 请求体
                data:{
                    username:'admin',
                    password:'admin'
                }},{
                    // url 参数
                    params:{
                        id: 100,
                        vip: 33
                    },
                    // 请求头信息
                    headers:{
                        name:'chen',
                        age:20
                    }
                    }).then(value => {
                        console.log(value);
            });
        }

        btns[2].onclick = function(){
            axios({
                // 请求方法：
                method: 'POST',
                // URL
                url:'/axios-server',
                // url 参数
                params:{
                    a:100,
                    n:200
                },
                // 头信息
                headers:{
                    name:'chen',
                    age: 100
                },
                // 请求体参数
                data:{
                    username:'admin',
                    password: 'admin'
                }
            }).then(value => {
                console.log(value);
            })
        }
    </script>
</body>
```

使用fetch函数发送AJAX请求

```js
fetch('http://127.0.0.1:8000/fetch-server',{
    // 请求方法
    method:'POST',
    // 请求头
    headers:{
        name:'chen'
    },
    // 请求体
    body:'username=admin$password=123456'
}).then(response => {
    return response.text(); //{"name":"chen"}
    // return response.json();  // {name: 'chen'}
})
.then(response => {
    console.log(response);
})
```

