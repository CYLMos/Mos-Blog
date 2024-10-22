---
title: 車牌辨識App
date: 2018-01-19 16:09:20
tags:
    - 程式
    - Java
    - Android
    - OpenCV
    - KNN
    - Tess-Two
categories: 程式
top_img: https://imgur.com/o7vOsdO.png
cover: https://imgur.com/o7vOsdO.png
---
OS期末專案做的車牌辨識app
使用OpenCV 3.2以及tess-two
有兩種辨識模式：tess-two與KNN
不要期待它的文字辨識正確率，通常會錯一兩個字
tess-two我沒辦法(其實可以重新訓練data...但有點麻煩)，KNN的車牌字型樣本太少，不過以少量的樣本來說結果還可以接受

這是比較成功的車牌影像
![](https://i.imgur.com/Txd2bHa.png)

[Source Code](https://github.com/CYLMos/Car_Plate_Recognition_APP)