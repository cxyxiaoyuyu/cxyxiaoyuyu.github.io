---
title: curl常用用法以及http协议
date: 2018-10-06 22:38:26
tags: http
categories: http
---

## curl命令常用参数

1.  #### 最简单的用法，直接加网址

        curl http://www.baidu.com       #返回下载进度，及网页源代码

2.  #### -X 指定请求方式
    *   GET 请求

            curl -X GET http://www.baidu.com  # -X GET 是可选的
    *   POST 请求

            curl -X POST -d '123' http://www.baidu.com

        -d 后是向服务端发送的数据，写-d 选项后可省略 -X POST

3.   #### -H 增加头部信息

            curl -H "Content-Type:application/json" -d '{"data":"123","key":"456"}' http://www.baidu.com
            #即指定发送数据的格式为json格式

4.  #### -s silent  静静的下载，不输出进度条

5.  #### -v 显示请求详细信息,HTTP协议的请求内容和响应

            $ curl -s -v http://www.baidu.com
            ...
            > GET / HTTP/1.1
            > Host: www.baidu.com
            > User-Agent: curl/7.55.0
            > Accept: */*
            >
            < HTTP/1.1 200 OK
            < Accept-Ranges: bytes
            < Cache-Control: private, no-cache, no-store, proxy-revalidate, no-transform
            < Connection: Keep-Alive
            < Content-Length: 2381
            < Content-Type: text/html
            < Date: Sun, 07 Oct 2018 02:59:01 GMT
            < Etag: "588604c4-94d"
            < Last-Modified: Mon, 23 Jan 2017 13:27:32 GMT
            < Pragma: no-cache
            < Server: bfe/1.0.8.18
            < Set-Cookie: BDORZ=27315; max-age=86400; domain=.baidu.com; path=/
            <



## http协议(hyper text translate protocol)  超文本传输协议

### 通过curl命令查看http请求和响应
参考curl命令-v参数，`>`开头的为http请求，`<`开头的为响应
请求的内容为
>   \> GET / HTTP/1.1
>   \> Host: www.baidu.com
>   \> User-Agent: curl/7.55.0
>   \> Accept: */*
>   \>

响应的内容为
>            < HTTP/1.1 200 OK
>            < Accept-Ranges: bytes
>            < Cache-Control: private, no-cache, no-store, proxy-revalidate, no-transform
>            < Connection: Keep-Alive
>            < Content-Length: 2381
>            < Content-Type: text/html
>            < Date: Sun, 07 Oct 2018 02:59:01 GMT
>            < Etag: "588604c4-94d"
>            < Last-Modified: Mon, 23 Jan 2017 13:27:32 GMT
>            < Pragma: no-cache
>            < Server: bfe/1.0.8.18
>            < Set-Cookie: BDORZ=27315; max-age=86400; domain=.baidu.com; path=/
>            <

另一个请求示例

    curl -X POST -d "data" -s -v -H "Xiaoyu: xxx" -- "https://www.baidu.com"

请求的内容为
>   \> POST / HTTP/1.1
>   \> Host: www.baidu.com
>   \> User-Agent: curl/7.55.0
>   \> Accept: */*
>   \> Xiaoyu: xxx
>   \> Content-Length: 4
>   \> Content-Type: application/x-www-form-urlencoded
>
>   data

请求的格式为
>   1 动词 路径 协议/版本   #动词有 GET POST PUT PATCH DELETE HEAD OPTIONS 等
>   2 Key1: value1
>   2 Key2: value2
>   2 Key3: value3
>   2 Content-Type: application/x-www-form-urlencoded
>   2 Host: www.baidu.com
>   2 User-Agent: curl/7.54.0
>   3                          #空格
>   4 要上传的数据             #可以为空


响应的内容为
>   < HTTP/1.1 302 Found
>   < Connection: Keep-Alive
>   < Content-Length: 17931
>   < Content-Type: text/html
>   < Date: Sun, 07 Oct 2018 03:08:53 GMT
>   < Etag: "54d97485-460b"
>   < Server: bfe/1.0.8.18
>   <
>   data                    #下载的数据，数据格式由content-type决定

响应的格式为
>   1 协议/版本号 状态码 状态解释
>   2 Key1: value1
>   2 Key2: value2
>   2 Content-Length: 17931
>   2 Content-Type: text/html
>   3
>   4 要下载的内容

### 通过Chrome浏览器查看http请求和响应

1. 打开 Network
2. 地址栏输入网址
3. 在 Network 点击，查看 request/response，点击「view source」
4. **点击「view source」**
5. 如果有请求的第四部分，那么在 FormData 或 Payload 里面可以看到
6. 如果是响应，可以点击Response查看响应的数据

{% asset_img request.png request %}

{% asset_img response.png response %}


