---
title: JS中的数据类型
date: 2018-10-29 18:46:30
tags:
---

## JS中一共有七大数据类型

### 1. 数值(number)
1. 整数和浮点数
> JavaScript 内部，所有数字都是以64位浮点数形式储存，即使整数也是如此。所以，1与1.0是相同的，是同一个数   `1 === 1.0`  // true

2. 数值的进制
> 十进制：没有前导0的数值
> 八进制：有前缀0o或0O的数值，或者有前导0、且只用到0-7的八个阿拉伯数字的数值
> 十六进制：有前缀0x或0X的数值
> 二进制：有前缀0b或0B的数值
> 默认情况下，JavaScript 内部会自动将八进制、十六进制、二进制转为十进制

3. NaN
> NaN是 JavaScript 的特殊值，表示“非数字”（Not a Number），主要出现在将字符串解析成数字出错的场合
> 0除以0也会得到NaN
> 一些数学函数的运算结果会出现NaN
> 需要注意的是，NaN不是独立的数据类型，而是一个特殊数值，它的数据类型依然属于Number
> NaN不等于任何值，包括它本身
> isNaN方法可以用来判断一个值是否为NaN

        NaN === NaN // false
        isNaN(NaN)  // true
        isNaN(123)  // false

        // isNaN()只对数值有效，如果传入其他值，会先转成数值类型
        isNaN('hello') // true
        //相当于
        isNaN(Number('hello')) //true
        isNaN({})              //true
        isNaN([1,2])           //true
        // 所以isNaN() 为true的时候，有可能是一个字符串，或对象或数组
        // 所以使用isNaN()时，需要先判断下数据类型
        // 但是当数组为空，或数组长度为1时返回false
        isNaN([]) //false
        isNaN([1]) //false


### 2. 字符串(string)
1. 字符串也可以叫做字符数组
> 字符串就是零个或多个排在一起的字符，放在单引号或双引号之中。

2. 空字符串与空格字符串
> 空字符串    没有字符,长度为0
> 空格字符串  只有一个空格的字符串，空格也是一个字符，所以长度为1

3. 多行字符串(3种) ,ES6新语法

        var s='1234\    //使用"\"换行
        5677'
        s
        "12345677"     //输出还是一行

        var s='1234'+   //使用"+"连接字符串
        '5677'
        s
        "12345677"    //输出还是一行

        //ES6新语法
        var s=`1234    //使用"`" 换行
        5678`
        s
        "1234        //输出为两行,length为9，换行符也为一个字符
        5678"

        //使用转义符
        var s='1234\n5678'   //"\n"表示换行符
        s
        "1234        //输出为两行
        5678"

4. 转义
> 反斜杠（\）在字符串内有特殊含义，用来表示一些特殊字符，所以又称为转义符。
* \0 : null
* \b : 后退键
* \f : 换页符
* \n : 换行符
* \r : 回车键
* \t : 制表符
* \v : 垂直制表符
* \' : 单引号
* \\ : 反斜杠

5. 字符串与数组
> 字符串可以被视为字符数组，因此可以使用数组的方括号运算符，用来返回某个位置的字符（位置编号从0开始）

        var s = "hello"
        s[0] // 'h'
        s[1] // 'e'
        s[4] // 'o'
        s[-1] // undefined

>  字符串是不可变的数据类型，不可以通过此方法改变字符串

        var s = "hello"
        delete s[0]
        s // 'hello'
        s[0] = "G"
        s// 'hello'


### 3. 布尔(boolean)
只有true 和 false 两个值,用于条件判断

### 4. 符号(symbol)  ES6新增

### 5. unidefine  只有一个值==> undefined
### 6. null       只有一个值==> null
undefined 与 null 都表示什么也没有
#### undefined 与 null 的区别
> 1. 一个变量被声明而没有赋值，则返回`undefined`
> 2. 一个非空对象，现在不想给值，`var xxx=undefined` ==> 空非对象
> 2. 一个对象，不想给值， `var obj=null` ==> 空对象


### 7. 对象(object)
> 简单说，对象就是一组“键值对”（key-value）的集合，是一种无序的复合数据集合。
> 数组和方法也是对象

        var obj = {a:1,b:2}

1. 如果键名是数字，会自动转为字符串，如果不加引号，会按照标识符规则，不符合就报错
2. 键名又称为属性，值可以为任意数据类型
3. 对象的引用
> 如果不同的变量名指向同一个对象，那么它们都是这个对象的引用，也就是说指向同一个内存地址。修改其中一个变量，会影响到其他所有变量。

        var o1 = {}
        var o2 = o1

        o1.a = 1
        o2.a //1 o1改变，也会影响到o2

        o2.b = 2
        o1.b //2 o2改变，也会影响到o1

        o1 = 1    //取消o1对原对象的引用
        o2 // {a:1,b:2}  //o2不变

4. 对属性的读取

        var obj = {'name':'xiaoyu'}
        obj['name'] // 'xiaoyu'
        obj.name    // 'xiaoyu'  <==> obj['name']

        var key =  'name'
        obj.key  //error!  obj['key'],这个对象没有key这个属性

5. 对象有哪些属性

        var obj = {'name':'xiaoyu','age':'18'}
        Object.keys(obj) //['name','age']

6. 对象的遍历
> `for...in` 循环用来遍历一个对象的全部属性。
> 它遍历的是对象所有可遍历（enumerable）的属性，会跳过不可遍历的属性。
> 它不仅遍历对象自身的属性，还遍历继承的属性。


        var obj = {'name':'xiaoyu','age':'18'}
        for(var attr in obj){
            console.log(attr+'==>'+obj[attr]) // name==>xiaoyu age==>18
        }

## 其他
typeof 查看数据类型，两个bug，一个特殊的name

    var num = 1
    typeof num  //number

    //bug1
    function f(){]
    typeof f //function  js没function这个数据类型

    //bug2
    var a = null
    typeof a //object

    //特殊的name  ==>  window.name 会将值通过toString()方法转为字符串
    var name = [1,2,4]
    typeof name // string
    name  // '1,2,4'



