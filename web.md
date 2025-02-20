### EventTarget.addEventListener()

>EventTarget.addEventListener() 方法将指定的监听器注册到 EventTarget 上，当该对象触发指定的事件时，指定的回调函数就会被执行。事件目标可以是一个文档上的元素 Element、Document 和 Window，也可以是任何支持事件的对象（比如 XMLHttpRequest）


# Web API
## Node
### removeChild()
>Node 接口的 removeChild() 方法会从 DOM 中移除一个子节点，并返回移除的节点

>**备注**： 只要对被移除的子节点保持引用，它仍然存在于内存中，但不再是 DOM的一部分。在以后的代码中仍可重复使用。
>如果不存储 removeChild() 的返回值，也不保留其他引用，该节点将在短时间内在内存中自动删除。

**语法**  `removeChild(child)`  
**参数**  child --- Node，即要从 DOM 中删除的子节点

```html
<body>
  <div class="parent">parent
    <p class="child">child1</p>
    <p class="child">child2</p>
  </div>
  <script>
    let parent = document.querySelector('.parent');
    let child = document.querySelector('.child');

    // 方法一：通过removeChild 
    child_remove = parent.removeChild(child); //删除第一个元素p及其文本

    // 通过循环删除
    while(parent.firstChild){
      parent.removeChild(parent.firstChild); // 删除div标签内的所有内容
    }

    // 方法二:replaceChildren
    parent.replaceChildren(); // 删除div标签内的所有内容

    // 方法三：通过innerHTML
    parent.innerHTML = ''; // 删除div标签内的所有内容

    // 方法四：通过textContent
    parent.textContent = ''; // 删除div标签内的所有内容
  </script>
</body>
```
**总结**
* innerHTML = ''：最简单直接，适合大多数场景。
* removeChild 循环：更可控，适合需要额外处理的场景。
* replaceChildren：现代方法，简洁高效，但兼容性较差。
* textContent = ''：清除所有子元素和文本内容。