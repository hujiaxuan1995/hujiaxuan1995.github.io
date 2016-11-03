title: CSS background-position属性
date: 2016-01-22 18:08:01
tags: css
categories: css
---
#定义
background-position 属性用于设置背景图像的起始位置。
这个属性在写轮播或一些特效的时候经常用到。然而并不能让我深刻地记住它
索性就写下来，忘记的时候可以来看看。
#用法
`
background-position: x y;
`

x y 的值可以是left,right,top,bottom center,也可以是相对值%或数字
x y坐标的值为图片相对容器的偏移量例如 

`
background: transparent url(bg.jpg) no-repeat scroll -70px -40px 
`

图片以容器左上角为参考向左偏移70px，向上偏移40px。

`
background-position: 50% 50%;
`

图片水平和垂直居中，与 background-position: center center 效果相同
等同于x：{容器(container)的宽度—背景图片的宽度}\*x百分比，超出的部分隐藏。
等同于y：{容器(container)的高度—背景图片的高度}\*y百分比，超出的部分隐藏。

`
background-position: -50% -50%;
`

等同于x：-{容器(container)的宽度—背景图片的宽度}\*x百分比，超出的部分隐藏。
等同于y：-{容器(container)的高度—背景图片的高度}\*y百分比，超出的部分隐藏。

