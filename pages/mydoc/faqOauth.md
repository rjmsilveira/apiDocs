---
title: 认证
last_updated: 2019-03-20
sidebar: mydoc_sidebar
permalink: faqOauth.html
folder: mydoc
---

    
**简要描述：** 

 请求接口返回406

 406表示认证不通过。
 
 请检查以下项：

 一：通用参数未传递

 二：未设置认证头

 三：参数传递方式不对

 四：签名值不对

```
1：签名算法不对导致签名值不对。
2：签名算法正确的，但是算签名的参数和传递给接口的参数，顺序不一致，导致认证失败
```
 
 详情请点击[通用说明](instruction.html)查看。



