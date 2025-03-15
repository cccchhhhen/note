### CSS单位
> 在css中单位长度用的最多的是px、em、rem，这三个的区别是：
> px是固定的像素，一旦设置了就无法因为适应页面大小而改变。
> em和rem相对于px更具有灵活性，他们是相对长度单位，意思是长度不是定死了的，更适用于响应式布局。
> 对于em和rem的区别一句话概括：**em相对于父元素，rem相对于根元素**。

### 外边距折叠
> 常规块盒子有一种机制叫外边距折叠，即垂直方向上的两个外边距相遇时，会折叠成一个外边距，且折叠之后的外边距高度为两者之中较大的那一个。  

如果想要清除两个盒子之间的外边距折叠，可以给目标盒子设置以下属性：
1. display: inline-block
2. float属性值不是"none"的元素
3. 绝对定位



## 一、grid

网格布局 —— 目前唯一一种 CSS 二维布局， flex 布局是一维布局

### 1、display

声明：`display：grid` (块级元素)

或 `display：inline-grid`(行内元素)

### 2、网格轨道

#### 2.1 `grid-template-columns` /`grid-template-rows` 

`grid-template-columns` 属性设置列宽，`grid-template-rows` 属性设置行高

eg:

* `grid-template-rows: 100px 300px;`  设置两行的行高

#### 2.2 fr

fr 单位代表网格容器中可用空间的一等份

eg：

* `grid-template-columns: 1fr 1fr；`    创建两个 相等宽度相的轨道

#### 2.3  repeat() 函数

**语法：**repeat(重复的次数，重复的值) 

eg：

* `grid-template-rows: repeat(2, 50px);`  设置两行的行高都为50px

#### 2.4 auto-fill 关键字

表示自动填充，让一行（或者一列）中尽可能的容纳更多的单元格

eg:

* `grid-template-columns: repeat(auto-fill, 200px)`   表示列宽是 200 px，但列的数量是不固定的

#### 2.5 minmax() 

**语法：**minmax(最小值，最大值)  ，minmax() 函数产生一个长度范围

eg：

* `grid-template-columns: 1fr 1fr minmax(300px, 2fr)`  第三个列宽最少也是要 300px，但是最大不能大于第一第二列宽的两倍

#### 2.6 auto关键字

auto 关键字：由浏览器决定长度

eg:

* `grid-template-columns: auto auto auto auto;`  创建四列

### 3  网格单元

一个网格单元是在一个网格元素中最小的单位

### 4 网格区域

网格元素可以向行或着列的方向扩展一个或多个单元，并且会创建一个网格区域。网格区域的形状应该是一个矩形

### 5、grid-gap

`grid-row-gap` 属性、`grid-column-gap` 属性分别设置行间距和列间距。`grid-gap` 属性是两者的简写形式。

eg:

* `grid-row-gap: 10px`;   `grid-column-gap: 20px;`
* 等价于 `grid-gap: 10px 20px;`

### 6、grid-template-areas

* grid-template-areas 属性用于定义区域，一个区域由一个或者多个单元格组成
* 这个属性跟网格元素的 grid-area 一起使用。 `grid-area` 属性指定项目放在哪一个区域

```html
<style>
        .wrapper{
            display: grid;
            /* . 代表空格，header 占两格，content1占一格，content2占两格 */
            grid-template-areas:  
            ". header header "
            "content1 content2 content2";
            border: 1px solid gray;
            text-align: center;
        }
        /* 指定 .header 放在 header 区域 */
        .header{
            grid-area: header;
            background-color: hotpink;
        }
        /* 指定 .content1 放在 content1 区域 */
        .content1{
            grid-area: content1;
            background-color: skyblue;
        }
        /* 指定 .content2 放在 content2 区域 */
        .content2{
            grid-area: content2;
            background-color: greenyellow;
        }
        
    </style>
    
    
    
    <div class="wrapper">
        <div class="box header">header</div>
        <div class="box content1">content1</div>
        <div class="box content2">content2</div>
    </div>
```

### 7、grid-auto-flow

指定在网格中被自动布局的元素怎样排列。默认值是row，放置顺序是"先行后列"

```css
grid-auto-flow: row|column|dense|row dense|column dense;
```

* row： 默认值。 通过填充每一行来放置网格元素，在必要时增加新行
* column：通过填充每一列来放置网格元素，在必要时增加新列。
* dense：该关键字指定自动布局算法使用一种"稠密"堆积算法，如果后面出现了稍小的元素，则会试图去填充网格中前面留下的空白。这样做会填上稍大元素留下的空白，但同时也可能导致原来出现的次序被打乱
* row dense：按行来填充网格中前面留下的空白
* column dense：按列来填充网格中前面留下的空白

### 8、justify-items 、align-items、 place-items 

* justify-items 属性设置**单元格内容**的水平位置（左中右）

* align-items 属性设置**单元格内容**的垂直位置（上中下）

* **语法：**justify-items: start | end | center | stretch;

  ​	  align-items: start | end | center | stretch;

* 默认 stretch

### 9、justify-content 、align-content 、place-content 

* justify-content 属性是**整个内容区域**在容器里面的水平位置（左中右）
* align-content 属性是**整个内容区域**的垂直位置（上中下）
* **语法：**justify-content: start | end | center | stretch | space-around | space-between | space-evenly;
             align-content: start | end | center | stretch | space-around | space-between | space-evenly;  
* start、center、end 是整个表格的位置
* space-around - 每个项目两侧的间隔相等。所以，项目之间的间隔比项目与容器边框的间隔大一倍
  space-between - 项目与项目的间隔相等，项目与容器边框之间没有间隔
  space-evenly - 项目与项目的间隔相等，项目与容器边框之间也是同样长度的间隔
  stretch - 项目大小没有指定时，拉伸占据整个网格容器

10、**grid-auto-columns 、 grid-auto-rows **

假如有多余的网格（隐式网格），那么它的行高和列宽可以根据 grid-auto-columns 属性和 grid-auto-rows 属性设置

```html
<style>
        .wrapper{
            display: grid;
            grid-template-columns: 100px 200px;
            grid-template-rows: 50px 50px;
            border: 1px solid gray;
            /*  只设置了两行，但实际的数量会超出两行，超出的行高会以 grid-auto-rows 算 */
            grid-auto-rows: 20px;
        }
        /* div 内的文本在垂直方向上（继承）是占满的，在水平方向上没有（内容宽度小于div） */
        /* 所有align-items有效，而justify-items无效 */
        /* 同理，align-content无效，，而justify-content有效 */
        .wrapper div{
            display: flex;
            align-items: center;
            justify-content: center;
        }
    </style>


<div class="wrapper">
        <div class="box1 " style="background-color: hotpink;">1</div>
        <div class="box2 " style="background-color: skyblue;">2</div>
        <div class="box3 " style="background-color: greenyellow;">3</div>
        <div class="box4 " style="background-color: peru;">4</div>
        <div class="box5 " style="background-color: purple;">5</div>
        <div class="box6 " style="background-color: gray;">6</div>
    </div>
```

### 11、**justify-self 、align-self 、place-self **

* justify-self 属性设置单元格内容的水平位置（左中右），跟 justify-items 属性的用法完全一致，但只作用于单个项目 
* align-self 属性设置单元格内容的垂直位置（上中下），跟 align-items属性的用法完全一致，也是只作用于单个项目
* 

### 12、**grid-column-start 、grid-column-end 、grid-row-start 、grid-row-end **

- `grid-column-start` 属性：左边框所在的垂直网格线
- `grid-column-end` 属性：右边框所在的垂直网格线
- `grid-row-start` 属性：上边框所在的水平网格线
- `grid-row-end` 属性：下边框所在的水平网格线

eg：

* .test{  grid-column-start: 3;    grid-column-end: 4;  }
* .test{  span: 3;}   网格跨度为3



*如果有重叠，就使用 z-index*

