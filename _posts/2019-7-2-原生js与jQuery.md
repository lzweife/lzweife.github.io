---
layout:     post
title:      原生js与jQuery
subtitle:   原生js与jQuery对比
date:       2019-7-2
author:     梦尽头
header-img: img/post-bg-swift.jpg
catalog: true
tags:
    - JavaScript 
    - jQuery
---

## 为什么不提倡使用jQuery

- 开发模式的变化：jQuery 代表着传统的以 DOM 为中心的开发模式，但现在复杂页面开发流行的是以 React、Angular、Vue 为代表的以数据/状态为中心的开发模式。

- 不支持同构渲染：重构就是前后端运行同一份代码，后端也可以渲染出页面，这对 SEO 要求高的场景非常合适。由于 React 等流行框架天然支持，已经具有可行性。当我们在尝试把现有应用改成同构时，因为代码要运行在服务器端，但服务器端没有 DOM，所以引用 jQuery 就会报错。这也是要移除 jQuery 的迫切原因。同时不但要移除 jQuery，在很多场合也要避免直接操作 DOM。

- 原生 API 足够好用：由于浏览器的历史原因，曾经的前端开发为了兼容不同浏览器怪癖，需要增加很多成本。jQuery 由于提供了非常易用的 API，屏蔽了浏览器差异，极大地提高了开发效率。这也导致很多前端只懂 jQuery。其实这几年浏览器更新很快，也借鉴了很多 jQuery 的 API，如 querySelector，querySelectorAll 和 jQuery 选择器同样好用，而且性能更优。

- 性能：前端开发一般不需要考虑性能问题，但你想在性能上追求极致的话，一定要知道 jQuery 性能很差。原生 API 选择器相比 jQuery 丰富很多，如 document.getElementsByClassName 性能是 $(classSelector) 的 50 多倍！

- 时机成熟：差的浏览器（IE）已经淘汰的差不多了。国际上 IE 占有率，IE8 已经跌出前 10，IE 11 比较多，还好支持他们并不难。

## 选择器



- class查询
```javascript
//jQuery
$('.class');

//native
document.querySelectorAll('.class');
document.getElementsByClassName('class');
```


- id查询

```javascript
//jQuery
$('#id');

//native
document.querySelector('#id');
document.getElementById('id');
```

- 属性查询

```javascript

//jquery
$('.class a[target=_blank]');

//nativa
document.querySelectorAll('.class a[target=_blank]');
```
- 后代查询

```javascript
//jQuery
$el.find('li');

//native
el.querySelectorAll('li');
```

## DOM操作

- remove：移除元素

```javascript
//jQuery
$el.remove();

//native
el.parentNode.removeChild(el);

// Native - Only latest, NO IE
el.remove();
```

- empty：移除所有子节点

```javascript
//jQuery
$el.empty();

//native
el.innerHTML = '';
```

- append：插入到子节点末尾

```javascript
//jQuery
$el.append("<div id='container'>hello</div>");

//native(html string)
el.insertAdjacentElement('beforeend','<div id="container">hello</div>')

//native (element)
el.appendChild(newElement);
```
- prepend：插入到子节点的前面

```javascript
//jQuery
$el.prepend("<div id='container'>hello</div>");

//native(html string)
el.insertAdjacentElement('afterbegin','<div id="container">hello</div>');

//native(Element)
el.insertBefore(newElement,el.firstChild);
```

- insertBefore:在选中元素前插入新节点

```javascript
//jQuery
$newEl.insertBefore(queryString);

//native(HTMl string)
el.insertAdjacentElement('beforebegin','<div id="container">hello</div>');

//native (Element)

const el = document.querySelector(selector);
if(el.parertNode) {
    el.parertNode.insertBefore(newEl,el);
}
```

- insertAfter：在选中元素后插入新节点
```javascript
// jQuery
$newEl.insertAfter(queryString);

// Native (HTML string)
el.insertAdjacentHTML('afterend', '<div id="container">Hello World</div>');

// Native (Element)
const el = document.querySelector(selector);
if (el.parentNode) {
  el.parentNode.insertBefore(newEl, el.nextSibling);
}
```              

- is：如果匹配给定的选择器，返回true
     
```javascript
// jQuery
$el.is(selector);

// Native
el.matches(selector);
```

- clone：深拷贝被选元素。（生成被选元素的副本，包含子节点、文本和属性。）
```javascript
//jQuery
$el.clone();

//Native
//深拷贝添加参数'true'
el.cloneNode();
```

- wrap：把每个被选元素放置在指定的HTML结构中。

```javascript
//jQuery
$(".inner").wrap('<div class="wrapper"></div>');

//Native
Array.prototype.forEach.call(document.querySelector('.inner'), (el) => {
  const wrapper = document.createElement('div');
  wrapper.className = 'wrapper';
  el.parentNode.insertBefore(wrapper, el);
  el.parentNode.removeChild(el);
  wrapper.appendChild(el);
});
```
- unwrap：移除被选元素的父元素的DOM结构
         
```javascript
// jQuery
$('.inner').unwrap();

// Native
Array.prototype.forEach.call(document.querySelectorAll('.inner'), (el) => {
      let elParentNode = el.parentNode

      if(elParentNode !== document.body) {
          elParentNode.parentNode.insertBefore(el, elParentNode)
          elParentNode.parentNode.removeChild(elParentNode)
      }
});
```

- replaceWith：用指定的元素替换被选的元素              

```javascript
//jQuery
$('.inner').replaceWith('<div class="outer"></div>');

//Native
Array.prototype.forEach.call(document.querySelectorAll('.inner'),(el) => {
  const outer = document.createElement("div");
  outer.className = "outer";
  el.parentNode.insertBefore(outer, el);
  el.parentNode.removeChild(el);
});
```

- text设置文本
```javascript
// jQuery
$el.text(string);

// Native
el.textContent = string;
```
- 设置HTML

```javascript
// jQuery
$el.html();

// Native
el.innerHTML;

// jQuery
$el.html(htmlString);

// Native
el.innerHTML = htmlString;
```

## css操作

- 获取样式

```javascript
//jQuery
$el.css("color");

//native
// 注意：此处为了解决当 style 值为 auto 时，返回 auto 的问题
const win = el.ownerDocument.defaultView;

// null 的意思是不返回伪类元素
win.getComputedStyle(el, null).color;
```

- 设置样式

```javascript
// jQuery
$(el).css({
  background: "#FF0000",
  "box-shadow": "1px 1px 5px 5px red",
  width: "100px",
  height: "100px",
  display: "block"
});

//native
el.style.background = "#FF0000";
el.style.boxShadow = "1px 1px 5px 5px red";
el.style.width = "100px";
el.style.height = "100px";
el.style.display = "block";

```

- 添加类名

```javascript
//jQuery
$el.addClass(className);

//Native
el.classList.add(className)
```

- 移除类名

```javascript
//jQuery
$el.removeClass(className);

//native
el.classList.remove(className);
```

- has class
  
```javascript
// jQuery
$el.hasClass(className);

// Native
el.classList.contains(className);
```

- Toggle class
  
```javascript
// jQuery
$el.toggleClass(className);

// Native
el.classList.toggle(className);
```

## 显示隐藏元素

```javascript
//jQuery
$el.show();
$el.hide();

//native
el.style.display = '';
el.style.display = 'none';
```
## 事件

- on绑定事件

```javascript
// jQuery
$el.on(eventName, eventHandler);

// Native
el.addEventListener(eventName, eventHandler);
```

- off解绑事件

```javascript
// jQuery
$el.off(eventName, eventHandler);

// Native
el.removeEventListener(eventName, eventHandler);
```

- ready

```javascript
// jQuery
$(document).ready(eventHandler);

// Native
// 检测 DOMContentLoaded 是否已完成
if (document.readyState !== 'loading') {
  eventHandler();
} else {
  document.addEventListener('DOMContentLoaded', eventHandler);
}
```

## 工具判断

- isArray 检测参数是不是数组。
```javascript

// jQuery
$.isArray(range);

// Native
Array.isArray(range);
```

- isWindow 检测参数是不是 window。

```javascript

// jQuery
$.isWindow(obj);

// Native
function isWindow(obj) {
  return obj !== null && obj !== undefined && obj === obj.window;
}
```

- inArray 在数组中搜索指定值并返回索引 (找不到则返回 -1)。

```javascript

// jQuery
$.inArray(item, array);

// Native
array.indexOf(item) > -1;

// ES6-way
array.includes(item);
```

- isNumeric
检测传入的参数是不是数字。 Use typeof to decide the type or the type example for better accuracy.

```javascript

// jQuery
$.isNumeric(item);

// Native
function isNumeric(n) {
  return !isNaN(parseFloat(n)) && isFinite(n);
}
````

- isFunction
检测传入的参数是不是 JavaScript 函数对象。

```javascript

// jQuery
$.isFunction(item);

// Native
function isFunction(item) {
  if (typeof item === 'function') {
    return true;
  }
  var type = Object.prototype.toString(item);
  return type === '[object Function]' || type === '[object GeneratorFunction]';
}
```

- isEmptyObject
检测对象是否为空 (包括不可枚举属性).

```javascript

// jQuery
$.isEmptyObject(obj);

// Native
function isEmptyObject(obj) {
  return Object.keys(obj).length === 0;
}
```

- isPlainObject
检测是不是扁平对象 (使用 “{}” 或 “new Object” 创建).

```javascript

// jQuery
$.isPlainObject(obj);

// Native
function isPlainObject(obj) {
  if (typeof (obj) !== 'object' || obj.nodeType || obj !== null && obj !== undefined && obj === obj.window) {
    return false;
  }

  if (obj.constructor &&
      !Object.prototype.hasOwnProperty.call(obj.constructor.prototype, 'isPrototypeOf')) {
    return false;
  }

  return true;
}
```
- extend
合并多个对象的内容到第一个对象。 object.assign 是 ES6 API，也可以使用 polyfill。

```javascript

// jQuery
$.extend({}, defaultOpts, opts);

// Native
Object.assign({}, defaultOpts, opts);
```

- trim
移除字符串头尾空白。

```javascript

// jQuery
$.trim(string);

// Native
string.trim();
```

- map 将数组或对象转化为包含新内容的数组。

```javascript

// jQuery
$.map(array, (value, index) => {
});

// Native
array.map((value, index) => {
});
```

- each 轮询函数，可用于平滑的轮询对象和数组。

```javascript

// jQuery
$.each(array, (index, value) => {
});

// Native
array.forEach((value, index) => {
});
```