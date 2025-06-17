## ✅ Excel VBA：ファイルを3つのパス以下から検索し、除外パスを考慮してB列に出力

---

### 📌 VBAコード（除外フォルダ対応）

```vba
Sub SearchFilesAndInsertPaths()
    Dim searchPaths(1 To 3) As String
    Dim excludePaths As Variant
    Dim ws As Worksheet
    Dim lastRow As Long
    Dim fileName As String
    Dim foundPath As String
    Dim i As Long

    ' 探索対象フォルダ（末尾に \ は不要）
    searchPaths(1) = "D:\target1"
    searchPaths(2) = "E:\archive"
    searchPaths(3) = "F:\shared"

    ' 除外するパス（部分一致でOK、完全パスでもフォルダ名だけでも可）
    excludePaths = Array("D:\target1\temp", "E:\archive\old", "ignoreme")

    Set ws = ThisWorkbook.Sheets(1)
    lastRow = ws.Cells(ws.Rows.Count, "A").End(xlUp).Row

    For i = 2 To lastRow ' 2行目から下へ
        fileName = Trim(ws.Cells(i, 1).Value)
        If fileName <> "" Then
            foundPath = FindFileInFolders(fileName, searchPaths, excludePaths)
            ws.Cells(i, 2).Value = foundPath ' B列に出力
        End If
    Next i

    MsgBox "完了しました！", vbInformation
End Sub

Function FindFileInFolders(fileName As String, searchPaths() As String, excludePaths As Variant) As String
    Dim fso As Object
    Dim folder As Object
    Dim path As Variant

    Set fso = CreateObject("Scripting.FileSystemObject")

    For Each path In searchPaths
        If fso.FolderExists(path) Then
            Set folder = fso.GetFolder(path)
            FindFileInFolders = RecursiveSearch(folder, fileName, excludePaths)
            If FindFileInFolders <> "" Then Exit Function
        End If
    Next path

    FindFileInFolders = ""
End Function

Function RecursiveSearch(folder As Object, fileName As String, excludePaths As Variant) As String
    Dim file As Object
    Dim subFolder As Object
    Dim exclude As Variant

    ' 除外対象チェック
    For Each exclude In excludePaths
        If InStr(1, folder.Path, exclude, vbTextCompare) > 0 Then
            Exit Function
        End If
    Next exclude

    For Each file In folder.Files
        If StrComp(file.Name, fileName, vbTextCompare) = 0 Then
            RecursiveSearch = file.Path
            Exit Function
        End If
    Next file

    For Each subFolder In folder.SubFolders
        RecursiveSearch = RecursiveSearch(subFolder, fileName, excludePaths)
        If RecursiveSearch <> "" Then Exit Function
    Next subFolder

    RecursiveSearch = ""
End Function

```

# 使い方


Excelで Alt + F11 → 標準モジュールへ貼り付け
Alt + F8 → SearchFilesAndInsertPaths を実行
結果は B列に反映されます
🔧 除外フォルダの指定ルール
"ignoreme" のように一部文字列でもOK（パスに含まれていれば除外）
"D:\target1\temp" のようなフルパスもOK
"old" のようなフォルダ名の一部だけでもOK
