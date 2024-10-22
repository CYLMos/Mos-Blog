---
title: MAUI的問題記錄
date: 2021-11-30 08:21:20
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
記錄

用VS 2022開新MAUI專案
關掉後再重開該專案，Project會顯示Incompatible

嘗試一堆方法後，發現在.csproj裡把SingleProject改成false後重Load就解決了(重Load後要再改成true)

希望這Bug在明年Q2正式Release前能修掉

---更新---

這問題好像是因為我不是用CS 2022 Preview導致
換成Preview後解決