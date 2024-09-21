# 資源管理：with 語法

在 Python 中，資源管理是一個重要的概念，因為系統資源是有限的，使用後必須適時釋放，尤其在處理檔案、資料庫連接或網路連線時，這一點尤為重要。

上下文管理器是 Python 特別設計來管理資源的一種設計模式，通過 __enter__() 和 __exit__() 方法來處理資源的初始化與清理。開發者可以使用 with 語法自動處理這些方法的呼叫，使資源管理變得簡單而高效。with 語法被視為語法糖，簡化了上下文管理器的使用，並自動在進入區塊時進行初始化，結束時進行清理，從而有效避免資源洩漏和性能問題。

with 語法通常用於：
- 檔案的讀寫
- 資料庫連接與操作
- 網路連線管理

這些操作在結束後需要正確釋放資源，否則可能導致資源的佔用。with 透過上下文管理器，自動確保資源在使用完畢後得到正確釋放，使程式碼更加簡潔且安全。

## 常見的 with 語法應用範例
### 1. 檔案處理
檔案的讀寫操作是最常見的資源管理範例之一。傳統上，我們需要手動打開並關閉檔案，但這樣容易遺漏關閉操作。使用 with 語法，我們可以自動打開和關閉檔案，確保在操作完成後檔案被正確關閉。

**不使用 with 語法**
```python=
# 手動開啟與關閉檔案
file = open('example.txt', 'r')
content = file.read()
print(content)
file.close()  # 要記得關閉檔案
```

**使用 with 語法**
```python=
# 使用 with 自動處理檔案的開啟與關閉
with open('example.txt', 'r') as file:
    content = file.read()
    print(content)

# 不需要呼叫 file.close()，Python 會自動關閉文件
```

### 2. 資料庫連線與查詢
資料庫連接是另一個需要手動管理的資源。如果忘記關閉資料庫連接，可能會導致連接資源被耗盡。with 語法可以自動確保資料庫連接在查詢完成後正確關閉，避免遺漏 conn.close() 的風險。

**不使用 with 語法**
```python=
import sqlite3

# 手動管理資料庫連接與關閉
conn = sqlite3.connect('example.db')
cursor = conn.cursor()
cursor.execute("SELECT * FROM member WHERE idno = ? AND pwd = ?", (idno, pwd))
user = cursor.fetchone()
conn.close()  # 必須確保連線被關閉
```
**使用 with 語法**
```python=
import sqlite3

# 使用 with 管理資料庫連接與事務
with sqlite3.connect('example.db') as conn:
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM member WHERE idno = ? AND pwd = ?", (idno, pwd))
    user = cursor.fetchone()

# conn 自動關閉，無需手動呼叫 close()
```

因為 with 語法後面接的是一條敘述，如果還要多加一些設定，通常會改寫成函數，更實務的範例如下：
```python=
import sqlite3

# 建立一個資料庫連接函數，並處理可能發生的例外
def connect_db():
    conn = sqlite3.connect('example.db')
    conn.row_factory = sqlite3.Row  # 使查詢結果可以用欄位名稱來存取
    return conn

# 使用 with 來處理資料庫連接與查詢
try:
    with connect_db() as conn: # try 之後改為函數呼叫
        cursor = conn.cursor()
        cursor.execute("SELECT * FROM member WHERE idno = ? AND pwd = ?", (idno, pwd))
        user = cursor.fetchone()
    # conn 會自動關閉，無需手動呼叫 close()
except sqlite3.Error as e:    # 捕捉並處理 SQLite 相關的錯誤
    print(f"資料庫操作失敗: {e}")
```

### 3. 網路連線
網路連線也需要仔細管理，否則長時間打開連接可能會造成系統問題。with 語法可以自動處理連接的開啟和釋放，在網路請求完成後自動釋放連接資源。

在這個範例中，我們使用 requests.get() 進行網路請求，並將結果以 JSON 形式讀取。with 會自動釋放連接，避免網路資源長時間被占用。

**不使用 with 語法**
```python=
import requests

# 手動管理網路連接
response = requests.get('https://jsonplaceholder.typicode.com/todos/1')
data = response.json()
print(data)
response.close()  # 手動關閉連線
```

**使用 with 語法**
```python=
import requests

# 使用 with 自動管理 HTTP 連線
with requests.get('https://jsonplaceholder.typicode.com/todos/1') as response:
    data = response.json()
    print(data)

# 連線會在 with 區塊結束後自動釋放
```