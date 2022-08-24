# Flex布局笔记

​		很早的时候大家都在使用传统的盒模型布局，但是目前已经是移动端市场的天下了，作为一名合格的前端工程师，使用更具备弹性的Flex布局是必要的，今天将彻底总结Flex布局相关技术。首先要说的是，当你学会了Flex布局，你将几乎可以解决需要的任何布局，话不多说开整。



## 名词

​		对于Flex布局里面有很多概念，第一步先按照协议约定去熟悉一些名词（有些名词代表的事情可能是你所曾理解的其他名字，但我们既然涉及到这个技术时就需要遵守名词的定义叫法，这个就叫专业）

​		**Flex**——弹性布局

​		**容器**（flex container）——使用flex布局的元素称为容器

​		**项目**（flex item）——在容器内的所有子元素称为项目（我们都知道是元素，但是以后改口就叫“项目”）

<img src="image\flex_20220409151145.jpg" style="zoom:50%;" />

​		容器内存在两根轴，这就区别于盒模型（好好理解一下）分别为：

​		**主轴**（main axis）——默认情况下主轴是水平的

​		**交叉轴**（cross axis）——默认情况下交叉轴是垂直的

<img src="image\flex_20220409151605.jpg" style="zoom:50%;" />

​		默认情况下容器内的项目沿着主轴方向排列。

## CSS属性

​		Flex的CSS属性分为两部分，一部分是作用在容器上的**容器属性**，还有一部分是作用在项目上**项目属性**。

### 容器属性

#### 1.display

​		首先要使用Flex布局，在容器的属性display必须设置为 flex 或 inline-flex。

​		块元素使用 flex；

​		内联元素使用 inline-flex；

​		在设置了 flex 后，项目的float、clear属性都将失效。

```css
.container {
    display:flex | inline-flex;
}
```

例子：

```html
<head>
    <style type="text/css">
    .container {
        display: flex;
        border: 1px black solid;
        width: 500px;
    }

    .item {
        width: 100px;
        height: 100px;
        background-color: paleturquoise;
        margin: 20px;
    }
    </style>
</head>
<div class="container">
    <div class="item">flex item</div>
    <div class="item">flex item</div>
    <div class="item">flex item</div>
</div>
```

效果图：

<img src="image\flex_20220409152444.png" />

#### 2.flex-direction

​		flex_direction用于指定主轴的方向，默认为 row，水平方向。项目始终沿着主轴的方向排列。

```css
.container {
    flex-direction: row | row-reverse | column | column-reverse;
}
```

​		row——水平方向；

​		row-reverse——水平方向，但项目从右到左边排列，最右边是第一个项目，第一个项目紧贴容器的最右边；

​		column——垂直方向；

​		column-reverse——垂直方向，项目从下往上排列，最下面是第一个项目，第一个项目紧贴容器的最下边。

#### 3.flex-wrap

​		flex-wrap用于确定项目元素是否换行展示，当所有的项目在主轴（以水平方向主轴为例）的宽度之和超过了容器的宽度，通过flex-wrap来决定是否换行显示。

```css
.container {
    flex-wrap: no-wrap | wrap | wrap - reverse;
}
```

​		no-wrap（默认）——不换行，意味着若项目超出容器宽度，会自动缩小item的宽度；

​		wrap——换行，项目排列的宽度超过容器宽度时就会另起一行；

​		wrap-reverse——换行，会向上换行。

​		*在设置为no-wrap属性时，既然项目一行的总宽度已经超过了容器，意味着每一个项目都被直接或者间接的设置了width属性（有width才会溢出），此时会自动缩小item的宽度这就说明no-wrap对项目的宽度影响优先级是非常高的！*

​		此时我们做一个有趣的实验看下，若项目的宽度是不相等的，观察下会如何压缩？

```html
<head>
    <style type="text/css">
    .container {
        display: flex;
        border: 1px black solid;
        width: 300px;
    }
    .item1 {
        width: 50px;
        height: 100px;
        background-color: paleturquoise;
        margin: 20px;
    }
    .item2 {
        width: 100px;
        height: 100px;
        background-color: paleturquoise;
        margin: 20px;
    }
    .item3 {
        width: 150px;
        height: 100px;
        background-color: paleturquoise;
        margin: 20px;
    }
    </style>
</head>
<div class="container">
    <div class="item1">flex item 1</div>
    <div class="item2">flex item 2</div>
    <div class="item3">flex item 3</div>
</div>
```

<img src="image\flex_20220409154144.png" />

​		结果就是，3个项目的总宽度不会超过容器，而3个项目被压缩后，仍然保持了原来的三者宽度的相对关系！

#### 4.flex-flow

​		flex-flow是对flex-direction与flex-wrap的缩写，例如：

```css
.container {
    flex-flow: column wrap;
}
```

#### 5.justify-content

​		justify-content用于指定item在主轴的对齐方式，item可沿主轴：

​	flex-start——开始方向对齐

​	flex-end——结束方向对齐

​	center——居中对齐

​	space-between——两端对齐

​	space-around——两侧对齐

```css
.container {
    justify-content: flex-start;  //默认值
}
```

<img src="image\flex_20220824-163946.png" />