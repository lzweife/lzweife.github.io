---
layout:     post
title:      JavaScript算法之数组去重
subtitle:   JavaScript实现数组去重的几种方式
date:       2019-8-15
author:     梦尽头
header-img: img/post-bg-swift.jpg
catalog: true
tags:
    - JavaScript算法
---

## 前言

数组操作是JavaScript脚本中的常见操作。以下讲述数组元素去重的几种方式和它们之间的性能差异。

## 双重for循环去重

```javascript
function distinct1(arr) {
    let result = [];
    let len = arr && arr.length;

    for (let i = 0; i < len; i++) {
        for (let j = i + 1; j < len; j++) {
            if (arr[i] === arr[j]) {
                j = ++i;//若元素前后相等，跳过内层循环，i加1，往后遍历
            }
        }
        result.push(arr[i]);
    }
    return result;
}
```

## indexOf去重

```javascript
function distinct2(arr) {
    let result = [];
    arr.forEach((v, i, array) => {
        //每次遍历都使用indexOf从数组i+1的位置开始查找一遍是否存在相同元素，-1表示不存在相同元素
        array.indexOf(v, i+1) === -1 && result.push(v)
    });
    return result
}
```

## sort先排序再去重

首先调用数组的sort方法进行排序， 遍历排序的结果，相邻元素进行对比，若不相等，push进结果数组，否则向后遍历

```javascript
function distinct3(arr) {
    arr = arr.sort();
    let result = arr[0];

    for (let i = 1; i < arr.length; i++) {
        if (arr[i] !== arr[i - 1]) {
            result.push(arr[i])
        }
    }
    return result;
}
```

## 对象法去重

利用对象键名的唯一性进行去重

此方法很快，时间复杂度为O(n)，但是由于会多创建一个对象，会带来额外的内存开销

```javascript
function distinct4(arr) {
    let ret = [];
    let retObj = {};
    let len = arr.length;

    for(let i = 0; i < len; i++) {
        let val = arr[i],
            type = typeof val;
        if(!retObj[val]) {
            ret.push(val);
            retObj[val] = [type];
        } else if(!retObj[val].indexOf(type) < 0) {
            ret.push(val);
            retObj[val] = [type];
        }
    }
    return ret
}
```

## filter去重

利用indexOf的第二个参数去避免不必要的查询

```javascript
function distinct5(arr) {
    return arr.filter((v, i, array) => array.indexOf(v, i+1) < 0)
}
```

利用数组的索引的唯一性来去重

```javascript
function distinct6(arr) {
    return arr.filter((v, i, array) => array.indexOf(v) === i)
}
```

## es6的set去重

```javascript
function distinct7(arr = testArr) {
    return [...new Set(arr)]
}
```


## 测试结果

```javascript
//随机生成100万个元素的随机数组

console.time('随机生成一百万的元素的数组');
var array = [];
for (var i = 0; i < 1000000; i++) {
    array.push(parseInt(Math.random()*100));
}
console.log(array);
console.timeEnd('随机生成一百万的元素的数组');

console.time('两层for循环去重');
console.log(distinct1(array));
console.timeEnd('两层for循环去重');

console.time('indexOf和forEach循环去重');
console.log(distinct2(array));
console.timeEnd('indexOf和forEach循环去重');

console.time('sort先排序再去重');
console.log(distinct2(array));
console.timeEnd('sort先排序再去重');

console.time('对象法去重');
console.log(distinct4(array));
console.timeEnd('对象法去重');

console.time('filter方法去重1');
console.log(distinct5(array));
console.timeEnd('filter方法去重1');

console.time('filter方法去重2');
console.log(distinct6(array));
console.timeEnd('filter方法去重2');

console.time('es6的set方法去重');
console.log(distinct7(array));
console.timeEnd('es6的set方法去重');
```

![去重性能对比](https://lzweife.github.io/img/sort_0.png)











