---
layout:     post
title:      ECMAScript 6+
subtitle:   ECMAScript 6+知识总结
date:       2020-3-10
updateDate: 2020-3-15
author:     梦尽头
header-img: img/post-bg-swift2.jpg
catalog: true
tags:
    - ECMAScript 6
    - JavaScript
---
# 				ECMAScript 6+


## 全局作用域

在js文件最外层用`var`定义的变量为全局变量，不能用`delete`方法进行删除。

在js文件最外层或函数内部未使用`var`关键字直接声明的变量不是全局变量，而是作为`window`全局对象的一个属性，可以使用`delete`方法进行删除。

## 局部作用域

也叫函数作用域，即在函数内部定义的变量，函数外部不能访问函数内部定义的变量，对外是封闭的。

## 块状作用域

ES6之前只有全局作用域和局部作用域，在函数内部中代码块再定义变量时，无法隔离函数内部中的变量信息。

```javascript
function test () {
  var a = 3
  if (a === 3) {
    var b = 7
    console.log('abc')
  } else {
    console.log('abcd')
  }
  console.log(b)
  return a + 4
}
test()
```

运行以上函数，你会发现变量`b`被打印了，而不是报错。

ES6中的`let`关键字定义变量`b`就能有效的隔离函数内部代码块中变量信息

## 动态作用域

```javascript
window.a = 3
function test () {
  console.log(this.a)
}
test()
test.bind({ a: 100 })()
```

`bind`将函数动态的绑定到一个对象上去，以上代码输出的是3和100

## Let

- `let` 声明的变量具有块级作用域

  ```JavaScript
  {
    let a = 1
    console.log(a) // 1
  }
  console.log(a) // undefined
  ```

- `let`声明的全局变量不是全局对象的属性

  ```javascript
  var a = 1
  console.log(window.a) // 1
  
  let b = 1
  console.log(window.b) // undefined
  ```

- `let`重复定义变量会抛出异常

  ```js
  var a = 1
  var a = 2
  console.log(a)  //2
  
  let b = 1
  let b = 2
  console.log(a)  //Uncaught SyntaxError: Identifier 'b' has already been declared
  ```

- `let`声明的变量不会进行变量提升

```js
function test () {
  console.log(a)
  var a = 1
}
test() // undefined

function test () {
  console.log(a)
  let a = 1
}
test() // Uncaught ReferenceError: Cannot access 'a' before initialization
```

## Const

`const`具有和`let`一样的特点，除此之外`const`定义的是常量，对其声明的变量进行修改会抛异常。

```js
const PI = 3.1415
console.log(PI)
PI = 22
console.log(PI)// Uncaught TypeError: Assignment to constant variable.
```

同时，`const` 声明的变量必须进行初始化，不然会抛出异常 `Uncaught SyntaxError: Missing initializer in const declaration。`

## Array

### 数组遍历

#### for（ES5）

支持`break`和`continue`

```js
const arr = [1, 2, 3]
for (let i = 0; i < arr.length; i++) {
  console.log(arr[i])
}
```

#### forEach（ES5）

不支持`break`和`continue`

```js
const arr = [1, 2, 3]
arr.forEach(function (item, index) {
  console.log(item, index)
})
```

#### every（ES5）

为了解决`forEach`不支持`break`和`continue`的缺陷

`every`数组循环默认返回`false`，仅遍历第一个元素

```js
const arr = [1, 2, 3]
arr.every(function (item, index) {
  console.log(item)
})
// 输出 1
```

改变其默认行为后（`return true`）循环输出全部元素

```js
const arr = [1, 2, 3]
arr.every(function (item, index) {
  console.log(item)
  return true
})
// 输出 1 2 3
```

#### for in（ES5）

用于遍历对象，支持`break`和`continue`

- 使用`for in`会遍历数组所有的可枚举属性，包括原型
- index索引为字符串数字，不能直接进行几何运算，可能会出现类型错误
- 遍历顺序有可能不是按照实际数组的内部顺序

```js
const arr = [1, 2, 3]
arr.a = 9
for (let index in arr) {
  console.log(index, arr[index])
}
// 输出
// 0 1
// 1 2
// 2 3
// a 9
```

#### for of （ES6）

不仅可以遍历数组、对象，还可以遍历自定义的数据结构。支持`break`和`continue`

```js
const arr = [1, 2, 3]
for (let item of arr) {
  console.log(item)
}
```

### 伪数组转换

伪数组：该对象按照索引的方式存储数据；该对象具有`length`属性。

```js
// es5
let args = [].slice().call(arguments) // collection
let imgs = [].slice().call(document.querySelectorAll('img')) // NodeList

// es6
let args = Array.from(arguments) // collection
let imgs = Array.from(document.querySelectorAll('img')) //NodeList

//生成长度为5，元素全为1的数组
let array = Array.from({length:5},function () {return 1})
```

### 创建数组

```js
let array = Array.of(1, 2, 3)// [1, 2, 3]
let array = Array(3).fill(1)// [1, 1, 1]
```

### 数组查找

- `filter`
- `find`
- `findIndex`

```js
// es5 
// 会返回所有符合条件的元素，大数组查找性能较低
let array = [1, 2, 3, 4, 3]
let find = array.filter(function (item) {
    return item % 2 === 0
})
// [2, 4]

// es6
// 返回满足条件的第一个元素即停止查找，没有符合条件的元素则返回undefined
let array = [1, 1, 3, 4, 3]
let find = array.find(function (item) {
  return item % 2 !== 0
})
// 4

// 返回满足条件的第一个元素的索引值即停止查找，没有符合条件的元素则返回-1
let array = [1, 1, 3, 4, 3]
let find = array.findIndex(function (item) {
  return item % 2 === 0
})
```

## Class

```js
class Animal {
  constructor (type) {
    this.type = type
  }

  run () {
    console.log(this.type + ' running ')
  }
}
let dog = new Animal('dog')
dog.run()
```

带`get` 和 `set`关键字的属性可以做一些限制对象属性读写的操作。 

### 静态方法

静态方法不属于对象实例，它属于这个类，静态方法内部拿不到实例对象的上下文。不能通过对象实例来访问静态方法，直接通过类就可以访问静态方法。

```js
// ES5初始化对象
let Animal = function (type) {
  this.type = type
  // 对象实例方法
  this.walk = function () {
    console.log(`I am walking`)
    Animal.eat()
  }
}

// 静态方法
Animal.eat = function (food) {
  console.log(`I am eating`)
}

let dog = new Animal('dog')
dog.walk()
// 输出
// I am walking
// I am eating
dog.eat()
// 输出 Uncaught TypeError: dog.eat is not a function

// 静态方法只能通过类直接访问
Animal.eat()
// 输出 I am eating
```

```js
// ES6初始化对象
class Animal {
  constructor (type) {
    this.type = type
  }
  // 对象实例方法
  walk () {
    console.log(`I am walking`)
    Animal.eat()
  }
  // 静态方法
  static eat () {
    console.log(`I am eating`)
  }
}

let dog = new Animal('dog')
dog.walk()
// 输出
// I am walking
// I am eating

// 静态方法只能通过类直接访问
Animal.eat()
// 输出 I am eating
```

实际开发场景中：**方法依赖于对象实例的某些属性或其他方法，就应该声明为对象实例方法；方法不依赖于实例对象的内容，就应该声明为静态方法**

### 继承

```js
// ES5 Dog类继承Animal类
let Animal = function (type) {
  this.type = type
}
// 对象实例方法
Animal.prototype.walk = function () {
  console.log(`I am walking`)
}
// 静态方法
Animal.eat = function (food) {
  console.log(`I am eating`)
}

let Dog = function () {
  // 初始化父类的构造函数
  Animal.call(this, 'dog')
  // call方法使this指针由Animal的对象实例指向Dog的对象实例

  this.run = function () {
    console.log('I am running')
  }
}

Dog.prototype = Animal.prototype
let puppy = new Dog('puppy')
puppy.walk()// I am walking
puppy.run()// Iam running
```

ES5实现对象继承的过程非常繁琐难懂，而ES6继承父类的语法非常简洁易懂

```js
// ES6 Dog类继承Animal类
class Animal {
  constructor (type) {
    this.type = type
  }
  // 对象实例方法
  walk () {
    console.log(`I am walking`)
  }
  // 静态方法
  static eat () {
    console.log(`I am eating`)
  }
}

class Dog extends Animal {
  constructor (type) {
    // 初始化父类的构造函数
    super(type)
    // 这里还可以额外定义子类的构造函数
  }
}

let puppy = new Dog('puppy')
puppy.walk()// I am walking
Dog.eat()// I am eating
```

## 函数

### 参数默认值

```js
// ES5 默认参数
function foo (x, y, z) {
  if (y === undefined) {
    x = 1
  }
  if (y === undefined) {
    z = 2
  }
  return x + y + z
}

// ES6 默认参数
function foo (x, y = 7, z = 2) {
  return x + y + z
}
console.log(foo(1))// 10
console.log(foo(2, undefined, 4))// 13
```

### 不确定参数

```js
// 不确定参数求和
// ES5
function sum () {
  let num = 0
  Array.prototype.forEach.call(arguments, function (item) {
    num += item * 1
  })
  return num
}
console.log(sum(1, 2, 3))// 6

// ES6 Array.from()
function sum () {
  let num = 0
  Array.from(arguments).forEach(function (item) {
    num += item * 1
  })
  return num
}
console.log(sum(1, 2, 3))// 6

// ES6 Rest Parameter
function sum (first, ...nums) {
  let num = 0
  console.log(nums)
  nums.forEach((item) => {
    num += item * 1
  })
  return first * num
}
console.log(sum(2, 2, 3))
// [2, 3]
// 10
```

### 箭头函数

- ES5中this总是指向调用该对象的对象。
- **箭头函数在自己的作用域内不绑定 `this`，即没有自己的 `this`，如果要使用 `this` ，就会指向定义时所在的作用域的 `this` 值**。很方便的解决了ES5中this指向复杂的问题。

```js
var test = {
  name: 'test',
  foo: function () {
    console.log(this)
    console.log(this.name)
  },
  bar: () => {
    console.log(this)
    console.log(this.name)
  }
}

test.foo()
// test对象
// test
test.bar()
// window对象
// 空
```

##  Object

### property

- 在 ES6 之前 Object 的属性必须是 key-value 形式
- 在 ES6 之后是可以用简写的形式来声明键值对

```js
let x = 1
let obj = {
  x,
  say () {
    console.log('hello')
  }
}
console.log(obj)// {x: 1, say: f}
obj.say()// hello
```

### 对象拷贝

`Object.assign()`：对象浅拷贝

## Set

ES6新增的数据结构，也叫集合，类似于数组，但它的每个成员都是唯一的。

```js
let s = new Set()
```

### 属性方法

- `size`：Set实例的成员个数

- `add(value)`：添加元素，添加重复的数据是无效的
- `delete(value)`：刪除元素
- `clear()`：清空Set
- `has(value)`：判断Set的成员是否存在
- 修改成员只能先删除后添加

### 遍历

- `keys()`：返回键名的遍历器
- `values()`：返回键值的遍历器
- `entries()`：返回键值对的遍历器
- `foreach()`：回调函数遍历每个成员

```js 
let set = new Set(['red', 'blue', 'green'])

console.log(set.keys())
console.log(set.values())
console.log(set.entries())
//SetIterator {"red", "blue", "green"}
//SetIterator {"red", "blue", "green"}
//SetIterator {"red" => "red", "blue" => "blue", "green" => "green"}

for (let item of set.values()) {
  console.log(item)
}
// red bulue green

for (let item of set.keys()) {
  console.log(item)
}
// red bulue green

for (let item of set.entries()) {
  console.log(item)
}
// ["red", "red"]
// ["blue", "blue"]
// ["green", "green"]

set.forEach((value, key) => {
  console.log(value + '1')
})
// red1 blue1 green1
```

## Map

又称字典。

- ES5中的Object对象是一种“**字符串--值**”的键值对对应，只接受字符串作为键名。
- ES6提供了新的数据结构Map，它是“**值--值**”的键值对对应，键名可以接受各种类型的值（甚至是对象），它比Object内容更加丰富灵活。也是一种Hash结构。

```js
let map = new Map([
  ['name', 'Tom'],
  [{ num: 2 }, '3']
])
console.log(map)
/*
Map(2) {"name" => "Tom", {…} => "3"}
[[Entries]]
0: {"name" => "Tom"}
1: {Object => "3"}
size: (...)
__proto__: Map
*/
```

### 属性方法

- `size`：Map成员总数

- `set(key, value)`：设置新的键值对，若已存在，则更新键值对
- `get(key)`：获取key的键值，不存在则返回undefined
- `has(key)`：判断key是否存在
- `delete(key)`：删除某个键，成功返回true，否则false
- `clear()`：清空Map

### 遍历

- `keys()`：返回键名的遍历器
- `values()`：返回键值的遍历器
- `entries()`：返回键值对的遍历器
- `foreach()`：遍历Map所有成员

```js
const map = new Map([
  ['F', 'no'],
  ['T', 'yes']
])

for (let item of map.keys()) {
  console.log(item)
}
// F T

for (let item of map.values()) {
  console.log(item)
}
// no yes

for (let item of map.entries()) {
  console.log(item)
}
// ['F', 'no'],
// ['T', 'yes']

map.forEach((value, key, map) => {
  console.log(key, value)
})
// F no
// T yes
```



