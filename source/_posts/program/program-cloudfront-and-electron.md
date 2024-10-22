---
title: 記錄個Cloudfront和Electron相關的問題
date: 2021-12-22 15:46:06
tags:
    - 程式
    - Electron
    - AWS
    - Cloudfront
    - NodeJS
categories: 程式
top_img: https://imgur.com/o7vOsdO.png
cover: https://imgur.com/o7vOsdO.png
---
最近專案需要用AWS Cloudfront來做一些事情，且需要透過執行Create Invalidation來把所有的Cache清掉。

但是發現Client Side (Electron)不管怎樣都有機會拿到舊的資料，然而Pipeline運作正常，呼叫Create Invalidation也有成功，S3 Bucket裡的內容也有更新，而且Electron裡也會固定呼叫Clear Cache的Function，過了很久一直搞不清楚原因。

後來發現問題出在哪了，但感覺有點蠢

```
app.commandLine.appendSwitch ("disable-http-cache");
```

在Electron加了上面這行，把Http Cache Disable即可
看來在Electron呼叫session.clearStorageData()似乎也不會把所有Cache清掉

----更新----

後來改成下面的寫法:
```
session.clearCache(() => {
    session.clearStorageData();
});
```

測試幾次New Build，Electron在經過Reload後都有拿到最新的資料
看來應該是沒有問題了