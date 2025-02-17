### HTML form
#### HTML 表单和输入
- \<form\> 元素用于创建表单，action 属性定义了表单数据提交的目标 URL，method 属性定义了提交数据的 HTTP 方法
    ```html
    <form action="url" method="post"></form>
    ```
- \<label\> 元素用于为表单元素添加标签，提高可访问性。  
    ```html
    <label for=''>
    ```
- \<input\> 元素是最常用的表单元素之一，它可以创建文本输入框、密码框、单选按钮、复选框等。  
    * type 属性定义了输入框的类型
    * id 属性用于关联 <label> 元素
    * name 属性用于标识表单字段。
    ```html
    <!--文本输入框-->
    <label for="name">username:</label>
    <input type="text" id="name" name="name" required>
    <!-- 密码输入框 -->
    <label for="password">密码:</label>
    <input type="password" id="password" name="password" required>
    <!--单选按钮-->
    <input type="radio" name="sex" value="male">男
    <input type="radio" name="sex" value="female">女
    <!--复选框-->
    <input type="checkbox" checked>
    <!--提交按钮(Submit)-->
    <input type="submit">
    <!--将表单内的相关元素分组，在相关表单元素周围绘制边框-->
    <!--disabled 禁用表单元素-->
    <fieldset disabled>
        <!--<legend> 标签为 <fieldset> 元素定义标题-->
        <legend>Personal information:</legend>
    </fieldset>
    ```
- \<select\> 元素用于创建下拉列表，而 <option> 元素用于定义下拉列表中的选项

### HTML table
- HTML 表格由 <table> 标签来定义。
- HTML 表格是一种用于展示结构化数据的标记语言元素。
- 每个表格均有若干行（由 \<tr>[table row] 标签定义），每行被分割为若干单元格（由 \<td>[ table data] 标签定义），表格可以包含标题行（\<th\>[table header]）用于定义列的标题  
- 跨行`colspan="2"`，跨列`rowspan="2"`

```html
<!--thead, tbody, 和 tfoot 元素默认不会影响表格的布局-->
<table border='1'>
        <caption>表格标题</caption>
        <!--<thead > 用于定义表格的标题部分: 在 <thead > 中，使用 <th> 元素定义列的标题-->
        <!--通过使用 <th>元素定义列标题，可以使其在表格中以粗体显示，与普通单元格区分开来-->
      <thead>
        <tr>
          <th>列标题1</th>
          <th>列标题2</th>
          <th>列标题3</th>
        </tr>
      </thead>
      <!--<tfoot> 标签用于组合 HTML 表格的页脚内容-->
      <tfoot>
        <tr>
            <td>tfoot1</td>
            <td>tfoot2</td>
            <td>tfoot3</td>
        </tr>
    </tfoot>
      <!--<tbody > 用于定义表格的主体部分-->
      <tbody>
        <tr>
          <td>行1，列1</td>
          <td>行1，列2</td>
          <td>行1，列3</td>
        </tr>
      </tbody>
    </table>
```
<table border='1'>
        <caption>表格标题</caption>
      <thead>
        <tr>
          <th>列标题1</th>
          <th>列标题2</th>
          <th>列标题3</th>
        </tr>
      </thead>
      <tfoot>
        <tr>
            <td>tfoot1</td>
            <td>tfoot2</td>
            <td>tfoot3</td>
        </tr>
    </tfoot>
      <tbody>
        <tr>
          <td>行1，列1</td>
          <td>行1，列2</td>
          <td>行1，列3</td>
        </tr>
      </tbody>
    </table>

### HTML 图像  


```html
<!--设置边框 border="0" HTML5不支持 src是必须的-->
<img border="0" src="url" alt="some_text" width="304" height="228">
<!--图像名称-->
<img name="">
<!--title:显示文本，当鼠标停在图片上时，会显示标注的文本内容-->
<img title=''>
<!--loading="lazy" 只有鼠标滚动到该图片所在位置才会显示-->
<!--loading="eager" 默认，图像立即加载-->
<img loading="eager|lazy">    
<!--当点击一个服务器端图像映射时，点击坐标会以 URL 查询字符串的形式发送到服务器  a有效-->
<img ismap>
<!--创建图片链接-->
<a href="url"><img  src="smiley.gif" ></a>
<!--创建图像映射-->
<!--usemap 属性与 <map> 元素的 name 或 id 属性相关联，以建立 <img> 与 <map> 之间的关系。-->
<!--注意：只有当 <img> 元素不属于 <a> 或 <button> 元素的后代时，才允许使用 usemap 属性。-->
<img src="planets.gif" usemap="#planetmap">

<map name="planetmap">
  <area shape="rect" coords="0,0,82,126" href="sun.htm" alt="Sun">
  <area shape="circle" coords="90,58,3" href="mercur.htm" alt="Mercury">
</map>
```
**area 属性值**  
- **coords**

|值|描述|  
|--|----|  
|x1, y1, x2, y2|如果 shape 属性设置为 "rect"，则规定矩形左上角和右下角的坐标|
|x, y, radius|如果 shape 属性设置为 "circle"，则规定圆心坐标和半径|
|x1, y1, x2, y2, .., xn, yn|如果 shape 属性设置为 "poly"，则它规定多边形边缘的坐标。如果第一个和最后一个坐标对不相同，浏览器必须添加最后一个坐标对才能闭合多边形。|

- shape 

|值|描述| 
|-|-|
|default|规定整个区域|
|rect|定义矩形区域|
|circle|定义圆形区域|
|poly|定义多边形区域|

### HTML a

```html
<a href="url" target="_blank" rel="noopener">新窗口打开</a>
```
- **target**：定义链接的打开方式。
    * **blank**: 在新窗口或新标签页中打开链接。
    * **self**: 在当前窗口或标签页中打开链接（默认）。
    * **parent**: 在父框架中打开链接。
    * **top**: 在整个窗口中打开链接，取消任何框架
- **download**：提示浏览器下载链接目标而不是导航到该目标  
(如果指定了文件名，浏览器会提示下载并保存为指定文件名)
- **title**：定义链接的额外信息，当鼠标悬停在链接上时显示的工具提示
- **id**：用于链接锚点，通常在同一页面中跳转到某个特定位置  
`<a href='#section'>`
- **hreflang**: 指定链接的目标URL的语言
- **type**: 指定链接资源的MIME类型
- **class**: 用于指定元素的类名
- **style**: 直接在元素上定义CSS样式

### HTML 列表
- 无序列表 \<ul\>\</ul\>标签
- 有序列表 \<ol\>\</ol\>标签
    * type='A' 大写字母
    * type='a' 小写字母
    * type='I' 罗马数字
    * type='i' 小写罗马数字
    * 默认 编号
- 列表项 \<li\> 标签
    * style="list-style-type:circle" 圆圈
    * style="list-style-type:square" 正方形
    * style="list-style-type:disc" 圆点
- 自定义列表  
    * 自定义列表以 \<dl\> 标签开始
    * 每个自定义列表项以 \<dt\> 开始
    * 每个自定义列表项的定义以 \<dd\> 开始

### HTML5 Audio(音频)

```html
<audio controls>
  <source src="horse.ogg" type="audio/ogg">
  <source src="horse.mp3" type="audio/mpeg">
您的浏览器不支持 audio 元素。
</audio>
```
- control 属性供添加播放、暂停和音量控件。
- 在\<audio\> 与 </audio> 之间你需要插入浏览器不支持的\<audio\>元素的提示文本 。\<audio\> 元素允许使用多个 \<source\> 元素. 
- <source> 元素可以链接不同的音频文件，浏览器将使用第一个支持的音频文件

**音频格式的MIME类型**

|Format|MIME-type|
|-|-|
|MP3|audio/mpeg|
|Ogg|audio/ogg|
|Wav|audio/wav|

**Audio 属性**
- autoplay 音频在就绪后马上播放 `<audio autoplay>`
- controls 布尔属性,音视频控件包括：播放、暂停、进度条、音量 `<audio controls>`
- loop 循环播放
- muted 静音
- preload 规定是否预加载音频。如果设置了 autoplay 属性，则忽略该属性。
    `<audio preload="auto|metadata|none">`
    * auto - 当页面加载后载入整个音频
    * meta - 当页面加载后只载入元数据
    * none - 当页面加载后不载入音频

### HTML 视频（Video）

