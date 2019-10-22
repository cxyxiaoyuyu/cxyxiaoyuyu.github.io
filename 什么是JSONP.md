---
title: 什么是JSONP
date: 2019-03-19 23:36:26
tags:
---

## 先简单说下跨域问题
> 1. 协议相同
> 2. 域名相同
> 3. 端口相同

满足以上三个就是同源

### 可以发请求的HTML标签
> form表单
> a标签
> link标签
> img标签
> script标签

## 基本需求

比如我有100元，我每次点击付钱的按钮就会支付一元，后台将这100元存于数据库(本文中存于txt)
假设页面有一个id为button的button按钮

在最开始的前端

### form表单发请求
    我们都知道form表单可以发post或get请求到后台,但是会刷新页面，为了更好的用户体验我们怎么能每次变更数据都刷新页面呢，所以得想办法局部更新呀。

## 创建img标签发请求(get)

        前端代码
        button.addEventListener('click', (e)=>{
            let image = document.createElement('img')
            image.src = '/pay'
            image.onload = function(){ // 状态码是 200~299 则表示成功
                remain.innerText = remain - 1
            }
            image.onerror = function(){ // 状态码大于等于 400 则表示失败
                alert('失败')
            }
        })

        后端代码





    这种方法虽然可以实现，但是每此都要请求一个图片，当你返回不是图片类型的时候，浏览器也不是那么傻，它就报错啦，我们每一次都返回一个图片，总感觉多次一举。emmmmmm....有没有什么更好的办法呢,试试创建script标签发请求吧。

## 创建script标签发请求

        前端代码
        button.addEventListener('click', (e)=>{
            let script = document.createElement('script')
            script.src = '/pay'
            document.body.appendChild(script)
            script.onload = function(e){ // 状态码是 200~299 则表示成功
                e.currentTarget.remove() // 发一次请求就会多一个script标签，需要删除
                remain.innerText = remain - 1
            }
            script.onerror = function(e){ // 状态码大于等于 400 则表示失败
                e.currentTarget.remove()
            }
       })

        后端代码

### 为什么不直接返回script脚本呢
    既然后端返回给前端的是js,嵌入到页面时脚本就会执行，我何必多写一个onload函数，好像有点道理哎~
    那就后端来写局部刷新代码吧








    写完后，后端不开心了,作为后端，我只想做后端的事，这样前后端发生了耦合，如果后端程序员不熟悉前端，那不就写不下去了嘛，怎么办呢，解耦合呀。


### 解决前后端耦合 => Jsonp技术的由来
    怎么解耦合呢，那前端想了，说我们封装一个函数吧，你们成功后就调用一下这个回调函数，把要返回的值放在回调函数的第一个参数里。这样的话，后端说好呀，那你们封装好函数，函数名传给我，我成功就调用一下。



    以上，这个技术就叫jsonp啦，总结一下


    请求方：frank.com 的前端程序员（浏览器）
    响应方：jack.com 的后端程序员（服务器）

    请求方创建 script，src 指向响应方，同时传一个查询参数 ?callbackName=yyy
    响应方根据查询参数callbackName，构造形如
    yyy.call(undefined, '你要的数据')
    yyy('你要的数据')
    这样的响应
    浏览器接收到响应，就会执行 yyy.call(undefined, '你要的数据')
    那么请求方就知道了他要的数据


### 约定

    哎呦,还不错，就是我们能不能做些规定,前端传过来回调函数的key值能不能统一,如下:

    callbackName -> callback
    yyy -> 随机数 function12312312312321325() (为了不重复)


### jquery封装的API

    $.ajax({
        url: "http://jack.com:8002/pay",
        dataType: "jsonp",
        success: function( response ) {
            if(response === 'success'){
            amount.innerText = amount.innerText - 1
            }
        }
    })

