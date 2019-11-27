---
title: CSS-BFC与IFC
date: 2019-11-01 16:53:50
tags: CSS
categories: CSS
---

# BFC (Block Formatting Context) 

{% asset_img mdn_bfc.png MDN对BFC的描述 %}

所以我们没有办法说明什么是BFC, 但我们知道如何创建一个BFC。

比如 `overflow: hidden` 的块级元素,但是我们只想让他变成BFC,发挥自己的作用,这个样式却会导致其他问题。 `display: flow-root`,可以单纯将一个元素变成一个BFC。

那BFC有什么作用呢

## BFC作用1-父元素包住子元素

我们知道如果子元素是浮动元素,父元素不设置高度,就不会被子元素撑起来,就不会包住子元素,这会导致很多问题。我们通常利用伪元素清楚浮动，但这里我们可以通过将父元素变为BFC的方式包住子元素

{% asset_img child_in_parent.png 将父元素变为BFC包住子元素 %}


## BFC作用2-兄弟节点划清界限

两个块级元素一个浮动,浮动元素就会飘到另一个不浮动元素上面,会导致有重合部分,或挡住另一元素

{% asset_img before_seprate_brother.png 兄弟节点之间有重合部分 %}

将不浮动的节点变为BFC,两个节点就会分开,就不会让兄弟元素挡住自己

{% asset_img seprate_brother.png 兄弟节点划清界限 %}

通俗来说,一个块级元素变为BFC后,里面的元素出不去,外面的元素进不来

# IFC (Inline Formatting Context)