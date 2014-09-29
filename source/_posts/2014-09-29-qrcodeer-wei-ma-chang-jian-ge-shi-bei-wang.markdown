---
layout: post
title: "QRCode二维码常见格式备忘"
date: 2014-09-29 14:06:38 +0800
comments: true
categories: QRCode, 二维码, 二维码格式
---

**二维码格式的简单笔记，主要是对QRCode支持的常见约定格式，做一个备忘，万一用得上呢<!--more-->**

--------------

###URL
* http://www.test.com
* URLTO:www.test.com

--------------

###eMail地址
* mailto:test@test.com

------------

###电话号码
*tel:139800xxxxx

**注意：如果是座机，加上区号```tel:08245834982```； 同样，国际通话加+86```tel:+86139800xxxxx```**

----------

###联系人信息
1. vCard [具体格式见wiki](http://zh.wikipedia.org/wiki/VCard)

   `http://blog.csdn.net/sasoritattoo/article/details/17119123`
2. MECARD   

   `MECARD:N:freemouse,King;ADR:maanshan,anhui,china;TEL:+865558888888;EMAIL:freemouse@email.com;URL:t.qq.com/tseeku;QQ:47302929;;`
3. BIZCARD

   `BIZCARD:N:freemouse;X:King;T:Software Engineer;C:Google;A:maanshan, An Hui, China 243000;B:+8655555555;E:freemouse@email.com;;`
   
------------

###短信 SMS
* smsto:139800xxxxx:你好，这里是测试内容

###彩信 MMS
* mmsto:139800xxxxx:内容

-------

###地理位置信息 Geographic information
* geo:40.71872,-73.98095,100

--------------

###GooglePlay市场地址
* market:detail?id=某app的ID

----------------

###WiFi信息
* WIFI:T:WPA;S:testssid;P:testpassword;;

    ![ wifi格式表格](/images/2014-09-29-01.png)








