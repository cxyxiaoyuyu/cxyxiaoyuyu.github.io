---
title: Promise解决回调地狱问题
date: 2019-10-24 14:19:53
tags: 
  - 异步
  - Promise
category: Promise
---

## 回调地狱问题的由来

需求： 封装一个方法，给定文件路径，可以读取文件内容，并把文件内容返回，如果错误返回错误结果

    const fs = require('fs')
    const path = require('path')

    // 普通读取文件的方式
    function getFileByPath(fpath){
      fs.readFile(fpath,'utf-8',(err,data) => {
        if(err) throw err
        console.log(data)
        return data
      })
    }
    
    let result = getFileByPath(path.join(__dirname,'.files/1.txt'))
    console.log(result)

 
这样确实可以读到文件内容，但是`result`却是`undefined`，因为`readFiles`是异步读取的

于是想到在调用`getFileByPath`时添加回调函数

    function getFileByPath(fpath，callback){
      fs.readFile(fpath,'utf-8',(err,data) => {
        if(err) throw err
        callback(data)
      })
    }
    
    getFileByPath(path.join(__dirname,'.files/1.txt'),(data)=>{
      console.log(data)
    })

但是如果error了，`readFile`里的`callback`就不执行了，
需要将成功的结果和失败的结果都返回给用户，所以添加两个回调函数，一个成功调用`succCb`，一个失败调用`errCb`

    function getFileByPath(fpath，succCb, errCb){
      fs.readFile(fpath,'utf-8',(err,data) => {
        if(err) return errCb
        succCb
      })
    }
    
    getFileByPath(path.join(__dirname,'.files/1.txt'),(data)=>{
      console.log(data)
    },(err)=>{
      console.log(err)
    })

看起来很完美呢，如果这时候再加个需求呢，读取文件1成功后，再读取文件2，读取文件2成功后再读取文件3，那么代码将会是下面的样子
    
    getFileByPath(path.join(__dirname,'.files/1.txt'),(data1)=>{
      console.log(data1)

      // 读取文件2
      getFileByPath(path.join(__dirname,'files/2.txt'),(data2)=>{
        console.log(data2)

        // 读取文件3
        getFileByPath(path.join(__dirname,'files/3.txt'),(data3)=>{
          console.log(data3)
        })
      })
    })

哈哈，层层缩进，如果后面还要读取很多文件呢，那么就陷入了回调地狱，看看下面这张图，很形象有木有

{% asset_img callback_hell.jpg 回调地狱 %}

## 用Promise解决回调地狱问题

如果不想让代码变成酱紫，我们就开始使用Promise，其实Promise的本质就是单纯为了解决回调地狱问题，它会让代码变的优雅，并不会减少代码量

    > console.log(Promise)
      ƒ Promise() { [native code] }

可以看出来Promise是一个构造函数，我们通过new Promise可以生成一个Promise的实例

关于Promise更详细的说明 [Promise](http://es6.ruanyifeng.com/?search=fetch&x=0&y=0#docs/promise)

如果用Promise来实现读取三个文件的需求呢

首先构建一个方法，返回一个promise
    
    function getFileByPath(fpath){
      var promise = new Promise(resolve,reject){
        fs.readFile(fpath,'utf-8',(err,data)=>{
          if(err) return reject(err)
          resolve(data)
        })
      }
      return promise
    }
  
开始读取文件

    getFileByPath('files1.txt').then((data1)=>{
      console.log(data1)

      getFileByPath('files2.txt').then((data2)=>{
        console.log(data2)

        getFileByPath('files3.txt').then((data3)=>{
          console.log(data3)
        })
      })
    })

咦，写完了，这好像还是回调地狱啊，纳尼？

    getFileByPath('files1.txt').then( data1 => {
      console.log(data1)
      return getFileByPath('files2.txt')
    }).then( data2 => {
      console.log(data2)
      return getFileByPath('file3.txt')
    }).then( data3 => {
      console.log(data3)
    })

emmmmm...，正确打开方式是这样，是不是很优雅

但是为什么第二个`then`会获取到`getFileByPath('files2.txt')`的结果呢？

> 上面代码中，第一个then方法指定的回调函数，返回的是另一个Promise对象。这时，第二个then方法指定的回调函数，就会等待这个新的Promise对象状态发生变化。如果变为resolved，就调用第一个回调函数，如果状态变为rejected，就调用第二个回调函数。



