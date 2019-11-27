---
title: CSS-REM与动态REM
date: 2018-11-01 14:01:13
tags: CSS
categories: CSS
---


# 什么是em

> 相对长度单位，这个单位表示元素的font-size的计算值。如果用在font-size 属性本身，它会继承父元素的font-size。

# 什么是rem

> 这个单位代表根元素的 font-size 大小（例如 <html> 元素的font-size）。当用在根元素的font-size上面时 ，它代表了它的初始值

所以em与rem没有任何关系

# 动态rem

> 动态rem是通过js动态改变html的font-size,也就是改变rem，一般是改为页面宽度的十分之一，确保最小字号不会小于12px。

动态rem可以用于移动端页面布局

如果页面宽度是1000px,将rem设为 1000px / 10 => 100px 所以1em就是100px, 将一个div元素设为3em,他就会占页面的十分之三 

这样元素的宽高都以屏幕宽度为基准,对于不同的移动设备,屏幕宽度不同,页面元素都会等比例缩放

代码如下

    <scrtip>
      document.write(`
          <style>
              html {
                  font-size: ${window.innerWidth/10}px
              }
          </style>
      `)
    </script>

这样就可以让rem为页面宽度的十分之一

# 利用scss 函数设置元素rem值

一般ui设计稿都是给定宽度的,我们不能一一计算每个元素的比例,这时候就可以通过scss里的函数自动计算比例.
假设设计稿的页面宽度为640px,xxx元素的宽度为320px(二分之一), 高度为160px(四分之一)。

    $pageWidth = 640
    function px2rem(px){
      return px/$pageWidth * 10 + rem 
    }

    .xxx {
      width: px2rem(320);  // 5em
      height: px2rem(160); // 2.5em
    }


