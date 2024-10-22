---
title: 使用C++讀出tar檔案的內容
date: 2018-04-08 20:09:37
tags:
    - 程式
    - C++
    - Linux
    - USTAR
    - Tar
categories: 程式
top_img: https://imgur.com/o7vOsdO.png
cover: https://imgur.com/o7vOsdO.png
---
這是學校OOP的作業2
要求是能正確讀出tar檔案格式的內容並將其內容顯示出來
另外還需判是否是USTAR格式，非USTAR則不讀取檔案，直接跳出

[USTAR相關資料](https://en.wikipedia.org/wiki/Tar_(computing))

USTAR基本上每個block的大小為512bytes，所以檔案大小為512的倍數
且在Header的部份的第257個bytes向後數6個bytes的內容為"ustar "

依照上面的兩種特性就能判斷是否為USTAR格式的文件

另外，USTAR內部文件的組成大致上是Header + Content + Header2 + Contetnt2 + .....
Header佔一個block，Content則視其大小決定，Content若有不足一個block，後方則補0x00

比如說Content大小為513 bytes，則第二個block的前1個byte為Content的內容，剩下511個bytes全數補0x00，所以以這例子來說，Content > 512 但 <= 1024，這個Content實際上佔掉了2個block

若只是單純想要讀取tar裡有哪些檔案的話，只需要拿到Header的資料即可
另外，tar的最後兩個block都會以0x00填滿來表示終結字符

以下是執行結果

是USTAR格式的檔案：
![](https://i.imgur.com/1ibB1qO.png)

非USTAR格式的檔案：
![](https://i.imgur.com/q1rRd1D.png)

[Source Code](https://github.com/CYLMos/Read_the_tar_file)