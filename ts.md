https://typescript.bootcss.com/interfaces.html

1.`tsc --init` 初始化 `tsconfig.json` 文件

2.`node` 编译 `js` 文件，`ts-node` 编译 `ts` 文件（`npm i -g ts-node`） 

3.只读属性 `readonly`

```ts
interface Point {
    readonly x: number;
    readonly y: number;
}
```

4.索引签名 **（Index Signatures）**

[propName: string]: any;  用于描述对象中动态键的类型

```ts
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
```

`[propName: string]` => `key `

`any` => `value`为**任意类型**

用 `string` 类型索引得到 `any`类型

5.函数签名**（Function Signature）**

` (start: number): string;`  用于描述函数的参数和返回类型