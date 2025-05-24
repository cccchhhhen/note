示例

Integration testing basics

Register Post-response

```js
pm.test("An access token is returned", () => {
    pm.expect(pm.response.json()).to.have.property('token') 
    pm.expect(pm.response.json().token).to.be.a('string')
    // Set the collection-scope "token" variable to the token received from the API
    // This lets us use it in other requests
    pm.collectionVariables.set('token', pm.response.json().token) // 将 token 保存为集合变量
})

```

>`pm.test(name, ()=>{})`
>
>* `name` ：测试名称
>* `()=>{}`：测试逻辑
>
> `pm.response.json()`  将 API 响应内容解析为 JSON 对象
>
>`pm.expect(...).to.have.property('token')`  断言，验证响应 JSON 中 必须存在 `token` 属性
>
>`.to.be.a('string')`  断言 `token` 的类型必须为字符串
>
>`pm.collectionVariables.set(key, value)`  Postman 提供的方法，用于操作 **集合级变量**（Collection Variables）
>
>### **测试流总结**
>
>1. 发送请求 → 获取 API 响应
>2. 解析响应 JSON → 检查是否存在 `token`
>3. 验证 `token` 类型 → 确保格式正确
>4. 存储 `token` → 便于后续请求认证

