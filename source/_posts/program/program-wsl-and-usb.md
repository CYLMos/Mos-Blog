---
title: 在WSL進行.Net 6 GUI專案的開發和USB的轉接
date: 2022-05-21 18:41:17
tags:
    - 程式
    - .Net
    - .Net 6
    - Avalonia
    - WSL
    - Linux
    - Ubuntu
categories: 程式
thumbnail: https://imgur.com/o7vOsdO.png
---
本篇主要記錄如何透過執行GUI應用程式(Avalonia)在WSL(Windows Subsystem for Linux)上，並能透過Visual Studio來Debug

基本上大部分步驟都參考[這篇](https://www.codeproject.com/Articles/5330464/Running-and-Debugging-Multiplatform-NET-NET-Core-N "這篇")，如果看得懂英文能直接看這篇即可

本篇除了會講上述的議題外，還會講到如何讓WSL能抓到Windows上的USB裝置

# 安裝Linux在WSL上
首先打開Powershell，並執行以下指令
```
wsl -- install -d Ubuntu
```
如果是第一次使用WSL，應該會要求重啟電腦

想查看可安裝版本可以下
```
wsl --list --online
```

安裝其他指定版本，以安裝Debian為例
```
wsl -- install -d Debian
```

安裝完成後會要求輸入使用者名稱跟密碼，要離開可以輸入`exit`
要再次進入可以在Powershell輸入`wsl`即可

進入Ubuntu之後，建議先輸入以下指令更新套件包，並安裝xterm
安裝xterm是為了下一個步驟測試用，非必要
```
sudo apt-get update
sudo apt-get install xterm
```

# 安裝VcXsvr
該程式能夠讓我們在Windows中開啟Linux的GUI應用程式
首先到該[網頁](https://sourceforge.net/projects/vcxsrv/ "網頁")下載並安裝

另外，聽說Windows 11不需要安裝該軟體，但我不確定

安裝完成後，可以找找一個叫XLaunch的程式，並將其開啟
![搜尋XLaunch](https://i.imgur.com/RR3VRPP.png)

之後依照下面的圖片設定Xlaunch
![XLaunch設定01](https://i.imgur.com/2x8EfuT.png)
![XLaunch設定02](https://i.imgur.com/5r20oJK.png)
![XLaunch設定03](https://i.imgur.com/150RLfN.png)

在右下角檢查有出現XLaunch的Icon就是有開啟成功了

# 測試執行Xterm
在Ubuntu上輸入以下指令開啟Xterm
```
xterm
```

結果應該會發現出現以下Error Message
```
xterm: Xt error: Can't open display:
xterm: DISPLAY is not set
```

主要是因為目前Ubuntu中沒有對DISPLAY進行設定
因此我們首先執行以下指令
```
export DISPLAY=$(route.exe print | grep 0.0.0.0 | head -1 | awk '{print $4}'):0.0
echo $DISPLAY
```

結果會因人而異，而我的結果是
```
192.168.2.103:0.0
```

接下來可以直接執行剛剛開啟xterm的指令了
這時就會發現xterm的程式就出現在你Windows的桌面上了
![xterm顯示成功](https://i.imgur.com/o7m8tVl.png)

# 使用Avalonia進行測試並Debug
首先將Avalonia的Demo專案Clone下來
```
git clone https://github.com/npolyak/NP.Avalonia.Demos.git
```

我們只會用到以下這兩個專案
- ConsoleAppForLinuxSubsystemDebuggingDemo
- AvaloniaAppForLinuxSubsystemDebuggingDemo

另外我們還需要安裝.Net 6的套件在Ubuntu上
```
wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update

sudo apt-get install apt-transport-https
sudo apt-get install dotnet-sdk-6.0
```

### 在Console專案進行Debug
首先用Visual Studio打開ConsoleAppForLinuxSubsystemDebuggingDemo
我的VS是2022的版本

打開後在執行那列可以選擇要如何執行該專案，我們要將它改成WSL，如下圖
![更改為使用WSL執行](https://i.imgur.com/aAbtZkd.png)

之後能將Break Point設定在任何地方進行Debug
以下是Break Point觸發和結果顯示
![Break Point觸發](https://i.imgur.com/Zc3W9Ew.png)

結果
```
Yes, it is Linux!!!
Yes, indeed Linux!!!
RuntimeInformation.OSDescription = 'Linux 5.10.16.3-microsoft-standard-WSL2 #1 SMP Fri Apr 2 22:23:49 UTC 2021'
RuntimeInformation.RuntimeIdentifier = 'ubuntu.20.04-x64'
Environment.OSVersion = 'Unix 5.10.16.3'
The program 'dotnet' has exited with code 0 (0x0).
```

### 在GUI專案進行Debug
用Visual Studio打開AvaloniaAppForLinuxSubsystemDebuggingDemo
打開後如在Console專案一樣，改成用WSL執行

不同的是在要在Properties/launchSettings.json進行設定，將裡面的`DISPLAY`屬性改成前面在Ubuntu設定的DISPLAY的值
```
"DISPLAY": "192.168.2.103:0.0"
```

之後執行，也一樣可以再任意地方加入Break Point來Debug，以下是執行結果
![WSL執行Avalonia GUI](https://i.imgur.com/ZIqT6K9.png)

# USB裝置接上WSL的Ubuntu
接下來要讓接在Windows上的USB裝置接到Ubuntu上
微軟在這部分也有[文件](https://docs.microsoft.com/en-us/windows/wsl/connect-usb "文件")能參考

首先有幾點條件要達成
- 作業系統要Windows 10以上
- 處理器要x64/86
- 需要WSL 2的Linux發行版
- Linux核心版本需高於或等於5.10.60.1

這邊不討論前兩點

### 使用WSL 2
在Powershell執行以下指令，可以顯示目前的Linux發行版是用WSL 1還是2
```
wsl -l -v
```

我的結果如下
```
  NAME      STATE           VERSION
* Ubuntu    Running         2
```

因為我的Ubuntu已經是使用WSL 2了，所以已經不用升級了
但若不是的話，需要執行以下指令來升級成WSL 2
```
wsl --set-version <distro name> 2
```

所以以我的為例子，我得要執行以下指令來升級
```
wsl --set-version Ubuntu 2
```

另外記得在升級前必須要將正在執行的Linux系統先關起來，指令如下
```
wsl --shutdown
```

### 升級Linux核心版本
首先在Ubnutu下執行以下指令確認核心版本
```
uname -a
```

我的結果如下
```
Linux DESKTOP-S9T9GJ3 5.10.16.3-microsoft-standard-WSL2 #1 SMP Fri Apr 2 22:23:49 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
```

版本是5.10.16.3，比5.10.60.1還低，因此得要升級Linux核心版本
可以透過以下指令達成，他會將WSL 2進行更新，一樣要先將Linux系統先關掉
```
wsl --update
```

這時候再去下`uname -a`，會發現Linux核心已經升級
```
Linux DESKTOP-S9T9GJ3 5.10.102.1-microsoft-standard-WSL2 #1 SMP Wed Mar 2 00:30:59 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
```

### 安裝必須套件和軟體
在條件都達成後，我們還需要在Windows和Linux裡面都安裝必要的軟體

首先是Ubuntu那邊
先執行以下指令安裝需要的套件
```
sudo apt install linux-tools-5.4.0-77-generic hwdata
sudo update-alternatives --install /usr/local/bin/usbip usbip /usr/lib/linux-tools/5.4.0-77-generic/usbip 20
```

接下來是Windows的部分，到該[網站](https://github.com/dorssel/usbipd-win/releases "網站")下載並安裝usbipd-win，下載msi檔即可
下載或安裝過程可能會告知有風險之類的，不用管

安裝完成後，在cmd執行以下指令取得目前的所有USB裝置
```
usbipd wsl list
```

我的結果如下
```
BUSID  VID:PID    DEVICE                                                        STATE
1-6    0a12:0001  BRLINK Bluetooth 5.0 Device                                   Not attached
1-9    0951:16e5  USB 輸入裝置                                                  Not attached
1-10   1bcf:0005  USB 輸入裝置                                                  Not attached
2-6    0480:a202  USB Mass Storage Device                                       Not attached
3-2    08bb:2902  USB PnP Sound Device, USB 輸入裝置                            Not attached
3-4    056a:0376  Wacom Tablet                                                  Not attached
```

我這邊想讓3-2這個BUS接到Ubuntu上，透過以下指令能達成
```
usbipd wsl attach --busid 3-2
```

這時候執行`usbipd wsl list`可以看到3-2的狀態已經是Attached了
```
BUSID  VID:PID    DEVICE                                                        STATE
3-2    08bb:2902  USB PnP Sound Device, USB 輸入裝置                            Attached - Ubuntu
```

另外如果要拔掉的話，執行以下指令來Detach
```
usbipd wsl detach --busid 3-2
```

接下來跳到Ubuntu那邊，執行以下指令來確認是否有接上
```
lsusb
```

我的結果如下
```
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 002: ID 08bb:2902 Texas Instruments PCM2902 Audio Codec  //This is my device
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

第二個就是我想接上的Device

### 其他
.Net 6要取得USB裝置，應該會需要用到System.IO.Ports的套件，又或是其他人已經寫好的套件

另外還要注意的是，要存取USB裝置會遇到權限問題，不想搞太麻煩，方便為主的話可以到`/dev`找對應的Port，並修改權限
可以執行以下指令來獲得最近插入的Port來得知你插入的裝置是在哪個Port
```
dmesg | grep tty
```

# 後記
由於工作上需要用.Net 6開發Linux的GUI應用程式和操控硬體，所以才會寫本篇做記錄

目前正在研究有沒有辦法透過使用Vendor ID或Device ID來取得對應的Port，如果有結果的話會再更新


------------

2022/05/31更新
下面的Code主要是透過下Command的方式，透過Vendor Id和Product Id來取得對應的Device
```
using System.IO.Ports;

var devices = SerialPort.GetPortNames();
var udevParam = "{device}";
var udevCommand = $"udevadm info --attribute-walk {udevParam} | grep -e idVendor -e idProduct";

var vid = "Your VId"
var pid = "Your PId"

foreach (var device in devices)
{
    string result = string.Empty;
    using (var proc = new Process())
    {
        proc.StartInfo.FileName = "/bin/bash";
        proc.StartInfo.Arguments = "-c \" " + udevCommand.Replace(udevParam, device) + " \"";
        proc.StartInfo.UseShellExecute = false;
        proc.StartInfo.RedirectStandardOutput = true;
        proc.StartInfo.RedirectStandardError = true;
        proc.Start();

        result = proc.StandardOutput.ReadToEnd();
        proc.WaitForExit();
    }

    var vendorIdResult = result.Contains(vid);
    var productIdResult = result.Contains(pid);

    if (vendorIdResult && productIdResult)
        return device;
}

return "Not found";
```