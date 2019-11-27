---
title: NodeJS AES RSA 加解密
date: 2019-11-06 17:51:46
tags: 加解密
categories: 加解密
---

# 前言

需求:
> 对加密文本textPlain进行AES加密,AES加密中的key通过RSA加密,并通过加解签对解密结果进行验证

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

    
    
    
# RSA 加解密

### RSA 公钥加密

    let keyEnc = publicEncrypt(key)          // RSA每次加密结果不一致

    // RSA公钥加密函数 返回16进制字符串
    function publicEncrypt(key){
      key = Buffer.from(key)
      let keyEnc = crypto.publicEncrypt({key:publicKey,padding:crypto.constants.RSA_PKCS1_PADDING}, key);
      return keyEnc.toString('hex');
    }

### RSA 私钥解密

    let keyDec = privateDecrypt(keyEnc)       // zCpdywHdFfPYQwYi

    // RSA私钥解密函数
    function privateDecrypt(keyEnc){
      let keyDec = crypto.privateDecrypt(
        {
          key:privateKey,
          padding:crypto.constants.RSA_PKCS1_PADDING
        },
        // `keyEnc`是16进制第二个参数就是`hex` `keyEnc`是Base64第二个参数就是`base64`
        Buffer.from(keyEnc,'hex')
      )
      return keyDec
    }


# AES 加解密

> AES加密用到的iv写在函数里了,固定为`1234567812345678`

## AES 加密

    let textEnc = AESEncrypt(plainText,key)    // 每次AES加密结果一致
    // xoJ9fYIqyoAA3Qbhm/h8nGy4KjcmHVtusheMTd61mpUB8Lr6SDFQ1pWimi+WwlZyL5ncvZ9kQKjoRcRXUB7/D6uVkl724DzxBu/Ns1ivWnA=

    // AES 加密函数
    function AESEncrypt(content,key){
      let iv = '1234567812345678'
      let cipher = crypto.createCipheriv('aes-128-cbc', key, iv);
      let encrypted = cipher.update(content,'utf8','binary');
      encrypted += cipher.final('binary');
      encrypted = Buffer.from(encrypted,'binary').toString('base64')
      return encrypted;
    }

## AES 解密

    // 用解密后的keyDec通过AES解密textEnc
    let textDec = AESDecrypt(textEnc,keyDec) 
    // {id:'123',name:'xiaoyu',address:'address:'上海市浦东新区'}

    function AESDecrypt(content,key){
      let iv = "1234567812345678";
      content = Buffer.from(content,'base64').toString('binary')
      let cipher = crypto.createDecipheriv('aes-128-cbc', key, iv);
      let decrypted = cipher.update(content,'binary','utf8');
      decrypted += cipher.final('utf8');
      return decrypted;
    }

# RSA 加解签
    
## RSA 加签

    let sign = getSign(plainText)       // 每次签名结果一致
    // 3b073ad8655293c8ec92dddeb6b986fbad190d642e2ca74f57f90aeedb1645be20449e0e3a7824f483d72de120221b0a29f2adf5c0cd0b913cee01d8d6a13eeb55c9f2b9a3868dc29181cddbd0fde15264d40a46f74ab7db23a09bc48d93a376eb8d50c84e67a7d08ea9810a76badfc4bb7f5e8cdbd045ef3551389eb1b9cef7

    //获取签名
    function getSign(content){
      let sign = crypto.createSign('RSA-SHA1');
      sign.update(content);
      return sign.sign(privateKey,'hex');
    }

## RSA 验签

    let result = verifySign(textDec,sign)      // true

    // 验证签名
    function verifySign(standard,sign){
      let verify = crypto.createVerify('RSA-SHA1');
      verify.update(standard,'utf8');
      return verify.verify(publicKey,sign,'hex');
    }

{% asset_img node加解密.jpg NodeJS 加解密 %}


