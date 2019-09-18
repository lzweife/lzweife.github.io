---
layout:     post
title:      JavaScript原型与原型链
subtitle:   原型与原型链解析
date:       2019-8-10
author:     梦尽头
header-img: img/post-bg-swift.jpg
catalog: true
tags:
    - JavaScript
    - 原型 
---

## 几个概念

面向对象（OO）的语言有一个标志，那就是它们都有类的概念，通过类可以创建任意多个具有相同属性和方法的对象。

ECMA-262把对象定义为：**无序属性的集合，其属性可以包含基本值、对象或者函数。**

许多面向对象语言都支持两种继承方式：接口继承和实现继承。

ECMAScript只支持实现继承，主要依靠原型链来实现。

- 每个实例对象（ object ）都有一个私有属性（称之为 __proto__ ）指向它的构造函数的原型对象（prototype ）。

- 该原型对象也有一个自己的原型对象( __proto__ ) ，层层向上直到一个对象的原型对象为 `null`。

- 根据定义，`null` 没有原型，并作为这个原型链中的最后一个环节。

- 几乎所有 JavaScript 中的对象都是位于原型链顶端的 Object 的实例。

## 对象的属性类型

ECMAScript中有两种属性：数据属性和访问器属性。以下的[[]]表示特性是内部值。

1.**数据属性**

数据属性包含一个数据值的位置，可以读取和写入值。有4个描述其行为的特性。

- [[Configurable]]:表示能否提供delete删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。直接在对象上定义的属性，默认值为true。

- [[Enumerable]]：表示能否通过for-in循环返回属性。直接在对象上定义的属性，默认值为true。

- [[Writable]]：表示能否修改属性的值。直接在对象上定义的属性，默认值为true。

- [[Value]]：包含这个属性的数据值。读取属性值时，从这个位置读取，写入属性值时，新值保存在这个位置。默认值为undefined。

2.**访问器属性**

访问器属性不包含数据值。它们包含一对getter和setter函数（非必需）。
读取访问器属性时，会调用getter函数，这个函数负责返回有效的值。
写入访问器属性时，会调用setter函数并传入新值，这个函数负责决定如何处理数据。

访问器属性不能直接定义，必须使用Object.defineProperty()

- [[Configurable]]:表示能否提供delete删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。直接在对象上定义的属性，默认值为true。


- [[Enumerable]]:表示能否通过for-in循环返回属性。直接在对象上定义的属性，默认值为true。

- [[getter]]：在读取属性时调用的函数，默认值为undefined。

- [[setter]]：在写入属性时调用的函数，默认值为undefined。


## 基于原型链的继承

JavaScript 对象有一个指向一个原型对象的链。
当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，
依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾。

```javascript
// 让我们从一个自身拥有属性a和b的函数里创建一个对象o：
let f = function () {
   this.a = 1;
   this.b = 2;
}
/* 这么写也一样
function f() {
  this.a = 1;
  this.b = 2;
}
*/
let o = new f(); // {a: 1, b: 2}

// 在f函数的原型上定义属性
f.prototype.b = 3;
f.prototype.c = 4;

// 不要在 f 函数的原型上直接定义 f.prototype = {b:3,c:4};这样会直接打破原型链
// o.[[Prototype]] 有属性 b 和 c
//  (其实就是 o.__proto__ 或者 o.constructor.prototype)
// o.[[Prototype]].[[Prototype]] 是 Object.prototype.
// 最后o.[[Prototype]].[[Prototype]].[[Prototype]]是null
// 这就是原型链的末尾，即 null，
// 根据定义，null 就是没有 [[Prototype]]。

// 综上，整个原型链如下: 

// {a:1, b:2} ---> {b:3, c:4} ---> Object.prototype---> null

console.log(o.a); // 1
// a是o的自身属性吗？是的，该属性的值为 1

console.log(o.b); // 2
// b是o的自身属性吗？是的，该属性的值为 2
// 原型上也有一个'b'属性，但是它不会被访问到。
// 这种情况被称为"属性遮蔽 (property shadowing)"

console.log(o.c); // 4
// c是o的自身属性吗？不是，那看看它的原型上有没有
// c是o.[[Prototype]]的属性吗？是的，该属性的值为 4

console.log(o.d); // undefined
// d 是 o 的自身属性吗？不是，那看看它的原型上有没有
// d 是 o.[[Prototype]] 的属性吗？不是，那看看它的原型上有没有
// o.[[Prototype]].[[Prototype]] 为 null，停止搜索
// 找不到 d 属性，返回 undefined
```

## 确定原型和实例的关系

- 使用`instanceof`操作符，只要用这个操作符来测试实例(instance)与原型链中出现过的构造函数,结果就会返回true。

```javascript
function SuperType(){
    this.property = true;
}

SuperType.prototype.getSuperValue = function(){
    return this.property;
}

function SubType(){
    this.subproperty = false;
}

SubType.prototype = new SuperType();

SubType.prototype.getSubValue = function(){
    return this.subproperty;
}

var instance = new SubType();
alert(instance.getSuperValue());//true
alert(instance.getSubValue());//false

alert(instance instanceof Object);//true
alert(instance instanceof SuperType);//true
alert(instance instanceof SubType);//true
```
由于原型链的关系, 我们可以说instance 是 Object, SuperType 或 SubType中任何一个类型的实例. 
因此, 这三个构造函数的结果都返回了true.

- 使用isPrototypeOf()方法，同样只要是原型链中出现过的原型,isPrototypeOf() 方法就会返回true。

```javascript
alert(Object.prototype.isPrototypeOf(instance));//true
alert(SuperType.prototype.isPrototypeOf(instance));//true
alert(SubType.prototype.isPrototypeOf(instance));//true
```

## 原型链的问题

- 当原型链中包含引用类型值的原型时,该引用类型值会被所有实例共享;

```javascript
function SuperType(){
    this.colors = ["red","blue","green"];
}

function SubType(){
}

SubType.prototype = new SuperType();

var instance1 = new SubType();
instance1.colors.push("black");
alert(instance1.colors);//red,blue,green,black

var instance2 = new SubType();
alert(instance2.colors);//red,blue,green,black
```
如上所示，对instance1.colors的修改，也影响了instance2的colors属性。

- 在创建子类型的实例(例如创建SubType的实例)时,不能向超类型(例如SuperType)的构造函数中传递参数。

由于以上两种原因，在实践中很少单独使用原型链。

## 借用构造函数

在解决原型中包含引用类型值带来的问题中，开发人员使用一种**借用构造函数**（constructor stealing）的技术。

基本思想：**在子类型构造函数的内部调用超类型构造函数。**

通过使用apply()和call()方法也可以在（将来）新创建的对象上执行构造函数

```javascript
function SuperType(){
    this.colors = ["red","blue","green"];
}

function SubType(){
    // 继承了SuperType
    SuperType.call(this);
}

var instance1 = new SubType();
instance1.colors.push("black");
alert(instance1.colors);//red,blue,green,black

var instance2 = new SubType();
alert(instance2.colors);//red,blue,green
```
`SuperType.call(this);`“借调”了超类型的构造函数。通过使用call()方法或者apply()方法，实际上是在新创建的SubType实例的环境下调用了
SuperType构造函数。这样就会在新SubType对象上执行SuperType()函数中定义的所有对象初始化代码。结果就是，SubType的每个实例都会具有自己的colors属性的副本。


相对于原型链，借用构造函数的一个优势是，可以在子类型构造函数中向超类型构造函数传递参数。

```javascript
function SuperType(name){
    this.name = name;
}

function SubType(){
    // 继承了SuperType
    SuperType.call(this,"Mike");
    this.age = 25;
}

var instance = new SubType();
alert(instance.name); // Mike
alert(instance.age); // 25
```

借用构造函数一举解决了原型链的两大问题:

其一, 保证了原型链中引用类型值的独立,不再被所有实例共享;

其二, 子类型创建时也能够向父类型传递参数.

但是借用构造函数也无法避免构造函数模式存在的问题，方法都在构造函数中定义，因此函数复用就无从谈起。
而且超类型(如SuperType)中定义的方法,对子类型而言也是不可见的。
因此，借用构造函数的技术也很少单独使用。

## 组合继承*

组合继承（combination inheritance）,有时候也叫做伪经典继承,指的是将原型链和借用构造函数的技术组合到一块,从而发挥两者之长的一种继承模式。

基本思想：**使用原型链实现对原型属性和方法的继承,通过借用构造函数来实现对实例属性的继承。**

这样,既通过在原型上定义方法实现了函数复用,又能保证每个实例都有它自己的属性。

```javascript
function SuperType(name){
    this.name = name;
    this.colors = ["red","blue","green"];
}

SuperType.prototype.sayName = function() {
  alert(this.name);
}

function SubType(name,age){
    // 继承属性
    SuperType.call(this,name);
    this.age = age;
}
//继承方法
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function() {
  alert(this.age);
}

var instance1 = new SubType("Mike",25);
instance1.colors.push("black");
alert(instance1.colors);//red,blue,green,black
instance1.sayName();//Mike
instance1.sayAge();//25

var instance2 = new SubType("Tom",27);
alert(instance2.colors);//red,blue,green
instance2.sayName();//Tom
instance2.sayAge();//27

```
SuperType定义了两个属性，name和colors。

SuperType的原型定义了一个方法sayName()。

SubType构造函数在调用SuperType构造函数时传入了name参数，紧接着定义自己的属性age。

然后将SuperType的实例赋值给SubType的原型，然后在该新原型上定义了方法sayAge()。

这样一来，就可以让两个不同的SubType实例既分别拥有自己的属性，包括colors属性，又可以使用相同的方法。

组合继承避免了原型链和借用构造函数的缺陷,融合了它们的优点,成为 JavaScript 中最常用的继承模式. 

而且, instanceof 和 isPrototypeOf( )也能用于识别基于组合继承创建的对象.

同时我们还注意到组合继承其实调用了两次父类构造函数, 造成了不必要的消耗, 那么怎样才能避免这种不必要的消耗呢?


## 原型式继承*

```javascript
function object(o){
	function F(){}
	F.prototype = o;
	return new F();
}
```

在object()函数内部, 先创建一个临时性的构造函数, 然后将传入的对象作为这个构造函数的原型,最后返回了这个临时类型的一个新实例.

从本质上讲, object() 对传入其中的对象执行了一次浅复制。

```javascript
function object(o){
	function F(){}
	F.prototype = o;
	return new F();
}

var person = {
	friends : ["Van","Louis","Nick"]
};
var anotherPerson = object(person);
anotherPerson.friends.push("Rob");

var yetAnotherPerson = object(person);
yetAnotherPerson.friends.push("Style");

alert(person.friends);//Van,Louis,Nick,Rob,Style

```

在这个例子中,可以作为另一个对象基础的是person对象,于是我们把它传入到object()函数中,然后该函数就会返回一个新对象. 
这个新对象将person作为原型,因此它的原型中就包含引用类型值属性. 
这意味着person.friends不仅属于person所有,而且也会被anotherPerson以及yetAnotherPerson共享.

在 ECMAScript5 中,通过新增 object.create() 方法规范化了上面的原型式继承。

`object.create()` 接收两个参数:
- 一个用作新对象原型的对象
- (可选的)一个为新对象定义额外属性的对象

```javascript
var person = {
	friends : ["Van","Louis","Nick"]
};
var anotherPerson = Object.create(person);
anotherPerson.friends.push("Rob");
var yetAnotherPerson = Object.create(person);
yetAnotherPerson.friends.push("Style");
alert(person.friends);//"Van,Louis,Nick,Rob,Style"
```

object.create() 只有一个参数时功能与上述object方法相同。

它的第二个参数与Object.defineProperties()方法的第二个参数格式相同: 每个属性都是通过自己的描述符定义的.
以这种方式指定的任何属性都会覆盖原型对象上的同名属性。

```javascript
var person = {
    name:"Mike",
	friends : ["Van","Louis","Nick"]
};
var anotherPerson = Object.create(person,{
    name:{
        value:"Tom"
    },
    friends:{
        value:["Jack"]
    }
});
var yetAnotherPerson = Object.create(person);

alert(anotherPerson.name);//Tom
alert(anotherPerson.friends);//Jack

alert(yetAnotherPerson.name);//Mike
alert(yetAnotherPerson.friends);//Van,Louis,Nick
```

目前支持 Object.create() 的浏览器有 IE9+, Firefox 4+, Safari 5+, Opera 12+ 和 Chrome.

原型式继承中, 包含引用类型值的属性始终都会共享相应的值, 就像使用原型模式一样.


## 寄生式继承

寄生式（parasitic）继承是与原型式继承紧密相关的一种思路。

寄生式继承的思路与(寄生)构造函数和工厂模式类似, 即创建一个仅用于封装继承过程的函数,该函数在内部以某种方式来增强对象,
最后再像真的是它做了所有工作一样返回对象.

```javascript
function object(o){
	function F(){}
	F.prototype = o;
	return new F();
}

function createAnother(original){
	var clone = object(original);//通过调用object函数创建一个新对象
	clone.sayHi = function(){//以某种方式来增强这个对象
		alert("hi");
	};
	return clone;//返回这个对象
}


var person = {
    name:"Mike",
    friends:["Van","Louis","Nick"]
};

var anotherPerson = createAnother(person);
anotherPerson.sayHi();// hi
```

这个例子中的代码基于person返回了一个新对象--anotherPerson. 
新对象不仅具有 person 的所有属性和方法, 而且还被增强了, 拥有了sayHi()方法.


使用寄生式继承来为对象添加函数, 会由于不能做到函数复用而降低效率;这一点与构造函数模式类似.

## 寄生组合式继承*
前面讲过,组合继承是 JavaScript 最常用的继承模式; 
不过, 它也有自己的不足. 组合继承最大的问题就是无论什么情况下,都会**调用两次父类构造函数**: 

- 一次是在创建子类型原型的时候
- 另一次是在子类型构造函数内部

```javascript
function SuperType(name){
        this.name = name;
        this.colors = ["red","blue","green"];
    }

    SuperType.prototype.sayName = function() {
        alert(this.name);
    }

    function SubType(name,age){
        // 继承属性
        SuperType.call(this,name);          //第二次调用SuperType()
        this.age = age;
    }
    //继承方法
    SubType.prototype = new SuperType();    //第一次调用SuperType()
    SubType.prototype.constructor = SubType;
    SubType.prototype.sayAge = function() {
        alert(this.age);
    }
```

**寄生组合式继承就是为了降低调用父类构造函数的开销而出现的**

所谓寄生组合式继承，即通过借用构造函数来继承属性，通过原型链的混成形式来继承方法。

基本思路是：不必为了指定子类型的原型而调用超类型的构造函数。

```javascript
function inheritPrototype(subType,superType){
	var prototype = object(superType.prototype);//创建对象
	prototype.constructor = subType;//增强对象
	subType.prototype = prototype;//指定对象
}
```

这个函数接收两个参数：子类型构造函数、超类型构造函数。

在函数内部，

第一步，创建超类型原型的一个副本。

第二步，为创建的副本添加constructor属性，从而弥补因重写原型而失去的默认的constructor属性。

第三步，将新创建的副本对象赋值给子类型的原型。

因此，我们可以调用inheritPrototype()函数，来替换掉前面例子中为子类型原型赋值的语句。

```javascript
function SuperType(name){
        this.name = name;
        this.colors = ["red","blue","green"];
    }

    SuperType.prototype.sayName = function() {
        alert(this.name);
    }

    function SubType(name,age){
        // 继承属性
        SuperType.call(this,name);          
        this.age = age;
    }
    //继承方法
    inheritPrototype(SubType,SuperType);

    SubType.prototype.sayAge = function() {
        alert(this.age);
    }
```

inheritPrototype的高效率体现在它只调用了一次SuperType构造函数,
因此避免了在SuperType.prototype上面创建不必要,多余的属性. 
于此同时,原型链还能保持不变; 因此还能正常使用 instanceof 和 isPrototypeOf() 方法。

开发人员普遍认为寄生组合式继承是引用类型最理想的继承方式。











