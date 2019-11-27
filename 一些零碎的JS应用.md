---
title: 一些零碎的JS应用
date: 2019-10-31 16:51:28
tags: js
categories: js
---

# 字符串反转

利用数组的reverse方法

    'abcdef'.split('').reverse().join('')  // 'fedcba'

# 字符串转为二进制

利用charCodeAt得到字符的unicode编码，一个字节的字符，与ascii码相同
这个得到的是10进制的，再利用toString转为2进制

    'a'.charCodeAt().toString(2) //  "1100001"

# 保留两位小数

### 四舍五入

    var num = 2.446242342
    num = parseFloat(num.toFixed(2))             // 2.45

`toFixed` 返回的是 **字符串**

### 不四舍五入

    Math.floor(15.7784514000 * 100) / 100      // 15.77


