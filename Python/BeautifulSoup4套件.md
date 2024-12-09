# BeautifulSoup4 套件
![image](https://hackmd.io/_uploads/rJxARitDa.png)

BeautifulSoup4 (BS4) 是一個專門用於 處理和操作 HTML 及 XML 文件的 Python 套件。它本身不直接解析文件，而是 **依賴底層的解析器** 來完成解析，並將結果轉交給 BS4 進一步處理。BS4 可以在一定程度上修正不完整或格式錯誤的標記，因此格式不正確的 HTML 檔案也能解析。

BS4 支援多種解析器，包括：
- Python 內建的 html.parser（輕量、無需額外安裝，但效能與容錯性較低）；
- 第三方的 lxml（速度快、效能高，適合大型或複雜文件的解析）；
- html5lib（完全遵循 HTML5 標準，能處理格式不良的文檔，但效能較低）。

透過這些解析器，BS4 能夠 遍歷、搜尋及修改已解析的 HTML/XML 樹結構，並支援 CSS 選擇器 來定位和提取特定元素，使資料提取過程更加直觀、簡單，適合用於網頁爬蟲與資料處理任務。

> 與 BS4 類似的網頁解析套件還有 PyQuery，它的語法設計與 jQuery 類似，更適合使用 CSS 選擇器和鏈式操作來快速處理網頁元素，非常適合從前端網頁開發轉向 Python 爬蟲的開發者。

## 安裝套件 
請先為專案建立虛擬環境，然後安裝 beautifulsoup4 套件與相關解析器
```bash=
py -m venv env                      # 建立 env 虛擬環境
env\Scripts\activate                # 啟動 env 虛擬環境
(env) pip install beautifulsoup4    # 安裝 beautifulsoup4 套件

Collecting BeautifulSoup4
  Using cached beautifulsoup4-4.12.2-py3-none-any.whl (142 kB)
Collecting soupsieve>1.2 (from BeautifulSoup4)
  Using cached soupsieve-2.5-py3-none-any.whl.metadata (4.7 kB)
Using cached soupsieve-2.5-py3-none-any.whl (36 kB)
Installing collected packages: soupsieve, BeautifulSoup4
Successfully installed BeautifulSoup4-4.12.2 soupsieve-2.5

(env) pip list                      # 檢視在 env 虛擬環境中裝了那些套件?

Package        Version
-------------- -------
beautifulsoup4 4.12.2
pip            23.3.2
soupsieve      2.5                  # beautifulsoup4 的相依套件

(env) pip install -U beautifulsoup4 # 更新套件命令
(env) pip install lxml html5lib     # 通常還要搭配解析網頁的模組
(env) pip install requests          # 通常要搭配 requests 模組進行 http 的請求
(env) pip freeze >requirements.txt  # 儲存使用套件清單
(env) deactivate                    # 離開 env 虛擬環境
```
:::info
soupsieve 是提供 Beautiful Soup 4 進行 CSS selector 的套件。
:::

## bs4 套件的結構
bs4 是一個套件，包含多個模組，用於解析 HTML 和 XML 文件。
```
bs4/
├── __init__.py
├── builder/
│   ├── __init__.py
│   ├── _html5lib.py
│   ├── _lxml.py
│   ├── _htmlparser.py
│   └── _soup.py
├── dammit.py
├── element.py
├── testing.py
└── ...
```
bs4 將常用的 class 以直接定義或從模組引入等方式寫在 `__init__.py` 中，讓我們可以簡單的使用如： `from bs4 import BeautifulSoup, UnicodeDammit` 的寫法就能引入想要用的類別，而不用寫以下包含了模組的引用方式
```python=
from bs4.模組名 import BeautifulSoup
from bs4.dammit import UnicodeDammit
```

## 解析器 (Parser)
BeautifulSoup 提供多種解析器來分析網頁原始碼並傳回 BeautifulSoup 物件（俗稱"美味的湯"）。
![image](https://hackmd.io/_uploads/H1O0SX37Jl.png)

以下是常用的解析器：
| 解析器    | 速度      | 容錯能力  | 特點      | 適用場景 | 
| -------- | -------- | -------- | -------- | -------- |
| html.parser | 中等 | 強   | Python 內建解析器，無需安裝，輕量級 |簡單、快速處理小型或結構良好的 HTML 文件|
| lxml        | 快速 | 強   | 第三方解析器，效能高，支援 HTML 與 XML 解析 |適合處理大型或結構複雜的文件|
| html5lib    | 慢 | 最強 | 完全遵循 HTML5 標準，模擬瀏覽器行為，容錯能力最強 |處理格式錯誤或結構不完整的 HTML 文件|

注意：lxml 和 html5lib 解析器需要額外安裝：
```bash=
pip install lxml html5lib
```

**範例：**
以下展示如何處理三種不同的 HTML 資料來源，並使用三種不同解析器進行解析。
```python=
import requests
from bs4 import BeautifulSoup

# 1. 使用 requests 獲取網頁原始碼
html = requests.get('http://httpbin.org/get')
# 1.1 若網頁回應的內容類型非 utf-8，則強制改為 utf-8
# html.encoding = 'utf-8' if html.encoding != 'utf-8' else html.encoding
# 1.2 如果網頁沒有指定編碼，再根據網頁的 Content-Type 來猜測編碼
# html.encoding = html.apparent_encoding

# 使用內建的解析器 html.parser
soup = BeautifulSoup(html.text, 'html.parser')
print(str(soup))  # 顯示未排版的網頁原始碼

# 2. 針對本地端的 html 檔案
# 使用解析器 lxml
with open("index.html", 'w', encoding='utf-8') as f:
    f.write(html.text) # 將 requests 抓回來的網頁原始碼寫入 index.html
with open("index.html", 'r', encoding='utf-8') as file:
    soup = BeautifulSoup(file, 'lxml')
print(soup.prettify())  # 顯示美化後的 HTML 結構，便於閱讀

# 3. 針對 html 字串
html = """
<html>
  <head><title>美味的湯</title></head>
  <body><h1>Hello, Beautiful Soup</h1>
  <p>要調製美味的湯需要你持續的投入時間
"""   # 這段 HTML 不完整，缺少閉合標籤
# 使用解析器 html5lib
soup = BeautifulSoup(html, 'html5lib')  # html5lib 會自動補全缺失的標籤
print(str(soup))        # 顯示未排版的網頁原始碼
print(soup.prettify())  # 顯示美化後的 HTML 結構，便於閱讀
```
:::success
如果不確定要使用哪一種解析器，可以從 lxml 開始，因為它在效能與容錯能力之間達到了良好的平衡。
:::

## BS4物件
Python 的 BS4 套件將抓取到的網頁原始碼解析後，建立一個樹狀結構物件(BeautifulSoup)，每個節點都是3種物件之一：、Tag、NavigableString、或 Comment。透過這些物件，我們可以使用各種方法來查找並操作特定的 HTML 標籤與內容。

| 物件類型 | 說明 | 範例 |
| -------- | -------- | -------- |
|BeautifulSoup|代表整個已解析的 HTML 文件，是樹狀結構的根物件|soup = BeautifulSoup(html, 'lxml')|
|Tag|HTML 文件中的一個標籤（節點），可操作其屬性與內容|soup.div 或 soup.find('div')|
|NavigableString|標籤中的文字內容，為可遍歷的文字節點|soup.title.string|
|Comment|HTML 文件中的註解，為 NavigableString 的子類別|soup.find(string=lambda text: isinstance(text, Comment))|

```python=
from bs4 import BeautifulSoup, Comment

# 解析 HTML 文件
html = '''
<html>
  <head>
    <title>範例網頁</title>
  </head>
  <body>
    <div class="content">
      <p>這是一段內容。</p>
      <p><!-- 這是一個註解 --></p>
    </div>
  </body>
</html>
'''
soup = BeautifulSoup(html, 'lxml')

# 1. BeautifulSoup 物件：代表整個 HTML 文件
print(type(soup))  # <class 'bs4.BeautifulSoup'>
print(soup.title)  # <title>範例網頁</title>

# 2. Tag 物件：存取 HTML 標籤
div_tag = soup.div
print(type(div_tag))  # <class 'bs4.element.Tag'>
print(div_tag['class'])  # ['content']  # 取得 class 屬性

# 3. NavigableString 物件：取得標籤內的文字
title_text = soup.title.string
print(type(title_text))  # <class 'bs4.element.NavigableString'>
print(title_text)  # 範例網頁

# 4. Comment 物件：存取 HTML 註解
comment = soup.find(string=lambda text: isinstance(text, Comment))
print(type(comment))  # <class 'bs4.element.Comment'>
print(comment)  # 這是一個註解
```

## 標籤物件(Tag)
每個 HTML 標籤會被轉換為 Tag 物件，我們可以透過 "物件.屬性" 的方式來存取標籤的名稱、屬性、內容等資訊，常見的屬性如下表

| 屬性      | 描述      | 補充說明  |
| -------- | -------- | -------- |
|.name|返回標籤名稱|soup.title.name → 'title'|
|.attrs|返回標籤的所有屬性字典|可使用 tag.attrs['屬性名稱'] 來取得特定屬性值。例如：tag.attrs['class'] 返回該標籤的 class 屬性值，但使用 get 方法更加安全|
|.string|返回標籤本身的文字內容|如果標籤內有嵌套的子標籤，則返回 None|
|.text|返回標籤含子節點內的所有文字內容|如果標籤內有嵌套的子標籤，text 會返回所有子標籤的文字，類似 ''.join(tag.strings)|
|.contents|返回所有直接子節點的列表|只返回直接子節點，不包含更深層的後代節點，並且可能是字串或 Tag 物件|
|.children|返回所有直接子節點的產生器|與 .contents 類似，但 .children 是 生成器 (iterator)，適合用於迴圈操作|
|.parent|返回父標籤|如果標籤是最外層標籤，則 .parent 可能返回 None|

**範例1：**
取得標籤文字的屬性與方法
```python=
from bs4 import BeautifulSoup

html = """
<div>
    <p>   Hello, <b>world   </b>!</p>
</div>
"""
soup = BeautifulSoup(html, 'lxml')
# print(str(soup))  # 顯示未排版的網頁原始碼

p_tag = soup.p
print(p_tag.string)                # None，因為 <p> 標籤內有 <b> 標籤
print(p_tag.text)                  #    Hello, world   !
# get_text() 方法與 text 屬性都會提取子標籤的文字
# 但get_text 方法提供了更多的選項來控制輸出，例如分隔符和去除多餘的空白。
print(p_tag.get_text(strip=True))  # Hello, world!
```

**範例2：**
使用了更多的標籤物件屬性，請耐心的逐一慢慢理解
```python=
from bs4 import BeautifulSoup

# 使用 html 字串作為 HTML 來源
html = """
<html>
  <head>
    <title>The Dormouse's story</title>
  </head>
  <body>
    <div id="content" class="main">
      <h1 class="first">Hello, Beautiful Soup</h1>
      <div>
          <p class="brief">paragraph 1</p>
          <p class="brief">paragraph 2<a href="https://www.google.com.tw">Google</a></p>
      </div>
    </div>
  </body>
</html>
"""

# 使用 BeautifulSoup 解析 HTML
soup = BeautifulSoup(html, 'lxml')

# 提取 title 標籤的名字
tag_name = soup.title.name
print(f"title 標籤名字：{tag_name}")  # title
# 顯示 title 的文字
print(f"title 標籤的文字：{soup.title.string}")  # The Dormouse's story

# 取得 HTML 文件中第一個 <div> 元素的所有屬性
tag_attrs = soup.div.attrs  # .attrs 會回傳一個字典，包含該標籤的所有屬性名稱與對應的值
print(f"div 標籤的所有屬性：{tag_attrs}")  # {'id': 'content', 'class': ['main']}

# 提取 H1 元素的文字
tag_text = soup.h1.text  # 也可以使用 soup.h1.string
print(f"h1 標籤的文字：{tag_text}")  # Hello, Beautiful Soup

# 提取 div 元素的 id 屬性值，返回字串
print(f'div 標籤的 id 值：{soup.div['id']}')  # content

# 提取 h1 元素的 class 屬性值，返回列表
print(f'h1 標籤的 class 值：{soup.h1['class']}')  # ['first']
# 提取 a 元素的 href 屬性值，返回字串
print(f'a 標籤的 href 值：{soup.a['href']}')  # https://www.google.com.tw

# 獲取標籤的所有子節點
tag_contents = soup.div.contents
print(f"div 標籤的所有子節點：{tag_contents}")  # ['\n', <h1 class="first">Hello, Beautiful Soup</h1>, '\n', <div>

# 使用 .children 遍歷 div 子代
print("div 標籤的子代:")
for child in soup.div.children:
    if child.name is None:  # 排除換行等非標籤的元素
        continue
    print('->', child)
# -> <h1 class="first">Hello, Beautiful Soup</h1>
# -> <div>
# <p class="brief">paragraph 1</p>
# <p class="brief">paragraph 2<a href="https://www.google.com.tw">Google</a></p>
# </div>
# 使用 .parent 獲取父代
print("div 標籤的父代:")
print(soup.div.parent)
# <body>
# <div class="main" id="content">
# <h1 class="first">Hello, Beautiful Soup</h1>
# <div>
# <p class="brief">paragraph 1</p>
# <p class="brief">paragraph 2<a href="https://www.google.com.tw">Google</a></p>
# </div>
# </div>
# </body>
```

## BS4 物件的常用方法
### 元素定位
以 div 為中心來看
![image](https://hackmd.io/_uploads/SkMEOq5P6.png)

#### 1. 尋找單一元素：找到返回 Tag，找不到則返回 None
1. find(tag, attributes, recursive, string, kwargs)：找到第一個滿足條件的標籤，返回 Tag 或 None。
    * string 參數支援正規表達式，可以使用正規表達式來匹配文字。
    * limit 參數可以限制返回的結果數量，提高效能。
2. select_one(selector)：使用 CSS 選擇器找到第一個匹配的元素，之後可再使用以下的 [CSS3 偽類選擇器](https://www.webdesigns.com.tw/CSS3-nth-child.asp)。
    * :first-child：可選擇第一個子元素
    * :last-child：可選擇最後一個子元素
    * :nth-child(n)：可選擇第 n 個子元素
        * n：從 1 開始
        * odd：選取奇數
        * even：選取偶數
3. find_next(tag, attributes, string, **kwargs): 找到符合條件的第一個後續節點。
4. find_previous(tag, attributes, string, **kwargs): 找到符合條件的第一個前一節點。
5. find_next_sibling()：找到下一個滿足條件的兄弟元素。
6. find_previous_sibling()：找到上一個滿足條件的兄弟元素。
7. find_parent()：找到父元素
8. get() 方法：從 Tag 物件中取得屬性值
get() 方法用於從 Tag 物件中取得屬性值，當屬性不存在時不會報錯，而是返回 None，這樣的特性可以避免程式發生錯誤。
9. get_text(separator, strip)：返回標籤含子節點內的所有文字內容，可指定分隔符號與去除空白。此方法若不加上參數跟直接取用 tag.text 是一樣的。
    * separator（分隔符號）： 用於區隔相鄰文字的分隔符號，預設為換行符號。
    * strip（去除空白）： 一個布林值，用於指定是否去除文字內容的前後空白。預設為 False。

![image](https://hackmd.io/_uploads/BylSO5cw6.png)

**範例：**
```python=
from bs4 import BeautifulSoup

# HTML 字串
html = """
<html>
  <head>
    <title>BeautifulSoup範例</title>
  </head>
  <body>
    <div id="content">
      <h1 class="title">你好，BeautifulSoup！</h1>
      <div class="container">
        <p class="inner-paragraph">內部段落 1</p>
        <p class="inner-paragraph">內部段落 2</p>
        <p class="inner-paragraph">內部段落 3</p>
      </div>
      <h1 class="title">歡迎！</h1>
      <p class="paragraph">這是一個範例段落。</p>
      <p class="paragraph">另一個段落在這裡。</p>
    </div>
  </body>
</html>
"""

# 使用BeautifulSoup解析HTML
soup = BeautifulSoup(html, 'html.parser')

# 1. find(tag, attributes, recursive, string, kwargs)
first_paragraph = soup.find('p', class_='paragraph')
print("1. 尋找 P 標籤 class='paragraph': ", first_paragraph, sep='\n')
# 回應：<p class="paragraph">這是一個範例段落。</p>
first_paragraph = soup.find('p', string=lambda s: s and '另一個段落' in s)
print("1.1 尋找 P 標籤內文字內容為：另一個段落: ", first_paragraph, sep='\n')
# 回應：<p class="paragraph">另一個段落在這裡。</p>

# 2. select_one(selector)
title_element = soup.select_one('.title')
print("2. 使用 CSS 選擇器尋找標籤 class='title': ", title_element, sep='\n')
# 回應：<h1 class="title">你好，BeautifulSoup！</h1>

# 2-1. select_one 搭配 :first-child 偽類選擇器可選擇第一個子元素
first_child_paragraph = soup.select_one('div.container :first-child')
print("2-1. div.container 第一個子元素: ", first_child_paragraph, sep='\n')
# 回應：<p class="inner-paragraph">內部段落 1</p>

# 2-2. select_one 搭配 :last-child 偽類選擇器可選擇最後一個子元素
last_child_paragraph = soup.select_one('div.container :last-child')
print("2-2. div.container 最後一個子元素: ", last_child_paragraph, sep='\n')
# 回應：<p class="inner-paragraph">內部段落 3</p>

# 2-3. select_one 搭配 :nth-child 偽類選擇器可選擇第 N 個子元素
second_child_paragraph = soup.select_one('div.container :nth-child(3)')
print("2-3. div.container 的第3個子元素: ", second_child_paragraph, sep='\n')
# 回應：<p class="inner-paragraph">內部段落 3</p>

# 2-4. select 搭配 :nth-child(odd) 偽類選擇器選擇所有奇數位置的子元素
odd_paragraphs = soup.select('div.container :nth-child(odd)')
print("2-4. div.container 下所有奇數位置的子元素: ", odd_paragraphs, sep='\n')
# 回應：[<p class="inner-paragraph">內部段落 1</p>, <p class="inner-paragraph">內部段落 3</p>]
for tag in odd_paragraphs:
    print(tag)
# 回應：
# <p class="inner-paragraph">內部段落 1</p>
# <p class="inner-paragraph">內部段落 3</p>

# 3. find_next(tag, attributes, string, **kwargs)
next_node = soup.find('div', class_='container').find_next()
print("3. div.container 的下一個節點: ", next_node, sep='\n')
# 回應：<p class="inner-paragraph">內部段落 1</p>

# 4. find_previous(tag, attributes, string, **kwargs)
previous_node = soup.find('div', class_='container').find_previous()
print("4. div.container 的上一個節點: ", previous_node, sep='\n')
# 回應：<h1 class="title">你好，BeautifulSoup！</h1>

# 5. find_next_sibling()
second_paragraph = soup.find('div', class_='container').find_next_sibling()
print("5. div.container 的下一個兄弟元素: ", second_paragraph, sep='\n')
# 回應：<h1 class="title">歡迎！</h1>

# 6. find_previous_sibling()
previous_paragraph = soup.find('div', class_='container').find_previous_sibling()
print("6. div.container 的上一個兄弟元素: ", previous_paragraph, sep='\n')
# 回應：<h1 class="title">你好，BeautifulSoup！</h1>

# 7. find_parent(tag, attributes, string, **kwargs)
parent_of_second_paragraph = soup.find('p', class_='inner-paragraph').find_parent()
print("7. 內部段落的父節點: ", parent_of_second_paragraph, sep='\n')
# 回應：
# <div class="container">
# <p class="inner-paragraph">內部段落 1</p>
# <p class="inner-paragraph">內部段落 2</p>
# <p class="inner-paragraph">內部段落 3</p>
# </div>

# 8. get(tag, attributes, recursive, string, **kwargs)
tags_attribute = parent_of_second_paragraph.get('class')  # 先決定要取得哪一個標籤的屬性，再取得該標籤的屬性值
print("8. 取得 div 標籤的 class 屬性: ", tags_attribute, sep='\n')

# 9. get_text(separator, strip)
container_div = soup.select_one('div.container')
all_text = container_div.get_text(separator=' ', strip=True)
print("9.1 以 tag.get_text() 取得 p 標籤文字: ", all_text, sep='\n')  # 回應：內部段落 1 內部段落 2 內部段落 3
all_text = container_div.text.strip()
print("9.2 以 tag.text 取得 p 標籤文字: ", all_text, sep='\n')
# 回應：
# 內部段落 1
# 內部段落 2
# 內部段落 3
```

#### 2. 尋找多個元素：找到返回 ResultSet(包含多個 Tag 的集合)，找不到則返回 None
1. find_all(tag, attributes, recursive, string, limit, kwargs)：找到所有滿足條件的標籤。
2. select(selector)：使用 CSS 選擇器找到標籤。
3. find_next_siblings()：找到下一個滿足條件的所有兄弟元素。
4. find_previous_siblings()：找到上一個滿足條件的所有兄弟元素。

**原始碼範例：**
```python=
from bs4 import BeautifulSoup

# HTML範例
html = """
<html>
  <head>
    <title>BeautifulSoup 範例</title>
  </head>
  <body>
    <div id="content">
      <h1 class="title">你好，BeautifulSoup！</h1>
      <p class="paragraph">這是一個範例段落。</p>
      <p class="paragraph">這是另一個段落。</p>
      <div class="container">
        <p class="inner-paragraph">內部段落 1</p>
        <p class="inner-paragraph">內部段落 2</p>
        <p class="inner-paragraph">內部段落 3</p>
      </div>
    </div>
  </body>
</html>
"""

# 使用BeautifulSoup解析HTML
soup = BeautifulSoup(html, 'html.parser')

# 1. find_all(tag, attributes, recursive, string, limit, kwargs)
paragraphs = soup.find_all('p', class_='paragraph')
print("1. 尋找所有標籤為 p 且 class 為 'paragraph' 的元素: ", paragraphs, sep='\n')
# 回應：[<p class="paragraph">這是一個範例段落。</p>, <p class="paragraph">這是另一個段落。</p>]

# 2. select(selector)
inner_paragraphs = soup.select('.container .inner-paragraph')
print("2. 使用 CSS 選擇器尋找 class 為 container 且 class 為 inner-paragraph 的標籤:", inner_paragraphs, sep='\n')
# 回應：[<p class="inner-paragraph">內部段落 1</p>, <p class="inner-paragraph">內部段落 2</p>, <p class="inner-paragraph">內部段落 3</p>]

# 3. find_next_siblings()
first_inner_paragraph = soup.find('p', class_='inner-paragraph')
following_paragraphs = first_inner_paragraph.find_next_siblings('p')
print("3. 尋找第一個內部段落後的所有兄弟元素: ", following_paragraphs, sep='\n')
# 回應：[<p class="inner-paragraph">內部段落 2</p>, <p class="inner-paragraph">內部段落 3</p>]
# 4. find_previous_siblings()
preceding_paragraphs = inner_paragraphs[2].find_previous_siblings('p')
print("4. 尋找第三個內部段落前的所有兄弟元素: ", preceding_paragraphs, sep='\n')
# 回應：[<p class="inner-paragraph">內部段落 2</p>, <p class="inner-paragraph">內部段落 1</p>]
```
### 3. 其它方法
1. replace(old, new)：替換 HTML 標籤內的文字內容，並修改原始的 BeautifulSoup 物件
2. prettify()：以漂亮的格式列印整個檔的內容，返回字串。


**原始碼範例：**
```python=
from bs4 import BeautifulSoup

# HTML範例
html = """
<html>
  <head><title>BeautifulSoup 範例</title></head>
  <body>
    <div id="content"><h1 class="title">  你好，BeautifulSoup！  </h1>
      <div id="container">
        <p class="paragraph">這是一個範例段落。</p>
        <p class="paragraph">另一個段落在這裡。</p>
      </div>
    </div>
  </body>
</html>
"""

# 使用BeautifulSoup解析HTML
soup = BeautifulSoup(html, 'html.parser')

# 1. 使用 replace() 替換文字
title_tag = soup.find('title')
print("1.1 原始 title 內容:", title_tag.get_text())  # 輸出：BeautifulSoup 範例

# 替換 title 內容
title_tag.string.replace_with('新的標題')
print("1.2 修改後的 title 內容:", title_tag.get_text())  # 輸出：新的標題


# 2. prettify()
pretty_html = soup.prettify()
print("\n2. 以漂亮的格式列印整個檔的內容: ", pretty_html, sep='\n')
```

## BeautifulSoup 的輸入與輸出編碼
在處理網頁抓取與解析時，BeautifulSoup 通常會自動處理不同的編碼格式，以確保正確解析並輸出網頁內容。然而，在某些情況下，仍可能需要手動處理輸入或輸出的編碼問題。

### 1. BeautifulSoup 的輸入編碼處理
BeautifulSoup 會自動嘗試檢測並解碼不同來源的 HTML 文件。如果內容編碼不正確或不符合預期，則可能需要手動處理，這時候就要使用 UnicodeDammit 了。

UnicodeDammit 是 BeautifulSoup 庫中的一個工具，用於處理和轉換不同編碼的文字，使其成為 Unicode 格式。這對於解析來自不同來源的網頁內容時特別重要，因為網頁可能使用多種不同的字元編碼。

當你從網頁獲取內容並需要解析時，如果不確定該網頁使用的字元編碼，或者該網頁的字元編碼可能會變化，這時就應該使用 UnicodeDammit。它會嘗試使用多種編碼來解碼文字，直到找到一個合適的編碼。

**範例：**
以下是一個簡單的範例，展示如何使用 UnicodeDammit 和 BeautifulSoup 來解析網頁內容，並輸出網頁的 prettified 內容。
```python=
import requests
from bs4 import BeautifulSoup, UnicodeDammit

# 假設 response 是從網頁獲取的內容
url = "https://www.ncut.edu.tw"
response = requests.get(url)

# 使用 UnicodeDammit 嘗試多種編碼來解碼內容
dammit = UnicodeDammit(response.content, ["utf-8", "latin-1", "iso-8859-1", "windows-1251"])

# 使用 BeautifulSoup 解析解碼後的內容
soup = BeautifulSoup(dammit.unicode_markup, 'lxml')  # 使用 BeautifulSoup 解析網頁內容

print(soup.prettify())  # 顯示美化後的 HTML 結構，便於閱讀
```
**說明：**
1. 導入必要的套件：包括 requests、BeautifulSoup 和 UnicodeDammit。
2. 發送 HTTP 請求：使用 requests.get 發送請求，獲取網頁內容。
3. 使用 UnicodeDammit 處理編碼：將網頁內容轉換為 Unicode 格式，並嘗試使用多種編碼來解碼內容。
4. 使用 BeautifulSoup 解析內容：將解碼後的內容傳遞給 BeautifulSoup 進行解析。
5. 輸出網頁內容：使用 soup.prettify() 方法輸出解析後的網頁內容。

這個範例展示了如何使用 UnicodeDammit 來處理不同編碼的網頁內容，確保能夠正確解析並輸出網頁的內容。

### 2. BeautifulSoup 的輸出編碼處理
在處理並輸出解析後的 HTML 或文字時，可能需要轉換為不同的編碼格式，特別是當目標環境（如終端機或檔案系統）不支援 Unicode 時，這時候就要使用 encode() 方法了。

encode() 方法可將 BeautifulSoup 解析後的內容轉換為特定的編碼格式，並將無法轉換的字元處理為替代字元或 HTML 實體。
```python=
from bs4 import BeautifulSoup

html = "<html><head><title>範例</title></head><body><p>這是段落。</p></body></html>"
soup = BeautifulSoup(html, 'lxml')

# 1. 標準輸出，不做任何格式化，寫入時使用 UTF-8 編碼
with open('output_pretty_unicode.html', 'w', encoding='utf-8') as file:
    file.write(soup.prettify())

# 2. 使用 prettify() 格式化輸出並轉換為 Big5 編碼
pretty_html_big5 = soup.prettify().encode('big5', errors='xmlcharrefreplace')

# 轉換後輸出到終端機（需解碼以正確顯示）
print(pretty_html_big5.decode('big5'))

# 將格式化的內容寫入檔案
with open('output_pretty_big5.html', 'wb') as file:
    file.write(pretty_html_big5)

# 3. 輸出格式化的 HTML 並轉換為 UTF-8 編碼
pretty_html_utf8 = soup.prettify().encode('utf-8')

# 轉換後輸出到終端機（需解碼以正確顯示）
print(pretty_html_utf8.decode('utf-8'))

# 將格式化的內容寫入檔案
with open('output_pretty_utf8.html', 'wb') as file:
    file.write(pretty_html_utf8)
```
說明：
1. prettify() 格式化輸出：將 HTML 結構化，方便閱讀。
2. encode() 轉換編碼：可指定不同的編碼（如 Big5 或 UTF-8）來處理輸出。
3. 處理無法轉換的字元：errors='xmlcharrefreplace' 將無法轉換的字元替換為 HTML 實體，以避免丟失重要資訊。

## 爬蟲範例
![image](https://hackmd.io/_uploads/ryDyE0mVJx.png)

### 1. 原價屋文章標題
以 CSS 選擇器，配合 select_one() 與 select() 方法抓取所有文章標題，最後存到 JSON 檔
```python=
import json
import requests
from bs4 import BeautifulSoup

URL = "http://www.coolpc.com.tw/phpBB2/portal.php"
html = requests.get(URL)

# 使用 html.parser 解析器將網頁文字變成 soup 物件
soup = BeautifulSoup(html.text, 'html.parser')

# 抓取頁面標題
print('-' * 26 + "\n" + soup.title.string.strip() + "\n" + '-' * 26)

# 抓取第一個文章標題
# 使用 select_one 方法抓出第一個符合的項目並回傳 bs4.element.Tag
getdata = soup.select_one(".ultimate-layouts-title-link")
print(f"第一篇文章標題：\n{getdata.get_text(strip=True)}")

# 抓取所有文章標題
# 利用 select 方法抓出所有符合的項目並回傳串列 bs4.element.ResultSet
print("\n所有文章標題")
getdatas = soup.select(".ultimate-layouts-title-link")
for getdata in getdatas:
    print(getdata.get_text(strip=True))
# 將所有文章標題存入 json 檔案
with open("coolpc.json", "w", encoding="utf-8") as f:
    json.dump([getdata.get_text(strip=True) for getdata in getdatas], f, ensure_ascii=False, indent=2)
```

### 2. 博客來-中文書>新書榜
搭配 RE 一起爬
```python=
import re

import requests
from bs4 import BeautifulSoup

URL = "https://www.books.com.tw/web/sys_newtopb/books/"  # 博客來新書網址

# 發送 HTTP 請求並處理錯誤
try:
    response = requests.get(URL)
    response.raise_for_status()  # 檢查是否出現 HTTP 錯誤
except requests.exceptions.RequestException as e:
    print(f"無法連線到網址 {URL}，原因：{e}")
    quit()

# 解析網頁內容
soup = BeautifulSoup(response.content, "lxml")
print(f"頁面標題：{soup.title.text}")

# 抓取新書排行榜列表
book_list_section = soup.find_all("ul", {"class": "clearfix"})[2]

# 解析書籍資料
books = []
for item in book_list_section.select(".item"):
    rank = item.select_one(".stitle .no").text.strip()
    title = item.select_one(".type02_bd-a h4 a").text.strip()

    # 判斷是否存在作者，避免發生例外
    author_tag = item.select_one(".type02_bd-a .msg li a")
    author = author_tag.text.strip() if author_tag else "不明"

    # 使用正規表示式抓取價格
    price_text = item.select_one(".type02_bd-a .msg .price_a").text
    price_match = re.search(r"(\d+)元", price_text)
    price = price_match.group(1) if price_match else "無價格資訊"

    # 將書籍資訊加入列表
    books.append({"rank": rank, "title": title, "author": author, "price": price})

# 列印書籍資訊
for book in books:
    print(f"TOP{book['rank']} {book['title']} 作者：{book['author']} 價格：{book['price']}元")
```

## 參考
* [給初學者的 Python 網頁爬蟲與資料分析 (3) 解構並擷取網頁資料](https://blog.castman.net/%E6%95%99%E5%AD%B8/2016/12/22/python-data-science-tutorial-3.html)
* [Beautiful Soup Documentation](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)