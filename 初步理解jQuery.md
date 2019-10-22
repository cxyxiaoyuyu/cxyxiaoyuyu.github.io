---
title: 初步理解jQuery
date: 2018-12-05 21:28:30
tags:
---

## 封装addClass函数

        function addClass(node,classes) {
            classes.forEach( (value) => node.classList.add(value) )
        }

        let divNode = document.getElementById('xxx')
        addClass(divNode,['a','b','c'])

## 命名空间
    > 如果别人也写了addClass这个函数，就会重复，所以需要命名空间

        window.xiaoyu = {}
        xiaoyu.addClass = function(node,classes) {
            classes.forEach( (value) => node.classList.add(value) )
        }

        let divNode = document.getElementById('xxx')
        xiaoyu.addClass(divNode,['a','b','c'])

## 能不能把node放在前面
#### 方法一 扩展node接口

        Node.prototype.addClass = function(classes){
            classes.forEach( (value) => this.classList.add(value) )
        }

        let divNode = document.getElementById('xxx')
        divNode.addClass(['a','b','c'])

#### 方法二  返回一个新的node对象

        function newNode(node){
            newNode = {
                0: node,
                length: 1,
            }
            newNode.addClass = function(classes){
                classes.forEach( (value) => this.classList.add(value) )
            }
            return newNode
        }

        let divNode = document.getElementById('xxx')
        let newnode = newNode(divNode)
        newnode.addClass(['a','b'])

## 给生成newNode对象的函数改个名字吧

        function jQuery(node){
            newNode = {
                0: node,
                length: 1,
            }
            newNode.addClass = function(classes){
                classes.forEach( (value) => this.classList.add(value) )
            }
            return newNode
        }

        window.$ = jQuery
        let divNode = document.getElementById('xxx')
        let $newnode = $divNode)
        $newnode.addClass(['a','b'])

## 让这个参数可以接收selector，以及元素结点

        window.jQuery = function(nodeOrSelector){
            if(typeof nodeOrSelector === 'string'){
                temp = document.querySelectorAll(nodeOrSelector)
                    var nodes = {}
                for(var i=0;i<temp.length;i++){
                    nodes[i] = temp[i]
                }
                nodes.length = temp.length
            }else{
                console.log(2)
                    nodes = {
                        0: nodeOrSelector,
                        length: 1
                    }
            }

            // 参数可以不为数组
            nodes.addClass = function(){
                let classes = []
                for(let i=0;i<arguments.length;i++){
                    classes.push(arguments[i])
                }
                classes.forEach((value)=> {
                    for(let i=0;i<nodes.length;i++){
                        nodes[i].classList.add(value)
                    }
                })
            }

            // 获取文本或设置文本内容
            nodes.text = function(text){
                if(typeof text === 'undefined'){
                    let texts = []
                    for(let i=0;i<nodes.length;i++){
                        texts.push(nodes[i].textContent)
                    }
                    return texts
                }
                else{
                    for(let i=0;i<nodes.length;i++){
                        nodes[i].textContent = text
                    }
                }
            }

            return nodes
        }

        window.$ = jQuery
        let $div = $('div')

        $div.addClass('a','b')
        $div.text('hello jquery')






