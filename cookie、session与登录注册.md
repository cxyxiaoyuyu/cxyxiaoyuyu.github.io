---
title: cookie、session与登录注册(基于Node.js)
date: 2019-10-28 14:56:36
tags:
  - cookie
  - session
categories: 
---


本文代码基于Node.js,参考[完整代码](https://github.com/cxyxiaoyuyu/login_register)

### 登录与注册流程

#### 注册流程
* 注册时传入用户名,密码,确认密码
* 注册成功将用户名与密码存入数据库
* 密码与确认密码不同，或已经注册的用户，返回400

#### 注册具体实现

客户端传递注册时需要的数据

    let value = ['username','password','password_confirm']
      value.forEach(name=>{
          data[name] = $(`input[name=${name}]`).val()
      })
      $.post({
          url: './sign_up',
          type: 'post',
          data
      }).then( response => {
          window.location.href = './sign_in'
      }).fail( error => {
          alert(error.responseText)
      })

服务端获取数据readBodyData

    // 因为数据是一个一个上传的
    function readBodyData(request){
      return new Promise(function(resolve,reject){
        let data = ''
        request.on('data',chunk => {
          data += chunk.toString()
        })
        request.on('end', ()=>{
          let params = data.split('&')
          let hash = {}
          params.forEach(value => {
            let temp = value.split('=')
            hash[temp[0]] = temp[1]
          })
          resolve(hash)
        })
      })
    }
  
读取数据库文件,判断用户是否已经注册

    // else if(path === '/sign_up' && method === 'POST')

    readBodyData(request).then(data => {   
       let { username,password,password_confirm } = data

      let users = JSON.parse(fs.readFileSync('./database.json','utf8') || "[]")
      let userExist = false
      for(i=0;i<users.length;i++){
        if(users[i].username === username){
          userExist = true
        }
      }

      // 已经注册返回400,提示已经注册,注册成功将用户名密码写入数据库

      if(userExist){
          response.statusCode = 400
          response.setHeader('Content-Type', 'application/json;charset=utf-8')
          response.write('该用户已注册,请勿重复注册')
        }else{
          users.push({username,password})
          fs.writeFileSync('./database.json',JSON.stringify(users))
          response.statusCode = 200
        }
        response.end()
    })

#### 登录流程
* 登录时传入用户名密码
* 如果与数据库匹配，则登录成功，跳转到主页面 
* 如果用户不存在或密码错误，返回404

#### 登录具体实现

客户端传递数据与注册类似

    let value = ['username','password']
    value.forEach(name=>{
        data[name] = $(`input[name=${name}]`).val()
    })
    $.post({
        url: './sign_in',
        type: 'post',
        data
    }).then( response => {
        // 登录成功 前往主页
        window.location.href = './'
    }).fail( error => {
        console.log(error,'error')
        alert(error.responseText)
    })

匹配数据库,用户名密码是否匹配

    readBodyData(request).then(data=>{
      let { username,password } = data

      // 判断数据库是否有该用户
      let users = JSON.parse(fs.readFileSync('./database.json','utf8') || "[]")
      console.log(users)
      let userMatch = false
      for(i=0;i<users.length;i++){
        if(users[i].username === username && users[i].password === password){
          userMatch = true
        }
      }
      if(userMatch){
        response.statusCode = 200
      }else{
        response.setHeader('Content-Type', 'application/json;charset=utf-8')
        response.write('用户名或密码错误')
      }
      response.end()
    })

以上已经实现的基本的登录与注册
但是我们也可以直接输入主页的url访问主页，并不要经过登录啊，有没有什么方法可以判断,用户访问主页，如果登录了就返回主页面，如果没有登录就跳转到登录页面呢？如果我们还想要在主页显示用户的用户名呢？

由于Http是无状态协议,也就是说，服务器单从网络连接上无从知道客户身份。
> HTTP无状态协议，是指协议对于事务处理没有记忆能力。
> 缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。
> 另一方面，在服务器不需要先前信息时它的应答就较快。

如果每次请求的时候,都带上相关标志呢, 让我们来看看cookie是如何工作的吧

### cookie判断是否登录
在登录成功的时候, 设置响应头, 
  
    response.setHeader('Set-Cookie',`username=${username}`)
    
    // 设置多个cookie
    response.setHeader('Set-Cookie',[`username=${username}`,'a=123','b=456'])

这样,客户端在请求同一域名的情况下，都会带上这个cookie
比如在登录成功跳转到主页面的时候发送请求,就会带上刚才拿到的cookie

{% asset_img requestWithCookie.jpg 请求时带上cookie %}


我们也可以在控制台中看到cookie,并且可以随意修改cookie

{% asset_img cookie.jpg 查看所有cookie %}

这时,我们就可以通过请求头的cookie判断用户是否登录

    if(request.headers.cookie){
      let cookies = request.headers.cookie.split('; ')
      let cookieObj = {}
      cookies.forEach(value => {
        let temp = value.split('=')
        cookieObj[temp[0]] = temp[1]
      })
      let username = cookieObj.username

      // 登录成功替换页面用户名
      body = body.replace('__username__',username)
    }else(
      response.writeHead(301, {'Location': './sign_in'})
    )

好啦,通过cookie判断用户是否登录,并且通过cookie可以获取到用户相关信息,当然这个值也是服务器自己设置的

但是刚才说过cookie是不安全的,它是可以手动修改的,如果我们主页显示的是用户的隐私信息呢,比如余额,薪资什么的,只要将cookie的username改为其他人的,不就想看谁的就看谁的了嘛,那不就太危险了,有什么解决办法呢?

### session存储信息
如果我们在服务器设立一小块内存,存储用户的隐私信息,并给每个用户信息设置一个Id标志,每次登录成功,生成一个随机数Id传给客户端,用户每次通过cookie将这个随机数传到服务器,那这个内存就存储了所有的session, 这个随机数标志就是sessionId

因为随机数范围很大,所以用户根本无法篡改
那么现在我们登录成功的需要做的事情是这样,假设`sessions`在内存中是这样的`sessions = {}`

    // 设置sessionId
    let sessionId = Math.random() * 100000
    response.setHeader('Set-Cookie',`sessionId=${sessionId}`)
    // 用session存储用户隐私数据
    sessions[sessionId] = { username,password }

主页面从sessions中获取username

    let username = sessions[cookieObj.sessionId].username


### 总结

> 一般来说，Session 基于 Cookie 来实现。

#### cookie
* 服务器通过 Set-Cookie 头给客户端一串字符串
* 客户端每次访问相同域名的网页时，必须带上这段字符串
* 客户端要在一段时间内保存这个Cookie
* Cookie 默认在用户关闭页面后就失效，后台代码可以任意设置 Cookie 的过期时间
* 大小大概在 4kb 以内

#### session
* 将 SessionID（随机数）通过 Cookie 发给客户端
* 客户端访问服务器时，服务器读取 SessionID
* 服务器有一块内存（哈希表）保存了所有 session
* 通过 SessionID 我们可以得到对应用户的隐私信息，如 id、email
* 这块内存（哈希表）就是服务器上的所有 session





