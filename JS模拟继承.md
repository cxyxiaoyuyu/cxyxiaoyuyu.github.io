---
title: JS模拟继承
date: 2019-10-30 15:00:52
tags:
  - 原型链
  - js
categories: 原型与原型链
---

# 什么是继承

> 继承（英语：inheritance）是面向对象软件技术当中的一个概念。如果一个类别B“继承自”另一个类别A，就把这个B称为“A的子类”，而把A称为“B的父类别”也可以称“A是B的超类”。继承可以使得子类具有父类别的各种属性和方法，而不需要再次编写相同的代码。

而javascript中没有类,虽然es6引入了class,但那只是一个语法糖。所以javascript不能继承,只能通过原型模拟继承

> 每个实例对象( object )都有一个私有属性(称之为 __proto__ )指向它的构造函数的原型对象( prototype )。该原型对象也有一个自己的原型对象( __proto__ ) ，层层向上直到一个对象的原型对象为 null。根据定义，null 没有原型，并作为这个原型链中的最后一个环节。

比如 

    num = new Number(123)
    num.__proto__ === Number.prototype // true
    Number.prototype.__proto__ === Object.prototype  // true
    Object.prototype.__proto__ === null    // true

    num.toString()  // '123'
  
这里的`num`为什么会有`toString`方法呢,打印一下num看看
{% asset_img num.png num为什么有toString方法 %}

区域1是`Number`的原型对象`Number.prototype`
区域2是`Object`的原型对象`Object.prototype`
javascript为了节省空间，num在调用toString方法时会先在自身的属性上找，如果没有则继续向`__proto__`属性中找，找到的话就调用该属性，如果没找到则继续向下一级`__proto__`中找，找到`Object.prototype`都还没找到，就返回`undefined`。而Object的原型对象有`toString`属性,所以num可以调用该方法

那我们说Number继承了Object,所以Number实例对象具有`Object.prototype`的各种属性和方法
    
# 怎样继承
正是因为`Number.prototype.__ptoto__` 指向了 `Object.prototype`对象, num才能具有`Object.prototype`的属性和方法
这里的Number继承Object,是js自身实现,如果我们需要实现一个'类'继承另一个'类'呢？

## 继承属性

比如父类Animal 子类Cat Animal具有方法run Cat具有方法say 我们想让Cat也有run方法通过继承Animal

    function Animal(name){
      this.name = name
    }
    Animal.prototype.run = function(){
      console.log('I can run')
    }
    function Cat(){
      this.age = 1
    }
    let animal = new Animal()
    let cat = new Cat()

这个时候我们看下animal和cat
{% asset_img 继承前的cat和animal.png 继承前的cat和animal %}

如果我们想让cat也具有name属性,可以在定义Cat时调用一下Animal方法,用**call**绑定this,这样cat的name就是'cat'了

    function Animal(name){
      this.name = name
    }
    Animal.prototype.run = function(){
      console.log('I can run')
    }
    function Cat(){
	  Animal.call(this,'cat')
      this.age = 1
    }
    let animal = new Animal()
    let cat = new Cat()
    
{% asset_img cat继承属性.png cat继承属性 %}


## 继承方法

继承属性还不够,我们还需要继承共有方法呢！根据上文,我们要让`Cat`继承`Animal`,让`Cat.prototype` 的 `__proto__` 指向 `Animal.prototype` 不就可以了吗？那我们来试试

    Cat.prototype.__proto__ = Animal.prototype

{% asset_img cat继承方法.png cat继承方法 %}

真的实现了呢，现在我们的小猫也可以 `run`了,但是这种方法在IE不兼容,有什么解决办法呢？
总结一下我们就是让一个对象的__proto__ 只想另一个对象的prototype,好熟悉,有木有？在`num = new Number()` 的时候 `num.__proto ==== Number.prototype`
那就套一下呗
  
    Cat.prototype = new Animal('cat')
    let cat2 = new Cat()

{% asset_img 直接new父类.png 直接new父类 %}

但是这样Cat.prototype却会多出一个name属性,我们并不想要这个name属性
我们不应该直接new这个Animal,不然它就会给实例对象添加name属性。
通过一个空函数,间接改变指向

    var f = function(){}       
    f.prototype = Animal.prototype
    Cat.prototype = new f()

这个时候

    Cat.prototype.__proto__ === Animal.prototype  // true


其实上面的代码就是`Object.create()`的[Polyfill](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create)
所以我们也可以直接这么写

  Cat.prototype = Object.create(Animal.prototype)

## 改变constructor指向

虽然我们已经实现了`Cat.prototype.__proto__ === Animal.prototype `

但是`Cat.prototype.constructor`方法缺丢失了,打印一下看看

{% asset_img constructor丢失.jpg Cat.prototype.constructor丢失 %}

可以看到`Cat.prototype`只有一个`__proto__`属性, 

但是当我们打印`Cat.prototype.constructor`是有输出的，输出的是Animal函数

{% asset_img animal_constructor.jpg Cat.prototype.constructor为Animal函数 %}

那为什么会这样呢, 因为
  
 Cat.prototype.constructor  === Cat.prototype.__proto__.constructor        // true
 Cat.prototype.__proto__.constructor ===  Animal.prototype.constructor               // true
 Animal.prototype.constructor === Animal    // true

 {% asset_img Animal与Cat原型关系.jpg Animal与Cat原型关系 %}

所以我们调用的是Animal.prototype的constructor,当然是Animal函数啦

所以我们需要手动加上`Cat.prototype`的`constructor`属性

    Cat.prototype.constructor = Cat

这时候再看`Cat.prototype`就会多了一个constructor

{% asset_img Cat有了constructor.jpg Cat有了constructor属性 %}

# ES5 写法
总结一下ES5 的写法

    function Animal(name){
      this.name = name
    }
    Animal.prototype.run = function(){
      console.log('I can run')
    }
    function Cat(){
	    Animal.call(this,'cat')
	    this.age = 1
    }
    var f = function(){}       
    f.prototype = Animal.prototype
    Cat.prototype = new f()
    Cat.prototype.constructor = Cat

    // 后面再给Cat添加公有属性
    Cat.prototype.say = function(){
      console.log('miao miao miao')
    }

    也叫作寄生组合继承

# ES6 写法
ES6引入了class语法糖,跟java类似
    
    class Animal{
        constructor(name){
            this.name = name
        }
        run(){
            console.log("I can run")
            return undefined
        }
    }
    class Cat extends Animal{
        constructor(name){
            super(name)
            this.age = 1
        }
        say(){
          console.log('miao miao miao')
        }
    }
