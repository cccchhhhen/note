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