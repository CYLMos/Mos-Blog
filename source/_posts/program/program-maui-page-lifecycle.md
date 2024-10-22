---
title: Maui Page Lifecycle
date: 2024-10-22 21:01:00
tags:
    - 程式
    - .Net
    - Maui
categories: 程式
top_img: https://imgur.com/o7vOsdO.png
cover: https://imgur.com/o7vOsdO.png
---
記錄個在Maui Page的Lifecycle

一個ContentPage除了Constructor外會有4個Event，依序為:
- Appearing
- Loaded
- Disappearing
- Unloaded

執行順序基本上就是由上而下，看起來很簡單，但問題出在執行Navigation後要注意與第二個Page的Lifecycle的執行順序

舉例來說有兩個Page，分別是Page1和Page2，且Page1為Default Page

所以App剛開起來時執行順序如下:
- Page1.Constructor
- Page1.Appearing
- Page1.Loaded

接下來Page1執行Navigate到Page2時，執行順序將會如下:
- **Page2.Constructor**
- **Page1.Disappearing**
- Page2.Appearing
- Page2.Loaded
- **Page1.Unloaded**

可以注意到Page2的Constructor會優先被執行，接下來Page1才會執行Disappearing

而Unloaded則是在Page2已經完成Loaded後才會執行

所以當有個Global的資料要Update之類的，記得要注意

比如說有個Global的資料Data = 1，而他在Page1.Disappearing會執行Data - 1，預期Data會在Page1.Disappearing時等於0

但若在Page2.Constructor也有修改Data資料，例如執行Data - 1，那結果就會不對了，以下是舉例的順序:
- Data = 1
- Page2.Constructor (Data(1) - 1 = 0)
- Page1.Disappearing (Data(0) - 1 = -1)
...

為了避免這狀況，應該要把Page2.Constructor的Data - 1移到Page2.Appearing才對

另外Unloaded由於會在最後才執行，且依照我的經驗，執行的速度可能會有點Delay，所以有離開Page後要立刻執行的最好放在Disappearing，Unloade則放一些釋放資源等較不用立刻執行的動作

以下是Lifecycle的測試Code
[Source Code](https://github.com/CYLMos/Maui-Page-Lifecycle-Test)
