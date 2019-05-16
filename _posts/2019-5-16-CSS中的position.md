---
layout:     post
title:      CSS中的position
subtitle:   深入学习position属性
date:       2019-5-16
author:     梦尽头
header-img: img/post-bg-swift2.jpg
catalog: true
tags:
    - vue
---

## 前言
CSS `position`属性用于指定一个元素在文档中的定位方式。`top`，`right`，`bottom` 和 `left` 属性则决定了该元素的最终位置。

## 语法
```css
position : static | relative | absolute | fixed | sticky
```

## 取值

#### `static `
`static `是`position`的**默认值**，没有定位，元素出现在正常的文档常规流中，此时 `top`,` right`, `bottom`, `left` 和 `z-index `属性无效。

#### `relative`
也叫**相对定位**，设置了`relative`属性的元素相对于在未添加任何定位时的位置，会在不改变页面布局的前提下调整元素位置，因此会在此元素未添加定位时所在位置留下空白。
`position:relative` 对 `table-*-group`, `table-row`, `table-column`, `table-cell`, `table-caption` 元素无效。

#### `absolute`
也叫**绝对定位**，设置了`absolute`属性的元素，不为其预留空间，相对于它最近的非 `static` 定位的父元素偏移，来确定元素位置。
 `absolute` 与 `fixed` 的表现类似，但是`fixed`是相对于浏览器视窗来进行定位的。

#### `fixed`
也叫**固定定位**，设置了`fixed`属性的元素，不为其预留空间，而是相对于屏幕视口（`viewport`）的位置来指定元素位置。元素的位置在屏幕滚动时不会改变。

#### `sticky`
也叫**粘性定位**，粘性定位可以被认为是相对定位和固定定位的混合。元素在跨越特定阈值前为相对定位，之后为固定定位。

##  [演示](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position)