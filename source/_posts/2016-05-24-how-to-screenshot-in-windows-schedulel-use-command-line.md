---
layout: post
title: '如何在windows 裡面自動定時螢幕截圖存檔 (command line)'
date: 2016-05-24 06:28
comments: true
categories: 
---
目前每天都讓 aws 上面 windows server 自動十二點半開機，然後一點啟動模型下單程式，收盤前會下單，機器兩點關機。
為了要做一個紀錄，我希望可以在每天收盤之後，讓電腦拍一下螢幕快照做歷史紀錄，否則每天自動關機，都沒有留下紀錄，到時候要回來追蹤的時候都毫無辦法。 

很幸運的，老早就有人把windows裡面要做的動作，做成command line tool ，我們要使用 `nircmd` 這個小軟體 
然後指令如下，下面這個是官方舉的例子，意思是
Save 10 screenshots in a loop, and wait 60 seconds between the screenshot save calls. The filenames of the screenshot will contain the time and date of the saved screenshot.

```
nircmd.exe loop 10 60000 savescreenshot c:\temp\scr~$currdate.MM_dd_yyyy$-~$currtime.HH_mm_ss$.png
```

但因為我只需要在收盤之後把畫面拍下來，然後放到 dropbox 的目錄裡面，這樣一同步之後，我在家裡就可以看到當天的數據紀錄了。我們來改一點點參數，變成這樣：

```
nircmd.exe savescreenshot c:\dropbox\Hmodel_~$currdate.yyyy_MM_dd$-~$currtime.HH_mm_ss$.png
```
