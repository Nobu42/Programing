#  標準ユーザーで使える汎用的 Excel VBA スクリプト集

---

##  1. 指定フォルダ以下のファイル一覧を取得し、Excelに出力

```vba
Sub ListFilesToSheet()
    Dim folderPath As String
    Dim fso As Object
    Dim folder As Object, file As Object
    Dim row As Long

    folderPath = "C:\Users\Nobu\Documents\"  ' ★変更可能
    Set fso = CreateObject("Scripting.FileSystemObject")
    Set folder = fso.GetFolder(folderPath)

    row = 2
    With ThisWorkbook.Sheets(1)
        .Cells(1, 1).Value = "ファイル名"
        .Cells(1, 2).Value = "フルパス"
        .Cells(1, 3).Value = "更新日時"

        For Each file In folder.Files
            .Cells(row, 1).Value = file.Name
            .Cells(row, 2).Value = file.Path
            .Cells(row, 3).Value = file.DateLastModified
            row = row + 1
        Next
    End With
End Sub
```

 **用途**：棚卸、ファイル一覧報告、定期調査など

---

##  2. Excelに記載されたファイルパスの存在確認（○×で判定）

```vba
Sub CheckFileExists()
    Dim i As Long
    Dim lastRow As Long
    Dim path As String

    With ThisWorkbook.Sheets(1)
        lastRow = .Cells(.Rows.Count, 1).End(xlUp).Row

        For i = 2 To lastRow
            path = .Cells(i, 1).Value
            If Dir(path) <> "" Then
                .Cells(i, 2).Value = "○"
            Else
                .Cells(i, 2).Value = "×"
            End If
        Next
    End With
End Sub
```

 **用途**：ファイルの有無チェック、紐づけミスの確認など  
 **列Aにパスを書き、結果を列Bに出力**

---

## 📋 3. 選択範囲の文字列を一括で「"」で囲む（コピー用に便利）

```vba
Sub QuoteWrapSelection()
    Dim cell As Range
    For Each cell In Selection
        If cell.Value <> "" Then
            cell.Value = """" & cell.Value & """"
        End If
    Next
End Sub
```


　**用途**：SQLやCSV作成、コマンド生成補助  
 **選択範囲を先にドラッグしてから実行すること**

---

## � 4. 別ブックからデータをコピーしてくる（定型処理向け）

```vba
Sub CopyDataFromAnotherWorkbook()
    Dim srcWb As Workbook
    Dim destWb As Workbook
    Dim filePath As String

    filePath = Application.GetOpenFilename("Excelファイル (*.xlsx), *.xlsx")
    If filePath = "False" Then Exit Sub

    Set destWb = ThisWorkbook
    Set srcWb = Workbooks.Open(filePath)

    ' 例：srcWbのSheet1のA1:C10をdestWbのSheet1のA1から貼り付け
    srcWb.Sheets(1).Range("A1:C10").Copy Destination:=destWb.Sheets(1).Range("A1")

    srcWb.Close SaveChanges:=False
End Sub
```

 **用途**：定型レポートのデータ取り込み、月次データ集約など

---

##  5. 今日の日付のファイル名で保存する（YYYYMMDD）

```vba
Sub SaveAsWithTodayDate()
    Dim today As String
    today = Format(Date, "yyyymmdd")

    ThisWorkbook.SaveAs Filename:=ThisWorkbook.Path & "\Report_" & today & ".xlsx", FileFormat:=xlOpenXMLWorkbook
End Sub
```

 **用途**：日次レポート保存、手動運用のミス防止  
 **保存先は現在のブックのフォルダと同じ場所**

---

## 6. 空白行を自動で削除する（先頭列が空の行を対象）

```vba
Sub DeleteBlankRows()
    Dim i As Long
    With ActiveSheet
        For i = .Cells(.Rows.Count, 1).End(xlUp).Row To 1 Step -1
            If Trim(.Cells(i, 1).Value) = "" Then
                .Rows(i).Delete
            End If
        Next i
    End With
End Sub
```

 **用途**：取り込んだデータの整形、レポート提出前の仕上げに

---

## 補足情報

- `Alt + F11` で VBA エディタを開き、`挿入` → `標準モジュール` に貼り付け
- マクロ実行は `Alt + F8` から対象マクロを選んで実行
- セキュリティの設定により「マクロを有効化」する必要があり
- Excelは保存形式 `.xlsm`（マクロ有効ブック）で保存

---

## カスタマイズするぜーーーー！

- フォルダを再帰的に走査したい
- ファイルのタイムスタンプやサイズでフィルターしたい
- ワンクリックで社内報告資料を自動作成したい  


