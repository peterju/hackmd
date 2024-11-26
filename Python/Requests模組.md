# request 模組

![image](https://hackmd.io/_uploads/SJWS-DfP6.png)

PyPI 中的 requests 模組是一個常用的第三方套件，用來處理 HTTP 請求。這個套件讓你能夠發送 HTTP/1.1 請求，方便地與網路上的資源進行互動。

:::info
**內建的 urllib 套件**

> Python 標準庫中的 urllib.request 與 urllib.response 模組，也能處理 HTTP 請求，相較 requests 模組起來比較低階，適合那些對底層操作更感興趣的使用者。
> 若需要進行 URL 的操作，如解析、修改、構造 URL 的情況下，可以使用 urllib.parse 進行處理。
:::

## 安裝模組 
請先為專案建立虛擬環境，然後安裝 request 模組
```bash=
py -m venv env              # 建立 env 虛擬環境
env\Scripts\activate        # 啟動 env 虛擬環境
(env) pip install requests  # 安裝 requests 套件

(env) pip list              # 檢視在 env 虛擬環境中裝了那些套件?
(env) deactivate            # 離開 env 虛擬環境
```

## HTTP Request
HTTP 1.1 協定中定義了 9 種方法
![image](https://hackmd.io/_uploads/B1gXo6GPp.png =600x)

對於最常被使用的 4 種方法， requests 模組提供了專屬方法：
1. get(url, params=None, **kwargs)：發送 GET 請求。
2. post(url, data=None, json=None, **kwargs)：發送 POST 請求，可傳遞表單或 JSON 資料。
3. put(url, data=None, **kwargs)：發送 PUT 請求。
4. delete(url, **kwargs)：發送 DELETE 請求。

![crud-operations](https://hackmd.io/_uploads/ByyOSvMPp.png)

其餘的 HTTP 請求方法(head, connect, options, trace, patch)，則要用

5. `request(method, url, **kwargs)` 
  這個方法可以指定 method 參數為任意的 http method。

request.get() 與 request.post() 後面有獨立的小節說明其細部內容。

### 使用瀏覽器中的 DevTools 觀察封包
1. 在瀏覽器按下 F12，切換到 Network 頁籤
2. 在瀏覽器上輸入關鍵字進行查詢
3. 點選任一連線後，觀察右側的 Headers 頁籤

![image](https://hackmd.io/_uploads/SkhpAozQkx.png)

## HTTP Response
上述所有的 HTTP 請求方法都會傳回 Response 物件，代表伺服器的回應，我們必須解析這個物件的內容。

![image](https://hackmd.io/_uploads/H1DghvMv6.png)

Response 物件的常用屬性：
1. url：請求的 URL。
2. status_code：HTTP 回應的狀態碼 Status code。
3. encoding：網頁編碼
4. headers：包含 HTTP 回應的標頭 Headers 的字典。
5. cookies：cookies 資訊
6. text：HTTP 回應的 Body 內容，以純文字形式呈現。
7. content： 以二進位形式返回 HTTP 回應的內容。你可以透過 response.content 存取，特別適用於處理非文本類型的回應，例如圖片或影片。

Response 物件的常用方法：
1. json()：解析 JSON 格式的回應內容。
2. raise_for_status()： 只要伺服器回應的狀態碼表示錯誤（非 2xx 範圍），就會引發 requests.exceptions.HTTPError 異常。

範例程式碼：
```python=
import requests

# 發送 GET 請求
# httpbin.org 網站可讓我們測試 http 的各種方法
# http://httpbin.org/get 會回應伺服器收到的 GET 請求內容
response = requests.get('http://httpbin.org/get')

# 檢查請求是否成功
# 方法1：使用 response.status_code
if response.status_code == 200:
    print("Request 成功")
else:
    # 若請求失敗，顯示錯誤碼
    print(f'請求失敗，錯誤碼為: {response.status_code}')
    exit(1)  # 使用 exit(1) 表示異常結束並返回錯誤碼

# 方法2：使用 raise_for_status() 方法
try:
    response.raise_for_status()
    print("Request 成功")
except requests.exceptions.HTTPError as err:
    print("請求失敗")
    print(f"message: {err}")
    print(f"URL: {err.response.url}")
    print(f"Status code: {err.response.status_code}")
    print(f"headers: {err.response.headers}")
    exit(1)  # 使用 exit(1) 表示異常結束並返回錯誤碼

# 顯示回應的物件屬性
print(f'網址：{response.url}')          # 網址
print(f'網頁編碼：{response.encoding}')  # 網頁編碼
response.encoding = 'utf-8' if response.encoding != 'utf-8' else response.encoding

print('標頭資訊', response.headers)      # 回應的標頭資訊
print(response.headers['content-type'])  # 顯示網頁回應的內容類型

print('cookies 資訊')
cookies = response.cookies              # cookies 資訊
for key, value in cookies.items():
    print(f"{key}: {value}")
print('網頁內容')
print(response.text)                    # 網頁內容
print()

# GET 請求 + 自訂標頭
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Safari/537.36'
}
response = requests.get('http://httpbin.org/get', headers=headers)
print(response.text)
# 比較一下以瀏覽器直接開啟網址： http://httpbin.org/get 的內容
# 某些網站一定要有瀏覽器的標頭才能瀏覽
# response = requests.get('https://www.kingstone.com.tw/', headers=headers)
# print(response.text[:400])  # 只顯示前 400 個字元
print()

# GET 請求 + 傳遞參數
payload = {'id': '6943'}  # 等同於在網址後輸入 ？id=6943
response = requests.get('https://www.vscinemas.com.tw/vsweb/film/detail.aspx', params=payload)
print(response.text[:200])  # 只顯示前 200 個字元
print()

# GET 請求 + 圖片存檔
response = requests.get('http://httpbin.org/image/png')
with open('demo.png', mode='wb') as file:
    file.write(response.content)

data_dict = {'key1': 'value1', 'key2': 'value2'}

# POST 請求+form-data
response = requests.post('http://httpbin.org/post', data=data_dict)
print(response.text)
print()

# POST 請求+json
response = requests.post('http://httpbin.org/post', json=data_dict)
print(response.text)
print()
```
:::warning
HTTP 可以同時發送 POST 和 GET 的複合型請求，即可以在同一請求中使用 data 與 params 或者使用 json 與 params。在這種情況下，params 會被視為 GET 參數，而 data 或 json 則是 POST 請求主體的一部分。然而，如果同時指定了 data 和 json，則 data 將被忽略，因為這兩者都是 POST 請求的主體資料。
:::

## 發出 GET 請求：request.get()

GET 請求傳送的資訊會在 URL 裡出現，因此僅適合傳送較少且沒有資安疑慮的文字資訊。

![image](https://hackmd.io/_uploads/SJkQEaGDa.png)

requests.get() 是 requests 模組提供的用於發送 HTTP GET 請求的方法。它可以向指定的 URL 發送 GET 請求，並返回一個伺服器回應的 Response 物件。

![image](https://hackmd.io/_uploads/ByzG7azw6.png)

requests.get() 方法常用參數的如下：
* url(網址)： 欲訪問的 URL。
* params(參數)： 附加到 URL 的查詢參數，可為 dict, tuple, list 或 str。
* headers(標頭)： 請求的標頭信息，以 dict 形式提供。
* cookies(Cookie)： 要附加到請求的 Cookie，可以是 dict 或 CookieJar 類型。
* proxies(代理)：若網站會限制單位時間同一IP的請求次數，可設定代理來繞過此限制，proxies 參數是一個字典。
* auth(身份驗證)： HTTP 基本身份驗證的 tuple (username, password)。
* timeout(超時)： 請求的超時時間，可以是連接超時和讀取超時的 tuple。
* allow_redirects(允許重定向)： 布林值，用於指定是否允許重定向。
* stream(串流處理)： 布林值，預設為 False，代表立即下載整個回應內容。
    * 大文件下載： 當您需要下載大文件時，將 stream 設置為 True 可以逐步下載文件而不是一次性將整個文件讀取到記憶體中，以節省記憶體。
    * 即時處理： 如果您需要即時處理回應內容而不等待整個內容下載完成，可以使用 stream 來進行即時處理。
    * 串流資料： 在處理串流資料時，設置 stream=True 可以逐步獲取和處理資料。

範例程式碼：
```python=
import requests

url = 'http://httpbin.org/get'

params = {'name': 'John', 'phone': '0912-345678'}  # 設定參數
headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Safari/537.36'}                                   # 設定標頭
cookies = {'session_id': 'abc123'}                # 設定 Cookie
proxies = {
    'http': 'http://username:password@proxy.example.com:8080',
    'https': 'http://username:password@proxy.example.com:8080',
}                                                 # 設定代理
auth = ('username', 'password')                   # 設定身份驗證
# 設定超時時間，連接超時 5 秒，讀取超時 10 秒
connection_timeout, read_timeout = 5, 10
timeout = (connection_timeout, read_timeout)
allow_redirects = True                            # 允許重定向
stream = False                                    # 是否立即下載回應內容

# 使用 requests.get() 方法並提供上述引數
response = requests.get(
    url,
    params=params,
    headers=headers,
    cookies=cookies,
    # proxies=proxies,
    auth=auth,
    timeout=timeout,
    allow_redirects=allow_redirects,
    stream=stream,
)

# 顯示結果
print("狀態碼:", response.status_code)
print("文字內容:", response.text)
```
:::success
免費代理伺服器
- http 代理可參考 https://free-proxy-list.net/
- https 代理可參考 https://www.sslproxies.org/

:::
## 發出 POST 請求：request.post()

| 表單使用 GET 請求| 表單使用 POST 請求 |
| -------- | -------- |
|![image](https://hackmd.io/_uploads/Bk7hVaMPa.png)|![image](https://hackmd.io/_uploads/SyVESafD6.png)|

requests.post 方法用於發送 HTTP POST 請求，允許您提交數據到指定的 URL。它可以向指定的 URL 發送 POST 請求，並返回一個伺服器回應的 Response 物件。

> POST 請求傳送的資訊不會在 URL 裡出現，因此適合傳送大量資訊，但有資安疑慮的資訊則建議選擇提供 HTTPS 傳輸協定的伺服器。

requests.post() 方法常用的參數如下：
* url： 要訪問的 URL，這是必需的參數。
* data： 要傳遞的資料，通常用於提交表單資料。可以是 dict、tuple、list或文件物件。
* json： 要傳遞的 JSON 資料。如果同時指定了 data 和 json，則 data 將被忽略，並且 Content-Type 將被設置為 "application/json"。
* headers： 請求標頭，允許自定義 HTTP 標頭。
* cookies： 要附加到請求的 Cookie，可以是字典或 CookieJar 物件。
* auth： HTTP 基本身份驗證的 tuple (username, password)。
* timeout： 請求的超時時間，可以是一個單獨的浮點數（秒），或是包含連接超時和讀取超時的 tuple。
* allow_redirects： 指定是否允許重定向，預設為 True。
* stream： 指示是否立即下載回應內容，預設為 False。
* files： 要上傳的文件，以字典描述
    * key 是文件欄位（file field）的名稱，通常是 file, image
    * value 是代表檔案資料的 tuple，包含3個元素：
        *  file_name：要上傳的文件的名稱，通常是字串。
        *  file_object：檔案物件，通常是使用 open 函數打開的文件。
        *  content_type：文件的媒體類型（MIME type），通常是字串。這個部分是可選的，如果省略，requests 會嘗試猜測文件類型。

:::info
**MIME（Multipurpose Internet Mail Extensions）文件的媒體類型**

content_type 是指文件的媒體類型，也就是 MIME類型。以下是一些常見的類型：

* text/html：HTML 檔案。
* text/plain：純文字檔案。
* image/jpeg：JPEG 圖檔。
* image/png：PNG 圖檔。
* image/gif：GIF 圖檔。
* audio/mpeg：MPEG 音訊檔案。
* video/mpeg：MPEG1 視訊檔案。
* video/mp4：MPEG4 視訊檔案。
* application/json：JSON 格式的檔案。
* application/xml：XML 檔案。
* application/zip：ZIP 壓縮檔案。
* application/pdf：Adobe PDF 檔案。
* application/ms-excel：MS Excel 檔案。
* application/ms-powerpoint：MS powerpoint 檔案。
:::

範例程式碼：
```python=
import requests

url = 'http://httpbin.org/post'

data = {'name': 'John', 'phone': '0912-345678'}  # 要傳遞的資料
headers = {  # 自訂標頭
    'Content-Type': 'application/x-www-form-urlencoded',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Safari/537.36',
}

cookies = {'session_id': 'abc123'}  # 要附加的 Cookie
auth = ('username', 'password')  # HTTP 基本身份驗證
timeout = (5, 10)  # 連接超時 5 秒，讀取超時 10 秒
allow_redirects = True  # 是否允許重定向
stream = False  # 是否立即下載回應內容
# 產生要上傳的檔案
with open('README.md', 'w') as file:
    file.write('# README\n\nThis is a test file.')

# 以字典描述要上傳的 'README.md' 檔案
files = {'file': ('README.md', open('README.md', 'rb'), 'text/plain')}

# 使用 requests.post() 方法發送 POST 請求
response = requests.post(
    url,
    data=data,                        # 要傳遞的資料
    json=None,                        # 在這個例子中未使用 json
    headers=headers,                  # 自訂標頭
    cookies=cookies,                  # 附加 Cookie
    auth=auth,                        # HTTP 基本身份驗證
    timeout=timeout,                  # 設定連接與讀取超時
    allow_redirects=allow_redirects,  # 是否允許重定向
    stream=stream,                    # 是否立即下載回應內容
    files=files,                      # 上傳檔案
)

# 顯示結果
print("狀態碼:", response.status_code)
print("文字內容:", response.text)

```

## 爬蟲概念
「網路爬蟲」是一個透過程式「自動抓取」網站資料的過程，本課程以靜態網頁為對象。
1. 確認爬蟲對象，取得其網址
2. 使用 requests 將網頁抓下來
3. 使用正規表示式 RE 或 BeautifulSoup 等工具分析網頁，抓出感興趣的內容
4. 將抓取的資訊存入資料庫、json、list 或 dict，供未來使用。

### 爬蟲範例1：
想要抓取的文字若能以正規表示式描述，例如 Email，則可應用此範例
```python=
import re

import requests

URL = "https://csie.ncut.edu.tw/content.php?key=86OP82WJQO"

# 描述 Email 的正規表示式物件 pattern
pattern = re.compile(r'\b[\w.%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}\b')
# pattern = re.compile(r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b')
# pattern = re.compile(r'mailto://([^"]+)')

response = requests.get(URL)
match_list = pattern.findall(response.text)  # 從網頁原始碼中尋找所有 Email
unique_emails = list(set(match_list))  # 將列表轉換為集合再轉回列表，以去除重複的 Email
for email in unique_emails:
    print(email)

# 描述姓名的正規表示式物件 pattern
pattern = re.compile(r'<div class="member_name"><a href="[^"]+">([^<]+)</a>')
match_list = pattern.findall(response.text)  # 從網頁原始碼中尋找所有姓名
for _ in match_list:
    print(_)
```

### 爬蟲範例2：
本程式分兩部分
1. 使用正規表示式從網站取得 Proxy IP (77行前)
2. 測試 Proxy IP 是否有效 (77行後)
```python=
# 透過 Proxy IP 來源網站取得 Proxy IP，並測試 Proxy IP 是否有效
import json
import random
import re
from pprint import pprint

import requests

SOURCE = "https://www.sslproxies.org/"  # Proxy IP 來源網站 （https）
TARGET = "http://httpbin.org/get"  # 測試網站 （http）

# 設定 HTTP 標頭
headers = {
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7',
    'Accept-Encoding': 'gzip, deflate, br',
    'Accept-Language': 'zh-TW,zh;q=0.9,en-US;q=0.8,en;q=0.7,ja;q=0.6,zh-CN;q=0.5,la;q=0.4',
    'Cache-Control': 'max-age=0',
    'Connection': 'keep-alive',
    'Cookie': '_ga_SNR7NPLEYG=GS1.1.1651249603.1.0.1651250646.0; _ga_BGEHGPV3SB=GS1.1.1707539001.1.1.1707539323.0.0.0; _gid=GA1.3.1607128698.1707801742; _ga=GA1.1.381372473.1651249604; _ga_Q0EL30K2K5=GS1.1.1707801741.1.0.1707801770.0.0.0; _ga_54MVLT2EZN=GS1.1.1707843944.5.1.1707843969.0.0.0; __RequestVerificationToken_L05ldFNlcnZpY2Vz0=MrnqY4BqFXwyAR3uGWq5prQZPEwGWyzIJgIpuGFLyP8hqJ6eLKM9EWlC8NVA4MZqmyjtxmWT-9ZtzrO04NCTXcM_njimY7J0_WFWHlyWtzE1',
    'Sec-Ch-Ua': '"Not A(Brand";v="99", "Google Chrome";v="131", "Chromium";v="131"',
    'Sec-Ch-Ua-Mobile': '?0',
    'Sec-Platform': '"Windows"',
    'Sec-Fetch-Dest': 'document',
    'Sec-Fetch-Mode': 'navigate',
    'Sec-Fetch-Site': 'none',
    'Sec-Fetch-User': '?1',
    'Upgrade-Insecure-Requests': '1',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Safari/537.36',
}

# 從 valid_proxy.json 讀取之前的有效 Proxy IP
try:
    with open('valid_proxy.json', 'r') as f:
        valid_ips = set(json.load(f))
except FileNotFoundError:
    valid_ips = set()

# 取得新的 Proxy IP
response = requests.get(SOURCE, headers=headers)  # 取得網頁內容
# 使用正規表達式取得 Proxy IP：「\d+」代表數字一個位數以上
proxy_ips = re.findall(r'\d+\.\d+\.\d+\.\d+:\d+', response.text)
print(f"自 {SOURCE} 共取得 {len(proxy_ips)} 個 Proxy IP")

# valid_ips 加入新取得的 Proxy IP
valid_ips.update(proxy_ips)

# 測試 valid_ips 中的 Proxy IP 是否有效
for ip in list(valid_ips):  # 將集合轉換為列表進行迭代
    try:
        response = requests.get(
            TARGET,
            headers=headers,
            # proxies={'http': ip, 'https': ip},
            proxies={'https': ip},
            timeout=5,
        )
        if response.status_code == 200:
            print(f"使用 Proxy IP：{ip} 成功")
        else:
            # 移除失效的 Proxy IP
            valid_ips.remove(ip)
            print(f"使用 Proxy IP：{ip} 失敗")
    except Exception as e:
        valid_ips.remove(ip)
        print(f"使用 Proxy IP：{ip} 失敗, 錯誤訊息：{e}")

# 輸出有效的 Proxy IP
print("\n有效的 Proxy IP：")
if valid_ips:
    pprint(valid_ips)
    print(f"共 {len(valid_ips)} 個")

# 將有效的 Proxy IP 寫入檔案
with open('valid_proxy.json', 'w') as f:
    json.dump(list(valid_ips), f)

# 使用範例：從 valid_proxy.json 讀取有效的 Proxy IP 並隨機選擇一個 Proxy IP 進行測試
if valid_ips:
    proxy = random.choice(list(valid_ips))
    print(f"\n隨機選擇的 Proxy IP：{proxy}")
    try:
        response = requests.get(
            TARGET, headers=headers, proxies={"https": proxy}, timeout=5
        )
        if response.status_code == 200:
            pprint(response.json())  # 輸出網頁內容
    except Exception as e:
        print(f"使用 Proxy IP：{proxy} 失敗, 錯誤訊息：{e}")
else:
    print("沒有有效的 Proxy IP 可供選擇")
```

## HTTP Request 標頭欄位詳解 (進階)

當我們嘗試去看 HTTP Request Headers 時，會看到很多不明所以的標頭欄位，以下就可能造成疑惑的標頭欄位進行說明：

* `"Accept":"text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7"`
這個 "Accept" 標頭告訴伺服器瀏覽器優先接受 HTML 內容，其次是 XHTML、XML、三種圖片格式，接著是通用的任何內容，最後是簽名的內容交換格式。質量因子（q值）用於定義這些媒體類型的相對優先順序。
    * text/html: 表示客戶端希望接受 HTML 內容，這是瀏覽器顯示網頁的主要格式。
    * application/xhtml+xml: 表示客戶端能夠接受 XHTML（可擴展超文本標記語言） 格式的內容。
    * application/xml;q=0.9: 表示客戶端能夠接受 XML 格式的內容，並賦予其質量因子 0.9，這表示它的優先順序較低。
    * image/avif, image/webp, image/apng: 表示客戶端能夠接受三種圖片格式，分別是 AVIF、WebP 和 APNG。
    * */*;q=0.8: 表示客戶端能夠接受任何類型的內容，並賦予其質量因子 0.8，這表示它的優先順序相對較低，當與其他可接受的內容發生衝突時，這個通配符類型的內容會被視為次要選擇。
    * application/signed-exchange;v=b3;q=0.7: 表示客戶端能夠接受簽名的內容交換格式，並賦予其質量因子 0.7，這表示它的優先順序較低。
* `"Accept-Encoding": "gzip, deflate, br"`
表示客戶端支援這三種壓縮算法，而伺服器可以根據這些資訊，選擇適合客戶端的壓縮算法來進行資料傳輸，以提高效率和節省頻寬。

瀏覽器的標頭內容可能會在首次與伺服器建立連線時進行雙向的協商和交握，這些協商和交握的結果可能會影響後續的連線行為，包括是否添加特定的標頭。

舉例來說，當瀏覽器首次訪問一個網站時，伺服器可以回應 HSTS 標頭，告知瀏覽器在未來的某段時間內必須使用安全的 HTTPS 連線。這樣，即使使用者在地址欄中輸入的是不安全的 HTTP URL，瀏覽器也會自動將其升級為 HTTPS。這種協商的行為是由伺服器發起的，並在後續的連線中生效。

類似地，有些標頭（例如 X-Amzn-Trace-Id）可能是在首次連線時由伺服器產生和返回，並在之後的請求中一直存在。這通常與特定的分佈式追蹤系統或其他類型的服務相關聯。

所以若你在標頭中看到不熟悉的屬性，或許就是因為伺服器要求的關係。

* `"Upgrade-Insecure-Requests": "1"`
客戶端瀏覽器在發送請求時附加，用以指示伺服器它可以升級非安全請求（HTTP）到安全請求（HTTPS）。
* `"X-Amzn-Trace-Id": "Root=1-65858fc2-50c4e4447d61a3501e2a7e5b"`
這個標頭是由 AWS X-Ray 或 AWS CloudWatch 之類的服務產生並添加到 HTTP 請求中的，使得在整個 AWS 分佈式系統中能夠追蹤特定的請求。分析這個標識符可以提供有關請求的追蹤信息，例如請求的開始和結束時間，以及請求在分佈式系統中的流程。

以下是一系列安全性（Security）相關的 HTTP 標頭，它們主要用於提高網頁的安全性和保護使用者隱私。
* `"Sec-Ch-Ua": ""Not_A Brand";v="8", "Chromium";v="120", "Brave";v="120""``
這個標頭用於描述瀏覽器的使用者代理（User Agent）的安全性特性。
    * Not_A Brand 表示瀏覽器不屬於特定的品牌，這可能是為了隱藏具體的瀏覽器識別信息。
    * v="8" 表示這個標頭中提到的瀏覽器版本是 8。
    * Chromium;v="120" 表示瀏覽器是基於 Chromium 項目的，並且版本號是 120。
    * Brave;v="120" 表示瀏覽器同時也標明自己是 Brave 瀏覽器，版本號是 120。
* `"Sec-Ch-Ua-Mobile": "?0"`
這個標頭指示瀏覽器不是行動裝置（Mobile Device）。"?0" 的值表示不是行動裝置。
* `"Sec-Ch-Ua-Platform": ""Windows""`
這個標頭指示瀏覽器運行在 Windows 平台。
* `"Sec-Fetch-Dest": "document"`
這個標頭指示瀏覽器的 Sec-Fetch 機制中，這個請求的目的是獲取文檔（document）。
* `"Sec-Fetch-Mode": "navigate"`
這個標頭指示瀏覽器的 Sec-Fetch 機制中，這個請求的模式是導航（navigate），通常表示這是一個鏈接或地址欄輸入導致的請求。
* `"Sec-Fetch-Site": "none"`
這個標頭指示瀏覽器的 Sec-Fetch 機制中，這個請求的站點（site）是 "none"，表示不是從同源站點發出的請求。
* `"Sec-Fetch-User": "?1"`
這個標頭指示瀏覽器的 Sec-Fetch 機制中，這個請求是否包含使用者身份信息。"?1" 的值表示這個請求包含使用者身份信息。
* `"Sec-Gpc": "1"`
這個標頭是 Secure Policy 特性（Sec-GPC）的一部分，它是瀏覽器中的一個標識符，用於支援更高級的安全和隱私功能。

## 參考
- [Requests 函式庫](https://steam.oxxostudio.tw/category/python/spider/requests.html)
- [強大的 HTTP 請求套件 requests](https://quantpass.org/python_requests/)
- [httpbin 介绍](https://xuanwo.io/2016/11/12/httpbin-intro/)
- [requests發送http請求](https://www.youtube.com/watch?v=xua4Gno7xLo)
- [selenium 網頁自動化、網路爬蟲](https://www.youtube.com/watch?v=ximjGyZ93YQ)