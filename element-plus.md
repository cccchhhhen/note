vue3 UI iframe

## 一、`el-form`

1.在每一个 `form` 组件中，需要一个 `form-item` 字段作为输入项的容器，用于获取值与验证值

2.表单验证

```ts
import type { FormInstance, FormRules } from 'element-plus';
const loginFormRef = ref<FormInstance>()
const rules = reactive<FormRules>({
    username: [
        {required: true, message: 'please input the username', trigger: 'blur'}
    ]
})
<el-form :model="loginForm" :rules="rules" ref="loginFormRef">
<el-form-item prop="username">
```

