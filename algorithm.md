#### 数字取整
```js
Math.floor(value)
~~value // ~ 是按位取反运算符
return parseInt(value) // 解析字符串并返回整数
```

#### 数组转字符串
```js
// join() 方法将一个数组（或一个类数组对象）的所有元素连接成一个字符串并返回这个字符串，用逗号或指定的分隔符字符串分隔。
//如果数组只有一个元素，那么将返回该元素而不使用分隔符。
array.join('-')
for
```

**返回最大数** `Math.max(...array)`

### String
#### String.prototype.split()
`String.split(separator[, limit])` 方法接受一个模式，通过搜索模式将字符串分割成一个有序的子串列表，将这些子串放入一个数组，并返回该数组

### Array

#### Array.prototype.unshift()
`Array.unshift(element1, element2,  …, elementN)` 方法将指定元素添加到数组的开头，并返回数组的新长度  
**注**：如果多个元素作为参数传递，它们将被插入到对象开头的块中，与它们作为参数传递的顺序完全相同

##### 在非数组对象（array-like object）中使用unshift()

```js
const arrayLike = {
  length: 3,
  unrelated: "foo",
  2: 4,
};
Array.prototype.unshift.call(arrayLike, 1, 2);
console.log(arrayLike);
// { '0': 1, '1': 2, '4': 4, length: 5, unrelated: 'foo' }
```
>**unshift 的工作原理**  
>unshift 方法的作用是在数组（或类数组对象）的开头插入一个或多个元素，并更新length属性。它的内部逻辑大致如下：
>**计算插入的元素数量**：unshift 会计算传入的参数数量（这里是 1 和 2，共 2 个元素）。  
>**移动现有元素**：将原有的元素向后移动，腾出空间给新插入的元素。移动的偏移量等于插入的元素数量（这里是 2）。  
>**插入新元**素：将新元素插入到数组的开头。  
>**更新 length 属性**：length 属性会增加，增加的值等于插入的元素数量（这里是 2）

```js
const plainObj = {};
// 这里没有长度属性，所以这里的长的为 0
Array.prototype.unshift.call(plainObj, 1, 2);
console.log(plainObj);
// { '0': 1, '1': 2, length: 2 }

const arrayLike = {
    length: 3,
    unrelated: "foo",
    3: 4,
  };
  Array.prototype.unshift.call(arrayLike, 1, 2);
  console.log(arrayLike); 
  // { '0': 1, '1': 2, length: 5, unrelated: 'foo' }
  //   3:4 丢失：索引6超出length：5的范围
  // 解决：将length改为4
```

在 JavaScript 中，对象的属性顺序规则如下：  
* 数字键（索引）：按照数字从小到大排序;
* 字符串键（非索引）：按照添加到对象的顺序排序

#### Array.prototype.push()
`Array.push(element0, element1,  … , elementN)` 方法将指定的元素添加到数组的末尾，并返回新的数组长度  

**注**：
* push() 方法是一个修改方法。它改变了 this 的内容和长度。
* 不改变this：可以使用 arr.concat([element0, element1,  ... , elementN])来代替。请注意，这些元素需要被包装在一个额外的数组中——否则，如果元素本身是一个数组，由于 concat()的行为，它将被展开而不是作为单个元素添加到原数组的末尾。

**在数组中添加元素**
```js
array.push(element)
```
**合并两个数组**
```js
array1.push(...array2);
```
**在非数组对象中使用 push()**
```js
const arrayLike = {
    length: 3,
    unrelated: "foo",
    2: 4,
  };
  Array.prototype.push.call(arrayLike, 1, 2);
  console.log(arrayLike);
  // { '2': 4, '3': 1, '4': 2, length: 5, unrelated: 'foo' }
  
  const plainObj = {};
  // 这里没有长度属性，所以长度为 0
  Array.prototype.push.call(plainObj, 1, 2);
  console.log(plainObj);
  // { '0': 1, '1': 2, length: 2 }
```

#### Array.prototype.indexOf()
`Array.indexOf(searchElement[, fromIndex])` 方法返回数组中第一次出现给定元素的下标，如果不存在则返回 -1

**fromIndex(可选)**  
开始搜索的索引（从零开始），会转换为整数。
* 负索引从数组末尾开始计数——如果 frommindex < 0，使用 frommindex +  
* array.length。注意，在这种情况下，仍然从前到后搜索数组。
* 如果 fromIndex < -array.length 或者省略了 fromIndex ，将使用 0，而导致整个数组被搜索。
* 如果 fromIndex >= array.length，数组不会继续搜索并返回 -1

**描述**：indexOf() 使用严格相等（与 === 运算符使用的算法相同）将 searchElement 与数组中的元素进行比较。NaN 值永远不会被比较为相等，因此当 searchElement 为 NaN 时 indexOf() 总是返回 -1

**在非数组对象上调用 indexOf()**
```js
const arrayLike = {
    length: 3,
    0: 2,
    1: 3,
    2: 4,
  };
  console.log(Array.prototype.indexOf.call(arrayLike, 2));  // 0
  console.log(Array.prototype.indexOf.call(arrayLike, 5));  // -1

```
#### Array.prototype.reverse()
**原地操作**
- `reverse()`   

**返回浅拷贝数组**
- `toReversed()` 
- 展开语法 `const reverted = [...numbers].reverse();`
- `Array.from()`

#### Array.prototype.slice()
`slice([start, end])` 方法返回一个新的数组对象，这一对象是一个由 start 和 end 决定的原数组的浅拷贝（包括 start，不包括 end），其中 start 和 end 代表了数组元素的索引。原始数组不会被改变

* start 可选
    - 提取起始处的索引（从 0 开始），会转换为整数。
    - 如果索引是负数，则从数组末尾开始计算——如果 start < 0，则使用 start + array.length。
    - 如果 start < -array.length 或者省略了 start，则使用 0。
    - 如果 start >= array.length，则不提取任何元素。
* end 可选
    - 提取终止处的索引（从 0 开始），会转换为整数。slice() 会提取到但不包括 end 的位置
    - 如果索引是负数，则从数组末尾开始计算——如果 end < 0，则使用 end + array.length。
    - 如果 end < -array.length，则使用 0。
    - 如果 end >= array.length 或者省略了 end，则使用 array.length，提取所有元素直到末尾。
    - 如果 end 在规范化后小于或等于 start，则不提取任何元素。



#### Array.prototype.filter()
`Array.filter(callbackFn[, thisArg])` 方法创建给定数组一部分的浅拷贝，其包含通过所提供函数实现的测试的所有元素
* **callbackFn**  
为数组中的每个元素执行的函数。它应该返回一个真值以将元素保留在结果数组中，否则返回一个假值。该函数被调用时将传入以下参数：
    - element 数组中当前正在处理的元素。
    - index 正在处理的元素在数组中的索引。
    - array 调用了 filter() 的数组本身
* **thisArg可选**  
执行 callbackFn 时用作 this 的值

#### Array.prototype.map()
`map(callbackFn[, thisArg])` 方法创建一个新数组，这个新数组由原数组中的每个元素都调用一次提供的函数后的返回值组成
参数与filter中的一致

#### Array.prototype.splice()
`splice()` 方法就地移除或者替换已存在的元素和/或添加新的元素   
`toSpliced()` 要创建一个删除和/或替换部分内容而不改变原数组的新数组  
`slice()` 要访问数组的一部分而不修改它
    
`splice(start[, deleteCount, item1, item2, …, itemN])`
* **start** 从 0 开始计算的索引，表示要开始改变数组的位置，它会被转换成整数
* **deleteCount** 一个整数，表示数组中要从 start 开始删除的元素数量
* **item1、…、itemN** 从 start 开始要加入到数组中的元素。如果不指定任何元素，splice() 将只从数组中删除元素


#### Array.prototype.concat()
`concat(concat(value0, value1,  … , valueN))` 方法用于合并两个或多个数组。此方法不会更改现有数组，而是返回一个新数组
* **value1、……、valueN** 数组和/或值，将被合并到一个新的数组中。如果省略了所有 valueN 参数，则 concat 会返回调用此方法的现存数组的一个浅拷贝。

**使用 Symbol.isConcatSpreadable 合并类数组对象**  
concat 默认情况下不会将类数组对象视作数组——仅在 Symbol.isConcatSpreadable 被设置为真值（例如，true）时才会将类数组对象视作数组
```js
const obj1 = { 0: 1, 1: 2, 2: 3, length: 3 };
const obj2 = { 0: 1, 1: 2, 2: 3, length: 3, [Symbol.isConcatSpreadable]: true };
console.log([0].concat(obj1, obj2));
// [ 0, { '0': 1, '1': 2, '2': 3, length: 3 }, 1, 2, 3 ]
```  

**Symbol.isConcatSpreadable** 可以控制对象在 concat 方法中的展开行为。
* 如果对象没有 Symbol.isConcatSpreadable 属性或该属性为 false，则对象会作为一个整体被添加到结果数组中。
* 如果 Symbol.isConcatSpreadable 为 true，则对象会被展开，其元素会被逐个添加到结果数组中。
```js
const obj1 = { 0: 1, 1: 2, 2: 3, length: 3 };
const obj2 = { 0: 1, 1: 2, 2: 3, length: 3, [Symbol.isConcatSpreadable]: true };
console.log([0].concat(obj1, obj2)); 
// [ 0, { '0': 1, '1': 2, '2': 3, length: 3 }, 1, 2, 3 ]
const obj3 = { 0: 1, 1: 2, 2: 3, length: 3, [Symbol.isConcatSpreadable]: false };
console.log([0].concat(obj1, obj3)); 
// [
//     0,
//     { '0': 1, '1': 2, '2': 3, length: 3 },
//     {
//       '0': 1,
//       '1': 2,
//       '2': 3,
//       length: 3,
//       [Symbol(Symbol.isConcatSpreadable)]: false
//     }
// ]
```





### 表达式和运算符
#### 展开语法(Spread syntax)

### JavaScript标准内置对象
#### parseInt
`parseInt(string[, radix])`  
解析一个字符串并返回指定基数的十进制整数，radix是2-36 之间的整数，表示被解析字符串的基数
* **string**
要被解析的值。如果参数不是一个字符串，则将其转换为字符串 (使用toString抽象操作)。字符串开头的空白符将会被忽略
* **radix**  
从 2 到 36 的整数，表示进制的基数。例如指定 16 表示被解析值是十六进制数。如果超出这个范围，将返回 NaN。假如指定 0 或未指定，基数将会根据字符串的值进行推算。注意，推算的结果不会永远是默认值 10！
```js
console.log(parseInt(0o21, 8)); // 15
```
**解析：**  
* 0o21 是一个八进制数，其十进制值为 17。
* parseInt 的第一个参数如果不是字符串，会先转换为字符串。因此，0o21 会被转换为字符串 "17"。
* radix 是 8，表示将字符串 "17" 按照八进制解析，结果为15。
```js
console.log(parseInt("FXX123", 16)); // 15
```

**解析：**  
* radix 是 16，表示将字符串 "FXX123" 按照十六进制解析。
* parseInt 会从字符串的开头开始解析，直到遇到一个无效字符为止。
* 在十六进制中，有效的字符是 0-9 和 A-F（不区分大小写）。
* 对于字符串 "FXX123"：
* F 是有效的十六进制字符，对应的十进制值是 15。
* X 是无效的十六进制字符，解析到此结束。
* 因此，parseInt 只解析了 "F"，并将其转换为十进制值 15

#### for...in 和 for...of


|特性|for...in|for...of|
|-|-|
|遍历内容|对象的键名（key）|可迭代对象的值（value）|
|适用对象|对象（Object）|可迭代对象（数组、字符串、Map 等）|
|原型链|会遍历原型链上的可枚举属性|不会遍历原型链|
|数组遍历|遍历索引（字符串形式的数字）|遍历元素值|
|顺序|不保证顺序|保证顺序|
|性能|较慢（需要检查原型链）|较快|