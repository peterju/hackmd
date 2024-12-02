# 12.正規表示式

![](https://hackmd.io/_uploads/BkxSfb_u3.png)

正規表示式(Regular Expression)常簡寫為 regex、regexp 或 RE，是一種用來描述字串模式的語法規則。可用一組約定的符號當作樣板，比對想要的字串格式，例如想找到符合電子郵件或手機格式的資料。

許多的程式設計語言與文字編輯器也都支援了以正規表達式，是一種強大且通用的文字模式匹配工具。

## 1. RE 的符號
![image](https://hackmd.io/_uploads/HyBDHlNlC.png)

:::info
RE的線上比對工具：https://regex101.com/
:::

### 1.1 基本正規表示式符號
- . 代表除了換行符號外的任意單一字元
- \d 代表單個數字字元,相當於 [0-9]
- \w 代表單個單字字元(a-z, A-Z, 0-9, _)
- \s 代表單個空白字元(空格、tab等)
- \D \W \S 分別是 \d \w \s 的反向
- [] 中括號內的字元集合，[abc]ar 可匹配 "aar"、"bar"、"car"
- [0-9]、[a-z]：中括號內的連字號代表範圍，可匹配 0-9、a-z (區分大小寫)
- [^A-Z]：若 ^ 若出現在中括號內，表示否定，就是不要 A-Z 其餘都可以
- | 用來匹配兩個模式的任一個
- () 小括號用來建立分組

#### 範例:
- .at 可匹配 "cat", "hat", "rat"等
- \d\d\d 可匹配三位數字
- \w\w\w 可匹配三個單字字元
- [cab]ar 可匹配 "car", "bar", "aar"
- ^c 可匹配開頭是 'c' 的字串
- [^0-9] 可匹配任何非數字字元
- c(at|ow) 可匹配 "cat" 或 "cow"
- `(\d{4})-(\d{6})` 針對【電話號碼是 0936-279195】可匹配 "0936" 或 "279195"

### 1.2 數量符號
數量符號可用來設定前面模式出現的次數:

- `?`：前面的字元是可選的，可出現 0 次或 1 次
- `*`：前面的字元可出現任意次(含0次)
- `+`：前面的字元至少出現一次
- `{n}`：前面的字元重複n次
- `{m,n}`：前面的字元至少重複 m 次，最多 n 次
- `.*?`：非貪婪匹配，盡可能匹配最短的字串

#### 範例:
- `colou?r`：可匹配 "color" 和 "colour"
- `\d{3,}`：可匹配3個或更多數字的情況。
- `\d*`：可匹配任意數目的數字
- `a+b`：可匹配 "ab", "aaab", "aaaaaab"等
- `\w{3}`：可匹配連續3個字元
- `\d{2,4}`：可匹配2到4個數字
- `.*?`：用於非貪婪匹配，如下圖
> 原始字串：`This is a test. This is only a test.`
> 正規表示式：`This.*?test`
![image](https://hackmd.io/_uploads/SkS6_JjQ1l.png)

### 1.3 模式的開頭和結尾
- ^ 若出現在模式開頭,表示必須從字串的第一個位置開始匹配
- $ 若出現在模式結尾,表示直到字串的最後一個位置才結束匹配

#### 範例:
- ^Hello：可匹配開頭是"Hello"的字串
- world$：可匹配結尾是"world"的字串
- ^Hello world$：完全匹配 "Hello world" 字串，前後沒有空白與其它字元

## 2. Python 對 RE 的支援
Python 使用內建的 re 模組來實作正規表示式。

### 2.1 尋找一個符合字串
使用 re.search()、re.match()、re.fullmatch() 來比對字串。
```python=
import re

# 從 string 中尋找 pattern
match = re.search(pattern, string) 

# string 的開頭是否為 pattern
match = re.match(pattern, string)

# 整個 string 是否與 pattern 相同
match = re.fullmatch(pattern, string)
```

#### Match 物件
上面三個方法若匹配成功會回傳一個 Match 物件，而 Match 物件還可以透過下列屬性獲得更多資訊:

- match.group() 獲得匹配的子字串內容
- match.start() 匹配子串的起始索引位置
- match.end() 匹配子串的結束索引位置

範例 1：
```python=
import re

# 範例字串
text = "哈囉，世界！這是一個範例文字。"

# 使用 re.match() 來匹配以 "哈囉" 開頭的字串
match_obj = re.match(r'哈囉', text)
if match_obj:
    print("re.match() 匹配成功:", match_obj.group())
    print(f"開始位置: {match_obj.start()}, 結束位置: {match_obj.end()}")
else:
    print("re.match() 未找到匹配")

# 使用 re.search() 來搜尋包含 "世界" 的字串
search_obj = re.search(r'世界', text)
if search_obj:
    print("re.search() 匹配成功:", search_obj.group())
    print(f"開始位置: {search_obj.start()}, 結束位置: {search_obj.end()}")
else:
    print("re.search() 未找到匹配")

# 使用 re.fullmatch() 來匹配整個字串
fullmatch_obj = re.fullmatch(r'哈囉，世界！這是一個範例文字。', text)
if fullmatch_obj:
    print("re.fullmatch() 匹配成功:", fullmatch_obj.group())
    print(f"開始位置: {fullmatch_obj.start()}, 結束位置: {fullmatch_obj.end()}")
else:
    print("re.fullmatch() 未找到匹配")
```
輸出:
```
re.match() 匹配成功: 哈囉
開始位置: 0, 結束位置: 2
re.search() 匹配成功: 世界
開始位置: 3, 結束位置: 5
re.fullmatch() 匹配成功: 哈囉，世界！這是一個範例文字。
開始位置: 0, 結束位置: 15
```

### 2.2 尋找多個符合字串
透過 re.findall(pattern, string) 可找到字串中所有匹配的模式，會回傳一個列表。
```python=
import re

text = "My emails are test@test.com and hello@world.org"
match = re.findall(r'[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+', text)

print(match)

for email in match:
    print(email)
```
輸出:
```
['test@test.com', 'hello@world.org']
test@test.com
hello@world.org
```
#### 非貪婪匹配
RE 本質上是貪婪的，也就是找出最長匹配，若要找出所有可能的最短匹配，就要使用非貪婪匹配
```python=
import re

# 範例 1：文字段落
text1 = "This is a test. This is only a test."
print(re.findall(r'This.*test', text1))      # 貪婪匹配
print(re.findall(r'This.*?test', text1))     # 非貪婪匹配

# 範例 2：HTML 標籤
html = "<p>First paragraph</p><p>Second paragraph</p>"
print(re.findall(r'<p>.*</p>', html))        # 貪婪匹配
print(re.findall(r'<p>.*?</p>', html))       # 非貪婪匹配
```

### 2.3 取代符合的字串
使用 re.sub(pattern, repl, string) 可以用新字串 repl 替換匹配的模式pattern:
```python=
import re

text = "My phone is 0936-279195, your phone is 0988-123456"
new_text = re.sub(r'\d{4}-\d{6}', '******', text)

print(new_text)
```
輸出:
```
My phone is ******, your phone is ******
```
搭配分組進行字串替換
```python=
import re

text = "My phone is 0936-279195, your phone is 0988-123456"
pattern = r"(\d{4})-(\d{6})" 
replacement = r"\1-####"

result = re.sub(pattern, replacement, text)
print(result)
```

repl 也可以是一個回呼函數，用來對每個匹配到的地方做進一步的處理。
```python=
import re

text = "Their phone numbers are 0936-279195 and 0988-123456"

def replace_phone(match):
    phone = match.group()
    return phone[:4] + "-******"

new_text = re.sub(r'\d{4}-\d{6}', replace_phone, text)
print(new_text)
```
輸出:
```
Their phone numbers are 0936-***** and 0988-*****
```

### 2.4 可重複使用的物件模式
對於重複使用的正規表示式，建議使用 re.compile() 預先編譯為模式物件，效能會較佳：
```python=
import re

pattern = re.compile(r'\d{4}-\d{6}')

# 使用模式物件的方法
match = pattern.search("Call me at 0936-279195")
print(match.group())

matches = pattern.findall("test 0936-123456 and 0977-456789")
print(matches)

```
輸出:
```
0936-279195
['0936-123456', '0977-456789']
```

:::info
**re.match()與re.search()的差別**

re.match只有匹配字串的開頭，如果字符串開頭就不符合正則表達式，則匹配失敗，函式回傳 None；而re.search()則是整個字串都會做匹配，只要找到一個匹配就表示成功，整個字串都沒有匹配才會回傳 None。
:::

### 2.5 Match物件的 group() 方法
- group(0)代表匹配整個正則表達式的字串
- group(1)、group(2)則代表第一個和第二個小括號分組匹配的內容
- groups()返回一個包含所有小括號分組字串的元組。
```python=
# 宣告使用 re 模組
import re

# 可省略建立 re 樣板的作法
txt = "Hello sir, ur phone is 0936-279195"
print(re.match(r'\d{4}-\d{6}', txt))
print(re.search(r'\d{4}-\d{6}', txt))

# Match物件的 group() 方法可得知符合的文字
text = "Hello sir, ur mail is peterju@ncut.edu.tw"
result = re.search(r'[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+', text)
print(result.group())
result = re.search(r'([a-zA-Z0-9_.+-]+)@([a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+)', text)
print(result.group(0))
print(result.group(1))
print(result.group(2))
print(result.groups())
```

### 2.6 RE 使用時機
* 避免過於複雜的正規表示式。
* 如果你只需要尋找一個匹配項的話，可以使用 re.search() 方法。
* 如果你需要尋找多個匹配項，可以使用 re.findall() 方法。
* 如果你只需要尋找給定的文字串是否以某一個模式開頭的話，可以使用 re.match() 方法。
* 如果需要替換字串中符合模式的部分，可以使用 re.sub() 方法。
* 如果你需要對一個或多個文字串進行多次匹配的話，可以先使用 re.compile() 將正規表達式模式編譯成正規表達式對象，然後再使用各種方法進行匹配操作。

## 3. 以正規表示式解析 HTML
### 3.1 驗證電子郵件
```python=
email = "test@example.com"
pattern = r'^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$'
print(re.match(pattern, email))
```
### 3.2 解析 HTML 標籤
```python=
import re

html_text = '''
<div class="member_name">
    <a href="/profile/john">John Doe</a>
</div>
<div class="member_info_title">職稱</div>
<div class="member_info_content">工程師</div>
<div class="member_contact">
    <a href="mailto://john@example.com">聯絡</a>
</div>
'''

# 提取姓名
name_pattern = re.compile(r'<div class="member_name"><a href="[^"]+">([^<]+)</a>')
names = name_pattern.findall(html_text)

# 提取職稱
title_pattern = re.compile(
    r'<div class="member_info_title">.*?職稱</div>\s*<div class="member_info_content">([^<]+)</div>'
)
titles = title_pattern.findall(html_text)

# 提取 Email
email_pattern = re.compile(r'mailto://([^"]+)')
emails = email_pattern.findall(html_text)

print("Names:", names)
print("Titles:", titles)
print("Emails:", emails)
```
雖然正規表示式可以解析 HTML，但正規表示式容易因微小的結構變化而失效，且可讀性較差，所以不建議用於複雜的 HTML 解析。對於網頁結構，推薦使用專業的 HTML 解析器(套件)如下：
1. BeautifulSoup (下一小節介紹)
2. lxml
