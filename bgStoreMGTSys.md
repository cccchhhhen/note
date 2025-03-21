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



