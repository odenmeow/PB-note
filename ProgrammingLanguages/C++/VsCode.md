# vscode設定

## settings.json

```json
{

  // 讓Code Runner 將C++執行在終端機中

  "code-runner.runInTerminal": true,

  // 預設 C++ 程式碼的編碼方式為Big5(繁體中文標準庫) 改utf8

  "[cpp]": {

    "files.encoding": "utf8",

    "editor.defaultFormatter": "ms-vscode.cpptools"

  },

  // "editor.defaultFormatter": "esbenp.prettier-vscode",

  "editor.formatOnSave": true,

  "code-runner.executorMap": {

    "javascript": "node",

    "java": "cd $dir && javac $fileName && java $fileNameWithoutExt",

    "c": "cd $dir && gcc $fileName -o $fileNameWithoutExt && $dir$fileNameWithoutExt",

    "zig": "zig run",

    // "cpp": "cd $dir && g++ $fileName -o $fileNameWithoutExt && $dir$fileNameWithoutExt",

    "cpp": "cd $dir && g++ -finput-charset=UTF-8 -fexec-charset=UTF-8 $fileName -o $fileNameWithoutExt && $dir$fileNameWithoutExt",
```

# terminal更改 (console輸出的地方)

### 先查看terminalOutPut編碼

```batch
PS C:\C++\FirstTest> $OutputEncoding




IsSingleByte      : True

BodyName          : us-ascii

EncodingName      : US-ASCII

HeaderName        : us-ascii

WebName           : us-ascii

WindowsCodePage   : 1252

IsBrowserDisplay  : False

IsBrowserSave     : False

IsMailNewsDisplay : True

IsMailNewsSave    : True

EncoderFallback   : System.Text.EncoderReplacementFallback

DecoderFallback   : System.Text.DecoderReplacementFallback

IsReadOnly        : True

CodePage          : 20127
```

### 設定UFT8 再度查看

```batch
PS C:\C++\FirstTest> $OutputEncoding = [System.Text.Encoding]::UTF8

PS C:\C++\FirstTest> $OutputEncoding




BodyName          : utf-8

EncodingName      : Unicode (UTF-8)

HeaderName        : utf-8

WebName           : utf-8

WindowsCodePage   : 1200

IsBrowserDisplay  : True

IsBrowserSave     : True

IsMailNewsDisplay : True

IsMailNewsSave    : True

IsSingleByte      : False

EncoderFallback   : System.Text.EncoderReplacementFallback

DecoderFallback   : System.Text.DecoderReplacementFallback

IsReadOnly        : True

CodePage          : 65001
```

### 測試發現對於輸出無效

```batch
PS C:\C++\FirstTest> cd "c:\C++\FirstTest\" ; if ($?) { g++ -finput-charset=UTF-8 -fexec-charset=UTF-8 測試.cpp -o 測試 } ; if ($?) { .\測試 }

hi

銝剜?

PS C:\C++\FirstTest> chcp

使用中的字碼頁: 950
```

### 改輸出的編碼後，突然會講中文了

#### 每次重開都要輸入下面這行

> [Console]::OutputEncoding = [System.Text.Encoding]::UTF8

```batch
PS C:\C++\FirstTest> [Console]::OutputEncoding = [System.Text.Encoding]::UTF8

PS C:\C++\FirstTest> cd "c:\C++\FirstTest\" ; if ($?) { g++ -finput-charset=UTF-8 -fexec-charset=UTF-8 測試.cpp -o 測試 } ; if ($?) { .\測試 }

hi

中文
```

# 改變powershell 預設開啟腳本

## 預設開啟()

> $PROFILE 先輸入

> 然後去打開那個文件，輸入

> [Console]::OutputEncoding = [System.Text.Encoding]::UTF8

```batch
PS C:\Users\a0979> $PROFILE

C:\Users\a0979\OneDrive\文件\WindowsPowerShell\Microsoft.PowerShell_profile.ps1

PS C:\Users\a0979>
```

## 改變限制範圍(小心用避免權限給太多)

```batch
PS C:\Users\a0979> Get-ExecutionPolicy

Restricted

PS C:\Users\a0979> Set-ExecutionPolicy RemoteSigned -Scope CurrentUser9>
```

## GPT 詳細回應

在上述錯誤中，您遇到了 PowerShell 的執行權限問題。在某些系統上，PowerShell 默認是禁用腳本執行的。您可以按照以下步驟更改執行策略：

1. 以系統管理員身份打開 PowerShell。

2. 輸入以下命令來檢查當前的執行策略：

   powershellCopy code

   `Get-ExecutionPolicy`

3. 如果策略為 `Restricted`，表示禁止執行任何腳本。您可以使用以下命令將執行策略更改為 `RemoteSigned`：

   powershellCopy code

   `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser`

   或者如果您確定可以信任本機腳本，可以使用 `Unrestricted`：

   powershellCopy code

   `Set-ExecutionPolicy Unrestricted -Scope CurrentUser`

   請注意，`RemoteSigned` 只允許執行本地腳本而不是從網絡下載的腳本，而 `Unrestricted` 將允許執行任何腳本。

4. 然後，再次嘗試創建 PowerShell 啟動文件：

   powershellCopy code

   `New-Item -Type File $PROFILE -Force`

5. 打開啟動文件並添加 `[Console]::OutputEncoding = [System.Text.Encoding]::UTF8`。

6. 保存文件並重新啟動 PowerShell。

這樣應該能夠解決您遇到的執行策略問題。
