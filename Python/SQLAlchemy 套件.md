# SQLAlchemy 套件
![image](https://hackmd.io/_uploads/Sk1VU6SWyg.png)

SQLAlchemy 是一款功能強大的 Python ORM（物件關聯映射）工具，用來讓開發者以 Python 類別和物件管理關聯式資料庫。它支援多種關聯式資料庫，並簡化了操作與資料庫溝通的過程。

![image](https://hackmd.io/_uploads/BJ5M8aSbkx.png)

## 安裝 SQLAlchemy 套件
```
pip install SQLAlchemy
```

## 安裝實際使用的資料庫驅動套件
### MySQL / MariaDB
```bash!
pip install pymysql         # 純 Python 實作
# 範例連接字串：mysql+pymysql://user:password@host:port/database

# 進階: MySQL 官方維護，具備進階功能如連接池、加密連線，適合企業級應用或需要與官方 API 深度整合的專案
pip install mysql-connector
# 範例連接字串：mysql+mysqlconnector://user:password@host:port/database

# 使用 ODBC 連接 MySQL，需安裝 ODBC 驅動程式
pip install pyodbc
# 範例連接字串：mysql+pyodbc://user:password@host:port/database?driver=MySQL+ODBC+Driver+version

# 若要在 Windows 上使用 ODBC，還需安裝 MySQL ODBC Driver 驅動
# 可以從 MySQL 官方網站下載： https://dev.mysql.com/downloads/connector/odbc/

pip install aiomysql        # MySQL 非同步
# 範例連接字串：mysql+aiomysql://user:password@host:port/database
```
### Microsoft SQL Server (MSSQL)
```bash!
# pyodbc 能夠使用各種資料庫，但還需在平台上安裝對應的 ODBC 驅動程式
# 若要在 Windows 上使用 ODBC，還需安裝 ODBC Driver for SQL Server 驅動
# 可以從 Microsoft 官方網站下載： https://learn.microsoft.com/en-us/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver16

pip install pyodbc
# 範例連接字串：mssql+pyodbc://user:password@DSN

# 專為 MSSQL 設計的 pymssql 驅動程式，連接和操作 SQL Server 簡單方便，適合開發中小型專案，但套件更新較慢
pip install pymssql
# 範例連接字串：mssql+pymssql://user:password@host:port/database
```
### PostgreSQL
```bash!
pip install psycopg2
# 範例連接字串：postgresql+psycopg2://user:password@host:port/database

pip install asyncpg     # 非同步驅動程式
# 範例連接字串：postgresql+asyncpg://user:password@host:port/database
```

## 資料庫引擎建立函數 create_engine
語法：`create_engine(url, **kwargs)`
```python=
from sqlalchemy import create_engine

engine = create_engine(
    # 'sqlite:///path/to/database.db',         # sqlite 檔案型資料庫
    # 'sqlite:///:memory:',                    # sqlite 記憶體型資料庫
    # 'mssql+pyodbc://user:password@host:port/database?driver=ODBC+Driver+17+for+SQL+Server', # MSSQL 連接字串
    'mysql+pymysql://user:password@host:port/database',  # MySQL 連接字串(使用 PyMySQL)
    # 'postgresql://user:password@host:port/database', # PostgreSQL 連接字串
    # 'oracle://user:password@host:port/service_name',   # Oracle 連接字串
    echo=True,         # 輸出 SQL 語句，幫助除錯，預設為 False
    pool_size=10,      # 設定連接池(數量)大小，預設為 5
    max_overflow=20,   # 允許額外的連接數量，預設為 10
    pool_timeout=60,   # 設定連接超時時間，預設為 30 秒
    pool_recycle=1800, # 設定連接回收的秒數，預設為 -1，表示不回收
    connect_args={'charset': 'utf8'}  # 設定特定資料庫連接的參數,此例為 MySQL 設定編碼
)
```
### URL 參數
- dialect: 資料庫的類型，例如 sqlite、postgresql、mysql 等。
- driver: （可選）用來指定具體的驅動程式，例如 ODBC 驅動，通常可以省略，SQLAlchemy 會自動選擇。
- user: 用於連接資料庫的使用者名稱。
- password: 使用者的密碼。
- host: 資料庫伺服器的主機名或 IP 位址。
- port: 資料庫伺服器的埠號（預設埠號可省略）。
- database: 要連接的資料庫名稱。
- service_name: 對於 Oracle 資料庫，這是需要連接的服務名稱。

--- 

## 實際建立資料庫模型
在此以 SQLite 檔案型資料庫為例，建立一個 Python 檔案 app.py，在其中定義會員和文章的資料庫模型。
```python=
from sqlalchemy import Column, Integer, String, ForeignKey
from sqlalchemy.orm import relationship, sessionmaker, declarative_base
from sqlalchemy import create_engine

# 建立基礎類別
Base = declarative_base()

# 定義 Member 模型
class Member(Base):
    __tablename__ = 'members'
    
    id = Column(Integer, primary_key=True)
    name = Column(String(50), nullable=False)
    email = Column(String(100), unique=True, nullable=False)
    password = Column(String(50), nullable=False)

    # 文章關聯
    posts = relationship("Post", back_populates="member")

    # 建構子方法，方便建立新的物件
    def __init__(self, name, email, password):
        self.name = name
        self.email = email
        self.password = password

    def __repr__(self):
        return f"<Member(name='{self.name}', email='{self.email}')>"

# 定義 Post 模型
class Post(Base):
    __tablename__ = 'posts'
    
    id = Column(Integer, primary_key=True)
    title = Column(String(100), nullable=False)
    content = Column(String, nullable=False)
    member_id = Column(Integer, ForeignKey('members.id'))

    # 會員關聯
    member = relationship("Member", back_populates="posts")

    # 建構子方法
    def __init__(self, title, content, member_id):
        self.title = title
        self.content = content
        self.member_id = member_id

    def __repr__(self):
        return f"<Post(title='{self.title}', member_id='{self.member_id}')>"

# 建立 SQLite 資料庫
engine = create_engine('sqlite:///members.db')
Base.metadata.create_all(engine)  # 自動建立資料表
```
上述程式碼定義了一個 Member 類別，對應資料庫的 members 資料表，其中包含三個欄位：id（會員 ID）、name（姓名）和 email（電子郵件）。

## 設置 Session
SQLAlchemy 使用 Session 來管理與資料庫的互動。Session 負責將資料庫的操作集中處理，並協助管理交易（transaction）。以下是在 app.py 中設置 Session 的步驟：
```python=
# 設置 Session
Session = sessionmaker(bind=engine)
session = Session()

# 新增會員
new_member = Member(name='Bob', email='bob@example.com', password='password456')

session.add(new_member)
session.commit()
```
> 與傳統的 cursor 概念有些相似，但 session 適用於 SQLAlchemy 以物件方式操控資料庫，提供了一個更高層次的抽象。

## 基本 CRUD 操作
接下來，我們可以進行基本的 CRUD 操作（建立、讀取、更新、刪除）。

### 1. 新增會員及文章
透過建構子方法，將新增會員和文章的操作變得簡單且清晰。
```python=
# 新增會員
try:
    new_member = Member(name='Alice', email='alice@example.com', password='password123')
    session.add(new_member)
    session.commit()
    print("新增會員成功！")

    # 新增文章
    new_post = Post(title='我的第一篇文章', content='這是我的第一篇文章內容。', member_id=new_member.id)
    session.add(new_post)
    session.commit()
    print("新增文章成功！")
except Exception as e:
    session.rollback()  # 若新增失敗則回滾交易
    print(f"新增失敗：{e}")
```

### 2. 查詢會員及其文章
可以查詢所有會員或特定會員。特定會員的查詢可以使用 filter_by() 方法來過濾。
```python=
# 查詢所有會員及其文章
try:
    members = session.query(Member).all()
    for member in members:
        print(member)
        for post in member.posts:  # 顯示該會員的所有文章
            print(f" - 文章標題: {post.title}，內容: {post.content}")
except Exception as e:
    print(f"查詢失敗：{e}")

# 查詢特定會員及其文章
try:
    member_name = 'Alice'  # 指定要查詢的會員名稱
    member = session.query(Member).filter_by(name=member_name).first()
    if member:
        print(member)
        for post in member.posts:  # 顯示該會員的所有文章
            print(f" - 文章標題: {post.title}，內容: {post.content}")
    else:
        print("會員不存在")
except Exception as e:
    print(f"查詢失敗：{e}")
```

### 3. 更新會員及文章
更新會員資料或文章內容。
```python=
# 更新會員資料
try:
    member_to_update = session.query(Member).filter_by(name='Alice').first()
    if member_to_update:
        member_to_update.email = 'alice_new@example.com'
        session.commit()
        print("會員資料更新成功！")
    else:
        print("會員不存在")
except Exception as e:
    session.rollback()
    print(f"更新失敗：{e}")

# 更新文章內容
try:
    post_to_update = session.query(Post).filter_by(title='我的第一篇文章').first()
    if post_to_update:
        post_to_update.content = '這是更新後的文章內容。'
        session.commit()
        print("文章內容更新成功！")
    else:
        print("文章不存在")
except Exception as e:
    session.rollback()
    print(f"更新失敗：{e}")

```

### 4. 刪除會員及文章
刪除會員或其撰寫的文章
```python=
# 刪除會員
# 刪除會員及其文章
try:
    member_to_delete = session.query(Member).filter_by(name='Alice').first()
    if member_to_delete:
        for post in member_to_delete.posts:  # 刪除該會員的所有文章
            session.delete(post)
        session.delete(member_to_delete)
        session.commit()
        print("會員及其文章刪除成功！")
    else:
        print("會員不存在")
except Exception as e:
    session.rollback()
    print(f"刪除失敗：{e}")

```

## 參考網頁
- [SQLAlchemy 2.0(01)概觀](https://hackmd.io/@shaoeChen/B1CJAsHJ3)