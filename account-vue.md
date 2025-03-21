## 一、向后端提供数据

> 表单提交 
>
> * `@submit.prevent`="..."
> * `async () => {  }`
> * `await axios.post( url ,data);`

```vue
<template>
     <form class="loginForm" @submit.prevent="handleLoginSubmit">
        ...
     </form>
</template>
```

```js
const handleLoginSubmit = async () => {
        try{
            const response = await axios.post('http://127.0.0.1:9000/login',formData.value);
        }catch(err){
        }
    }
```







## 二、Login -> AccountList

> 要求：登录页面跳转其他页面

```vue
<!-- 核心原因：未使用 <router-view> 路由出口 App.vue-->
<template>
  <!-- ❌错误写法：写死组件 -->
  <Login></Login><!-- 无论路由如何变化，永远显示 Login -->
</template>
```



```vue

<template>
  <!-- 路由出口 -->
   <router-view></router-view>
</template>
```



## 三、AccountList获取数据

> 1.要求：通过axios 请求后端的数据

```js
let accountData = reactive({})
accountData = await axios.get(...) // ❌ 错误！
```

> **错误：**直接覆盖 reactive 对象
>
> * reactive() 返回的是一个 Proxy 代理对象
> * 使用 = 直接赋值会 破坏响应式引用，导致后续数据变更无法触发更新

```js
<!-- 正确做法 -->
let accountList = reactive({
    data: [],
    loading: false,
    err: null
});
const getAccountList = async () => {
    try{
        accountList.loading = true;
        const response = await axios.get('http://127.0.0.1:9000/account-list');
        console.log(response);
        // 解构后端数据，直接提取嵌套data
        accountList.data = response.data.data;  // 跳过冗余层级 不能通过 accountList.data.data获取
        accountList.err = null;
    }catch(err){
        // accountList.err = err.message;
    }finally{
        accountList.loading = false;
    }
}
```

| 问题场景     | 错误写法              | 正确写法                        |
| ------------ | --------------------- | ------------------------------- |
| 初始化结构   | reactive({})          | reactive({ data: { data: [] }}) |
| 访问嵌套属性 | accountData.data.data | response.data.data              |

> 问题：渲染是没有定义的属性会爆红线
>
> 快速开发解决方法: **any**

```html
<div v-for="(item, index) in accountList.data" :key="index">
    <div>{{ (item as any).account }}</div>
</div>
```

## 四、vue拦截器 -- token鉴权

> **`axios` 封装**

```ts
const service = axios.create({
    baseURL: 'https://XXX.XXX.com', // 你的API基础URL
    timeout: 5000, // 请求超时时间
    // 其他axios配置...
    withCredentials: true // 跨域请求时是否需要使用凭证
})
```

> **请求拦截器**

```ts
// 请求拦截器
service.interceptors.request.use(config => {
  // 在发送请求前做什么
  return config
}, error => {
    // 对请求错误做些什么
    return Promise.reject(error);
})
```

> **响应拦截器**

```ts
// 响应拦截器
service.interceptors.response.use(
    // 对响应数据做什么
  response => response.data, 
  error => {
    // 对响应错误做什么
    return Promise.reject(error)
  }
)
```



```ts
// src/utils/request.ts
import axios from 'axios';
import { getToken } from '../utils/auth';
const service = axios.create({
    baseURL:'http://127.0.0.1:9000'
})
// 请求拦截器
service.interceptors.request.use(config => {
  // 请求头携带 token 
  const token = getToken(); // 12. 获取存储的 token
  if (token) {
    config.headers['token'] = token; // 13. 自动携带 token
  }
  return config
})

// 响应拦截器
service.interceptors.response.use(
  response => response.data, // 14. 直接返回 data 内容
  error => {
    if (error.response?.status === 401) { // 15. 处理 401 未授权
      localStorage.removeItem('token')
      window.location.href = '/login'
    }
    return Promise.reject(error)
  }
)
export default service;
// 在导入的时候，可以任意命名，比如import axios from './utils/axios'
```

## 五、接口封装

```ts
// src/api/index.js

// define interface function
import axios from "@/utils/request";
// login interface
export const userLogin = (data:any) => axios.post('login',data);
// reg interface
export const userReg = (data:any) => axios.post('reg', data);
// get account list interface
export const getAccountInter = () => axios.get('account-list');
// create account interface
export const createAccount = (data:any) => axios.post('create-account', data);
```

## 六、接口调用

```ts
// src/components/Login.vue
import { userLogin } from '../api';
...
const response = await userLogin(formData.value);
```

## 七、路由守卫



## 五、问题

### 5.1 crypto-js

> 问题：通过 `npm i crypto-js` 导入`import CryptoJS from 'crypto-js';` 后`crypto-js`爆红
>
> 解决：`npm i --save-dev @types/crypto-js`

### 5.2  .d.ts

> 在 `TypeScript` 中，文件名中的 `.d.ts` 后缀具有特殊含义，这里的 `.d` 代表 **Declaration**（声明）。这种命名约定是 `TypeScript` 官方设定的标准，用于区分普通代码文件和类型声明文件。

### 5.3 AccountList接口

AccountList一进行接口封装就错误

```js
// get account list interface 不能使用？？？
export const getAccountList = (params) => axios.get('account-list',{params});

// const response = await getAccountList();
```



## 六、知识点

1. api单独放在api文件中
2. utils工具
3. 预请求options 服务器处理

### 6.1 vue 拦截器

```ts

```

```js
// src/api/index.js

// define interface function
import axios from "@/utils/request";

// login interface
export const userLogin = data => axios.post('login',data);
```

```js
//src/components/Login.vue
import {userLogin} from '@/api';
...
const handleLoginSubmit = async () => {
    try{
       ...
        const response = await userLogin(formData.value);
       ...
    }catch(err){
        console.log('登录出错啦~~',err);
    }
}
```

```js
// src/utils/auth.js
// 处理 token
export const setToken = token => sessionStorage.setItem('chen_token',token);

export const getToken = token => sessionStorage.getItem('chen_token');

export const removeToken = token => sessionStorage.removeItemItem('chen_token');
```

```ts
// 路由拦截
router.beforeEach(async(to, from, next) => {
    if(to.path === '/login'){
        next();
    }else{
        if(mainStore() && mainStore().nav.length === 0){
            // 发送请求
            const res = await getResource();
            // 数据缓存
            mainStore().setNav(res.data.res);
            // 动态处理路由
            let addDynamicRoutes = dynamicRoutes(res.data.res);
            // 动态路由配置addRoute()
            router.addRoute(addDynamicRoutes);
            // 刷新后页面不空白
            next({...to});
            // 或者
            next({path: to.path});
        }else{
            next();
        }
    }
})

// 根据数据动态生成路由
let HomeRoute = routes.filter(v=>v.path==='/home')[0];
HomeRoute.children = [];
data.forEach(item=>{
    HomeRoute.children.push(
    {
        path: item.path,
        name: item.name,
        component: () => import(`../views/home/content/...`)
    })
})
```

