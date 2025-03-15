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

