---
layout: post
title: 'EXCEL 內容轉換至 Word 的程式碼'
date: 2013-10-06 07:34
comments: true
categories:
---
嘔心瀝血到處蒐集拼湊出來的把 excel 內容，自動轉換貼到 word 表格的程式碼
我受夠了，一直MAINTAIN 兩份不同的檔案了阿。

VBA 裡面要先做一個動作，要去 include word 的函式庫進來
Step 1 , 先從VBA的選單 ` " 工具 " ` -> ` "設定引用項目"`
Step 2 , 按瀏覽 , 接著去路徑
`   C:\Program Files\Microsoft Office\Office12\ `

裡面會有一個 ` MSWORD.OLB `的檔案 ，再按確定就可以引用完成了。

![sample.jpg](http://user-image.logdown.io/user/359/blog/359/post/144475/MB3g5k2uQHCfjsEXI1UA_sample.jpg)

```
Sub transfer()

'宣告指向 Microsoft Word 文件的變數
Dim appWD As Word.Application

Set appWD = Nothing  '使用完後將appWD變數釋放掉，避免佔用記憶體


'在其他應用程式中，使用要處理物件的 OLE 程式識別符號 作為 CreateObject 函數，
Set appWD = CreateObject("Word.Application")

'如果要檢視其他應用程式的階段作業，請將 Visible 屬性設定為 True。
appWD.Visible = True
appWD.Documents.Add


' 設定word 版面為橫版面

appWD.ActiveDocument.PageSetup.Orientation = wdOrientLandscape


'填入指定格式的文字並而後新增空白段落

appWD.Selection.Font.Size = 22
appWD.Selection.Font.Bold = True
appWD.Selection.TypeText ("Excel 2 Word")  '插入指定的文字
appWD.Selection.TypeParagraph  '插入一個新的空白段落

'複製Excel儲存格某範圍至word檔 -- 評估原則一

appWD.Selection.Font.Size = 16
appWD.Selection.TypeText ("評估原則一、保護標的與生命循環")  '插入指定的文字

Range("C2:H5").Copy
appWD.Selection.Paste
appWD.Selection.TypeParagraph
'appWD.Selection.TypeText ("測試這列文字停在哪邊第一段結束")  '插入指定的文字
appWD.Selection.TypeParagraph

'複製Excel儲存格某範圍至word檔  -- 評估原則二

appWD.Selection.Font.Size = 16
appWD.Selection.TypeText ("評估原則二、預防損害原則")  '插入指定的文字

Range("C6:H12").Copy
appWD.Selection.Paste
appWD.Selection.TypeParagraph
'appWD.Selection.TypeText ("測試這列文字停在哪邊第二段結束")  '插入指定的文字
appWD.Selection.TypeParagraph

'複製Excel儲存格某範圍至word檔  -- 評估原則三

appWD.Selection.Font.Size = 16
appWD.Selection.TypeText ("評估原則三、告知原則")  '插入指定的文字

Range("C19:H26").Copy
appWD.Selection.Paste
appWD.Selection.TypeParagraph
'appWD.Selection.TypeText ("測試這列文字停在哪邊第三段結束")  '插入指定的文字
appWD.Selection.TypeParagraph

'複製Excel儲存格某範圍至word檔  -- 評估原則四

appWD.Selection.Font.Size = 16
appWD.Selection.TypeText ("評估原則四、蒐集限制原則")  '插入指定的文字

Range("C27:H32").Copy
appWD.Selection.Paste
appWD.Selection.TypeParagraph
'appWD.Selection.TypeText ("測試這列文字停在哪邊第四段結束")  '插入指定的文字
appWD.Selection.TypeParagraph

'複製Excel儲存格某範圍至word檔  -- 評估原則五

appWD.Selection.Font.Size = 16
appWD.Selection.TypeText ("評估原則五、個人資料利用原則")  '插入指定的文字

Range("C33:H36").Copy
appWD.Selection.Paste
appWD.Selection.TypeParagraph
'appWD.Selection.TypeText ("測試這列文字停在哪邊第五段結束")  '插入指定的文字
appWD.Selection.TypeParagraph

'複製Excel儲存格某範圍至word檔  -- 評估原則六

appWD.Selection.Font.Size = 16
appWD.Selection.TypeText ("評估原則六、當事人自主原則及查閱及更正原則")  '插入指定的文字

Range("C37:H39").Copy
appWD.Selection.Paste
appWD.Selection.TypeParagraph
'appWD.Selection.TypeText ("測試這列文字停在哪邊第六段結束")  '插入指定的文字
appWD.Selection.TypeParagraph

'複製Excel儲存格某範圍至word檔  -- 評估原則七

appWD.Selection.Font.Size = 16
appWD.Selection.TypeText ("評估原則七、個人資料完整性原則")  '插入指定的文字

Range("C40:H42").Copy
appWD.Selection.Paste
appWD.Selection.TypeParagraph
'appWD.Selection.TypeText ("測試這列文字停在哪邊第七段結束")  '插入指定的文字
appWD.Selection.TypeParagraph

'複製Excel儲存格某範圍至word檔  -- 評估原則八

appWD.Selection.Font.Size = 16
appWD.Selection.TypeText ("評估原則八、安全管理原則")  '插入指定的文字

Range("C43:H70").Copy
appWD.Selection.Paste
appWD.Selection.TypeParagraph
'appWD.Selection.TypeText ("測試這列文字停在哪邊第八段結束")  '插入指定的文字
appWD.Selection.TypeParagraph

'複製Excel儲存格某範圍至word檔  -- 評估原則九

appWD.Selection.Font.Size = 16
appWD.Selection.TypeText ("評估原則九、責任原則")  '插入指定的文字

Range("C71:H79").Copy
appWD.Selection.Paste
appWD.Selection.TypeParagraph
'appWD.Selection.TypeText ("測試這列文字停在哪邊第九段結束")  '插入指定的文字
appWD.Selection.TypeParagraph

For i = 1 To 9

' 設定每一欄位的固定寬度，先至 word  量測過後，再回來調整這邊的數據, 單位是公分

appWD.ActiveDocument.Tables(i).Columns(1).Width = CentimetersToPoints(2)
appWD.ActiveDocument.Tables(i).Columns(2).Width = CentimetersToPoints(3.5)
appWD.ActiveDocument.Tables(i).Columns(3).Width = CentimetersToPoints(8)
appWD.ActiveDocument.Tables(i).Columns(4).Width = CentimetersToPoints(8)
appWD.ActiveDocument.Tables(i).Columns(5).Width = CentimetersToPoints(2)
appWD.ActiveDocument.Tables(i).Columns(6).Width = CentimetersToPoints(2)


'需設定每個table 裡面的  row 可以跨分頁
appWD.ActiveDocument.Tables(i).Rows.AllowBreakAcrossPages = True

   ' 網路上找來的解法 : 設定每個table 的第一列為標題列，必須重複
    Dim oTable As Table
    Dim oCell As Cell
    Set oTable = appWD.ActiveDocument.Tables(i)
    For Each oCell In oTable.Range.Cells
    If oCell.Range.Information(wdEndOfRangeRowNumber) = 1 Then
    oCell.Range.Rows.HeadingFormat = True
    End If
    Next
    Set oCell = Nothing
    Set oTable = Nothing


appWD.ActiveDocument.Tables(i).Select

'所有段落統一靠左
appWD.Selection.Paragraphs.Alignment = wdAlignParagraphLeft


'所有段落設定為最小行高

appWD.Selection.ParagraphFormat.LineSpacingRule = wdLineSpaceExactly
appWD.Selection.ParagraphFormat.LineSpacing = 21

Next i

'複製Excel圖表至word檔
'ActiveSheet.ChartObjects("圖表 2").Activate
'ActiveChart.ChartArea.Select
'ActiveChart.ChartArea.Copy
'appWD.Selection.PasteAndFormat (wdChartPicture)

Set appWD = Nothing  '使用完後將appWD變數釋放掉，避免佔用記憶體

MsgBox (" Copy to Word is done ")
'
End Sub

```
