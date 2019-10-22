---
title: JS中的类型转换
date: 2018-11-03 20:00:17
tags:
---

## ==> String
#### 方法1  `toString()`方法
> `null` 和 `undefined` 没有`toString()` API
> `object` 的 `toString()` 不能得到想要的结果
> `console.log()`  默认调用`toString()`方法

        123.toString() // '123'
        true.toString() // 'true'
        null.toString()  // Cannot read property 'toString' of null
        undefined.toString()  //Cannot read property 'toString' of undefined
        var a = {a:1}
        a.toString()  // "[object Object]"
        ({a:1}).toString() // "[object Object]"



#### 方法2  `+ ''` 加空字符串

        1 + '1' // '11'  ==> '+' 会自动进行类型转换,优先变为字符串
        123 + '' // '123'
        true + ''  // 'true'
        null + '' // 'null'
        undefined + '' // 'undefined'
        ({a:1}) + '' // "[object Object]"
        '' + {a:1}  // "[object Object]"


#### 方法3  全局函数`String()`
        String(1)  // '1'
        String(true) // 'true'
        String(null)  // 'null'
        String(undefined) // undefined
        String({a:1})  // "[object Object]"

## ==> Boolean
#### 方法1  全局方法`Boolean()`

        Boolean(1) // true
        Boolean(0) // false
        Boolean(NaN) // false
        Boolean('') // false
        Boolean(' ') // true
        Boolean('123') // true
        Boolean(null)  // false
        Boolean(undefined) //false
        Boolean({})  // true
        Boolean({a:1}) //true



#### 方法2 双重取反 ==> `!!`

        !! 1  // true
        !! NaN // false
        !! 0 // false
        !! '' // false
        !! ' ' // true
        !! '123' // true
        !! null // false
        !! undefined //false
        !! {}  // true
        !! {a:1} // true

#### 五个**falsy**值
>  0
> NaN
> null
> undefined
> ''

## ==> Number
#### 方法1  `Number()`

        Number('1') // 1
        Number('1.123') // 1.123
        Number('1.abc') // NaN
        Number('')    // 0
        Number(true)  // 1
        Number(false) // 0
        Number(null) // 0
        Number(undefined) // NaN
        Number({a:1})  // NaN

#### 方法2  `parseInt()`
关于parseInt 和parseFloat 我后面会单独写一篇博客，详细解释，这里只简单演示
> `parseInt()` 主要是将字符串转为整数
> 它会从第一个字符开始，直到不是数字结束

        parseInt('1') //1
        parseInt('1.23') // 1
        parseInt('  123') // 123
        parseInt('1234abc')  // 1234
        parseInt('abc') // NaN
        parseInt('a567bc') // NaN
        parseInt(true) // NaN
        parseInt(null) // NaN
        parseInt(undefined) // NaN
        parseInt({a:1}) // NaN
        parseInt([1,2,3]) // 1

#### 方法3  `parseFloat()`
> parseFloat() 主要是将字符串转为浮点数

        parseFloat('3.14') // 3.14
        parseFloat('314e-2') // 3.14
        parseFloat('0.0314E+2') // 3.14
        // 其他与parseInt()结果相同

#### 方法4  `- 0`

        '1' - 0 // 1
        '1.23' - 0  // 1.23
        '1.abc' -0 // NaN
        '' - 0  // 0
        true -0 // 1
        false - 0  // 0
        null - 0 // 0
        undefined - 0 // NaN
        ({a:1}) - 0

#### 方法5  取正

        + '1' // 1
        + '1.23' // 1.23
        + '1.abc' // NaN
        + '' // 0
        + true // 1
        + false // 0
        + null // 0
        + undefined // NaN
        + {a:1} // NaN

## JS中普通类型与对象的区别
#### 普通类型  Number Bool String Symbol Null Undefined
> 数据直接存在栈内存中

        var a = 1
        var b = a
        a = 2
        b // 1
将a中的数据直接赋值给b，所以改变a，不会影响b ==> 深复制
所以普通类型之间的复制都是深复制

#### 引用类型  Object
> 栈内存中保存的是对象在堆内存中的引用地址
> 通过这个引用地址可以快速查找到保存中堆内存中的对象

        var a = {name:1}
        var b = a
        a.name = 2
        b // {name:2}
将a赋值给b的时候，实际上是b也指向了与a地址相同的对象，
改变a会影响b == > 浅复制

        var a = {name:1}
        var b = a
        a = {name:2}
        b // {name:1}
`a = {name:2}` 时，a指向了另一个对象的地址,而b还指向原来地址的对象,所以给a重新赋值不会影响b



