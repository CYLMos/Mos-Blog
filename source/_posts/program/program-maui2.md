---
title: MAUI的問題記錄2
date: 2021-12-12 21:30:46
tags:
    - 程式
    - C#
    - MAUI
    - Visual Studio 2022
    - .Net 6
categories: 程式
top_img: https://imgur.com/o7vOsdO.png
cover: https://imgur.com/o7vOsdO.png
---
在Build完要Deploy到Android模擬器上時，出現Need recipe file的訊息
然後就Deploy失敗

我發現如果Solution Name和Project Name一樣的話，可以成功Run起來
發現該問題是因為我改Project Name後就沒辦法Deploy，不過Win的部分還是正常

然後該問題目前好像還在修的樣子
[相關的Issue](https://github.com/dotnet/maui/issues/2942)