---
title: JS RSA AES 加解密
date: 2019-11-13 10:16:41
tags: 加解密
categories: 加解密
---

# 前言

需求:

> 对加密文本textPlain进行AES加密
> AES加密中的key通过RSA加密
> 并通过加解签对解密结果进行验证

本文为了统一, 随机数key为固定字符串
    
    let key = 'zCpdywHdFfPYQwYi' 
    let textPlain = "{id:'123',name:'xiaoyu',address:'address:'上海市浦东新区'}"

如果key随机生成,代码如下

    let key = randomString(16)

    function randomString(len) {
      len = len || 32
      // 默认去掉了容易混淆的字符oOLl,9gq,Vv,Uu,I1
      let $chars = 'ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678';    
      let maxPos = $chars.length;
      let str = ''
      for (i = 0; i < len; i++) {
        str += $chars.charAt(Math.floor(Math.random() * maxPos));
      }
      return str;
    }

其中RSA加密中的公密钥可通过[此链接](https://travistidwell.com/jsencrypt/demo/)生成
    
    let privateKey = `-----BEGIN RSA PRIVATE KEY-----
    MIICWwIBAAKBgG+u7KERg6DWKDhK7NRUe63FPpxbzBAUWxULzjYCJexsvgzXjj/m
    UPvTR/wjFXf49sidHDcEwhzx18k8aJ0+Rt5AUtNExTeAIIGo/mytyQwlS0GuazdW
    Sqw+1IOEJe88f/I62prFuuVuY+cIxucXDVpxwv5mfkMjeiTIixWyju4tAgMBAAEC
    gYABkOtiitNUswwXVf2iMJkXY7ewC5198DsivWqj2T3c5d14YqFtBfbGwTy2LWvE
    peoYABQANYHfVAjZmo5A/jmSKWHaCHd+k8sMQeThdklGVGHBctZ46aJ32c4CRmZa
    pHAtgYVYBxOyn/55WlJb9cYEYGE1M9ouOmsEwW/sUruyZQJBANmmXVhte5LNCrmr
    c7GFbJQLNT4YTcGPXHyRdxjGBRfcgAK0IHREUrBGjCY+N4DYd3QxRYTAF+EYileF
    ou3C1bcCQQCDXKz9d3pibVtfi/qcO6bn3Bdqfv641sEO5++dD/SCAeUSTrd8KO/e
    ZoVIkYV8lcj5Ol6cRhXr9TGTGHUXzbs7AkACvxmwcvk7OgEnpKU6GT/sglOyq+XX
    SnLVU5SYKG+KBItM/W7naLFObiV4mhJjOtgPDEwfR7lFauTcA+CxdWLzAkBaOEVr
    lCqO/VKbTdVihim85tGCSBSEOKLBrRRJyBKDU+/TYhLtCGSuV7VEwQVwKqOsYKVo
    KU6XD4Dr6QlKvvbPAkEAkjlU5ua5BfGh601U8va1xWyxh0TqemBTsyoQ8bSwOpZH
    NgGjWqv5Z0QoK99pJv7VfReKbLyIg2MXhpigTogeWA==
    -----END RSA PRIVATE KEY-----`

    let publicKey = `-----BEGIN PUBLIC KEY-----
    MIGeMA0GCSqGSIb3DQEBAQUAA4GMADCBiAKBgG+u7KERg6DWKDhK7NRUe63FPpxb
    zBAUWxULzjYCJexsvgzXjj/mUPvTR/wjFXf49sidHDcEwhzx18k8aJ0+Rt5AUtNE
    xTeAIIGo/mytyQwlS0GuazdWSqw+1IOEJe88f/I62prFuuVuY+cIxucXDVpxwv5m
    fkMjeiTIixWyju4tAgMBAAE=
    -----END PUBLIC KEY-----`


另外还需下载[aes.js](http://react.file.alimmdn.com/aes.js) [jsencrypt.js](https://travistidwell.com/jsencrypt/) [jsrsasign.js](https://github.com/kjur/jsrsasign)

    <script src="./jsencrypt.js"></script>
    <script src="./aes.js"></script>
    <script src="./jsrsasign-all-min.js">

# RSA 加解密

    let RSACrypt = new JSEncrypt()

## RSA 加密

    RSACrypt.setKey(publicKey)
    let keyEnc = RSACrypt.encrypt(key)        // 每次加密结果不一致

## RSA 解密

    RSACrypt.setKey(privateKey)
    let keyEnc = RSACrypt.decrypt(keyEnc)     // zCpdywHdFfPYQwYi

# AES 加解密

## AES 加密
    
    let textEnc = AESEncrypt(textPlain,key)  // 每次加密结果一致
    // xoJ9fYIqyoAA3Qbhm/h8nGy4KjcmHVtusheMTd61mpUB8Lr6SDFQ1pWimi+WwlZyL5ncvZ9kQKjoRcRXUB7/D6uVkl724DzxBu/Ns1ivWnA=

    // AES加密函数
    function AESEncrypt(textPalin,key){
      let key = CryptoJS.enc.Utf8.parse(key)
      let textEnc = CryptoJS.AES.encrypt(textPlain, key, { 
          iv: CryptoJS.enc.Utf8.parse("1234567812345678"),
          mode: CryptoJS.mode.CBC,
          //padding: CryptoJS.pad.Iso10126
      })
      return textEnc.toString()
    }

## AES 解密
    
    // 利用解密后的keyDec解密textEnc
    let textDec = AESDecrypt(textEnc,keyDec)     //  {id:'123',name:'xiaoyu',address:'address:'上海市浦东新区'}

    // AES解密函数
    function AESDecrypt(word,key) {
        let key = CryptoJS.enc.Utf8.parse(key)
        let decrypt = CryptoJS.AES.decrypt(word, key, { 
            iv: CryptoJS.enc.Utf8.parse("1234567812345678"),
            mode: CryptoJS.mode.CBC, 
            //padding: CryptoJS.pad.Pkcs7 
        })
        let decryptedStr = decrypt.toString(CryptoJS.enc.Utf8)
        return decryptedStr.toString();
    }

# RSA 加解签

## RSA 加签

    let sign = getSign(textPlain)     // 每次加签结果一致
    // 3b073ad8655293c8ec92dddeb6b986fbad190d642e2ca74f57f90aeedb1645be20449e0e3a7824f483d72de120221b0a29f2adf5c0cd0b913cee01d8d6a13eeb55c9f2b9a3868dc29181cddbd0fde15264d40a46f74ab7db23a09bc48d93a376eb8d50c84e67a7d08ea9810a76badfc4bb7f5e8cdbd045ef3551389eb1b9cef7

    // 加签函数
    function getSign(signData){
        let signPrivateKey = privateKey
        let sig = new KJUR.crypto.Signature({"alg": "SHA1withRSA", "prvkeypem": signPrivateKey});
        let hashAlg = 'sha1'; // 设置sha1
        let sign = sig.signString(signData, hashAlg); // 加签
        return sign;
    }

## RSA 验签

    let result = verifySign(textDec,sign)    // true

    // 验签函数
    function verifySign(standData,signData){
      try {
          let signPublicKey = publicKey
          let sig = new KJUR.crypto.Signature({"alg": "SHA1withRSA","prvkeypem": signPublicKey});
          sig.updateString(standData);
          let result = sig.verify(signData);
          return result;
      } catch(e) {
          console.error(e);
      }
    }

{% asset_img js加解密.jpg js加解密 %}

# 在vue项目中使用加解密

安装各加密模块

    npm install crypto-js
    npm install jsencrypt
    npm install jsrsasign

    import AESCrypto from 'crypto-js'
    import JSEncrypt from 'jsencrypt'
    import Jsrasign from 'jsrsasign'

RSA加解密对象需要初始化

    var RSACrypto = new JSEncrypt()

在加解签时需要修改

    // 将 new KJUR.crypto.Signature 改为
    new Jsrasign.Signature

# 利用Crypto进行格式转换

## 字符串转其他格式

    let str = 'abc'
    let str_utf8 = CryptoJS.enc.Utf8.parse(str)

    // 转为 hex
    let str_hex = CryptoJS.enc.Hex.stringify(str_utf8)   // '616263'

    // 转为 base64
    let str_base64 = CryptoJS.enc.Base64.stringify(str_utf8)   // "YWJj"

## hex转为其他格式

    let hex = '616263'
    let hex_hex = CryptoJS.enc.Hex.parse(hex)

    // 转为字符串
    hex_str = CryptoJS.enc.Utf8.stringify(hex_hex)    // 'abc'

    // 转为 base64
    let hex_base64 = CryptoJS.enc.Base64.stringify(hex_hex)  //  "YWJj"


# 我遇到的一个问题

以上所写的都是客户端加密客户端解密,但是通常工作都是客户端加密,服务端解密,服务端加密,客户端解密。

当后端传回来的keyEnc为

    6e1c9600284212f2425d9a490a1f4d9b2d0e2d8605691cc02debe2dcf7a4b6cc51f81534ed6df592cdb035e14d03b8276a90192b76ba51c240d8e26b55bb9ad195b1044fbe5ce444c3be2940f33b698df812020619c1e4b4071361732d8f0d6fe98c222467d64ce8f0a9f77a02eef22b57833e6474e5fc5cf4e00c97f2706fad

将这个16进制字符串转为base64,然后用`RSACrypt.decrypt`对它进行解密,得到的结果却是`茁yˎѬ۽ܽ_뀅`,然后代入AES解密得到的是乱码。
然后用node去解密得到是`<Buffer b8 0c c1 79 cb 4e d1 ec db bd dc 7d 5f ab 80 05>`,通过`toString()`得到的是,通过`toString('hex')`得到`b80cc179cb4ed1ecdbbddc7d5fab8005`,与`茁yˎѬ۽ܽ_뀅`转为的16进制并不一样。
将这个16进制字符串带入AES解密,得到了正确的解密结果。
这样看来,node解密的结果是正确的，难道js的解密方法不对吗？

AES 解密 如果key是16进制的话

    // 将 let key = CryptoJS.enc.Utf8.parse(key) 改为
    let key = CryptoJS.enc.Hex.parse(key)

然后查看源码的时候发现在`pkcs1unpad2`函数里有utf-8 decode,仔细研究发现这个
`b80cc179cb4ed1ecdbbddc7d5fab8005`不是一个utf-8编码的字符串,因为根据[utf-8的编码规则](http://localhost:4000/2019/11/01/%E6%88%91%E7%BB%88%E4%BA%8E%E6%87%82%E4%BA%86%E8%AE%A1%E7%AE%97%E6%9C%BA%E6%98%AF%E5%A6%82%E4%BD%95%E5%AD%98%E5%82%A8%E6%95%B0%E6%8D%AE%E7%9A%84/#more),没有`10110110`开头的,但是源码把它当作utf-8的16进制解析,所以与node结果不一致。

那我能不能不让它decode,在decode之前就直接得到它

    function pkcs1unpad2(d, n) {
      // ...
      var array = []
      while (++i < b.length) {
        var c = b[i] & 255;
        array.push(c)
        
      }
      array = array.map( value => 
        value.toString(16).length===1?0+value.toString(16):value.toString(16)
      ).join('')

      return array
    }

然后这里得到跟node解密一样的16进制字符串,问题已解决。

因为后台进行RSA加密的时候,key并不一定就是字符串,也有可能就是非utf-8编码的16进制的Buffer。比如：

    var key_utf16 = 'b80cc179cb4ed1ecdbbddc7d5fab8005'
    var key = Buffer.from(key_utf16, 'hex')

这时我们再用utf-8解码的时候，会得到错误的结果。