---
layout: post
title: '多個EXCEL表的Worksheet (資料表頁籤)，合併至一個檔案內不同的Worksheet(資料表頁籤)之VBA 程式碼 (合併多個檔案，步驟一)'
date: 2013-10-25 03:16
comments: true
categories:
---
以下程式碼出處是來自於 [`彰化一整天的blog http://blog.bestdaylong.com/2008/07/excelcopy_31.html`](http://blog.bestdaylong.com/2008/07/excelcopy_31.html)  再經過我自身需求的改寫所得出符合我需要的程式碼。

常常工作上可能會遇到需要把很多個同樣格式，但是不同檔案的EXCEL表合併起來，所以要做的動作有兩個：
1. 先把`不同檔案`的EXCEL表，每一個檔案裡面的`第一個頁籤(worksheet)、工作表`，自動複製到`一個檔案`內的`不同頁籤`
2. 接著再把一個檔案內的不同頁簽的資料再全部`合併成同一個頁簽`內即可。

`請預先將excel表內的各欄位設計如下，程式碼會自動抓取 G欄位 內各項儲存格的內容，作為參數`

![muti_worksheet_to2.jpg](http://user-image.logdown.io/user/359/blog/359/post/154224/LsoPQFe4TGG9WVjhroqz_muti_worksheet_to2.jpg)

##以下為第1步驟的程式碼
#抓取資料夾檔名
```
WorkName = Excel.ActiveWorkbook.Name
MsgBox Excel.Workbooks(WorkName).Path

file_path = Excel.Workbooks(WorkName).Path


fs = Dir(file_path & "\*.*")
Do Until fs = ""
r = r + 1
y = 1
Cells(y + r, 2) = fs
fs = Dir
Loop
```

#合併EXCEL檔案檔名
```
    Dim objsheet As Worksheet


    n = UCase(Range("G2"))
    m = UCase(Range("G3"))
    x = Range("G4")



    WorkName = Excel.ActiveWorkbook.Name '此檔案名稱


    Excel.Workbooks.Add '開新的workbook

    desc = Excel.ActiveWorkbook.Name   '新檔案視窗編號
    i = 2
    While Windows(WorkName).ActiveSheet.Range("b" & i) <> ""
    '    Filename = Windows(WorkName).ActiveSheet.Range("b" & i) & ".xlsx"
        Filename = Windows(WorkName).ActiveSheet.Range("b" & i) & "." & x
        Workbooks.Open Filename:=Excel.Workbooks(WorkName).Path & "\" & Filename    '開啟檔案
        Set objsheet = Windows(Filename).ActiveSheet '切換視窗

         '取消篩選
         ActiveSheet.AutoFilterMode = False


        '選取來源範圍
    '     objsheet.Range("A1:AZ500").Select
          objsheet.Range(n & ":" & m).Select

         'copy
         Selection.Copy

        Windows(desc).Activate '切換視窗
        Sheets.Add  '增加活頁
        ActiveSheet.Paste   '貼上
        ActiveSheet.Range("A1").Select  '將選取範圍取消

        With ActiveSheet
            .Rows.RowHeight = 20
        End With

        ActiveSheet.Name = "No_" & (i - 1)


        'ActiveSheet.Name = Windows(WorkName).ActiveSheet.Range("b" & i)     '將活頁名稱改成檔案名稱


        '避免copy太多資料時,要關閉檔案時.會問記憶體的資料是否要保留
        ActiveSheet.Range("A1").Copy

       '將來源檔案關閉

       Workbooks(Filename).Saved = True
       Windows(Filename).Close

        i = i + 1 '讀取下一個檔案名稱
    Wend
    MsgBox "已將所有檔案匯入各項不同worksheet資料表頁籤中"
```
