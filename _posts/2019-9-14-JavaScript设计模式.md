---
layout:     post
title:      JavaScript设计模式
subtitle:   常见js设计模式解析
date:       2019-9-14
author:     梦尽头
header-img: img/post-bg-swift.jpg
catalog: true
tags:
    - 设计模式
    - JavaScript
---

## 概念

设计模式：在面向对象软件设计过程中针对特定问题的简洁而优雅的解决方案。

设计模式能提高代码的重用性，可读性，使代码更容易的维护和扩展。

## 单例模式（Singleton）

单例（Singleton）模式是JavaScript中最基本的模式。

单例模式提供了一种将代码组织为一个逻辑单元的手段，这个逻辑单元中的代码可以通过单一变量进行访问。

在JavaScript里，单例作为一个命名空间提供者，从全局命名空间里提供一个唯一的访问点来访问该对象。

单例模式有以下优点：

- **划分命名空间，减少全局变量的数量。**

- **使用单体模式可以使代码组织的更为一致，使代码容易阅读和维护。**

- **可以被实例化，且实例化一次。**

单例模式实现方式：

1.**使用对象字面量**

```javascript
var mySingleton = {
    property1: "something",
    property2: "something else",
    method1: function () {
        console.log('hello world');
    }
};
```
添加自己的私有成员和方法，只暴露你想暴露的public成员和方法。

```javascript
var mySingleton = function () {

    /* 这里声明私有变量和方法 */
    var privateVariable = 'something private';
    function showPrivate() {
        console.log(privateVariable);
    }

    /* 公有变量和方法（可以访问私有变量和方法） */
    return {
        publicMethod: function () {
            showPrivate();
        },
        publicVar: 'the public can see this!'
    };
};

var single = mySingleton();
single.publicMethod();  // 输出 'something private'
console.log(single.publicVar); // 输出 'the public can see this!'
```

如果我们想做到只有在使用的时候才初始化。

```javascript
//匿名函数
var Singleton = (function () {
    var instantiated;
    function init() {
        /*这里定义单例代码*/
        return {
            publicMethod: function () {
                console.log('hello world');
            },
            publicProperty: 'test'
        };
    }

    return {
        getInstance: function () {
            if (!instantiated) {
                instantiated = init();
            }
            return instantiated;
        }
    };
})();

/*调用公有的方法来获取实例:*/
Singleton.getInstance().publicMethod();
```

```javascript
// 单例模式
var Singleton = function(name){
    this.name = name;
};
Singleton.prototype.getName = function(){
    return this.name;
}
// 获取实例对象
var getInstance = (function() {
    var instance = null;
    return function(name) {
        if(!instance) {
            instance = new Singleton(name);
        }
        return instance;
    }
})();
// 测试单例模式的实例
var a = getInstance("aa");
var b = getInstance("bb");

// 因为单例模式是只实例化一次，所以下面的实例是相等的

console.log(a === b); // true
console.log(a.getName());// aa
console.log(b.getName());// aa
```

## 工厂模式（Factory）

工厂就是把成员对象的创建工作转交给一个外部对象，好处在于消除对象之间的耦合。
通过使用工厂方法而不是new关键字及具体类，可以把所有实例化的代码都集中在一个位置，有助于创建模块化的代码。

工厂模式类似于现实生活中的工厂可以产生大量相似的商品，去做同样的事情，实现同样的效果。

XHR工厂：

```javascript
var XMLHttpFactory =function(){};　　　　　　//这是一个简单工厂模式
XMLHttpFactory.createXMLHttp =function(){
    var XMLHttp = null;
    if (window.XMLHttpRequest){
　　　　　　XMLHttp = new XMLHttpRequest()
    }else if (window.ActiveXObject){
　　　　　　XMLHttp = new ActiveXObject("Microsoft.XMLHTTP")
　　　　}
　　return XMLHttp;
　　}

　　//XMLHttpFactory.createXMLHttp()这个方法根据当前环境的具体情况返回一个XHR对象。
　　var AjaxHander =function(){
　　　　var XMLHttp = XMLHttpFactory.createXMLHttp();
　　　　//...
　　}
```

```javascript
function CreatePerson(name,age,sex) {
    var obj = new Object();
    obj.name = name;
    obj.age = age;
    obj.sex = sex;
    obj.sayName = function(){
        return this.name;
    }
    return obj;
}
var p1 = new CreatePerson("longen",'28','男');
var p2 = new CreatePerson("tugenhua",'27','女');
```

复杂的工厂模式定义是：将其成员对象的实列化推迟到子类中，子类可以重写父类接口方法以便创建的时候指定自己的对象类型。


## 适配置器模式（Adapter）

将一个类的接口转换成客户希望的另外一个接口。适配器模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。

```javascript
//假如有一个3个字符串参数的函数，但是现在拥有的却是一个包含三个字符串元素的对象，那么就可以用一个配置器来衔接二者
　　var clientObject = {
   　　str1:'bat',
   　　str2:'foo',
   　　str3:'baz'
　　}
　　function interfaceMethod(str1,str2,str3){
　　　　alert(str1)
　　}
　　//配置器函数
　　function adapterMethod(o){
   　　interfaceMethod(o.str1, o.str2, o.str3);
　　}
　　adapterMethod(clientObject)
　　//adapterMethod函数的作为就在于对interfaceMethod函数进行包装，并把传递给它的参数转换为后者需要的形式。
```

适配器模式的工作机制是：用一个新的接口对现有类的接口进行包装。



