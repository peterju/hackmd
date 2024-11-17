# Nuitka 執行檔打包器
[Nuitka](https://nuitka.net/) 可將 Python 原始碼轉換成 C++，然後再編譯成可執行檔

## 安裝與使用
1. 安裝
```bash=
pip install Nuitka
```
2. 打包指令
```bash=
nuitka --standalone --onefile --enable-plugin=tk-inter --windows-icon-from-ico=圖示.ico --output-dir=out --windows-console-mode=disable 主程式.py
```
參數說明：
* --standalone：產生獨立的可執行檔，包含所有依賴項。
* --onefile：將所有內容打包成一個單一的可執行檔。
* --enable-plugin=tk-inter：啟用 Tkinter 插件，確保 Tkinter GUI 正常運行。
* --windows-icon-from-ico=chklink.ico：設置可執行檔的圖示。
* --output-dir=out：指定輸出目錄。
* --windows-console-mode=disable：禁用 Windows 終端機顯示。

其它參數：
* --show-memory：提供記憶體相關統計資訊(預設關閉)
* --show-progress：提供進度與統計資料(預設關閉)
