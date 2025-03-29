### 一、包

`npm install less vue-router element-plus -s`

`npm install less @element-plus/icon-vue -s`

[element-plus](https://element-plus.org/zh-CN/component/overview.html)

#### 1.element-plus

```ts
// 完整引入element-plus main.ts
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
```

##### 1.1 container

* `<el-container>`：外层容器。 当子元素中包含 `<el-header>` 或 `<el-footer>` 时，全部子元素会垂直上下排列， 否则会水平左右排列。
* `<el-header>`：顶栏容器。
* `<el-aside>`：侧边栏容器。
* `<el-main>`：主要区域容器。
* `<el-footer>`：底栏容器。

> 背景颜色
>
> ```less
> --el-menu-bg-color: #545c64;
> // 不使用 background-color 二级菜单会没有渲染到
> ```
>
> 文本颜色
>
> ```less
> --el-menu-text-color:#fff;
> ```
>
> 

#### 2.element-plus/icon

```ts
// 引入element-plus/icon  main.ts
import * as ElementPlusIconsVue from '@element-plus/icons-vue'

for (const [key, component] of Object.entries(ElementPlusIconsVue)) {
    app.component(key, component)
  }
```

#### 3.axios

##### 3.1 官网

[axios](https://www.axios-http.cn/docs/intro)

##### 3.2 使用

```ts
axios({
        url: '/api/home/getTableData',
        method: 'get'
    }).then(res => {
        console.log(res)
    })
```

##### 3.3 axios二次封装

```ts
// src/api/request.ts
import axios from "axios";
import { ElMessage } from "element-plus";

const service = axios.create();
// const NETWORK_ERROR = '网络错误...';

// 添加请求拦截器
service.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
service.interceptors.response.use(function (response) {
    // console.log(response);
    const {code, data, msg} = response.data;
    if( code === 200) {
        return data;
    }else{
        const NETWORK_ERROR = '网络错误...';
        ElMessage.error(msg || NETWORK_ERROR);
        return Promise.reject(msg || NETWORK_ERROR);
    }
  });

function request(options:any ){
    options.method = options.method || 'get';
    return service(options);
}

export default request;
```

```ts
// src/api/api.ts
/**
 * api 统一管理
 */
import request  from "./request";

// 请求首页左侧表格数据
export default {
    getTableData(){
        return request ({
            url:'/api/home/getTableData',
            method: 'get'
        });
    }
}
```

```ts
// main.ts
// 引入api
import api from "@/api/api";

// 挂载全局api -> 不推荐
app.config.globalProperties.$api = api;

// 全局挂载
// app.provide('$api', api)
```

```ts
// src/compponents/Home.vue
import { ref, onMounted, getCurrentInstance } from 'vue';
// 组件中使用
// import { inject } from 'vue'
// const $api = inject('$api')
const { proxy } = getCurrentInstance();

const getTableData = async () => {
    const data = await proxy.$api.getTableData();
    // const data = await $api.getTableData();
    tableData.value = data.tableData;
}
onMounted( () => {
    getTableData();
})
```







#### 4.mockjs

##### 4.1 官网

#####  [mockjs](http://mockjs.com/)

##### 4.2 下载

##### `npm i --save-dev @types/mockjs` 

路径：`/src/api/mock.ts`

通过`npm i mockjs -D`创建`mock.ts`会报错，只能`mock.js`

##### 4.3 使用

```ts
// src/api/mock.ts
import Mock from 'mockjs';
import homeApi from '@/mockData/home'
// 1 url( URL 字符串或 URL 正则) 2 method 3 data
Mock.mock(/api\/home\/getTableData/, 'get', homeApi.getTableData);
```

##### 4.4 main.ts引入

```ts
// 引入 mockjs
import "@/api/mock.ts"
```

#### 5.Echarts

##### 5.1官网

[echarts](https://echarts.apache.org/zh/option.html#xAxis)

`npm i echarts -D`

