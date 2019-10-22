---
title: 几个关于CSS的套路
date: 2018-10-08 22:28:21
tags:
---

##  横向布局
1. 将子元素全部浮动起来
2. 父元素添加 clearfix 属性
3. 给clearfix类添加三行样式,清除浮动带来的影响

    <head>
        ...
        <style>
            clearfix::after {
                content: '';
                display: block;
                clear: both;
            nav>ul>li {
                    float: left;
                }
       </style>
    </head>
    <body>
        <nav>
            <ul class="clearfix">
                <li></li>
                <li></li>
                <li></li>
                <li></li>
                <li></li>
            </ul>
        </nav>
    </body>
>   如果是两列显示在一行可以将li的高度设为50%;

##  文档流
>   文档流就是文档内元素的流动方向，内联:左->右,块级:上->下
>   脱离文档流会影响body的高度
>   脱离文档流会导致宽度收缩，需设置`width: 100%;`解决

##  元素的高度
*   块级元素的高度由其内部文档流元素高度的总和**决定**
*   内联元素的高度

##  position 定位
*   sticky      粘性定位
*   static      当前位置
*   relative    相对定位(参照自身原来的位置)
*   absolute    绝对定位(参照父级)
*   fixed       固定定位(参照浏览器)
>   通常将父级元素设置为相对定位，子元素设置为决定定位，子元素参照父级设定位置

##  背景图片居中，自适应

    background-position: center center;
    background-size: cover;

##  引用SVG图标
常用的网站：[iconfont.cn](http://www.iconfont.cn)

##  其他很实用的小技巧
-   max-height,max-weight
-   一般不给元素设定固定的height和width,容易出bug
>   通常从内向外加pading，将元素撑到指定宽高
-   内联元素不接受宽高度, 设置margin-top和margin-bottom不生效
>   display: block / inline-block;
-   hr 一条横线,样式设置

    hr {
        height: 0;
        border: none;
        border-top: 1px solid red;
        }

-   `work-break: break all`
>   对于non-CJK (CJK 指中文/日文/韩文) 文本，可在任意字符间断行。
