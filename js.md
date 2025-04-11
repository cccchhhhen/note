### 单项绑定onchange
```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset=utf-8>
    </head>
    <body>
    	<input id="input" type="text" />
        <span id="span"></span>

        <script type="text/javascript">
            // 补全代码
            var input = document.getElementById('input');
            var span = document.getElementById('span');
            input.onchange=function(e){
                span.innerHTML = input.value;
            }
        </script>
    </body>
</html>
```

数组

1. 创建长度为n的数组

   * **方法 1: 使用 Array 构造函数**

     `const arr = new Array(n); `  数组中的每个元素都是 undefined

   * **方法 2: 使用 Array.from**

     `const arr = Array.from({ length: n}); 数组中的每个元素都是 undefined`

   * **方法 3: 使用 Array 构造函数和 fill**

     `const arr = new Array(100).fill(0);`  每个元素都被初始化为 0

   * **方法 4: 使用 Array.from 和映射函数**

   * **方法 5: 使用 for 循环 + push**

     

     