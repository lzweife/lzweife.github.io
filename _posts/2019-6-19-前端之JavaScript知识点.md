---
layout:     post
title:      前端之JavaScript知识点
subtitle:   JavaScript知识点
date:       2019-6-19
author:     梦尽头
header-img: img/post-bg-swift.jpg
catalog: true
tags:
    - JavaScript
---
## 前言
总结收集JavaScript的一些关键知识点

## js数据类型
6大数据类型

五种基本数据类型
- `Number`
- `String`
- `Boolean`
- `Undefined`
- `Null`

一种引用数据类型
- `Object(Array,Date,RegExp,Function)`

## 浏览器同源策略

- 如果两个页面的**协议，端口（如果有指定）和主机**都相同，则两个页面具有相同的源。

- 同源策略限制了从同一个源加载的文档或脚本如何与来自另一个源的资源进行交互。这是一个用于隔离潜在恶意文件的重要安全机制。

- 不同源的客户端脚本在没有明确授权的情况下，不能读写对方资源。

![生命周期](https://lzweife.github.io/img/JavaScript_0.jpg)

## 跨域访问策略

**JSONP**

`JSON with Padding` 是 json 的一种"使用模式"。
- 利用`script`标签的`src`属性来实现跨域。
- 通过将前端方法作为参数传递到服务器端，然后由服务器端注入参数之后再返回，实现服务器端向客户端通信。
- 由于使用`script`标签的`src`属性，因此只支持`get`方法

**CORS**

- CORS是一个W3C标准，全称是"跨域资源共享"（`Cross-origin resource sharing`）。

- 它允许浏览器向跨源服务器，发送`XMLHttpRequest`请求，从而克服了AJAX只能同源使用的限制。

- 主流浏览器都支持该功能，IE浏览器不能低于IE10。

- 整个CORS通信过程，都是浏览器自动完成，不需要用户参与。
对于开发者来说，CORS通信与同源的AJAX通信没有差别，代码完全一样。
浏览器一旦发现AJAX请求跨域，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。
因此，实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。

- JSONP只支持GET请求，CORS支持所有类型的HTTP请求。JSONP的优势在于支持老式浏览器，以及可以向不支持CORS的网站请求数据。

**服务器代理**

当你需要有跨域的请求操作时发送请求给后端，让后端帮你代为请求，然后最后将获取的结果发送给你。



## 原型&原型链
- 与Java、C++或其他传统面向对象编程的语言相比，JavaScript没有传统的面向对象模型，即从类创建对象的模型。

- 事实上，JavaScript根本就没有类。在JavaScript中，对象从其他对象那里继承行为，称之为**原型式继承**或**基于原型的继承**。

- JavaScript使用原型式继承，**对象A的行为被对象B继承，那么A对象被称为原型（prototype）**。

- 对象有原型，原型有自己的原型，将其串联连接起来就形成了**原型链**

- 当引用一个对象的属性或行为时，会沿着该对象的原型链一直向上查找，直到找到匹配的属性或行为。

**几个重要属性**

- `prototype`JavaScript的对象中都包含了一个`prototype`内部属性，这个属性所对应的就是该对象的原型。
- `__proto__``prototype`作为对象的内部属性，是不能被直接访问的。所以为了方便查看一个对象的原型，Firefox和Chrome中提供了`__proto__`这个非标准（不是所有浏览器都支持）的访问器。
- `constructor`在JavaScript的原型对象中，`constructor`返回创建所有指向该原型的实例的构造函数。




