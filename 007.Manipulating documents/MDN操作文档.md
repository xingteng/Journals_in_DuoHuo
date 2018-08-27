# [客户端Web-API](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Client-side_web_APIs/Manipulating_documents)-操作文档

- 作者：星腾（前端组）
- 学习时间：2018/8/15 - 8/18

## 文档对象模型

在浏览器标签中当前载入的文档用文档对象模型来表示。这是一个由浏览器生成的“树结构”，使编程语言可以很容易的访问HTML结构 —  例如浏览器自己在呈现页面时，使用它将样式和其他信息应用于正确的元素，而页面呈现完成以后，开发人员可以用JavaScript操作DOM。

文档中每个元素和文本在树中都有它们自己的入口 — 称之为**节点**。你将用不同的术语来描述节点的类型和它们相对于其他节点的位置：

- **元素节点**: 一个元素，存在于DOM中。
- **根节点**: 树中顶层节点，在HTML的情况下，总是一个`HTML`节点（其他标记词汇，如SVG和定制XML将有不同的根元素）。
- **子节点**: *直接*位于另一个节点内的节点。例如上面例子中，`IMG`是`SECTION`的子节点。
- **后代节点**: 位于另一个节点内*任意位置*的节点。例如 上面例子中，`IMG`是`SECTION`的子节点，也是一个后代节点。`IMG`不是`BODY`的子节点，因为它在树中低了`BODY`两级，但它是`BODY`的后代之一。
- **父节点**: 里面有另一个节点的节点。例如上面的例子中`BODY`是`SECTION`的父节点。
- **兄弟节点**: DOM树中位于同一等级的节点。例如上面例子中，`IMG`和`P`是兄弟。
- **文本节点**: 包含文字串的节点

在用DOM工作之前，熟悉这些术语是很有用的，因为你将会遇到大量代码术语的使用。你在研究CSS时也会遇到这些术语（例如后代选择器、子选择器）

## 基本的DOM操作

要操作DOM内的元素，首先需要选择它，并将**它的引用存储在一个变量**中。在script元素中 ：

```javascript
var link = document.querySelector('a');
```

现在你有了一个存储在变量中的元素引用

更新 `Node.textContent`属性的值来修改链接中的文字。在上面的代码后面加入一行代码：   

```js
link.textContent = 'Mozilla Developer Network';
```

我们也能修改链接指向的URL，使得它被点击时不会走向错误的位置。在底部再加入下列代码：   

```js
link.href = 'https://developer.mozilla.org';
```

有很多方法可以选择一个元素，并在一个变量中存储一个引用。[`Document.querySelector()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/querySelector)是推荐的主流方法，它允许你使用CSS选择器选择元素，使用很方便。上面的`querySelector()`调用会匹配它在文档中遇到的第一个[``](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/a)元素。如果想对多个元素进行匹配和操作，你可以使用[`Document.querySelectorAll()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/querySelectorAll)，这个方法匹配文档中每个匹配选择器的元素，并把它们的引用存储在一个[array](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/First_steps/Arrays)中。

还有一些方法，比如  

- `Document.getElementById()` 选择一个`id`属性值已知的元素
- `Document.getElementByName()` 返回页面中包含的所有已知类型元素的数组 

## 创建并放置新的节点

```js
var sect = document.querySelector('section'); //先获取到<section>元素的引用
var para = document.createElement('p'); //用Document.createElement()创建一个新的段落
para.textContent = 'We hope you enjoyed the ride.'; //赋予文本内容
sect.appendChild(para); //用Node.appendChild()追加新的段落
var linkPara = document.querySelector('p');
linkPara.appendChild(text);//获取内部连接的段落的引用，并把文本节点绑定到这个节点上

```

## 移动和删除元素

使用上面的`Node.appendChild()`同样可以改变位置  

删除节点：首先，你拥有要删除的节点和其父节点的引用。在当前情况下，我们使用[`Node.removeChild()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Node/removeChild)即可，如下：

```js
sect.removeChild(linkPara);
```

要删除一个仅基于自身引用的节点可能稍微有点复杂，这也是很常见的。没有方法会告诉节点删除自己，所以必须像下面这样操作。

```js
linkPara.parentNode.removeChild(linkPara);
```

## 操作样式

*第一种方法*是直接在想要动态设置样式的元素内部添加内联样式。这是用[`HTMLElement.style`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/style)属性来实现。设置这个对象的属性直接修改元素样式。

```js
//例子
para.style.color = 'white';
para.style.backgroundColor = 'black';
para.style.padding = '10px';
```
重新载入页面查看段落，你会看到这些代码的确为文档添加了内联样式：   

```html
<p style="color: white; background-color: black; padding: 10px; width: 250px; text-align: center;">We hope you enjoyed the ride.</p>
```

*第二种*方法

在HTML的`<head>`中添加下列代码 :   

```html
<style>
.highlight {
  color: white;
  background-color: black;
  padding: 10px;
  width: 250px;
  text-align: center;
}
</style>
```

现在我们改为使用HTML操作的常用方法 — `Element.setAttribute()` — 这里有两个参数，你想在元素上设置的属性，你要为它设置的值。在这种情况下，我们在段落中设置类名为highlight：   

```js
para.setAttribute('class', 'highlight');
```

刷新页面，看不到改变 — CSS仍然应用到段落

## 从Window对象中获取有用的信息

接下来我们要解决的问题是：不管窗口的大小是多少，确保应用程序和它所在的窗口视图一样大。

我们可以要做一个[window-resize-example.html](https://github.com/mdn/learning-area/blob/master/javascript/apis/document-manipulation/window-resize-example.html)和[bgtile.png](https://github.com/mdn/learning-area/blob/master/javascript/apis/document-manipulation/bgtile.png)文件的本地拷贝。打开文件看一看 — 你可以看到我们用一个[`<div>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/div)元素包裹屏幕的小部分，用来获得应用的background tile。

```js
//获取div的引用，然后获取视窗的宽度和高度
var div = document.querySelector('div');
var WIDTH = window.innerWidth;
var HEIGHT = window.innerHeight
//将动态地改变div的宽度和高度，使其等于视窗的宽度和高度
div.style.width = WIDTH + 'px';
div.style.height = HEIGHT + 'px';
```

保存并刷新浏览器 — 现在可以看到不管你使用什么大小的屏幕，div变得和视窗一样大。如果要调整窗口大小使其更大，你可以看到div会保持相同大小 — 因为我们只能设置一次。

课后练习：动态购物单[地址](https://github.com/xingteng/Journals_in_DuoHuo/blob/master/007.Manipulating%20documents/shopping-list.html)

---

参考文档：MDN-[操作文档](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Client-side_web_APIs/Manipulating_documents)