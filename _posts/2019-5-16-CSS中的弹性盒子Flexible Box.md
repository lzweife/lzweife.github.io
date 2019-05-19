---
layout:     post
title:      CSS中的弹性盒子Flexible Box
subtitle:   深入学习弹性盒模型
date:       2019-5-16
author:     梦尽头
header-img: img/post-bg-swift2.jpg
catalog: true
tags:
    - CSS
---

## 前言
弹性布局是指通过调整其内元素的宽高，从而在任何显示设备上实现对可用显示空间最佳填充的能力。弹性容器扩展其内元素来填充可用空间，或将其收缩来避免溢出。

## 相关术语
![示意图](https://mdn.mozillademos.org/files/12998/flexbox.png)
#### 弹性容器`Flex container`
指的是包含着弹性项目的父元素。
通过设置 `display` 属性的值为 `flex` 或 `inline-flex` 来定义弹性容器。

#### 弹性项目`Flex item`
弹性容器的每个子元素都称为弹性项目。
弹性容器直接包含的文本将被包覆成匿名弹性单元。

#### 轴`Axis`
每个弹性框布局包含两个轴。
弹性项目沿其依次排列的那根轴称为**主轴(`main axis`)**。
垂直于主轴的那根轴称为**侧轴(`cross axis`)**。
- `flex-direction`确立主轴。
- `justify-content` 定义了在当前行上，弹性项目沿主轴如何排布。
- `align-items` 定义了在当前行上，弹性项目沿侧轴默认如何排布。
- `align-self` 定义了单个弹性项目在侧轴上应当如何对齐，这个定义会覆盖由 `align-items` 所确立的默认值。

#### 方向`Direction`
弹性容器的**主轴起点(main start)/主轴终点(main end)**和**侧轴起点(cross start)/侧轴终点(cross end)**描述了弹性项目排布的起点与终点。它们具体取决于弹性容器的主轴与侧轴中，由 `writing-mode` 确立的方向（从左到右、从右到左，等等）。

- `order` 属性将元素与序号关联起来，以此决定哪些元素先出现。
- `flex-flow` 属性是 `flex-direction` 和 `flex-wrap` 属性的简写，决定弹性项目如何排布。

#### 行`Line`
根据 ``flex-wrap` 属性，弹性项目可以排布在单个行或者多个行中。此属性控制侧轴的方向和新行排列的方向。

#### 尺寸`Dimension`
根据弹性容器的主轴与侧轴，弹性项目的宽和高中，对应主轴的称为**主轴尺寸(main size)** ，对应侧轴的称为 **侧轴尺寸(cross size)**。

- `min-height` 与 `min-width` 属性初始值将为 0。
- `flex` 属性是 `flex-grow`、`flex-shrink` 和 `flex-basis` 属性的简写，描述弹性项目的整体的伸缩性。

## 定义弹性盒子
```css
display : flex
display : inline-flex
//注意：厂商前缀标记会附加给 display 属性值，而不是加给 display 属性本身。例如：display : -webkit-flex。
```

这样做将元素定义为弹性容器，其子元素则成为弹性项目。
值 `flex` 使弹性容器成为块级元素。值 `inline-flex` 使弹性容器成为单个不可分的行内级元素。

由于弹性盒子使用了不同的布局算法，某些属性用在弹性容器上没有意义：

- 多栏布局模块的 `column-* `属性对弹性项目无效。
- `float` 与 `clear` 对弹性项目无效。使用` float `将使元素的` display `属性计为`block`。
- `vertical-align` 对弹性项目的对齐无效。

## `flex-grow`

`flex-grow` 属性定义弹性项目（flex item）的拉伸因子，初始值为0，`<number>`类型，负值无效。
![flex-grow](https://lzweife.github.io/img/flex-grow.png)
[演示地址](https://codepen.io/pen/?&editable=true)

## `flex-shrink`

`flex-shrink` 属性指定了 flex 元素的收缩规则，初始值为1，`<number>`类型，负值无效。
flex 元素仅在默认宽度之和大于容器的时候才会发生收缩，其收缩的大小是依据` flex-shrink` 的值。
![flex-shrink](https://lzweife.github.io/img/flex-shrink.png)
[演示地址](https://codepen.io/anon/pen/MdvymX?&editable=true)

## `flex-basis`

`flex-basis` 指定了 flex 元素在主轴方向上的初始大小。
如果不使用 `box-sizing` 来改变盒模型的话，那么这个属性就决定了 flex 元素的内容盒（content-box）的宽或高（取决于主轴的方向）的尺寸大小。
初始值为`auto`。

#### 取值
- 可以是一个数字后面跟着绝对单位例如 px, mm, pt; 
- 也可以是一个百分数，那么这个百分数就是相对于其父弹性盒容器的宽或者高（取决于主轴方向）。负值是不被允许的。
- `auto`：基于 flex 的元素的内容自动调整大小。

```html
<ul class="container">
  <li class="flex flex1">1: flex-basis test</li>
  <li class="flex flex2">2: flex-basis test</li>
  <li class="flex flex3">3: flex-basis test</li>
  <li class="flex flex4">4: flex-basis test</li>
  <li class="flex flex5">5: flex-basis test</li>
  <li class="flex flex7">7: flex-basis test</li>
</ul>

<ul class="container">
  <li class="flex flex6">6: flex-basis test</li>
</ul>
```
```css
.container {
  font-family: arial, sans-serif;
  margin: 0;
  padding: 0;
  list-style-type: none;
  display: flex;
  flex-wrap: wrap;
}

.flex {
  background: #6AB6D8;
  padding: 10px;
  margin-bottom: 50px;
  border: 3px solid #2E86BB;
  color: white;
  font-size: 20px;
  text-align: center;
  position: relative;
}

.flex:after {
  position: absolute;
  z-index: 1;
  left: 0;
  top: 100%;
  margin-top: 10px;
  width: 100%;
  color: #333;
  font-size: 18px;
}

.flex1 {
  flex-basis: auto;
}

.flex1:after {
  content: 'auto';
}

.flex2 {
  flex-basis: -webkit-max-content;
  flex-basis: -moz-max-content;
  flex-basis: max-content;
}

.flex2:after {
  content: 'max-content';
}

.flex3 {
  flex-basis: -webkit-min-content;
  flex-basis: -moz-min-content;
  flex-basis: min-content;
}

.flex3:after {
  content: 'min-content';
}

.flex4 {
  flex-basis: -webkit-fit-content;
  flex-basis: -moz-fit-content;
  flex-basis: fit-content;
}

.flex4:after {
  content: 'fit-content';
}

.flex5 {
   flex-basis: 20%;
}

.flex5:after {
  content: '20%';
}

.flex7 {
   flex-basis: 300px;
}

.flex7:after {
  content: '300px';
}

.flex6 {
  flex-basis: -webkit-fill-available;
  flex-basis: -moz-available;
  flex-basis: fill;
}

.flex6:after {
  content: 'fill/-webkit-fill-available/-moz-available';
}
```
![flex-basis](https://lzweife.github.io/img/flex-basis.png)

## `flex`

CSS属性 `flex` 规定了弹性元素如何伸长或缩短以适应flex容器中的可用空间。
这是一个简写属性，用来设置 `flex-grow`, `flex-shrink` 与 `flex-basis`。

#### 语法

`flex` 属性可以指定1个，2个或3个值。

**单值语法** ：
- 一个无单位数(`<number>`): 它会被当作`<flex-grow>`的值。
- 一个有效的宽度(`width`)值: 它会被当作 `<flex-basis>`的值。
- 关键字`none`，`auto`或`initial`。

**双值语法**：

第一个值必须为一个无单位数，并且它会被当作`<flex-grow>` 的值。
第二个值必须为以下之一：

- 一个无单位数：它会被当作 `<flex-shrink>` 的值。
- 一个有效的宽度值: 它会被当作 `<flex-basis>` 的值。

**三值语法**:

- 第一个值必须为一个无单位数，并且它会被当作 `<flex-grow>` 的值。
- 第二个值必须为一个无单位数，并且它会被当作  `<flex-shrink>` 的值。
- 第三个值必须为一个有效的宽度值， 并且它会被当作 `<flex-basis>` 的值。

#### 取值

- `initial`
元素会根据自身宽高设置尺寸。它会缩短自身以适应 flex 容器，但不会伸长并吸收 flex 容器中的额外自由空间来适应 flex 容器 。
相当于将属性设置为"`flex: 0 1 auto`"。

- `auto`
元素会根据自身的宽度与高度来确定尺寸，但是会伸长并吸收 flex 容器中额外的自由空间，也会缩短自身来适应 flex 容器。
这相当于将属性设置为 "`flex: 1 1 auto`".

- `none`
元素会根据自身宽高来设置尺寸。它是完全非弹性的：既不会缩短，也不会伸长来适应 flex 容器。
相当于将属性设置为"`flex: 0 0 auto`"。
`<'flex-grow'>`
定义 flex 元素的 `flex-grow` 属性，详见 `<number>`。默认值为 0，负值无效。
`<'flex-shrink'>`
定义 flex 元素的` flex-shrink` 属性，详见 `<number>`。默认值为1，负值无效。
`<'flex-basis'>`
定义 flex 元素的 `flex-basis` 属性。若值为0，则必须加上单位，以免被视作伸缩性。 默认值为 `auto`。
