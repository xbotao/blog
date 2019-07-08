---
layout: post
title: 'C#-object与json字符串的转换'
urlname: CSharp-object-convert-json
tags:
  - 编程
  - 'C#'
translate_title: 'conversion-of-c#-object-to-json-string'
date: 2018-04-14 00:00:00
---

C#中对象与字符串的转换借助第三方包`Newtonsoft.Json`实现。在工程上右键使用VS Nuget包管理进行添加。



<!--more-->

## 代码

对象转JSON字符串:

```c#
string strResult = Newtonsoft.Json.JsonConvert.SerializeObject(result);
```

JSON字符串转对象：

```c#
UserInfoStruct user = Newtonsoft.Json.JsonConvert.DeserializeObject<UserInfoStruct>(strData);
```

