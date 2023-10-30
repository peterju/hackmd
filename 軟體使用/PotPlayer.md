# PotPlayer 影音播放器

> PotPlayer 是韓國人姜勇囍用 VC++ 所開發的一款內建多種解碼器與硬體加速的影音播放軟體。


![](https://hackmd.io/_uploads/BJvNRcdM6.png)

PotPlayer 由於內建了常用的解碼器，不需要另外安裝解碼器，就能播放 MKV、MP4、AVI、MPEG、FLV、DVD、VCD、SVCD、MOV、RMVB…等大部分的影片檔案； MP3、WAV、FLAC、MIDI、APE、MPC…等大部分的聲音檔案也都能播放。此外！PotPlayer 也支援 DXVA (DirectX Video Acceleration)、CUDA 等硬體加速功能，可以降低CPU的負載，如果你的電腦比較舊，想要觀賞高畫質影片，強烈推薦使用這款軟體。


## 下載與安裝
請先至 [PotPlayer](https://potplayer.daum.net/) 官網下載對應的版本 

![](https://hackmd.io/_uploads/rJX9ghufa.png)

下載後安裝，選擇元件步驟請點選關聯主要檔案格式
![](https://hackmd.io/_uploads/H1F4P0OG6.png)

安裝到最後一步時，請勾選【偵測硬體編碼器/解碼器】
![](https://hackmd.io/_uploads/rkfnv0ufT.png)

選取所有的解碼器

![](https://hackmd.io/_uploads/BJiuZn_zp.png)

## 偏好設定

開啟 PotPlayer 之後，按下【F5】叫出偏好設定

![](https://hackmd.io/_uploads/By3qrnOMa.png)

### 初始化

如果設定被弄得亂七八糟時，可以按下左下角的【初始化】，就可以將所有設定還原為預設值。


### 自動播放相同目錄下的所有檔案
PotPlayer 預設會播放同一個目錄下檔案名稱相似的檔案，很適合播放影集類的影片檔，但有時候同一個目錄下的影片檔案名稱差異很多，若想要可以連續播放所有的檔案，可以設定如下

基本 -》相似檔案開啟方式 -》同時開啟全部檔案

![](https://hackmd.io/_uploads/rk-vlc2fa.png)

### 重複播放清單
PotPlayer 預設會以清單播放的形式進行，就算點選單一檔案，該檔案也會視為一個清單中的唯一檔案，因此若清單中有多個檔案時，它會依照順序播放，但若要重複播放清單，可以設定如下

播放 -》循環播放影片 -》全部重複 (對影片隨機播放不要勾)

![](https://hackmd.io/_uploads/Hyu4I2OzT.jpg)

### 開多個 PotPlayer 預備播放
基本-》多重執行方式-》允許多個 PotPlayer 同時執行
![](https://hackmd.io/_uploads/rysGD3OMT.png)

### 影片播放在延伸螢幕
在筆電連接投影機時，就等於有 2 顆螢幕，這時可按下 Win+P 組合鍵啟動延伸螢幕功能。

在偏好設定中，左側的選單依序點選 「播放」 -> 「全螢幕」，確定主全螢幕是目前顯示器(推薦)，並勾選以全螢幕獨佔模式輸出到顯示器，便可在影片播放時，按下 Enter 或 Alt+Enter 將影片以全螢幕播放的方式送到第二顆螢幕去，此時若按下 Esc 則縮回原來較小的視窗則會回到第一顆主螢幕。

透過這個方式，可以讓我們在主螢幕利用 Space 控制影片暫停或繼續播放，以及觀察影片播了多久時間與剩餘時間，方便操作者可進行各種播放準備而不會影響使用者觀賞影片。

![](https://hackmd.io/_uploads/H1lGOnufT.png)

### 設定可播放的檔案類型
若有些影音檔案並非使用 PotPlayer 播放，可透過以下的設定副檔名關聯方式進行註冊，以後點選該檔案就能開啟 PotPlayer 播自動播放了。

副檔名關聯設定可以勾選想播放的副檔名，若想無腦選擇，則勾【選擇視訊】即可
![](https://hackmd.io/_uploads/Skw_t3_zT.png)

### 設定進度條縮圖
設定當滑鼠游標指向影片進度條時，顯示該時間點的影片縮圖
![](https://hackmd.io/_uploads/Hyfgq2uzT.png)

範例：
![](https://hackmd.io/_uploads/ByOUo3_MT.png)


## 軟體功能

### 重複播放片段(AB區段重複)
若想重複播放某一片段的影片，可設定起訖的點，即A點為開始，播放到B點結束。

播放影片後
- 在想要開始的時間點按下鍵盤的【[】
- 在想要結束的時間點按下鍵盤的【]】
- 若要取消或回復AB段重複播放，可按下【\】

![](https://hackmd.io/_uploads/BkNPBROza.png)

### 製作影片縮圖

製作縮圖的功能目的在提供預覽，讓使用者看圖片就可以知道這部影片的內容大概是什麼(預設5分鐘截16張圖)

播放影片檔後，在想要擷取的影片位置
- 按下 space 暫停影片播放 (不一定要暫停)
- 按下鍵盤的「Alt+N」截取目前影片成為圖片

![](https://hackmd.io/_uploads/ryhlA2uGp.png)

開啟本機電腦的【影片資料夾】，開啟剛才截圖的檔案
![](https://hackmd.io/_uploads/rJwzC2OGp.jpg)

### 轉錄/擷取聲音

將影片中想要的聲音片段轉錄為音檔，屬於邊聽邊錄的形式

播放影片檔後，在想要擷取的影片位置

- 按下 space 暫停影片播放
- 按下鍵盤的「Shift + G」，叫出【音訊錄製】視窗，設定音訊格式與音質
- 按下【開始】進行音訊錄製
- 開始播放影片
- 再按下鍵盤的「Shift + G」叫出【音訊錄製】視窗
- 在想要停止錄音之處按下【停止】，再按【開啟截取資料夾】

![](https://hackmd.io/_uploads/rJidA6dza.png)

![](https://hackmd.io/_uploads/ryL8JAdza.png)


### 轉錄/擷取影片
將想要的影片片段，轉錄為影片檔，屬於邊看邊錄的形式

播放影片檔後，在想要擷取的影片位置
- 按下 space 暫停影片播放
- 按下鍵盤的「Alt + C」，叫出【錄製視訊】視窗
- 若有需要可改變影片存放目錄、影片檔名、視訊編碼與音訊編碼
- 按下【開始】進行視訊錄製
- 開始播放影片
- 再按下鍵盤的「Alt + C」叫出【錄製視訊】視窗
- 在想要停止錄影之處按下【停止】，再按【開啟儲存資料夾】

![](https://hackmd.io/_uploads/r1sRzRdfT.png)

![](https://hackmd.io/_uploads/r13Dz0dM6.png)
