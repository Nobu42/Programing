# 💼 管理者権限なしで使える汎用的PowerShellスクリプト集

---

## 📁 1. 特定フォルダ以下のファイル一覧をCSV出力

```powershell
# フォルダ内の全ファイルを再帰的に走査してCSVに保存する
$targetDir = "$env:USERPROFILE\Documents"
$outputCsv = "$env:USERPROFILE\Desktop\file_list.csv"

# Get-ChildItemで再帰的にファイルを取得
Get-ChildItem -Path $targetDir -Recurse -File |
    Select-Object FullName, Length, LastWriteTime |
    Export-Csv -Path $outputCsv -NoTypeInformation -Encoding UTF8
```

📌 **用途**：バックアップチェック、棚卸し、ファイル管理など  
📝 **補足**：`Length`はファイルサイズ（バイト単位）

---

## 📊 2. CSVから条件に一致する行を抽出して別ファイルに出力

```powershell
# 部署名が "営業部" のデータだけを抽出して新しいCSVに保存
$inputCsv = "$env:USERPROFILE\Documents\社員一覧.csv"
$outputCsv = "$env:USERPROFILE\Documents\営業部_抽出.csv"

Import-Csv $inputCsv | Where-Object { $_.部署 -eq "営業部" } |
    Export-Csv -Path $outputCsv -NoTypeInformation -Encoding UTF8
```

📌 **用途**：人事・営業データ分析、部門ごとの抽出  
📝 **補足**：カラム名（ここでは `部署`）はCSVのヘッダと一致する必要があります

---

## 📦 3. ファイル拡張子ごとにフォルダを分けて整理する

```powershell
# ダウンロードフォルダ内のファイルを拡張子別に自動仕分け
$sourceDir = "$env:USERPROFILE\Downloads"

Get-ChildItem -Path $sourceDir -File | ForEach-Object {
    $ext = $_.Extension.TrimStart('.')  # 拡張子（例: pdf）
    if (-not [string]::IsNullOrEmpty($ext)) {
        $destDir = Join-Path $sourceDir $ext
        if (-not (Test-Path $destDir)) {
            New-Item -Path $destDir -ItemType Directory | Out-Null
        }
        Move-Item $_.FullName -Destination $destDir
    }
}
```

📌 **用途**：ダウンロードフォルダの整理、自動分類  
📝 **補足**：拡張子が空のファイルはスキップされます

---

## 🧾 4. テキストファイルの内容を検索・抽出して保存

```powershell
# ログファイルから "Error" を含む行だけを抽出して保存
$logFile = "$env:USERPROFILE\Documents\app.log"
$errorFile = "$env:USERPROFILE\Documents\error_only.log"

Select-String -Path $logFile -Pattern "Error" |
    ForEach-Object { $_.Line } |
    Set-Content -Path $errorFile -Encoding UTF8
```

📌 **用途**：ログ解析、トラブル対応時の迅速な調査  
📝 **補足**：`Select-String`はgrepのような用途に使えます

---

## 📅 5. 今日の日付のフォルダを作成してファイルをまとめる

```powershell
# 今日の日付でフォルダを作成し、指定フォルダ内のファイルを移動
$today = Get-Date -Format "yyyyMMdd"
$sourceDir = "$env:USERPROFILE\Desktop\ToArchive"
$destDir = Join-Path $sourceDir $today

if (-not (Test-Path $destDir)) {
    New-Item -Path $destDir -ItemType Directory | Out-Null
}

Get-ChildItem -Path $sourceDir -File | ForEach-Object {
    Move-Item $_.FullName -Destination $destDir
}
```

📌 **用途**：日次処理やアーカイブ、自動バックアップ整理  
📝 **補足**：`ToArchive`はあらかじめ作っておくか、スクリプトで作ってもOK

---

## 🧹 6. 一定期間更新されていないファイルを削除（例：30日以上前）

```powershell
# ダウンロードフォルダから30日以上前のファイルを削除
$targetDir = "$env:USERPROFILE\Downloads"
$daysThreshold = 30

Get-ChildItem -Path $targetDir -File |
    Where-Object { $_.LastWriteTime -lt (Get-Date).AddDays(-$daysThreshold) } |
    Remove-Item -WhatIf
```

📌 **用途**：ストレージ整理、古いファイルのクリーンアップ  
📝 **補足**：`-WhatIf` を外すと実行されます。最初は外さずにテストを！

---

## 🧠 補足情報

- `$env:USERPROFILE` はログイン中ユーザーのホームディレクトリ
- `-Recurse` はサブフォルダも含めて処理
- `Export-Csv` で `-Encoding UTF8` を使うと文字化け防止
- `-WhatIf` は削除などのコマンドに対して「実行しないけど何をするか教える」安全なオプションです

---

## ✅ カスタマイズも承ります！

「こういう業務を自動化したい」  
「Excelのデータに基づいてファイルを操作したい」  
など、実務に即したスクリプトもお手伝いできます！

お気軽にご相談ください。
