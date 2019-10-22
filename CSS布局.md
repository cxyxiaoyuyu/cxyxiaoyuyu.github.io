---
title: CSS布局
date: 2018-10-13 00:50:02
tags:
---
*  左右布局
*  左中右布局
*  水平居中
*  垂直居中

## 左右布局

    <div class="clearfix">
        <div class="a">左</a>
        <div class="b">右</a>
    </div>

    .clearfix {
        content: '';
        display: block;
        clear: both;
    }
    .a,
    .b {
            width: 50%;
            height: 200px;
            float: left;
    }
>   父元素的class设为clearfix
>   子元素全部设为左浮动,可设置width的百分比决定每个子元素占据的宽度

### 左中右布局(三列布局)

    <div class="left">左</div>
    <div class="middle">中</div>
    <div class="right">右</div>

    .left{
        width: 200px;height: 300px; background: yellow; float: left;
    }
    .right{
        width: 170px; height: 300px; background: green; float: right;
    }
    .middle{
        height: 300px; background: red; margin-left: 220px; margin-right: 180px;
    }
>   通过给左右两边设定宽度,左右元素分别向左右浮动
>   中间元素设置左右边距

###  水平居中
*   内联元素
给该内联元素所在的块父级元素添加`text-align: center;` 样式
*   块级元素
给该元素添加`margin: 0 auto;` 样式

### 垂直居中
*   居中文字
将该元素的`line-height`设为与`height` 一样的值
*   块级元素

    .parent {
        position: relative;
    }
    .child {
        position: absolute;
        top: 50%;
        transform: translateY(-50%);
    }

