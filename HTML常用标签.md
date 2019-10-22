---
title: HTML常用标签
date: 2018-10-07 11:49:03
tags:
---

## iframe 和 a
1.  iframe 标签通常用于**嵌套页面**
2.  framborder 属性设置为0用于消除边框
3.  **name** 属性与a标签**结合**起来
> target="xxx"         #跳转到name为xxx的窗口或iframe
> target="\_blank"     #在新的窗口打开
> target="\_self"      #在当前窗口打开
> target="\_parent"    #在父级窗口打开
> target="\_top"       #在顶层窗口打开

        <iframe src="https://www.baidu.com" name="xxx"></iframe>
        <a target="xxx" href="http://qq.com">QQ</a>


4.  a标签的download属性提供下载功能

        <a herf="./test.png" download>test</a>

5.  a标签的href属性可以发送http请求(只有**锚点**不发送请求，其他全部发送**get**       <请求)
> href="#"     #跳到页面顶部，锚点变为"#"
> href=""      #刷新页面，跳转自身
> href="//qq.com"  #以当前协议访问qq.com
> href="?q=xxx"    #发送get请求,在地址栏显示
> href="javascript:;"  #鼠标点击什么也不做,javascript**伪协议**

## form 和 table
1.  ### form
>   除了a标签可以发送请求外，form标签也可以发送请求，通常是post请求
>   action 指定请求路径，请求路径中也可加查询参数，method指定请求动词
>   form 中提交信息的标签，必须添加name属性
>   要想发送请求，form标签里必须有提交按钮
>>  form 中有`<button>`,没写type属性，自动升级为提交按钮
>>  form 中如果有`<input>`,只有type为**submit**时,才为提交按钮

        <form action='query' method="post">
            <label>Username<input type="text" name="name"></label>
            <label>Password<input type="password" name="pwd"></label>
            <input type="submit" value="submit">
        </form>


input与文字关联
*   通常写法,id与for关联

        <input type="checkbox" id="xxx"><label for="xxx">Something</label>
*   常用套路,label将input包起来

        <label><input type="checkbox">Something</label>


2.  ### table
    table的全部用法见下图
    ![table](/img/table.png 'table的全部用法')

##  其他标签
*   nav,navigation 用来写导航条
*   ul,un-ordered list 无序列表
*   ol,ordered list    有序列表
*   除了span标签和div标签，其他标签都有默认样式
*   body默认margin为8px
*   strong和b都是加粗，strong表示语气上的强调，b只是单纯加粗
*   空元素   `<img>` `<input>` `<meta>` `<link>` `<br>` `<hr>` `<col>` `<colgroup>`
*   可替代元素  `<img>` `<object>` `<video>` `<textarea>` `<input>`


