一、route属性

| 属性             | 作用                                                      |
| ---------------- | --------------------------------------------------------- |
| `route.fullpath` | 获取当前路由的全部路径；无论是hash模式还是history模式     |
| `route.hash`     | 相当于锚点                                                |
| `route.matched`  |                                                           |
| `route.meta`     | 用来获取在路由中自定义的meta属性                          |
| `route.name`     |                                                           |
| ` route.params`  |                                                           |
| `route.query`    | 用来做搜索查询使用的参数                                  |
| `route.path`     | 获取路由的地址但是不包括query后面的地址和hash产生的地址值 |

```js
const router = createRouter({
    history: createWebHistory(),
    routes:[
        {
            path: '/:articleName',
            name: 'article',
            component: Article,
            meta: {name:'chen'},
            children:..
        }
    ]
})
// http://localhost:5173/article?a=100#hash
fullPath: "/article?a=100#hash"
hash: "#hash"
href: "/article?a=100#hash"
meta: {name: 'chen'}
name: "article"
params: {articleName: 'article'
path: "/article"
query: {a: '100'}
```



一、.router属性和方法

[链接](https://router.vuejs.org/zh/api/interfaces/Router.html)

1、属性

1.1 `router.currentRoute`

一个被ref包装的route对象，`router.currentRoute.value`  ===> `route`

