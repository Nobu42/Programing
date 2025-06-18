# Excel原理主義に負けない VBAテンプレ集（脱Excel布教対応）

---

## ✅ 1. CSVファイルを一括読み込みして統合（WiresharkのCSVも対応）

```vba
Sub ImportAllCSVFiles()
    Dim folderPath As String, fileName As String
    Dim ws As Worksheet, lastRow As Long
    Dim i As Long

    folderPath = InputBox("CSVフォルダのパスを入力してください")
    If Right(folderPath, 1) <> "\" Then folderPath = folderPath & "\"

    fileName = Dir(folderPath & "*.csv")
    Set ws = ThisWorkbook.Sheets(1)
    lastRow = 1

    Do While fileName <> ""
        With ws.QueryTables.Add(Connection:="TEXT;" & folderPath & fileName, Destination:=ws.Cells(lastRow, 1))
            .TextFileParseType = xlDelimited
            .TextFileCommaDelimiter = True
            .Refresh BackgroundQuery:=False
        End With
        lastRow = ws.Cells(ws.Rows.Count, 1).End(xlUp).Row + 1
        fileName = Dir()
    Loop
End Sub
```
# 2. 特定のIPアドレスだけ抽出（例：192.168.0.1）

```
Sub FilterByIPAddress()
    Dim ipToFind As String
    ipToFind = InputBox("抽出したいIPアドレスを入力してください")

    With ActiveSheet
        .AutoFilterMode = False
        .Rows(1).AutoFilter Field:=3, Criteria1:=ipToFind ' 例: 3列目がIP
    End With
End Sub
```

# 3. 再送・遅延などのキーワードを含む行を抽出

```
Sub ExtractByKeywords()
    Dim rng As Range, cell As Range
    Dim wsResult As Worksheet
    Dim keywordList As Variant
    Dim rowNum As Long

    Set rng = ActiveSheet.UsedRange
    Set wsResult = Sheets.Add(After:=Sheets(Sheets.Count))
    wsResult.Name = "Filtered"

    keywordList = Array("Retransmission", "ZeroWindow", "Dup Ack")

    rowNum = 1
    For Each cell In rng.Columns(8).Cells ' 例: Info列が8列目
        For Each kw In keywordList
            If InStr(cell.Value, kw) > 0 Then
                rng.Rows(cell.Row).Copy wsResult.Cells(rowNum, 1)
                rowNum = rowNum + 1
                Exit For
            End If
        Next kw
    Next cell
End Sub
```

# 4. ピボットテーブルでIP別パケット数を可視化

```
Sub CreatePivot_IPSummary()
    Dim wsData As Worksheet, wsPivot As Worksheet
    Dim ptCache As PivotCache, pt As PivotTable

    Set wsData = ActiveSheet
    Set wsPivot = Sheets.Add
    wsPivot.Name = "IP集計"

    Set ptCache = ThisWorkbook.PivotCaches.Create( _
        SourceType:=xlDatabase, _
        SourceData:=wsData.UsedRange)

    Set pt = ptCache.CreatePivotTable( _
        TableDestination:=wsPivot.Cells(1, 1), _
        TableName:="IPSummary")

    With pt
        .PivotFields("Source").Orientation = xlRowField ' 列名に応じて変更
        .AddDataField .PivotFields("Length"), "パケット数", xlCount
    End With
End Sub
```

# 5. 条件付き書式で「遅延があるパケット」を赤くする

```
Sub HighlightHighDelay()
    Dim lastRow As Long
    lastRow = Cells(Rows.Count, 1).End(xlUp).Row

    With Range("G2:G" & lastRow) ' G列が「Time Delta」などの遅延列と仮定
        .FormatConditions.Add Type:=xlCellValue, Operator:=xlGreater, Formula1:="=0.1"
        .FormatConditions(.FormatConditions.Count).Interior.Color = RGB(255, 150, 150)
    End With
End Sub
```

# 6. ワンクリック集計ボタンを追加（UIで布教力UP）

```
Sub AddMacroButton()
    Dim btn As Button
    Set btn = ActiveSheet.Buttons.Add(10, 10, 120, 30)
    With btn
        .Caption = "IP集計"
        .OnAction = "CreatePivot_IPSummary"
    End With
End Sub
```

