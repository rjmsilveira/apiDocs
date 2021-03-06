---
title: 开发通用说明
last_updated: 2019-03-20
summary: "请求云平台服务认证准备。包括通用参数说明，认证所需的签名规则介绍，签名计算示例以及Access Token以及Refresh Token说明"
sidebar: mydoc_sidebar
permalink: instruction.html
folder: mydoc
---

   
## 开发通用说明

 说明：通用说明里的参数是所有接口的公用参数，请求接口时需要传递通用参数+接口参数。（开发通用说明仅适用于REST API接口，WebSocket接口[请查看](dispatch.html)）
- HTTP请求头需指定Content-Type:application/json
- 字符编码：utf-8
- HTTP  请求头Authorization字段必须有签名或access token值
- HTTP和websocket接口全部使用SSL/TLS，端口为8080，服务端支持SSLV3,TLS1.1, TLS 1.2

**必选参数如下：**

|参数名|必选|类型|说明|
|:----    |:---|:----- |-----   |
|ts |是  |Long |进行接口调用时的时间戳，即当前时间戳 （时间戳：当前距离Epoch（1970年1月1日） 以秒计算的时间，即unix-timestamp）   |
|appid |是  |String | 酷宅给第三方应用分配的appid    |
|nonce     |是  |String | 8位随机字符串，只由字母数字组成    |
|version     |是  |Int | version:8 协议版本    |

## 签名规则
- 未登录的所有接口（包括登录接口）调用都需要计算签名，签名值放在HTTP的Authorization头中。如：
 Authorization:Sign QtKh6EnKoNmPnv17Ump3b/6r2hjojWb4nqSt4lnyj2U=
 
```
签名算法见本章下文的签名计算
```

- GET请求：
将所有参数（sign除外）按照参数名的字母顺序排序，并用&连接：

```
appid="I25m0KljbFfGsTjRc3eTwTEPVwKzsvCF"&deviceid="1000052354"&nonce="2323dfgh"&ts=1545219251
```

计算签名:将app secret作为key，生成被签名串的HMAC-SHA256 签名。
将得到的签名BASE64编码后设置到Authorization头中

- POST请求:
待签名串为整个body的json数据(通用说明里的参数以及接口中的参数)，以登录接口为例。 如：
```{"appid":"I25m0KljbFfGsTjRc3eTwTEPVwKzsvCF","nonce":"232323df","ts":1545219251,"version":8,"phoneNumber":"+86********","password":"****"}```
或
```{"appid":"I25m0KljbFfGsTjRc3eTwTEPVwKzsvCF","nonce":"232323df","ts":1545219251,"version":8,"email":"dev@coolit.cc","password":"****"}```
注意：phoneNumber的值需要加上国家码如：+86
phoneNumber和email只需要传递其中一个。亚洲区域传递phoneNumber，其他区域才传递email

## 签名计算

签名算法demo: 【nodejs】 以登录接口为例

method:POST（GET方式查看签名规则）

```
var crypto = require('crypto');
var appid="I25m0KljbFfGsTjRc3eTwTEPVwKzsvCF";
var appsecret="S1fHFiMqzykNdxlSrk9Pjdczp7rsvt3M";
var params={"appid":"I25m0KljbFfGsTjRc3eTwTEPVwKzsvCF","phoneNumber":"+8613570211955","password":"lybywl163","ts":1545219251123,"version":8,"nonce":"asbsedwq"}
var buffer = new Buffer(JSON.stringify(params),"utf-8");
var theSign = crypto.createHmac('sha256', appsecret).update(buffer).digest('base64')
```

上述参数算出来的签名值
```"QtKh6EnKoNmPnv17Ump3b/6r2hjojWb4nqSt4lnyj2U="```

可以拿demo的参数放到自己的签名算法中，算出来的签名值和提供的签名值一致，表示签名算法通过。

## Access Token以及Refresh Token说明（即登录成功返回的at和rt）
登录后调用的接口都需要在Authorization头中增加access token，如下：

Authorization: Bearer 1832d4a0f5f5f5845cb1922c1091c8aa5dfe0041

（1832d4a0f5f5f5845cb1922c1091c8aa5dfe0041为登录返回的at）

登录接口成功后会返回access token （登录成功后返回的at，即access token）
 
access token有效期为一个月。同一个帐号在同一个应用下不能同时登录，每登录一次会重新刷新token，这样后面登录的用户会刷新token，让前面登录的用户token失效，被挤下线

refresh token有效期为二个月。access token失效后可通过refresh token请求refresh token刷新接口，重新获取新的access token和refresh token。[请查看 refresh token刷新](token.html)


