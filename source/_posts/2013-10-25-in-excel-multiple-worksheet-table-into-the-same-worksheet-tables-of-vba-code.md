---
layout: post
title: 'EXCEL內，多個Worksheet(資料表)合併為同一資料表的VBA 程式碼 (合併多個檔案，步驟二)'
date: 2013-10-25 02:43
comments: true
categories:
---
以下程式碼出處是來自於 [` 彰化一整天的blog http://blog.bestdaylong.com/2008/10/excel.html` ](http://blog.bestdaylong.com/2008/10/excel.html)  再經過我自身需求的改寫所得出符合我需要的程式碼。

常常工作上可能會遇到需要把很多個同樣格式，但是不同檔案的EXCEL表合併起來，所以要做的動作有兩個：
1. 先把`不同檔案`的EXCEL表，每一個檔案裡面的`第一個頁籤(worksheet)、工作表`，自動複製到`一個檔案`內的`不同頁籤`
2. 接著再把一個檔案內的不同頁簽的資料再全部`合併成同一個頁簽`內即可。

`請預先將excel表內的各欄位設計如下，程式碼會自動抓取B欄位內各項儲存格的內容，作為參數`

![muti_worksheet_to1.jpg](http://user-image.logdown.io/user/359/blog/359/post/154220/oqSN5jxQ8akaoP1kQdWw_muti_worksheet_to1.jpg)


##以下為第2步驟的程式碼

```
    Dim a, b, c As Integer '宣告a,b,c為整數

    Dim objsheet As Worksheet

    Dim sheetname As String '工作表檔名

    Dim ispastervalue As String '是否要選擇性貼上值及格式

    Source = Excel.ActiveWorkbook.Name '新檔案視窗編號


    n = Range("b3")
    m = Range("b4")

    x = Range("b5")
    y = Range("b6")

    display_sheetname = UCase(Range("b7"))
    display_row_sheetname = UCase(Range("b8"))
    insert_page = UCase(Range("b9"))
    insert_row = Range("b10")
    ispastervalue = UCase(Range("b11"))


     '將之前合併的結果清除
    Sheet2.Cells.Delete Shift:=xlUp

    z = 1
    i = 1

    Filename = Range("b" & i) & "." & Range("b2")

    FullPath = Excel.Workbooks(Source).Path & "\" & Filename

    '檢查檔案是否存在
    If Dir(FullPath) = "" Then '檢查檔案是否存在
        MsgBox "檔案:" & FullPath & "不存在，請查看是否有拼錯字"
        Exit Sub
    End If


    Workbooks.Open Filename:=Excel.Workbooks(Source).Path & "\" & Filename

    WorkName = Excel.ActiveWorkbook.Name '此檔案名稱

    i = 12

    sheetname = Trim(Windows(Source).ActiveSheet.Range("b" & i))


    If sheetname = "" Then
       For j = 1 To Sheets.Count

            x = Range("b5")
            y = Range("b6")
           Windows(WorkName).Activate
           Set objsheet = Sheets(j) '切換視窗

           objsheet.Activate

            '讀取來源檔案的X(列數),Y(行數)
            If x = 0 Then
                x = n

                Do While True
                    kk = ""
                    For l = 1 To 10
                        For k = 1 To 10
                            If IsError(objsheet.Cells(x + l, k)) = False Then
                                kk = kk & objsheet.Cells(x + l, k)
                            End If
                        Next
                    Next
                    If kk = "" Then Exit Do
                    x = x + 1
                Loop
            End If

            If y = 0 Then
                  y = m

                  Do While True
                      kk = ""
                      For l = 1 To 10
                          For k = 1 To 10
                              If IsError(objsheet.Cells(k, y + l)) = False Then
                                  kk = kk & objsheet.Cells(k, y + l)
                              End If
                          Next
                      Next l
                      If kk = "" Then Exit Do
                      y = y + 1
                  Loop
            End If

            '選取目的
            Windows(WorkName).Activate




            '選取來源範圍
             objsheet.Range(objsheet.Cells(n, m), objsheet.Cells(x, y)).Select

             'copy
             Selection.Copy

            Windows(Source).Activate

            Sheet2.Activate

            If (display_row_sheetname = "Y") Then
                If display_sheetname = "Y" Then
                    Sheet2.Cells(z, 1) = objsheet.Name
                    Sheet2.Cells(z, 2) = objsheet.Name
                Else
                    Sheet2.Cells(z, 1) = objsheet.Name
                End If
                o = 1
            Else
                o = 0
            End If


            If display_sheetname = "Y" Then
               Sheet2.Cells(z + o, 2).Select
               For k = z To z + x - n + o
                  Sheet2.Cells(k, 1) = objsheet.Name
               Next
            Else
               Sheet2.Cells(z + o, 1).Select
            End If

            If (z > 3 And insert_page = "Y") Then
                If display_row_sheetname = "Y" Then
                    ActiveWindow.SelectedSheets.HPageBreaks.Add before:=ActiveCell.Offset(-1, 0)
                Else
                    ActiveWindow.SelectedSheets.HPageBreaks.Add before:=ActiveCell
                End If

            End If


            If ispastervalue = "Y" Then
                Sheet2.Activate
                Selection.PasteSpecial Paste:=xlPasteValuesAndNumberFormats, Operation:= _
        xlNone, SkipBlanks:=False, Transpose:=False
            Else
                ActiveSheet.Paste
            End If


            z = z + x - n + 1 + insert_row + o

           '將來源檔案關閉
           'Windows(Filename).Close

            i = i + 1 '讀取下一個檔案名稱

            Sheet1.Activate

       Next

    Else
        While sheetname <> ""

           x = Range("b5")
           y = Range("b6")

           Windows(WorkName).Activate

           '檢查活頁是否存在
           isfind = False
           For Each st In Sheets
               If st.Name = sheetname Then
                  isfind = True
                  Exit For
               End If
           Next

           If isfind = False Then
               MsgBox "工作表:" & sheetname & "不存在，請查看是否有拼錯字"
               Exit Sub
           End If

           Set objsheet = Sheets(sheetname) '切換視窗

           objsheet.Activate

            '讀取來源檔案的X(列數),Y(行數)
            If x = 0 Then
                x = n

                Do While True
                    kk = ""
                    For l = 1 To 10
                        For k = 1 To 10
                            If IsError(objsheet.Cells(x + l, k)) = False Then
                                kk = kk & objsheet.Cells(x + l, k)
                            End If
                        Next
                    Next
                    If kk = "" Then Exit Do
                    x = x + 1
                Loop
            End If

            If y = 0 Then
                  y = m

                  Do While True
                      kk = ""
                      For l = 1 To 10
                          For k = 1 To 10
                              If IsError(objsheet.Cells(k, y + l)) = False Then
                                  kk = kk & objsheet.Cells(k, y + l)
                              End If
                          Next
                      Next l
                      If kk = "" Then Exit Do
                      y = y + 1
                  Loop
            End If

            '選取目的
            Windows(WorkName).Activate

            '選取來源範圍
             objsheet.Range(objsheet.Cells(n, m), objsheet.Cells(x, y)).Select

             'copy
             Selection.Copy

            Windows(Source).Activate

            Sheet2.Activate

            If (display_row_sheetname = "Y") Then
                If display_sheetname = "Y" Then
                    Sheet2.Cells(z, 1) = objsheet.Name
                    Sheet2.Cells(z, 2) = objsheet.Name
                Else
                    Sheet2.Cells(z, 1) = objsheet.Name
                End If
                o = 1
            Else
                o = 0
            End If


            If display_sheetname = "Y" Then
               Sheet2.Cells(z + o, 2).Select
               For k = z To z + x - n + o
                  Sheet2.Cells(k, 1) = objsheet.Name
               Next
            Else
               Sheet2.Cells(z + o, 1).Select
            End If

            If (z > 3 And insert_page = "Y") Then
                If display_row_sheetname = "Y" Then
                    ActiveWindow.SelectedSheets.HPageBreaks.Add before:=ActiveCell.Offset(-1, 0)
                Else
                    ActiveWindow.SelectedSheets.HPageBreaks.Add before:=ActiveCell
                End If

            End If

            If ispastervalue = "Y" Then
                Sheet2.Activate
                Selection.PasteSpecial Paste:=xlPasteValuesAndNumberFormats, Operation:= _
        xlNone, SkipBlanks:=False, Transpose:=False
            Else
                ActiveSheet.Paste
            End If
             z = z + x - n + 1 + insert_row + o

           '將來源檔案關閉
           'Windows(Filename).Close

            i = i + 1 '讀取下一個檔案名稱

            Sheet1.Activate

            sheetname = Windows(Source).ActiveSheet.Range("b" & i)

        Wend
    End If
    Windows(Source).Activate
    Sheet2.Activate

      With ActiveSheet
      '.Columns.ColumnWidth = .StandardWidth
      .Rows.RowHeight = 20
      End With

    Sheet2.Cells(1, 1).Select

    '將來源檔案關閉
     Workbooks(Filename).Saved = True
     Windows(WorkName).Close

     MsgBox "合併完畢,請按底下[工作表]切換回設[參數設定]"

```
