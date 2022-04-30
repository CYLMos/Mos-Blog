---
title: Worker Service範例
date: 2022-04-30 14:35:40
tags:
    - 程式
    - C#
    - Worker Service
    - .Net
categories: 程式
thumbnail: https://imgur.com/o7vOsdO.png
---
工作比較閒的時候寫的，沒寫過所以拿來當個練習

功能上是每天在固定時間去證交所撈資料，然後將設定的股票的當日的資料存成Csv

在`appsettings.json`裡的 "AppSettings" 能夠設定像是

- 股票代碼 : `"StockNumber": [ "0050", "0056" ]`就是要撈0050跟0056這兩個股票的資料

- 證交所Url : 撈股票資料的Url，Query的參數用`%s`，理論上不要動

- 每天要撈資料的時間 : `"FetchTime": "16:00:00"`代表每日下午四點會撈資料

- CSV儲存路徑 : `"CsvPath": "C:\\Users\User\CsvData"`

可以透過以下指令去Publish Worker Service
```
dotnet publish -c Release -r win-x64 --no-self-contained -p:PublishSingleFile=true
```

之後用PowerShell執行
```
sc.exe create "Your Service Name" binPath="The Paht of The Service Exe file"
sc.exe start "Your Service Name"
```

如果是用CMD，sc不用加exe

[Source Code](https://github.com/CYLMos/MosWorkerService)