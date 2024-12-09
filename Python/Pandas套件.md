# Pandas 套件

![image](https://hackmd.io/_uploads/Bks2HZBIC.png)

Pandas 是一個強大的 Python 資料處理和分析套件，特別適合處理結構化資料。它源自 R 語言，專門用於處理表格式（Table-Like）的資料格式。Pandas 依賴於 NumPy 套件，為 Python 使用者提供了高效能、易用的資料結構和資料分析工具。

:::info
:star: 什麼是結構化資料？

結構化資料是指以表格形式組織的資料，例如：

- CSV 檔案
- Excel 試算表
- 資料庫表格

這種資料格式使得資料易於理解、處理和分析。
:::

## 安裝
使用以下命令通過 pip 安裝 Pandas
```bash
pip install pandas
```
:::info
:star: Pandas 的相依套件包括：

- numpy
- python-dateutil
- pytz
- tzdata

您可以使用 `pip show pandas` 命令查看詳細資訊。
:::

## Pandas 的基本資料結構
Pandas 主要提供兩種基礎資料結構：Series 和 DataFrame。
- Series：一維資料結構，類似列表或一維陣列。
- DataFrame：二維資料結構，是 Pandas 的核心結構。

### 1. Series(單行單列)
Series 是一維陣列結構，可儲存任何資料類型，並自帶索引標籤。

建立 Series：
```python=
import pandas as pd

# 基本 Series
data = [1, 2, 3, 4, 5]
series = pd.Series(data)
print(type(series))
print(series)

# 自訂索引的 Series
index = ['a', 'b', 'c', 'd', 'e']
series_custom = pd.Series(data, index=index)
print(series_custom)
```
輸出：
```
<class 'pandas.core.series.Series'>
0    1
1    2
2    3
3    4
4    5
dtype: int64

a    1
b    2
c    3
d    4
e    5
dtype: int64
```

#### Series 物件的屬性
Series 物件主要有兩個重要屬性：values 和 index。

```python=
import pandas as pd

# 未指定索引
my_obj = pd.Series([4, 7, -5, 3])
print("Values:", my_obj.values)
print("Values type:", type(my_obj.values))
print("Index:", my_obj.index)
print("Index type:", type(my_obj.index))

print("\n")

# 指定索引
my_obj_custom = pd.Series([8, 9, 10, 11], index=['a', 'b', 'c', 'd'])
print("Values:", my_obj_custom.values)
print("Values type:", type(my_obj_custom.values))
print("Index:", my_obj_custom.index)
print("Index type:", type(my_obj_custom.index))
```
輸出：
```
Values: [ 4  7 -5  3]
Values type: <class 'numpy.ndarray'>
Index: RangeIndex(start=0, stop=4, step=1)
Index type: <class 'pandas.core.indexes.range.RangeIndex'>

Values: [ 8  9 10 11]
Values type: <class 'numpy.ndarray'>
Index: Index(['a', 'b', 'c', 'd'], dtype='object')
Index type: <class 'pandas.core.indexes.base.Index'>
```

### 2. DataFrame(多行多列)
DataFrame 是一個二維表格結構，可以儲存不同類型的資料，類似於試算表或資料庫表格。

#### DataFrame 的組成
- 橫：列（row）- 對應索引（index）
- 直：行（column）- 對應欄位（columns）
- 交叉點：格（cell）- 存放資料（data）

![image](https://hackmd.io/_uploads/rykwciUUR.png)

#### DataFrame 的標籤(label)
- 索引標籤（Index Labels）：每一列的唯一標識符
- 欄位名稱（Column Names）：每一行的名稱

![image](https://hackmd.io/_uploads/HkGMjiUL0.png)

#### 建立 DataFrame
```python=
import pandas as pd

# 從字典建立 DataFrame
persons = {
    'Name': ['Alice', 'Bob', 'Charlie'],
    'Age': [25, 30, 35],
    'City': ['台北', '台中', '高雄']
}
df1 = pd.DataFrame(persons)
print("從字典建立的 DataFrame:")
print(df1)

print("\n")

# 從 CSV 檔案讀取 DataFrame
df2 = pd.read_csv('student.csv')
print("從 CSV 檔案讀取的 DataFrame:")
print(df2)
```

#### 查看 DataFrame 的部分資料
```python=
# 顯示前 n 列
print(df.head(10))  # 顯示前 10 列
print(df.head())    # 預設顯示前 5 列

# 顯示後 n 列
print(df.tail(10))  # 顯示最後 10 列
print(df.tail())    # 預設顯示後 5 列
```

### DataFrame 物件的屬性
DataFrame 物件有三個主要屬性：index、columns 和 values。分別代表了索引標籤、欄位名稱與資料。

```python=
import pandas as pd

persons = {
    'Name': ['Alice', 'Bob', 'Charlie'],
    'Age': [25, 30, 35],
    'City': ['台北', '台中', '高雄']
}
df = pd.DataFrame(persons)

print("索引標籤:")
print(df.index)

print("\n欄位名稱:")
print(df.columns)

print("\n資料值:")
print(df.values)
```

## 實用技巧與進階功能
### 1. 讀取與儲存檔案
```python=
# 讀取 CSV
df = pd.read_csv("data.csv")

# 儲存為 CSV
df.to_csv("output.csv", index=False)
```
### 2. 資料選取與過濾
#### 2.1 使用 loc[] 和 iloc[] 進行基於標籤和位置的資料選取
- loc[行標籤, 列標籤]：基於標籤的選取
- iloc[行位置, 列位置]：基於位置的選取
```python=
import pandas as pd

# 建立一個 DataFrame
df = pd.DataFrame({
    '姓名': ['張三', '李四', '王五', '趙六'],
    '年齡': [25, 30, 35, 40],
    '城市': ['台北', '台中', '高雄', '台南']
})

print("原始 DataFrame:")
print(df)

print("\n使用 loc[] 選取 '張三' 的資料:")
print(df.loc[0])  # 選取第一行（索引為0）的所有資料

print("\n使用 iloc[] 選取第二列資料:")
print(df.iloc[1])  # 選取第二行（位置為1）的所有資料

print("\n使用 loc[] 選取 '姓名' 和 '城市' 欄位:")
print(df.loc[:, ['姓名', '城市']])  # 選取所有行，但只選 '姓名' 和 '城市' 列
```

#### 2.2 使用布林索引進行資料過濾
布林索引是一種強大的資料過濾方法，允許您基於特定條件選取資料。您可以使用比較運算符建立布林條件，然後將這些條件應用於 DataFrame。
```python=
# 過濾年齡大於 30 的資料
print("年齡大於 30 的資料:")
print(df[df['年齡'] > 30])  # 建立一個布林條件，並用它來過濾 DataFrame

# 複合條件：年齡大於 30 且來自台北的資料
print("\n年齡大於 30 且來自台北的資料:")
print(df[(df['年齡'] > 30) & (df['城市'] == '台北')])  # 使用 & 組合多個條件
```

### 3. 資料清理
#### 3.1 處理缺失值
在實際的資料集中，經常會遇到缺失值。Pandas 提供了多種方法，如 dropna(), fillna() 來處理這些缺失值，包括刪除包含缺失值的行或列，或者用特定的值填充缺失值。

```python=
import pandas as pd
import numpy as np

# 建立包含缺失值的 DataFrame
df = pd.DataFrame({
    '姓名': ['張三', '李四', np.nan, '趙六'],
    '年齡': [25, np.nan, 35, 40],
    '城市': ['台北', '台中', '高雄', np.nan]
})

print("原始 DataFrame（包含缺失值）:")
print(df)

# 使用 dropna() 刪除包含缺失值的列
print("\n刪除包含缺失值的列:")
print(df.dropna())

# 使用 fillna() 填充缺失值
print("\n填充缺失值（數值填充為 0，字串填充為 '未知'）:")
print(df.fillna({'年齡': 0, '姓名': '未知', '城市': '未知'}))
```

#### 3.2 資料類型轉換
有時需要更改 DataFrame 中列的資料類型，例如將字串轉換為數值或日期。Pandas 的 astype() 方法可以實現這一點。

```python=
# 將年齡轉換為整數類型
df['年齡'] = df['年齡'].fillna(0).astype(int)  # 先填充缺失值，然後轉換為整數

print("將年齡轉換為整數類型後的 DataFrame:")
print(df)
print(df.dtypes)  # 顯示每一列的資料類型
```

#### 3.3 重新索引
reindex() 重新索引允許您更改、添加或刪除 DataFrame 的索引。這在需要重組或擴充資料時非常有用。

```python=
# 重新索引 DataFrame
new_index = ['A', 'B', 'C', 'D']
df_reindexed = df.set_index(pd.Index(new_index))  # 使用新的索引替換原有索引

print("重新索引後的 DataFrame:")
print(df_reindexed)
```

### 4. 資料分析
#### 4.1 基本統計
Pandas 提供了許多內建的統計方法，可以快速獲得資料的摘要統計信息。
```python=
import pandas as pd

# 建立一個新的 DataFrame 用於統計分析
df_stats = pd.DataFrame({
    '數學': [85, 90, 75, 95, 80],
    '英文': [70, 85, 90, 80, 75],
    '科學': [88, 92, 78, 85, 90]
})

print("基本統計描述:")
print(df_stats.describe())  # 提供包括計數、平均值、標準差、最小值、最大值等統計信息

print("\n各科目平均分:")
print(df_stats.mean())  # 計算每一列的平均值
```

#### 4.2 分組操作
分組操作允許您將資料分成不同的組，然後對每個組應用函數。這在進行比較分析時非常有用。
```python=
# 建立一個包含學產生績和班級的 DataFrame
df_grades = pd.DataFrame({
    '姓名': ['張三', '李四', '王五', '趙六', '錢七', '孫八'],
    '班級': ['A', 'B', 'A', 'B', 'A', 'B'],
    '數學': [85, 90, 75, 95, 80, 85],
    '英文': [70, 85, 90, 80, 75, 95]
})

print("按班級分組計算平均分:")
print(df_grades.groupby('班級').mean())  # 先按 '班級' 分組，然後計算每組的平均值
```
#### 4.3 合併資料
Pandas 提供了多種方法來合併不同的 DataFrame，包括基於鍵的合併和簡單的連接。
```python=
# 建立兩個 DataFrame 用於合併
df1 = pd.DataFrame({
    '學號': [1, 2, 3],
    '姓名': ['張三', '李四', '王五']
})

df2 = pd.DataFrame({
    '學號': [2, 3, 4],
    '成績': [85, 90, 95]
})

print("合併兩個 DataFrame（基於學號）:")
# 使用 merge 函數基於 '學號' 列合併兩個 DataFrame，使用外連接（保留所有行）
print(pd.merge(df1, df2, on='學號', how='outer'))
```

### 5. 資料視覺化
Pandas 與 Matplotlib 結合可以輕鬆建立各種圖表，幫助您直觀地理解資料。

```python=
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# 建立一個時間序列資料
dates = pd.date_range('20230101', periods=6)
df = pd.DataFrame(np.random.randn(6, 4), index=dates, columns=list('ABCD'))

# 繪製線圖
df.plot()
plt.title('簡單線圖')
plt.show()

# 繪製柱狀圖
df.iloc[0].plot(kind='bar')
plt.title('柱狀圖')
plt.show()
```

### 6. 時間序列處理
#### 6.1 日期範圍
Pandas 提供了強大的工具來產生和處理日期範圍，這在處理時間序列資料時非常有用。
```python=
# 使用 date_range 函數產生日期範圍
date_range = pd.date_range(start='2023-01-01', end='2023-12-31', freq='M')
print("2023年每月最後一天:")
print(date_range)
```

#### 6.2 重採樣和頻率轉換
重採樣允許您改變時間序列資料的頻率，例如將日資料轉換為月資料。
```python=
# 建立一個時間序列資料
ts = pd.Series(np.random.randn(365), index=pd.date_range('2023-01-01', periods=365))

# 重採樣為月資料
monthly_data = ts.resample('M').mean()  # 將日資料重採樣為月資料，並計算每月平均值
print("月平均資料:")
print(monthly_data)
```

## 網路爬蟲與 Pandas 結合使用
Pandas 是爬蟲應用中資料處理的重要工具，可以幫助我們高效地清理、篩選和分析從網頁中抓取的資料。

爬蟲資料處理的常見工作流程
1. 抓取資料：使用 requests 或 BeautifulSoup 等套件進行資料抓取。
2. 解析資料：將 HTML 或 JSON 內容轉換為結構化資料。
3. 轉換為 DataFrame：將資料儲存為 Pandas 的 DataFrame 進行後續處理。
4. 資料清理：處理重複值、缺失值，格式化資料。
5. 儲存與匯出：將處理後的資料儲存為 CSV、Excel，或導入資料庫。

### 1. 基本爬蟲與資料轉換
如何使用 requests 和 BeautifulSoup 爬取網頁資料，並將其轉換為 Pandas DataFrame。
```python=
import requests
from bs4 import BeautifulSoup
import pandas as pd

# 爬取網頁
url = "https://example.com/products"
response = requests.get(url)
soup = BeautifulSoup(response.text, 'html.parser')

# 解析數據
products = []
for item in soup.find_all('div', class_='product'):
    name = item.find('h2').text
    price = item.find('span', class_='price').text
    products.append({'name': name, 'price': price})

# 轉換為 DataFrame
df = pd.DataFrame(products)
print(df)
```
#### 解析表格
1. Pandas read_html 直接解析 HTML 表格
```python=
import requests
import pandas as pd

# 抓取網頁內容
url = "https://example.com/data"
response = requests.get(url)

# 解析 HTML 表格
tables = pd.read_html(response.text)

# 取第一個表格
df = tables[0]
print(df.head())
```
方法 2: 使用 BeautifulSoup 手動解析 HTML 表格
```python=
import requests
from bs4 import BeautifulSoup
import pandas as pd

# 抓取網頁資料
url = "https://example.com/table-data"
response = requests.get(url)
soup = BeautifulSoup(response.content, "html.parser")

# 解析 HTML 表格
table = soup.find("table")
rows = table.find_all("tr")

# 將資料轉換為結構化格式
data = []
for row in rows:
    cols = row.find_all("td")
    cols = [col.text.strip() for col in cols]
    data.append(cols)

# 將資料轉換為 DataFrame
df = pd.DataFrame(data[1:], columns=data[0])  # 第一行作為欄位名稱

print("抓取的表格資料：")
print(df)

# 儲存為 CSV
df.to_csv("table_data.csv", index=False)
print("已儲存為 CSV 檔案！")
```

### 2. 資料清理與處理
這一節將展示如何清理和處理爬取的資料，包括處理缺失值、轉換資料類型等。
```python=
# 移除價格中的貨幣符號並轉換為浮點數
df['price'] = df['price'].str.replace('$', '').astype(float)

# 處理缺失值
df['price'] = df['price'].fillna(df['price'].mean())

# 添加新列
df['discount'] = df['price'].apply(lambda x: 0.9 * x if x > 100 else x)

print(df)
```

當爬蟲目標提供 API 時，可以直接從 API 獲取 JSON 資料，並用 Pandas 處理
```python=
import requests
import pandas as pd

# 從 API 抓取資料
api_url = "https://api.example.com/data"
response = requests.get(api_url)
json_data = response.json()

# 將 JSON 轉換為 DataFrame
df = pd.DataFrame(json_data)

print("從 API 抓取的資料：")
print(df.head())

# 清理資料（範例：刪除缺失值）
df_cleaned = df.dropna()

# 儲存為 Excel
df_cleaned.to_excel("api_data.xlsx", index=False)
print("清理後的資料已儲存為 Excel 檔案！")
```

### 3. 資料分析與視覺化
如何使用 Pandas 進行基本的數據分析，並使用 matplotlib 進行視覺化。
```python=
import matplotlib.pyplot as plt

# 基本統計
print(df.describe())

# 分組統計
print(df.groupby('category')['price'].mean())

# 繪製柱狀圖
df['price'].plot(kind='bar')
plt.title('Product Prices')
plt.xlabel('Product Index')
plt.ylabel('Price')
plt.show()
```

### 4. 處理不同格式的爬蟲資料
展示如何處理常見的爬蟲數據格式，如 JSON 和 HTML 表格。
```python=
import json

# 處理 JSON 資料
json_data = '''
[
  {"name": "Product A", "price": 99.99},
  {"name": "Product B", "price": 149.99}
]
'''
df_json = pd.read_json(json_data)
print(df_json)

# 處理 HTML 表格
html = '''
<table>
  <tr><th>Name</th><th>Price</th></tr>
  <tr><td>Product C</td><td>79.99</td></tr>
  <tr><td>Product D</td><td>129.99</td></tr>
</table>
'''
df_html = pd.read_html(html)[0]
print(df_html)
```

Pandas 支援處理 JSON 格式並轉換為 DataFrame
```python=
import requests
import pandas as pd

# 抓取 JSON 資料
url = "https://api.example.com/data"
response = requests.get(url)
data = response.json()

# 將 JSON 轉為 DataFrame
df = pd.DataFrame(data["items"])
print(df.head())
```
### 5. 大規模資料處理
```python=
# 分批讀取大型 CSV 文件
chunksize = 1000
for chunk in pd.read_csv('large_data.csv', chunksize=chunksize):
    # 處理每個 chunk
    processed_chunk = chunk['price'] * 1.1  # 例如：將所有價格提高 10%
    # 可以在這裡進行其他操作，如保存處理後的數據
    print(processed_chunk.mean())  # 打印每個 chunk 的平均價格
```

### 6. 爬蟲資料的儲存與讀取
```python=
# 保存為 CSV
df.to_csv('products.csv', index=False)

# 保存為 Excel
df.to_excel('products.xlsx', index=False)

# 讀取 CSV
df_csv = pd.read_csv('products.csv')
print(df_csv)

# 讀取 Excel
df_excel = pd.read_excel('products.xlsx')
print(df_excel)
```

### 7. 多頁資料的整合
爬蟲可能會抓取多頁資料，Pandas 能將多頁資料合併。
```python=
import pandas as pd

dfs = []
for page in range(1, 6):  # 假設有 5 頁
    url = f"https://example.com/data?page={page}"
    response = requests.get(url)
    table = pd.read_html(response.text)[0]
    dfs.append(table)

# 合併所有頁面的資料
final_df = pd.concat(dfs, ignore_index=True)
print(final_df)
```

## 參考資料
- [Pandas 模組](https://utrustcorp.com/python-pandas/)
- [使用 Pandas 的 read_html 讀取網頁上的表格內容](https://matters.town/a/rwems1dhuf15)