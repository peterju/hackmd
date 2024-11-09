# Tkinter 套件
![image](https://hackmd.io/_uploads/ry47BWhBT.png)

[Tkinter](https://docs.python.org/3/library/tkinter.html#the-tkinter-__widget__-class) 是 Python 的標準庫（Standard Library）中的一個模組（module）。作為呼叫 Tcl/Tk GUI 工具包的介面，使得開發者能夠建立跨平台的基本圖形用戶界面（GUI）。

## Tkinter 的視窗
常用方法有：
* title(string)：設定視窗標題。
* geometry(string)：設定視窗的寬度和高度。
* resizable(width, height)：設定視窗是否可以調整大小。
* mainloop()：保持視窗執行，等待處理事件。
* after(delay, callback, *args)：可在指定的毫秒數後執行傳入的函式(callback function)。
* withdraw()：隱藏視窗。
* deiconify()：顯示已隱藏的視窗。
* quit()：只停止事件循環。
* destroy()：徹底關閉和銷毀整個視窗及其所有子元件。
* iconbitmap()：設定視窗圖示。
* config() / configure()：設定視窗屬性。常用的屬性如下：
    * bg / background：背景色。
    * fg / foreground：前景色。
    * width 和 height：設定視窗的寬度和高度。
    * cursor：設定滑鼠游標的形狀。
    * takefocus：設定是否可以使用 Tab 鍵切換到該視窗。
* attributes(option, value)：設定或查詢視窗的屬性。常用的屬性如下：
    * -alpha：設置視窗透明度 (0.0 - 1.0)。
    * -topmost：設置視窗置頂 (True / False)。
    * -width / -height：設置視窗寬度與高度(px)
```python=
import tkinter as tk

form = tk.Tk()                          # 以 tk.TK() 類別建立視窗物件

# 視窗相關設定
form.title('視窗')                       # 設定視窗標題
form.geometry('400x300')                # 設定視窗的寬度與高度
# form.geometry(width=400, height=300)
form.resizable(0, 0)                    # 視窗寬度與長度可否改變
# form.resizable(False, False)          # 0=False, 1=True

form.config(bg='pink', cursor="arrow")  # 背景顏色與游標形狀
form.attributes("-alpha", 0.85)         # 透明度
form.attributes("-topmost", True)       # 置頂
# form.iconbitmap('icon.ico')           # 設定視窗圖示
# 在此後添加 GUI 元件和事件處理

form.after(5000, form.destroy)          # 5 秒後關閉視窗
form.mainloop()                         # 保持實例視窗執行，等待處理事件
# tk.mainloop()                         # 保持所有實例視窗執行，等待處理事件
```
:::success
cursor 可用的樣式請參考：https://anzeljg.github.io/rin2/book2/2405/docs/tkinter/cursors.html
:::
## Tkinter 的佈局
Tkinter 提供了 3 種佈局方法，用於安排 GUI 元素的位置和大小，並可同時使用這些佈局。
- pack 方法適用於簡單的垂直或水平佈局，不需要精確控制元素的位置和尺寸。
- grid 方法適用於表格佈局，需要精確控制元素在列和欄中的位置。
- place 方法適用於想精確控制元素位置和大小，需要相對自由的佈局。

### 1. Pack 佈局
| side | padx、pady |
| -------- | -------- |
|![image](https://hackmd.io/_uploads/ryHuz6hSa.png)|![image](https://hackmd.io/_uploads/SkLcMp3Hp.png =310x)|

* pack() 方法是最基礎的佈局，可將畫面元件依序由上至下置中排列，放置到容器中。
* 如果要將部分元件安排由左至右排列，必須先加上 frame 容器，再將元件設定靠左，依序放入 frame 容器中。
* 可用的參數
    - side：元件放置的方向，可設定上下左右 4 個值
    `side = "top", "bottom", "left", "right"`
    - anchor：元件在其可用空間中的錨點位置，可設定北,南,西,東,西北,東北,西南,東南 8 個值
    `anchor = "n", "s", "w", "e", "nw", "ne", "sw", "se"`
    - fill：元件在可用空間的擴充方向，可設定不擴充, 水平擴充, 垂直擴充, 水平和垂直皆擴充，預設為不擴充
    `fill = tk.NONE / tk.X / tk.Y / tk.BOTH`
    `fill = "none" / "x" / "y", "both"`
    - expand：可否擴張元件的可用空間，預設為 False
    `expand = True / False`
    - padx、pady：指定元件周圍的水平和垂直內邊距
    `padx=5, pady=10`
    - ipadx、ipady：指定元件內容的水平和垂直內邊距
    `ipadx=5, ipady=10`
    
基本範例：
```python=
import tkinter as tk


def on_button_click():
    label.config(text="你好")


form = tk.Tk()            # 呼叫 tk.TK() 建立視窗
form.title("Tkinter Pack 佈局範例")
form.geometry("400x300")  # 設定視窗寬高

# 添加 label 元件
label = tk.Label(form, text="Hello, Pack!", bg="green", fg="#263238", font=("Arial", 24))
label.pack(side="top", fill="both", expand=True, padx=10, pady=5)

# 添加 button 元件
button = tk.Button(form, text="點我1", command=on_button_click)
button.pack(side="bottom", anchor="w", padx=10, pady=10)

form.mainloop()  # 等待處理事件保持視窗執行
```

更多的範例：
```python=
import tkinter as tk


def on_button1_click():
    label.config(text="你好")


def on_button2_click(msg):
    label.config(text=msg)


form = tk.Tk()            # 呼叫 tk.TK() 建立視窗
form.title("Tkinter Pack 佈局範例")

form.geometry("400x300")  # 設定視窗寬高

# 添加 label 和 button 元件
label = tk.Label(form, text="Hello, Pack!", bg="green", fg="#263238", font=("Arial", 24))
label.pack(side="top", fill="both", padx=10, pady=5)

button1 = tk.Button(form, text="點我1", command=on_button1_click)
button1.pack(side="bottom", anchor="w", padx=10, pady=10)

button2 = tk.Button(form, text="點我2", command=lambda: on_button2_click("Hi，Pack!"))
button2.pack(side="bottom", anchor="e", padx=10, pady=10)

button3 = tk.Button(form, text="離開", command=form.quit)
button3.pack(side="bottom", padx=10, pady=10)

form.mainloop()           # 等待處理事件保持視窗執行
```

### 2. Grid 佈局
![image](https://hackmd.io/_uploads/S1z0Z6hB6.png)

:::danger
**row 與 column 的翻譯**

台灣：橫列(row)豎欄(column)
大陸：橫行(row)豎列(column)
:::
* grid() 方法是網格式佈局，可將畫面元件指定 row 及 column 放置到容器中。
* 可用的參數
    - row、column：指定元件在 grid 中的【列】和【欄】的位置。例如：`row=0, column=2` 表示元素放置在第 0 列、第 2 欄。
    - rowspan、columnspan：這兩個參數分別指定元件在 grid 中跨越的列數和欄數。例如：`rowspan=3, columnspan=2` 表示元素跨越了 3 列和 2 欄。
    - sticky：指定元件的對齊方式，可設定方向字母，包含了
    `sticky = "n", "s", "e", "w", "nw", "se", ew", "ns", nsew"`
    - padx、pady：指定【元件周圍】的水平和垂直內邊距
    `padx=5, pady=10`
    - ipadx、ipady：指定【元件內容】的水平和垂直內邊距
    `ipadx=5, ipady=10`
```python=
import tkinter as tk

def on_button_click():
    label.config(text="你好")

form = tk.Tk()  # 呼叫 tk.TK() 建立視窗
form.title("Tkinter Grid 佈局範例")

form.geometry("400x300")  # 設定視窗寬高

# 添加 label 和 button 元件
label = tk.Label(form, text="Hello, Grid!", bg="green", fg="#263238", font=("Arial", 24))
label.grid(row=0, column=0, sticky="nsew", padx=10, pady=5)

button = tk.Button(form, text="點我", command=on_button_click)
button.grid(row=1, column=0, padx=10, pady=10, sticky="w")

form.rowconfigure(0, weight=1)    # 設定第 0 列的權重為 1, 會取得所有剩餘空間
form.columnconfigure(0, weight=1) # 設定第 0 欄的權重為 1, 會取得所有剩餘空間

form.mainloop()  # 等待處理事件保持視窗執行
```

因為 grid 佈局是自動排列，以欄 (column)為例，若某欄沒有元件，則該欄的寬度將自動收縮為零，即使你指定了元件在後面的欄也一樣。
```python=
form.geometry('300x200')
frame1 = tk.Frame(form, bg='red', width=50, height=50)
frame2 = tk.Frame(form, bg='green', width=50, height=50)

frame1.grid(row=0, column=0)
frame2.grid(row=0, column=4) # 設定在第5欄，前面 3 個欄沒有任何元件存在
```

![image](https://hackmd.io/_uploads/ryPFt0ULT.png)

:::danger
:star:  若 frame 內未放置任何元件，又沒有設定其 width、height 或 sticky，則其大小將視為 0，就算設定了背景色，會因其不佔空間而看不到。
:::

#### 列與欄的權重用途

如上一節所說，如果某個欄沒有元件，則看不見此欄，因為其寬度為 0，若希望某欄即使沒有元件，仍保留該欄位寬度，則可使用 columnconfigure 來設定【欄】的權重。【列】也是相同的思路，用的是 rowconfigure 方法。

請以下列2種方法，達成如下圖般左右分開的佈局。
* rowconfigure / grid_rowconfigure 方法： 用於設定特定列的屬性，如權重。
* columnconfigure / grid_columnconfigure 方法： 用於設定特定欄的屬性，如權重。

![image](https://hackmd.io/_uploads/S13L9AI8a.png)

```python=
# 做法 1 (5 欄)
frame1.grid(row=0, column=0)
frame2.grid(row=0, column=4)

# 設定 column 1, 2, 3 的權重為 1，亦即平均分配中間的剩餘空間，各自擁有 33.3%
form.columnconfigure((1, 2, 3), weight=1)
```
```python=
# 做法 2 (3 欄)
frame1.grid(row=0, column=0)
frame2.grid(row=0, column=2)

# 設定 column 1 的權重為 1，中間得到100%的剩餘空間
form.columnconfigure(1, weight=1)
```
```python=
# 做法 3 (2 欄)
# 分別在不同的 column 中，使用 sticky 設定其佔位方向
frame1.grid(row=0, column=0, sticky='w')         # 西
frame2.grid(row=0, column=1, sticky='e')         # 東

# 設定 column 0, 1 的權重，各自擁有水平空間的 50%
form.columnconfigure((0, 1), weight=1)
```
```python=
# 做法 4 (1 欄)
# 使用 sticky 屬性，但 column 都在 0，預設會重疊
frame1.grid(row=0, column=0, sticky='w')         # 西
frame2.grid(row=0, column=0, sticky='e')         # 東

# 設定 column 0 的權重為 1，即擁有水平所有的空間
form.columnconfigure(0, weight=1)
```

**思考題：**
假設增加至3個 frame 色塊，想將之排列為如下圖的倒三角形，該如何撰寫？

![image](https://hackmd.io/_uploads/r1xFojIIT.png)

以下3種做法皆可達成，但如何解釋呢？
```python=
# 做法 1
import tkinter as tk

form = tk.Tk()

form.geometry('300x200')
frame1 = tk.Frame(form, bg='red', width=50, height=50)
frame2 = tk.Frame(form, bg='green', width=50, height=50)
frame3 = tk.Frame(form, bg='blue', width=50, height=50)

frame1.grid(row=0, column=0, sticky='nw')
frame2.grid(row=1, column=1, sticky='s')
frame3.grid(row=0, column=2, sticky='ne')

form.rowconfigure(0, weight=1)
form.columnconfigure(1, weight=1)

form.mainloop()
```
```python=
# 做法 2
import tkinter as tk

form = tk.Tk()

form.geometry('300x200')
frame1 = tk.Frame(form, bg='red', width=50, height=50)
frame2 = tk.Frame(form, bg='green', width=50, height=50)
frame3 = tk.Frame(form, bg='blue', width=50, height=50)

frame1.grid(row=0, column=0, sticky='nw')
frame2.grid(row=1, column=0, sticky='s')
frame3.grid(row=0, column=0, sticky='ne')

form.rowconfigure(0, weight=1)
form.columnconfigure(0, weight=1)

form.mainloop()
```
```python=
# 做法 3
import tkinter as tk

form = tk.Tk()

form.geometry('300x200')
frame1 = tk.Frame(form, bg='red', width=50, height=50)
frame2 = tk.Frame(form, bg='green', width=50, height=50)
frame3 = tk.Frame(form, bg='blue', width=50, height=50)

frame1.grid(row=0, column=0)
frame2.grid(row=1, column=1, sticky='s')
frame3.grid(row=0, column=2)

form.rowconfigure(1, weight=1)
form.columnconfigure(1, weight=1)

form.mainloop()
```
:::success
**練習題：請以 grid 佈局做出如下圖的視窗**
- 視窗大小：400x300
- 5 個 frame 顏色：red, yellow, blue, lightgreen lightblue
- 3 個 label 文字：left, center, right

![image](https://hackmd.io/_uploads/r1c0kk_Ip.png)

:::
### 3. Place 佈局
![image](https://hackmd.io/_uploads/B1WE04d8T.png)

* place() 佈局方法若使用 x、y 參數時是絕對坐標佈局，使用 relx、rely 參數時是相對坐標佈局。
* 可用的參數
    - x、y：指定元件相對於其父容器的左上角的絕對坐標。單位是像素(pixels)。
    `x=10, y=10`
    - relx、rely：指定元件相對於其父容器的相對坐標。值在 0.0 到 1.0 之間。
    `relx=0.2, rely=0.2`
    - anchor：元件在其可用空間中的錨點位置，可設定北,南,西,東,西北,東北,西南,東南 8 個值
    `anchor = "n", "s", "w", "e", "nw", "ne", "sw", "se"`
    - width、height：元件的絕對寬度和高度
    `width=100, height=30`
    - relwidth、relheight：元件相對於其父容器的寬度和高度。值在 0.0 到 1.0 之間。
    `relwidth=0.5, relheight=0.5`
```python=
import tkinter as tk

def on_button_click():
    label.config(text="你好")

form = tk.Tk()
form.title("Tkinter Place 佈局範例")

form.geometry("400x300")

# 添加 label 和 button 元件
label = tk.Label(form, text="Hello, Place!", bg="green", fg="#263238", font=("Arial", 24))
label.place(relx=0.03, rely=0.03, relwidth=0.95, relheight=0.80)

button = tk.Button(form, text="點我", command=on_button_click)
button.place(relx=0.03, rely=0.88)

form.mainloop()  # 等待處理事件保持視窗執行
```


## tkinter 的常用事件
![image](https://hackmd.io/_uploads/Skplx-0rT.png =500x)

Tkinter 提供了兩種方法來綁定事件函式：【command 參數】和 【bind 方法】，command 參數主要用於簡單的固定事件，而 bind 方法則適合處理更複雜、多樣的事件。
```python=
def on_button_click():
    """事件處理函式"""
    label.config(text="你好")

# 使用 command 參數指定事件處理函式
button = tk.Button(text="按我", command=on_button_click)
# 使用 bind 方法綁定事件處理函式
button.bind("<Button-1>", on_button_click)
```
以下我們來看看 Tkinter 提供哪些事件？
1. 滑鼠事件
    ```python=
    widget.bind("<Button-1>", on_left_click)         # 滑鼠左鍵點擊，事件類型為 4
    widget.bind("<Button-2>", on_middle_click)       # 滑鼠中鍵點擊，事件類型為 4
    widget.bind("<Button-3>", on_right_click)        # 滑鼠右鍵點擊，事件類型為 4
    widget.bind("<Double-Button-1>", on_double_click)  # 滑鼠左鍵雙擊事件，事件類型為 4
    widget.bind("<ButtonPress>", on_mouse_press)     # 滑鼠按下，事件類型為 4
    widget.bind("<ButtonRelease>", on_mouse_release) # 滑鼠鬆開，事件類型為 5
    widget.bind("<B1-Motion>", on_left_drag)         # 滑鼠左鍵拖曳，事件類型為 6
    widget.bind("<Motion>", on_mouse_motion)         # 滑鼠移動，事件類型為 6
    widget.bind("<Enter>", on_mouse_enter)           # 滑鼠進入元件區域，事件類型為 7
    widget.bind("<Leave>", on_mouse_leave)           # 滑鼠離開元件區域，事件類型為 8
    widget.bind("<MouseWheel>", on_mouse_wheel)      # 滾動滑鼠滾輪，事件類型為 17
    ```
1. 鍵盤事件
    ```python=
    widget.bind("<KeyPress>", on_key_press)     # 按下任意按鍵，事件類型為 2
    widget.bind("<KeyRelease>", on_key_release) # 釋放任意按鍵，事件類型為 3
    widget.bind("<Return>", on_enter_key)       # 按下 Enter 鍵，事件類型為 36
    widget.bind("<Escape>", on_escape_key)      # 按下 Esc 鍵，事件類型為 9
    widget.bind("<F1>", on_f1_key)              # 按下 F1 鍵，事件類型為 67
    ```
1. 焦點事件
    ```python=
    widget.bind("<FocusIn>", on_focus_in)       # 獲得焦點，事件類型為 9
    widget.bind("<FocusOut>", on_focus_out)     # 失去焦點，事件類型為 10
    ```
1. 視窗事件
    ```python=
    widget.bind("<Configure>", on_win_config)   # 大小調整時觸發，事件類型為 22
    widget.bind("<Map>", on_win_map)            # 在容器視窗上可見，事件類型為 19
    widget.bind("<Unmap>", on_win_unmap)        # 在容器視窗上不可見，事件類型為 18
    ```
1. 其他事件
    ```python=
    widget.bind("<Visibility>", on_visibility_change) # 元件可見性變化，事件類型為 23
    ```

### tkinter 事件函式的 event 參數
當事件處理函數被呼叫時，它可以接收一個事件物件（event）作為參數。而 event 物件封裝了事件相關信息。包含了事件類型、發生的元件、滑鼠位置...等等。
- event.type：返回事件的類型，例如 `<ButtonPress>`。
- event.widget：返回觸發事件的元件。
- event.x 和 event.y：返回事件在元件中的發生位置的 x 和 y 坐標。
- event.keysym：keysym 返回按鍵的名稱（例如，"a"，"Enter"）。
- event.char：char 返回與按鍵相對應的字元。
- event.state：返回與事件相關的狀態信息，例如 Shift、Control 和 Alt 鍵是否被按下。
- event.num：返回與滑鼠按鈕事件相關的滑鼠按鈕號碼。

>原始碼範例如下：
```python=
import tkinter as tk

def on_mouse_click(event):
    print("-" * 30)
    print(f"Event Type: {event.type}")
    print(f"Widget: {event.widget}")
    print(f"X Coordinate: {event.x}")
    print(f"Y Coordinate: {event.y}")
    print(f"Mouse X Coordinate: {event.x_root}")
    print(f"Mouse Y Coordinate: {event.y_root}")
    print(f"Event Time: {event.time}")

form = tk.Tk()

frame = tk.Frame(form, width=300, height=200, bg='lightgray')
frame.bind("<Button-1>", on_mouse_click)  # 綁定滑鼠點擊事件到 Frame 上
frame.pack()

exit_button = tk.Button(form, text="離開", command=form.quit)
exit_button.pack()

form.mainloop()
```
## tkinter 定義的常數
Tkinter 模組中有一些常見的常數(Constants)，通常用於表示特定的屬性、事件或狀態。這些常數主要是提供易於記憶和使用的標示符，方便在程式過程中參照特定的屬性或事件。以下是一些常見的 Tkinter 常數：

* 對齊常數（Alignment Constants）：
    * tk.LEFT, tk.RIGHT, tk.TOP, tk.BOTTOM：用於指定部件的對齊方式，例如在 pack() 佈局管理器中。
    `label = tk.Label(root, text="Hello, Tkinter", anchor=tk.CENTER)`
* 方向常數（Orientation Constants）：
    * tk.HORIZONTAL, tk.VERTICAL：用於指定水平或垂直方向，通常在建立滾動條或其他控制元件時使用。
    `scrollbar = tk.Scrollbar(root, orient=tk.HORIZONTAL)`
* 對話框回應常數（Dialog Response Constants）：
    * tk.YES, tk.NO, tk.CANCEL, tk.OK, tk.YES_NO, tk.YES_NO_CANCEL：用於表示對話框回應的選項。
    `tk.messagebox.showinfo("Information", "This is an information message.")`
    `tk.messagebox.showwarning("Warning", "This is a warning message.")`
* 視窗顯示模式常數（Window Display Mode Constants）：
    * tk.NORMAL：用於表示視窗處於正常狀態。
    * tk.ICONIC：用於表示視窗處於最小化或圖示化狀態。
    * tk.WITHDRAWN：用於表示視窗被隱藏（withdrawn）。
    `form.iconify()  # 最小化主視窗`
    `form.withdraw() # 隱藏主視窗`
* 狀態常數（State Constants）：
    * tk.NORMAL：用於表示元件處於正常狀態。
    * tk.DISABLED：用於表示元件處於禁用狀態。
    `button = tk.Button(root, text="按我", state=tk.DISABLED)`
* 插入位置索引常數（Insertion Position Index Constants）：用於 Entry 或 Text 元件中指定特定的位置。
    * tk.INSERT：表示當前插入符號（輸入游標）的位置。
    * tk.END：表示文字的結尾位置。
    `entry.insert(tk.END, "Hello, Tkinter!")`

## tkinter 的常用 GUI 元件(widgets)
在 Tkinter 中，widgets 是可實例化的類別，每個類別代表一種 GUI 元素，例如 Label、Button、Entry 等。開發者可以使用這些 widgets 來構建 GUI，並透過配置它們的屬性和方法來定義應用程式的外觀和行為。

所有的 widgets 都是 Widget 類別的子類別，這個基礎類別提供了一些通用的屬性和方法，供所有的 widgets 繼承和使用。

![Tkinter_Widgets](https://hackmd.io/_uploads/SkHzZiSL6.png)

以下介紹的元件，除了 Menu 與 Canva 之外，皆有 ttk 的主題更新元件可用，建議改用之。

### Label(標籤)
顯示文字或圖片
```python=
import tkinter as tk

form = tk.Tk()
label1 = tk.Label(text="固定賦值", fg="red")
label1.pack(padx=10, pady=5)

label2 = tk.Label(text="固定賦值", fg="green")
label2.pack(padx=10, pady=5)
label2.config(text="動態賦值1", bg="lightgray")

label_var = tk.StringVar()
label3 = tk.Label(textvariable=label_var, fg="blue")
label3.pack(padx=10, pady=5)
label_var.set("動態賦值2")

form.mainloop()
```
![image](https://hackmd.io/_uploads/HJ-iNWkUa.png)
Label 元件還有一些其他常用的方法(pack 與 bind 方法就不提了)，例如：

* config()：可變更 Label 的各種屬性，例如文字顏色、背景顏色、字體等。
    ```python=
    # 變更字型、對齊方式、自動換行寬度、顯示圖像
    label.config(
        font=("Arial", 12, "bold"),  # 變更字型，設定為 Arial、12號字、粗體
        justify="center",  # 變更對齊方式，設定為置中對齊
        wraplength=150     # 變更自動換行寬度，設定為150像素
    )

    # 加入圖片，僅支援 gif 與部分的 png 與 bmp 格式
    photo = tk.PhotoImage(file="image.png") # 圖片路徑
    label.config(image=photo)               # 顯示圖像
    ```
* cget()：取得 Label 特定屬性目前的值
    ```python=
    text_color = label.cget("fg")  # 獲取文字顏色
    print("文字顏色:", text_color)
    ```
* destroy()：自容器中移除 Label 元件
`label.destroy()`

:::warning
**PhotoImage 的圖片檔案格式處理**
若進行圖片處理遇到 `couldn't recognize data in image file...` 的錯誤，代表此圖片的檔案格式不被認可，此時可使用 PIL(Python Imaging Library) 模組進行格式轉換，範例如下：
```python=
# 需先安裝 pillow 套件
# pip install Pillow

from PIL import Image, ImageTk
import tkinter as tk

form = tk.Tk()

# 打開圖像，轉換格式並顯示
image = Image.open("image.jpg")   # 替換為你的圖像路徑
image = image.convert("RGB")      # 轉換為RGB格式
photo = ImageTk.PhotoImage(image) # 轉換為PhotoImage格式

label = tk.Label(image=photo)
label.pack()

form.mainloop()
```

:::info
### tkinter 元件的賦值
在 tkinter 中，將元件的賦值方式大致分為兩種：
1. 固定給值： 使用直接的屬性（例如 text 屬性）將元件的值固定指定為一個固定的值。這種方式在建立元件時就確定了元件的初始值，之後通常不會動態變化。
    ```python=
    label = tk.Label(root, text="固定值")
    ```
2. 動態給值： 使用特定的 tkinter 變數（例如：StringVar, IntVar, BooleanVar, DoubleVar）作為元件 【textvariable 屬性】，使元件的值可以動態的與這個變數關聯。當這個變數的值改變時，元件的值也對應改變，實現了動態的效果。
:::

### Labelframe（標籤框）
![image](https://hackmd.io/_uploads/HyBDwVlDp.png)

Labelframe 是 tkinter 中的一種容器元件，用於將元件分組並提供標題。

常用屬性：
* text 屬性： 設置或獲取標籤框的標題文字。
* labelanchor 屬性： 設置或獲取標籤框標題的錨點位置。
* padx, pady 屬性： 設置標籤框內容的水平和垂直填充。
* borderwidth, relief 屬性： 設置標籤框的邊框寬度和樣式。
* width, height 屬性： 設置標籤框的寬度和高度。

常用方法：
* config(**options) 方法： 用於動態設置標籤框的屬性。
* cget(option) 方法： 用於獲取標籤框的指定屬性值。
* grid, pack, place 方法： 用於標籤框的佈局管理，決定標籤框在父容器中的位置。
* bind 方法： 用於綁定事件處理程序，例如當標籤框被單擊時執行特定的函數。

> 原始碼範例
```python=
import tkinter as tk

def on_button_click():
    # 使用 global 關鍵字聲明 title_text 為全域變數
    global title_text
    # 用 config 方法動態設置 Labelframe 的標題文字
    labelframe.config(text=title_text + " - 修改後")
    # 設置 Labelframe 的寬度和高度
    labelframe.config(width=300, height=200)
    form.columnconfigure(0, weight=1)

form = tk.Tk()
form.title("Labelframe 範例")
form.geometry("400x300")

# 建立 Labelframe
labelframe = tk.LabelFrame(form, text="個人資料", padx=10, pady=10, borderwidth=2, relief="groove")
labelframe.grid(row=0, column=0, padx=20, pady=20, sticky="nsew")

# 在 Labelframe 內添加元件
label_name = tk.Label(labelframe, text="姓名")
label_name.grid(row=0, column=0, padx=5, pady=5, sticky="e")

entry_name = tk.Entry(labelframe)
entry_name.grid(row=0, column=1, padx=5, pady=5, sticky="w")

label_type = tk.Label(labelframe, text="型別")
label_type.grid(row=1, column=0, padx=5, pady=5, sticky="e")

entry_type = tk.Entry(labelframe)
entry_type.grid(row=1, column=1, padx=5, pady=5, sticky="w")

label_phone = tk.Label(labelframe, text="手機")
label_phone.grid(row=2, column=0, padx=5, pady=5, sticky="e")

entry_phone = tk.Entry(labelframe)
entry_phone.grid(row=2, column=1, padx=5, pady=5, sticky="w")

# 在 Labelframe 外添加按鈕
button = tk.Button(form, text="點擊我", command=on_button_click)
button.grid(row=1, column=0, pady=10)

# 設置 Labelframe 的錨點位置
labelframe.config(labelanchor="nw")

# 獲取 Labelframe 的標題文字
title_text = labelframe.cget("text")

form.mainloop()
```

### Button（按鈕）
- 按鈕的文字可使用 text 指定，也可以後續透過 config() 方法設定。
- 按鈕被點擊時執行的函數可使用 command 指定，也可以後續透過 config() 方法設定。
```python=
import tkinter as tk

def change_text():
    button.config(text='已點擊' if button['text'] == '按鈕' else '按鈕')

form = tk.Tk()
button = tk.Button(text="按鈕")  # 固定賦值
# button = tk.Button(text="按鈕", command=change_text)  # 固定賦值
button.pack(padx=10, pady=10)

button.config(text='已點擊')     # 動態賦值
button.config(command=change_text)
form.mainloop()
```
![image](https://hackmd.io/_uploads/BJ3WH-1IT.png)

Button 元件還有一些其他常用的方法(pack 與 bind 方法就不提了)，例如：

* config()：可變更 Button 的各種屬性，例如字體(font)、文字(text)、背景色(bg)、前景色(fg)、按鈕的寬度和高度(width / height)、按鈕的狀態(state)、圖片(image)...等。
    ```python=
    def toggle_button():
      if button1['state'] == 'normal':
        button1.config(fg="black", bg="SystemButtonFace") # 設置按鈕的前景色和背景色
        button1.config(state=tk.DISABLED)      # 將按鈕設置為禁用狀態
      else:
        button1.config(fg="blue", bg="yellow") # 設置按鈕的前景色和背景色
        button1.config(state=tk.NORMAL)        # 否則恢復按鈕為啟用狀態

    button1 = tk.Button(text="目標按鈕")
    button1.pack(padx=10, pady=10)              # 設置按鈕的內邊距
    button1.config(relief=tk.RAISED)            # 設置按鈕的邊框樣式
    button1.config(font=("Arial", 12, "bold"))  # 設置按鈕的字體和字體大小
    button1.config(width=20, height=3)          # 設置按鈕的寬度和高度
    button1.config(fg="blue", bg="yellow")      # 設置按鈕的前景色和背景色

    button2 = tk.Button(text="切換按鈕", command=toggle_button)
    button2.pack(padx=10, pady=10)
    ```
* invoke()：模擬按下按鈕的操作。
    ```python=
    button = tk.Button(text="點我！", command=on_button_click)
    button.pack()

    button.invoke() # 模擬按下按鈕
    ```
* destroy()：自容器中移除 Button 元件
`button.destroy()`
* instate()：檢查按鈕是否處於 DISABLED/ NORMAL狀態
```python=
if button.instate(['!disabled']):    # 檢查按鈕是否處於非禁用狀態
    button.config(state=tk.DISABLED) # 將按鈕設置為禁用狀態
```

### Entry（輸入框）
輸入框可以使用 textvariable 來綁定變數，以便在程式中動態獲取或設置輸入的文字。
```python=
import tkinter as tk

form = tk.Tk()
entry1 = tk.Entry()
entry1.insert(0, "預設值")  # 直接設值
entry1.pack(padx=10, pady=10)
entry1.insert(tk.END, ", 補字")  # 直接設值

entry_var = tk.StringVar()
entry2 = tk.Entry(textvariable=entry_var)
entry2.pack(padx=10, pady=10)

entry_var.set("預設文字")  # 與 Tkinter 變數關聯

form.mainloop()
```
![image](https://hackmd.io/_uploads/rkPaHbJ86.png =500x)

Entry 元件還有一些其他常用的方法(pack 與 bind 方法就不提了)，例如：
* config()：可變更 Entry 的各種屬性。
    - show: 密碼輸入，輸入的字元會被轉換為指定字元
    - state: 設置為 "normal"（正常）或 "disabled"（禁用）
    - width: 以字元為單位的寬度
    - font: 字體
    - bg/background、fg/foreground: 背景色和前景色
    ```python=
    entry = tk.Entry(state=tk.DISABLED)
    entry.config(show="*", width=50, state=tk.NORMAL, bg="lightgray", font=("Arial", 12))
    entry.pack(padx=10, pady=10)
    ```
* get()：獲取 Entry 元件中的文字。
    `input_text = entry.get()`
* delete(startindex, endindex)：刪除 Entry 元件中從 startindex 到 endindex 之間的文字。
    `entry.delete(0, tk.END)`
* focus()：設為焦點
* icursor(index)：將輸入游標（插入符號）移動到指定的 index 位置。
* index(index)：返回文字的指定索引位置。
* select_range(start, end)：選取 Entry 元件中從 start 到 end 之間的文字。
* select_clear()：取消 Entry 的選取。
    ```python=
    entry.insert(0, "ABCDEFGH")
    entry.focus()                # 確保 Entry 元件獲得焦點
    entry.icursor(tk.END)        # 移動游標到文字的最後
    pos = entry.index(tk.INSERT) # 獲取目前游標的位置
    print("目前游標的位置:", pos)
    entry.select_range(2, 5)     # 選取第 2-5 位置的文字
    entry.select_clear()         # 取消 Entry 的選取
    ```

### Radiobutton：單選框
當有多個選擇但只能選一個的時後，就可以使用 Radiobutton 元件，同一組選擇的 variable 屬性必須設定同一個 tkinter 變數。
![image](https://hackmd.io/_uploads/H1bZuvx86.png)
主要的屬性如下：
* text（文字）： 按鈕上顯示的文字標籤。
* variable（變數）： 用來綁定一個 tkinter 變數，通常是 tkinter.StringVar()，這樣可以確保選項屬於同一個群組。
* value（值）： 指定當選擇此按鈕時，變數的值應該是什麼。這個值會被存儲在與 variable 綁定的 tkinter 變數中。
* bg / background: 設置背景色。
* fg / foreground: 設置前景色，即文字顏色。
* cursor：當滑鼠懸停在 Radiobutton 上時的游標樣式。
>本範例使用 grid 佈局
```python=
import tkinter as tk

def show_selected():
    if selected_value.get():
        result_label.config(fg="white", bg='blue', text=f"您的選擇是: {selected_value.get()}")

# 建立主視窗
form = tk.Tk()
form.title("Radiobutton 範例")

# Radiobutton
selected_value = tk.StringVar(value="")
radio_button_a = tk.Radiobutton(text="選項 X", variable=selected_value, value="選項 X", cursor="hand2", bg="#FFD700")
radio_button_b = tk.Radiobutton(text="選項 Y", variable=selected_value, value="選項 X", cursor="hand2", bg="#FFD700")
radio_button_c = tk.Radiobutton(text="選項 Z", variable=selected_value, value="選項 X", cursor="hand2", bg="#FFD700")

radio_button_a.grid(row=0, column=1, padx=10, sticky=tk.NSEW)
radio_button_b.grid(row=1, column=1, padx=10, sticky=tk.NSEW)
radio_button_c.grid(row=2, column=1, padx=10, sticky=tk.NSEW)

# 抓取選擇的按鈕
show_button = tk.Button(text="抓取選擇", command=show_selected, bg="#FFA07A")
show_button.grid(row=3, column=1, padx=10, sticky=tk.NSEW)

result_label = tk.Label(text="", pady=5)
result_label.grid(row=4, column=1, padx=10, pady=5, sticky=tk.W)

# 啟動主迴圈
form.mainloop()
```

Radiobutton 元件還有一些其他常用的方法(pack 與 bind 方法就不提了)，例如：
* invoke(): 方法用於模擬單選按鈕被單擊。
* select()：選中 Radiobutton。這會將與該單選按鈕關聯的變數設置為其值。
* deselect(): 取消選中 Radiobutton。這將取消與該單選按鈕關聯的變數。
* get(): 獲取與該 Radiobutton 關聯的變數的值。這是用於檢索目前選擇的值的方法。

>本範例使用 grid 佈局，進階參考
```python=
import tkinter as tk

def get_selection():
    selected_value.set(radiobutton_var.get())
    result_label.config(text=f"選擇是: {selected_value.get()}")

def clear_selection():
    radiobutton_var.set("")  # 清除所有選項的選擇
    selected_value.set("")  # 清空 label 的顯示
    result_label.config(text="")  # 清空 label 的顯示

def simulate_click():
    radiobutton2.invoke()
    get_selection()

# 建立主視窗
form = tk.Tk()
form.title("選項按鈕範例")
form.geometry("250x180")  # 調整視窗大小

# 與 Radiobutton 雙向同步的 StringVar
selected_value = tk.StringVar(value="")
radiobutton_var = tk.StringVar()

# Radiobutton
radiobutton1 = tk.Radiobutton(text="選項 X", variable=radiobutton_var, value="選項 X", bg="#FFD700")
radiobutton2 = tk.Radiobutton(text="選項 Y", variable=radiobutton_var, value="選項 Y", bg="#FFD700")
radiobutton3 = tk.Radiobutton(text="選項 Z", variable=radiobutton_var, value="選項 Z", bg="#FFD700")

radiobutton1.grid(row=0, column=0, padx=30, pady=5, sticky=tk.W)
radiobutton2.grid(row=1, column=0, padx=30, pady=5, sticky=tk.W)
radiobutton3.grid(row=2, column=0, padx=30, pady=5, sticky=tk.W)

# 抓取選擇的按鈕
get_selection_button = tk.Button(form, text="抓取選擇", command=get_selection, bg="#FFA07A")
get_selection_button.grid(row=1, column=1, padx=30, pady=5, sticky=tk.W)

# 清除選擇的按鈕
clear_selection_button = tk.Button(form, text="清除選擇", command=clear_selection, bg="#FFA07A")
clear_selection_button.grid(row=2, column=1, padx=30, pady=5, sticky=tk.W)

# 模擬單擊按鈕
simulate_click_button = tk.Button(form, text="模擬單擊", command=simulate_click, bg="#FFA07A")
simulate_click_button.grid(row=3, column=1, padx=30, pady=5, sticky=tk.W)

# 顯示結果的 Label
result_label = tk.Label(form, text="", pady=5)
result_label.grid(row=3, column=0, padx=30, pady=5, sticky=tk.W)

# 啟動主迴圈
form.mainloop()
```

:::success
**屬性的 text 與 value不同**

通常 Radiobutton 屬性的 text 與 value 會是相同的值，程式碼會比較好處理，但若遇到  text 與 value 必須不同，而且需要分別抓出選項的這二個值，則請看下來範例：
>本範例使用 grid 佈局，進階參考
```python=
import tkinter as tk

def get_text_by_value(value):
    """透過 winfo_children 方法來獲取主視窗中的所有元件，然後遍歷這些元件找到符合條件的 Radiobutton，最後取得其 text"""
    for widget in form.winfo_children():
        if isinstance(widget, tk.Radiobutton) and widget.cget("value") == value:
            return widget.cget("text")
    return None

def show_selected():
    selected_value_str = selected_value.get()
    selected_text = get_text_by_value(selected_value_str)

    if selected_text is not None:
        result_label.config(
            fg="white", bg='blue', text=f"您的選擇是: {selected_value_str}, 選項文字是: {selected_text}"
        )
    else:
        result_label.config(fg="white", bg='red', text="找不到選擇的 Radiobutton")

# 建立主視窗
form = tk.Tk()
form.title("Radiobutton 取得文字範例")

# Radiobutton
selected_value = tk.StringVar(value="")
radio_button_a = tk.Radiobutton(text="選項 X", variable=selected_value, value="1", bg="#FFD700")
radio_button_b = tk.Radiobutton(text="選項 Y", variable=selected_value, value="2", bg="#FFD700")
radio_button_c = tk.Radiobutton(text="選項 Z", variable=selected_value, value="3", bg="#FFD700")

radio_button_a.grid(row=0, column=1, padx=10, sticky=tk.NSEW)
radio_button_b.grid(row=1, column=1, padx=10, sticky=tk.NSEW)
radio_button_c.grid(row=2, column=1, padx=10, sticky=tk.NSEW)

# 抓取選擇的按鈕
show_button = tk.Button(text="抓取選擇", command=show_selected, bg="#FFA07A")
show_button.grid(row=3, column=1, padx=10, sticky=tk.NSEW)

result_label = tk.Label(text="", pady=5)
result_label.grid(row=4, column=1, padx=10, pady=5, sticky=tk.W)

# 啟動主迴圈
form.mainloop()
```
:::

### Checkbutton：複選框
每個元件有【選擇】與【不選擇】二種狀態，如果想進行複選，每個 Checkbutton 需要擁有獨立的 tk.IntVar() 變數。如此它們的狀態就可以獨立地被選中或取消選中。

如果多個 Checkbutton 的 variable 設為相同的 tk.IntVar() 變數，它們將共享這個變數，會產生類似於 Radiobutton 的結果，只有一個 Checkbutton 能被選中。
![image](https://hackmd.io/_uploads/HkdDuPxIT.png =500x)
主要的屬性如下：
* text（文字）：顯示在 Checkbutton 旁邊的文字標籤。
* variable（變數）：用於存儲 Checkbutton 的選中狀態的 tkinter 變數，通常是 tkinter.IntVar()，這樣可以確保屬於同一個群組。
* onvalue, offvalue：設定選中和未選中時，variable 的值。未設定時，variable 的預設值是 1 和 0。
* command：在 Checkbutton 被點擊時執行的函數。
* bg / background：設置背景色。
* fg / foreground：設置前景色，即文字顏色。
* cursor：當滑鼠懸停在 Checkbutton 上時的游標樣式。

> 原始碼範例
```python=
import tkinter as tk


def update_label():
    selected_interests = [interests[i] for i in range(len(interests)) if interests_vars[i].get()]
    result_label.config(text=f"選中的興趣：{', '.join(selected_interests)}")


# 建立主視窗
form = tk.Tk()
form.title("Checkbutton 範例")

# 興趣類別
interests = ["足球", "籃球", "棒球", "羽球"]

# 儲存選中狀態的變數列表
interests_vars = [tk.IntVar() for _ in interests]

# 使用迴圈建立 Checkbutton
for i in range(len(interests)):
    interest = interests[i]
    var = interests_vars[i]
    check_button = tk.Checkbutton(
        text=interest,
        variable=var,
        onvalue=1,
        offvalue=0,
        bg="#FFD700",  # 背景色
        fg="black",  # 文字顏色
        cursor="hand2",  # 游標樣式
    )
    # 使用 grid 佈局
    check_button.grid(row=i // 4, column=i % 4, padx=10, pady=5, sticky=tk.W)

# 顯示結果的 Label
result_label = tk.Label(text="選中的興趣：", pady=10)

# 按鈕用於更新結果
update_button = tk.Button(text="更新結果", command=update_label)
update_button.grid(row=(len(interests) - 1) // 4 + 1, column=0, columnspan=4, pady=10)

# 顯示結果的 Label
result_label.grid(row=(len(interests) - 1) // 4 + 2, column=0, columnspan=4)

# 啟動主迴圈
form.mainloop()
```
Checkbutton 元件還有一些其他常用的方法(pack 與 bind 方法就不提了)，例如：
* select(): 選中（勾選）Checkbutton。
* deselect(): 取消選中（取消勾選）Checkbutton。
* toggle(): 切換 Checkbutton 的選中狀態。
* invoke(): 模擬使用者點擊 Checkbutton，觸發其相關事件。

>本範例使用 grid 佈局
```python=
import tkinter as tk

def show_state():
    selected_interests = [interests[i] for i, var in enumerate(interests_vars) if var.get()]
    result_label.config(text=f"選中的球類：{', '.join(selected_interests)}")

def select_checkbuttons():
    for checkbutton in checkbuttons:
        checkbutton.select()

def deselect_checkbuttons():
    for checkbutton in checkbuttons:
        checkbutton.deselect()

def toggle_checkbuttons():
    for checkbutton in checkbuttons:
        checkbutton.toggle()

# 建立主視窗
form = tk.Tk()
form.title("Checkbutton 方法範例")

# 興趣類別
interests = ["足球", "籃球", "棒球", "羽球"]

# 儲存選中狀態的變數列表
interests_vars = [tk.IntVar() for _ in interests]

# 使用迴圈建立 Checkbutton
checkbuttons = []
for i, interest in enumerate(interests):
    check_button = tk.Checkbutton(text=interest, variable=interests_vars[i], cursor="hand2")
    # 使用 grid 佈局
    check_button.grid(row=i // 4, column=i % 4, padx=10, pady=5, sticky=tk.W)
    checkbuttons.append(check_button)

# 顯示結果的 Label
result_label = tk.Label(text="選中的球類：", pady=10)

# 按鈕用於更新結果
update_button = tk.Button(text="更新結果", command=show_state)
update_button.grid(row=(len(interests) - 1) // 4 + 1, column=0, columnspan=4, pady=10)

# 顯示結果的 Label
result_label.grid(row=(len(interests) - 1) // 4 + 2, column=0, columnspan=4)

# 按鈕用於全選
select_button = tk.Button(text="全選", command=select_checkbuttons)
select_button.grid(row=(len(interests) - 1) // 4 + 3, column=0, columnspan=2, pady=10)

# 按鈕用於取消全選
deselect_button = tk.Button(text="取消全選", command=deselect_checkbuttons)
deselect_button.grid(row=(len(interests) - 1) // 4 + 3, column=2, columnspan=2, pady=10)

# 按鈕用於切換狀態
toggle_button = tk.Button(text="切換狀態", command=toggle_checkbuttons)
toggle_button.grid(row=(len(interests) - 1) // 4 + 4, column=0, columnspan=4, pady=10)

# 使用 invoke 點擊 select_button
select_button.invoke()

# 啟動主迴圈
form.mainloop()
```

:::warning
**具有 textvariable 屬性的元件包括**
- Label: 用來顯示文字的標籤。
- Entry: 文字輸入框。
- Message: 用來顯示多行文字的元件。

**具有 variable 屬性的元件包括**
- Radiobutton: 單選按鈕。
- Checkbutton: 多選按鈕 (勾選框)。
:::

### Frame：容器，可將各種元件放在同一個容器方便管理
![image](https://hackmd.io/_uploads/HJ6ysSmIa.png =700x)

就像網頁的 `<div>` 標籤概念，Frame 可建立一個容器，可包含其他元件，例如按鈕、標籤、文字方塊，主要屬性有
* bg / background：設定 Frame 的背景顏色。
* width、height：設定 Frame 的寬度和高度。
* relief：設定 Frame 邊框的樣式，常用的有 "flat"、"raised"、"sunken" 等。
* padx、 pady：設定 Frame 內部的水平和垂直填充。
* bd / borderwidth：設定 Frame 的邊框寬度。

>本範例使用 place 佈局
```python=
import tkinter as tk

form = tk.Tk()
form.geometry("300x200")

frame = tk.Frame(width=200, height=100, background="lightblue", relief="raised", borderwidth=2)
frame.place(x=50, y=50)  # 使用 place 佈局方法

label = tk.Label(frame, text="這是一個 Frame")
label.place(relx=0.5, rely=0.5, anchor="center")  # 在 Frame 中使用相對座標佈局

form.mainloop()
```
frame 元件的方法都是繼承父類別的共通方法，例如 config(), bind(), destroy()。
```python=
import tkinter as tk
from tkinter import messagebox

def on_click(event):
    messagebox.showinfo("訊息", "Frame 被點擊了")

form = tk.Tk()
form.geometry("300x220")

frame = tk.Frame()
frame.place(x=50, y=50)
frame.config(bg="lightblue", width=200, height=100)
frame.bind("<Button-1>", on_click)  # 綁定左鍵單擊事件

button = tk.Button(text="移除 Frame", command=frame.destroy)
button.place(x=50, y=160, anchor="w")

form.mainloop()
```

### Listbox：列表框
![image](https://hackmd.io/_uploads/rJ3O7v78p.png)

Listbox 元件用於顯示一個有多個選項的列表供使用者選擇。常用的屬性包含：
* selectmode：設定選擇模式，可以是 tk.SINGLE（單選）或 tk.MULTIPLE（多選）。
* height、width：設定 Listbox 的高度和寬度。
* bg / background、fg / foreground：設定背景和前景顏色。
* font：設定字體。
```python=
import tkinter as tk

def on_select(event):
    selected_items = listbox.curselection()
    selected_text.set(", ".join(listbox.get(index) for index in selected_items))

form = tk.Tk()
form.geometry("400x300")  # 設定適當的視窗大小
form.title("Listbox 範例")

# 建立 Listbox，設置選擇模式為多選
items = ["書法", "歌唱", "彈琴", "功夫", "繪畫"]
listbox = tk.Listbox(
    selectmode=tk.MULTIPLE,
    height=len(items),
    width=25,
    bg="lightgray",
    fg="blue",
    font=("Arial", 12),
    selectbackground="lightblue",
)
listbox.place(relx=0.5, rely=0.4, anchor="center")  # 置中

# 插入一些選項
for item in items:
    listbox.insert(tk.END, item)

# 綁定選擇事件
listbox.bind("<<ListboxSelect>>", on_select)

# Label 用以顯示選擇的項目，加上底色
label = tk.Label(text="選擇的項目：")
label.place(x=50, y=200)
selected_text = tk.StringVar()
selected_label = tk.Label(textvariable=selected_text, font=("Arial", 12))
selected_label.place(x=50, y=220)

form.mainloop()
```
常見的 Listbox 方法如下
* insert(index, *elements): 在指定索引位置插入一個或多個元素。
* delete(first, last=None): 刪除指定範圍內的元素。
* get(first, last=None): 獲取指定範圍內的元素。
* curselection(): 返回當前所選的元素的索引。
* size(): 返回列表中的元素數量。
* selection_set(first, last=None): 將指定範圍的元素設置為選取狀態。
* selection_clear(first, last=None): 清除指定範圍內的元素的選取狀態。
```python=
import tkinter as tk

def get_selected_items():
    selected_items = listbox.curselection()
    selected_elements = [listbox.get(index) for index in selected_items]
    selected_text.set("目前所選元素: " + ", ".join(selected_elements))

def add_item():
    item = entry.get()
    if item:
        listbox.insert(tk.END, item)
        listbox.config(height=listbox.size()) #動態設定 listbox 的高度
        entry.delete(0, tk.END)

def remove_selected():
    selected_items = listbox.curselection()
    for index in reversed(selected_items):
        listbox.delete(index)
    listbox.config(height=listbox.size())     #動態設定 listbox 的高度

def clear_selection():
    listbox.selection_clear(0, tk.END)
    # selected_text.set("")

def select_all():
    listbox.selection_set(0, tk.END)
    # selected_items = listbox.curselection()
    # selected_elements = [listbox.get(index) for index in selected_items]
    # selected_text.set("目前所選元素: " + ", ".join(selected_elements))

form = tk.Tk()
form.geometry("250x250")
form.title("Listbox 方法範例")

blood_types = ["A", "B", "AB", "O"]
listbox = tk.Listbox(
    selectmode=tk.MULTIPLE,
    height=len(blood_types),
    width=10,
    bg="lightgray",
    fg="blue",
    selectbackground="lightblue",
    bd=0,
)
listbox.place(x=100, y=20)

for blood_type in blood_types:
    listbox.insert(tk.END, blood_type)

selected_text = tk.StringVar()
selected_label = tk.Label(textvariable=selected_text)
selected_label.place(x=30, y=120)

entry = tk.Entry(width=20)
entry.place(x=50, y=150)

add_button = tk.Button(text="新增", command=add_item)
add_button.place(x=40, y=180)

remove_button = tk.Button(text="刪除選取", command=remove_selected)
remove_button.place(x=90, y=180)

clear_button = tk.Button(text="取消選擇", command=clear_selection)
clear_button.place(x=160, y=180)

get_selected_button = tk.Button(text="取得所選元素", command=get_selected_items)
get_selected_button.place(x=40, y=210)

select_all_button = tk.Button(text="全選", command=select_all)
select_all_button.place(x=140, y=210)

form.mainloop()
```

### Scrollbar：捲動條
通常搭配 Text Widget 進行使用，可以設定為水平捲動或垂直捲動。

### Text：文字框
![image](https://hackmd.io/_uploads/SJwL5VEI6.png)

Tkinter 的 Text widget 是用來顯示和編輯多列文字的元件。常用的屬性有：
* height：設定 Text widget 的高度，以列數為單位。
* width：設定 Text widget 的寬度，以字元數為單位。
* wrap：指定文字如何被包裹。可選值包括 tk.NONE（不換行）、tk.CHAR（以字元換行）和 tk.WORD（以單字換行）。
* insertbackground：設定插入點的背景顏色。
* insertwidth：設定插入點的寬度（以像素為單位）。
* font：設定文字的字型、大小等。
* bg：設定背景顏色。
* fg：設定文字顏色。
* state：設定 Text widget 的狀態，可為 "normal"（可編輯）或 "disabled"（不可編輯）。
* yscrollcommand：須搭配 Scrollbar 元件，才能垂直捲動。
* xscrollcommand：須搭配 Scrollbar 元件，才能水平捲動。

Text 元件常用的方法有：
* insert(index, text, tags=None)：在指定的索引處插入文字，可以指定文字的標籤（tags）。
* delete(startindex, endindex=None)：刪除指定範圍的文字。如果 endindex 為 None，則只刪除指定索引的文字。
* index(index)：取得指定索引處的文字位置。
* mark_set(mark, index)：將指定標記移動到 Text 元件的特定位置，常用的標記名稱有 tk.INSERT、tk.SEL、tk.CURRENT、tk.END、tk.USER。
* see(index)：捲動 Text widget 以使指定索引的文字可見。
* get(startindex, endindex=None)：取得指定範圍的文字。
* search(needle, startindex, stopindex, forwards=True, backwards=True, exact=False, regexp=False)：在指定範圍內搜索文字，返回第一個找到的位置。

> Tkinter 的 Scrollbar 與 Text 元件使用的範例原始碼：
https://gist.github.com/peterju/4f0beebfff2bccfb8e0f2a91c342fffe

### Scale：滑動條
![image](https://hackmd.io/_uploads/BkT8r8ELT.png)

滑動條元件，可滑動一個數值範圍，方便選擇明確的數字。常用的屬性如下：
* from_：指定滑動條的最小值。
* to：指定滑動條的最大值。
* orient：指定滑動條的方向，可以是 tk.HORIZONTAL 或 tk.VERTICAL。
* length：指定滑動條的長度，單位是像素。
* showvalue：決定是否在滑動條上方顯示數值。可以設定為 0（不顯示）或 1（顯示）。
* tickinterval：設定刻度的間隔。
* resolution：滑動條的解析度，即滑動一次的步進值。
* command：滑動條值發生變化時呼叫的函數。
* variable：用於綁定一個 tkinter 變數，當滑動條值改變時，這個變數的值也會同步改變。
* digits：指定顯示數值的小數位數。
* label：設定滑動條的標籤
* troughcolor：設定滑動條的背景，這個屬性的值可以是一個顏色名稱（例如 "red"）、十六進制顏色碼（例如 "#FF0000"）或其他支援的顏色表示形式。

滑動條常用的方法有：
* `get()`：用於獲取滑動條的當前值。
* `set(value)`：用於設定滑動條的值。
* `config(**options)`：用於設定或修改滑動條的配置選項。
* `invoke(element)`：用於觸發或執行與滑動條相關聯的元素（例如連結的命令）。

> 範例程式碼：https://gist.github.com/peterju/e3fd16776bd1f2dd6d7800915dc3cb4c

### Spinbox：數值調整/選單輸入框
![image](https://hackmd.io/_uploads/B1Sy7AJDT.png)

Spinbox 也是一種輸入框，除了可直接輸入數值之外，可以用上下箭頭增減數值，或用滑鼠點選右方的上下箭頭進行數值增減。 與 Scale 元件相同都可以拿來輸入數值。

Spinbox 也可以提供列表值，可以用上下箭頭或用滑鼠點選右方的上下圖示進行選擇。功能上與 ttk 的 combobox 相似。

常用屬性：
* from_ 屬性： 設置可選值的起始範圍。
* to 屬性： 設置可選值的結束範圍。
* values 屬性： 指定一個離散的值列表，而不是範圍。
* increment 屬性： 設置增加或減少的步長。
* textvariable 屬性： 設置或獲取與 Spinbox 關聯的 StringVar（或其衍生類型）物件。
* state 屬性： 設置元件的狀態
    * "normal": 允許使用者以輸入、鍵盤、滑鼠修改值。
    * "readonly": 不允許輸入修改值，但鍵盤與滑鼠可以。
    * "disabled": 僅顯示其值，輸入、鍵盤、滑鼠皆無法修改值且以灰暗顯示。
* wrap 屬性： 如果設置為 True，則在達到範圍的末尾時，Spinbox 的值將從開始處繼續。

常用方法：
* delete(start, end) 方法： 用於從 Spinbox 中刪除文字。
* get() 方法： 用於取得當前 Spinbox 的值。
* icursor(index) 方法： 用於設置插入符號（游標）的位置。
* index(index) 方法： 用於取得指定索引位置的字元。
* insert(index, string) 方法： 用於在指定的索引位置插入字元。
* invoke() 方法： 用於手動觸發 Spinbox 上的動作。通常用於模擬用戶單擊箭頭按鈕的操作。

> 範例原始碼：
```python=
import tkinter as tk

def on_change_spinbox1():
    value_label.config(text="Current Value (Spinbox 1): " + spinbox_var1.get())

def on_change_spinbox2():
    value_label2.config(text="Current Value (Spinbox 2): " + spinbox_var2.get())

form = tk.Tk()
form.title("Spinbox 範例")
form.geometry("300x200")

# 建立 StringVar 以關聯 Spinbox1
spinbox_var1 = tk.StringVar()

# 第一個 Spinbox，使用 from_, to 屬性
spinbox1 = tk.Spinbox(
    form,
    from_=0,
    to=100,
    increment=5,
    textvariable=spinbox_var1,
    command=on_change_spinbox1,
    font=("Arial", 12),
)
spinbox1.grid(row=0, column=0, padx=10, pady=10, sticky="nsew")

# 顯示當前值的 Label1
value_label = tk.Label(
    form, text="Current Value (Spinbox 1): " + spinbox1.get(), font=("Arial", 12)
)
value_label.grid(row=1, column=0, padx=10, pady=10)

# 設置初始值
spinbox_var1.set("20")

# 建立 StringVar 以關聯 Spinbox2
spinbox_var2 = tk.StringVar()

# 第二個 Spinbox，使用 values、state、wrap 屬性
blood_types = ["A", "B", "O", "AB"]
spinbox2 = tk.Spinbox(
    form,
    values=blood_types,
    state="readonly",  # 設置為 "readonly" 以防止直接輸入
    wrap=True,
    textvariable=spinbox_var2,
    command=on_change_spinbox2,
    font=("Arial", 12),
)
spinbox2.grid(row=2, column=0, padx=10, pady=10, sticky="nsew")

# 創建顯示當前值的 Label2
value_label2 = tk.Label(
    form, text="Current Value (Spinbox 2): " + spinbox2.get(), font=("Arial", 12)
)
value_label2.grid(row=3, column=0, padx=10, pady=10)

# 設置初始值
spinbox_var2.set("")

# 顯示視窗
form.mainloop()
```

### Menu：選單
![image](https://hackmd.io/_uploads/H1nix0kD6.png)

> 在 ttk 中沒有對應的主題化版本

用於在視窗上建立選單。常用的屬性有：
* tearoff（拆分）： 用於決定選單是否可以被拆分出來。如果設置為 1，則選單可以被拆分。如果設置為 0，則不允許拆分。預設為 1。

常用的方法有：
* add_command(options)： 向選單中添加一個命令項目(點擊事件)。
* add_separator()： 向選單中添加分隔線。
* add_cascade(options, menu)： 向選單中添加子選單。
* delete(index)： 刪除選單中指定索引的項目。
* entryconfig(index, options)： 配置選單項目的選項。
* insert_separator(index)： 在選單中指定索引的位置插入分隔線。

> 範例原始碼：
```python=
import tkinter as tk
from tkinter import messagebox

def hello():
    messagebox.showinfo("說明", "Hello!")

def open_file():
    messagebox.showinfo("說明", "Open File")

def save_file():
    messagebox.showinfo("說明", "Save File")

def exit_app():
    form.destroy()

# 建立主視窗
form = tk.Tk()
form.title("Menu Widget 範例")

# 建立一個選單
menu_bar = tk.Menu(form)

# 在選單上建立 File 選單
file_menu = tk.Menu(menu_bar, tearoff=0)
menu_bar.add_cascade(label="File", menu=file_menu)  # 將子選單加入主選單

# 向 File 選單添加命令項目和分隔線
file_menu.add_command(label="Open", command=open_file)
file_menu.add_command(label="Save", command=save_file)
file_menu.add_separator()
file_menu.add_command(label="Exit", command=exit_app)

# 在選單上建立 Help 選單
help_menu = tk.Menu(menu_bar, tearoff=0)  # tearoff=0 表示不可拆分
menu_bar.add_cascade(label="Help", menu=help_menu)  # 將子選單加入主選單

# 向 Help 選單添加命令項目
help_menu.add_command(label="About", command=hello)

# 為視窗設置選單
form.config(menu=menu_bar)

# 啟動主視窗的事件迴圈
form.mainloop()
```

### Canvas：畫布
![image](https://hackmd.io/_uploads/Hkbg7qrUa.png)
> 在 ttk 中沒有對應的主題化版本

Canvas 是用來在視窗上繪製和操作 2D 圖形的元件。常見的屬性有：
* width：Canvas 的寬度。
* height：Canvas 的高度。
* bg：Canvas 的背景色。
* bd：Canvas 的邊框寬度。
* highlightthickness：在 Canvas 被選取時的邊框寬度。
* relief: Canvas 的邊框樣式，可以是 tk.FLAT、tk.SUNKEN、tk.RAISED、tk.GROOVE 或 tk.RIDGE。
* scrollregion：定義了 Canvas 中可見區域的邊界，超出此區域的內容可以通過捲軸來查看。
* yscrollincrement：在垂直捲軸上的滑動增量。
* xscrollincrement：在水平捲軸上的滑動增量。
* yscrollcommand：指定與垂直捲軸關聯的命令。
* xscrollcommand：指定與水平捲軸關聯的命令。

常見的方法有：
* create_line：用於在 Canvas 上繪製直線。
* create_rectangle：用於在 Canvas 上繪製矩形。
* create_oval：用於在 Canvas 上繪製橢圓。
* create_text：用於在 Canvas 上插入文字。
* delete：用於刪除 Canvas 上的特定圖形或文字。
* move：用於移動 Canvas 上的圖形或文字。
* itemconfig：用於修改 Canvas 上圖形或文字的配置。
* bind：用於綁定事件處理器，讓 Canvas 具有互動性。
* create_image：用於在 Canvas 上插入圖片。
* config：用於設定 Canvas 的各種屬性。
* xview：用於在 Canvas 上水平方向上進行視窗滾動。
* yview：用於在 Canvas 上垂直方向上進行視窗滾動。

> 範例原始碼：https://gist.github.com/peterju/d25066a90249da74a25a1d6f41868dff

### PanedWindow：窗格容器
PanedWindow 是 Tkinter 中的元件，用於建立具有可調整佈局的應用程式。它可以建立窗格，允許內部的 frame 以水平或垂直排列，並允許移動分隔線以調整各個 frame 的大小。以下是一些常用的屬性和方法：

**常用屬性：**
* orient：定義窗格的方向，可以是 tk.HORIZONTAL（水平）或 tk.VERTICAL（垂直）。
* handlesize：設定分隔線中的「手柄」（handle）尺寸。。
* sashrelief：定義分隔線的外觀，可以是 tk.RAISED、tk.SUNKEN、tk.FLAT、tk.GROOVE 或 tk.RIDGE。
* sashwidth：設定分隔線的寬度。
* opaqueresize：拉動分隔線時，分隔線是否採取不透明方式處理。預設為 True。

範例程式碼：
```python=
import tkinter as tk

def create_nested_paned_windows(parent):
    # 外層 PanedWindow（水平方向）
    outer_paned_window = tk.PanedWindow(
        parent,
        orient=tk.HORIZONTAL,
        handlesize=8,
        sashrelief=tk.RAISED,
        sashwidth=4,
        opaqueresize=True,
    )
    outer_paned_window.grid(row=0, column=0, sticky="nsew")

    # 左側窗格
    frame_left = tk.Frame(outer_paned_window, background="lightblue", width=200, height=300)
    outer_paned_window.add(frame_left, minsize=100)

    # 內層 PanedWindow（垂直方向）
    inner_paned_window = tk.PanedWindow(
        outer_paned_window,
        orient=tk.VERTICAL,
        handlesize=8,
        sashrelief=tk.RAISED,
        sashwidth=4,
        opaqueresize=False,
    )
    # 將內層 PanedWindow 添加到外層 PanedWindow 中
    outer_paned_window.add(inner_paned_window, minsize=100)

    # 將上方窗格添加到內層 PanedWindow 中
    frame_top = tk.Frame(inner_paned_window, background="lightgreen", width=200, height=150)
    inner_paned_window.add(frame_top, minsize=50)

    # 將下方窗格添加到內層 PanedWindow 中
    frame_bottom = tk.Frame(inner_paned_window, background="lightcoral", width=200, height=150)
    inner_paned_window.add(frame_bottom, minsize=50)

form = tk.Tk()
form.title("嵌套 PanedWindow 範例")
form.geometry("500x400")

# 設定行和列的權重，使得窗格可以隨著視窗的變化而調整大小
form.grid_rowconfigure(0, weight=1)
form.grid_columnconfigure(0, weight=1)

create_nested_paned_windows(form)

form.mainloop()
```

**常用方法1**
* add(child, options)：向窗格中添加子控件，child 是要添加的子控件，options 是一個字典，用於指定窗格的配置選項。
* forget(child)：從窗格中移除指定的子控件。
* cget(option)：取得元件的屬性值，option指的是整個 PanedWindow 元件的全域配置選項，例如 handlesize、sashwidth 等。
* panes()：返回窗格中所有子控件的列表。

程式碼範例：
```python=
import random
import tkinter as tk

def add_pane():
    # 新增一個窗格，並設定動態的背景色
    # random.randint(0, 0xFFFFFF): 產生一個從 0 到 0xFFFFFF之間的隨機整數。
    # :06X: 是 f-string 的格式化語法。指定了生成的十六進位數字要顯示為至少 6 位數（包括可能的前綴 "0x"），並以大寫字母表示。
    new_frame = tk.Frame(
        paned_window,
        background=f"#{random.randint(0, 0xFFFFFF):06X}",
        width=100,
        height=200,
    )
    paned_window.add(new_frame, minsize=100)

    # 檢查剩餘空間是否足夠，不足的話就增加視窗的寬度
    # sashwidth 屬性可取得分隔線的寬度
    total_width = sum(
        form.nametowidget(pane_id).winfo_reqwidth() + paned_window.cget("sashwidth")
        for pane_id in paned_window.panes()
    )
    if total_width > form.winfo_width():
        form.geometry(f"{max(total_width, 300)}x300")

def remove_pane():
    # 移除最後一個窗格
    if paned_window.panes():
        paned_window.forget(paned_window.panes()[-1])

        # 檢查剩餘窗格的總寬度，如果小於視窗寬度，則縮小視窗
        # sashwidth 屬性可取得分隔線的寬度
        total_width = sum(
            form.nametowidget(pane_id).winfo_reqwidth() + paned_window.cget("sashwidth")
            for pane_id in paned_window.panes()
        )
        if total_width < form.winfo_width():
            form.geometry(f"{max(total_width, 300)}x300")

def print_panes():
    # 印出目前的窗格列表
    print("Panes:", *paned_window.panes(), sep="\n")

form = tk.Tk()
form.title("Dynamic Background PanedWindow Example")
form.geometry("400x300")

# 建立 PanedWindow，水平方向
paned_window = tk.PanedWindow(
    form, orient=tk.HORIZONTAL, handlesize=8, sashrelief=tk.RAISED, sashwidth=4
)
paned_window.pack(expand=True, fill=tk.BOTH)

# 建立操作按鈕的 Frame
button_frame = tk.Frame(form)
button_frame.pack(side=tk.BOTTOM, fill=tk.X)

# 操作按鈕
add_button = tk.Button(button_frame, text="Add Pane", command=add_pane)
add_button.pack(side=tk.LEFT, padx=5)

remove_button = tk.Button(button_frame, text="Remove Pane", command=remove_pane)
remove_button.pack(side=tk.LEFT, padx=5)

print_button = tk.Button(button_frame, text="Print Panes", command=print_panes)
print_button.pack(side=tk.LEFT, padx=5)

form.mainloop()
```
**常用方法2**
* sash_place(index, x, y)：設定指定分隔線的位置。
* sash_coord(index)：獲取指定分隔線的位置。
* config()：設定元件的屬性。

程式碼範例：
```python=
import tkinter as tk

# 儲存分隔線位置的字典
sash_positions = {}

def save_sash_coord():
    # 儲存所有分隔線的位置
    for index in range(len(paned_window.panes()) - 1):
        coord = paned_window.sash_coord(index)
        # 將tuple (x, y) 中的 x 部分儲存起來
        x = int(coord[0])
        sash_positions[index] = x
        print(f"Sash Coord {index} saved:", coord)

def restore_sashes():
    # 將所有分隔線位置調整至存儲的位置
    for index, position in sash_positions.items():
        paned_window.sash_place(index, position, 0)

def change_sashwidth():
    # 改變分隔線的寬度
    new_width = paned_window.cget("sashwidth") + 2
    paned_window.config(sashwidth=new_width)
    print("分隔線寬度更改為:", new_width)

form = tk.Tk()
form.title("PanedWindow 分隔線位置儲存與回復")
form.geometry("500x300")

paned_window = tk.PanedWindow(
    form, orient=tk.HORIZONTAL, handlesize=8, sashrelief=tk.RAISED, sashwidth=4
)
paned_window.pack(expand=True, fill=tk.BOTH)

frame1 = tk.Frame(paned_window, background="lightblue", width=100, height=200)
paned_window.add(frame1, minsize=100)

frame2 = tk.Frame(paned_window, background="lightgreen", width=100, height=200)
paned_window.add(frame2, minsize=100)

frame3 = tk.Frame(paned_window, background="lightcoral", width=100, height=200)
paned_window.add(frame3, minsize=100)

# 修改按鈕文字和功能
save_sash_coord_button = tk.Button(form, text="儲存分隔線位置", command=save_sash_coord)
save_sash_coord_button.pack(side=tk.LEFT, padx=5)

restore_sashes_button = tk.Button(form, text="回復分隔線位置", command=restore_sashes)
restore_sashes_button.pack(side=tk.LEFT, padx=5)

change_sashwidth_button = tk.Button(form, text="改變分隔線寬度", command=change_sashwidth)
change_sashwidth_button.pack(side=tk.LEFT, padx=5)

form.mainloop()
```
## Tkinter 套件下的獨立模組
tkinter 除了標準的元件可供使用外，還有須單獨引入的模組功能，包含了
1. ttk
2. messagebox
3. simpledialog
4. scrolledtext
5. filedialog
6. colorchooser
7. font

### 1. 外型現代化的 ttk 模組

[ttk 模組](https://docs.python.org/zh-tw/3/library/tkinter.ttk.html)是 tkinter 套件（Python的標準GUI工具包）中的一個模組，它提供了一組用於建立現代風格的 GUI 元件的工具。 ttk 代表 "themed Tkinter"，意味著它支援主題樣式，可以讓你的應用程序在不同平台上看起來更加一致和現代。

| tk模組元件 | ttk模組元件 |
| -------- | -------- |
|![image](https://hackmd.io/_uploads/HJ8hcqtUa.png =500x) |![image](https://hackmd.io/_uploads/Hk62c5KI6.png =500x) |
> 範例原始碼：https://gist.github.com/peterju/96c68bafaf0697021b8d593c20b8b988

ttk 模組的元件分為 2 類：
1. 美化的原生元件
2. 增加的新元件

#### 1.1 ttk 美化的原生元件
ttk 美化了下列 12 種 Tkinter 的原生元件：
1. Button (按鈕)
1. Checkbutton (選擇框)
1. Entry (輸入框)
1. Frame (框架)
1. Label (標籤)
1. Labelframe (標籤框架)
1. Menubutton (選單按鈕)
1. PanedWindow (分格窗口)
1. Radiobutton (單選按鈕)
1. Scale (滑動條)
1. Scrollbar(滾動條)
1. Spinbox (數值調整/選單輸入框)

![image](https://hackmd.io/_uploads/SJvax5YLp.png)

使用方法範例：https://gist.github.com/peterju/af41561f03f7aebd0b18d3325399d84f#file-ttk-py

#### 1.2 ttk 增加的新元件
增加了下列 6 種元件
* Combobox (下拉選單)
* Notebook (頁籤)
* Progressbar (進度條)
* Separator (分隔線)
* Sizegrip（調整視窗尺寸的三角控點）
* Treeview (顯示樹狀或表格結構的資料)

![image](https://hackmd.io/_uploads/BJ0frcY86.png)

使用方法範例：https://gist.github.com/peterju/af41561f03f7aebd0b18d3325399d84f#file-ttk-tk-py

上述每一個元件都還有自己的屬性與方法，請自行搜尋進行了解。

### 2. Messagebox：訊息視窗
![image](https://hackmd.io/_uploads/rJ4dhPpLp.png)

常用於顯示標準的訊息或對話視窗，但不提供使用者輸入。它主要用於顯示消息、警告或錯誤訊息，並接受用戶的確認或回應。需要明確 import messagebox 模組才能使用，常用的方法如下：
- showinfo(title, message)：顯示一個帶有標題和消息的【訊息】對話視窗。
- showwarning(title, message)：顯示一個帶有標題和訊息的【警告】對話視窗。
- showerror(title, message)：顯示一個帶有標題和訊息的【錯誤】對話視窗。
- askquestion(title, message)：顯示一個帶有標題和訊息的【是/否】對話視窗，返回值是字串，可能的值有 "yes"、"no"。
- askokcancel(title, message)：顯示一個帶有標題和訊息的【確定/取消】對話視窗，返回值是布林，可能的值有 True、False。
- askyesno(title, message)：顯示一個帶有標題和訊息的【是/否】對話視窗，返回值是布林，可能的值有 True、False。
- askretrycancel(title, message)：顯示一個帶有標題和訊息的【重試/取消】對話視窗，返回值是布林，可能的值有 True、False。

使用方法範例：
```python=
import tkinter as tk
from tkinter import messagebox  # from 套件 import 模組

# 建立主視窗
form = tk.Tk()
form.title("Messagebox 範例")
form.geometry("300x250")

def show_info_message():
    messagebox.showinfo("Information", "This is an information message.")

# 建立按鈕觸發 Messagebox 事件
info_button = tk.Button(text="Show Info Message", command=show_info_message)
info_button.pack(pady=5)


def show_warning_message():
    messagebox.showwarning("Warning", "This is a warning message.")

warning_button = tk.Button(text="Show Warning Message", command=show_warning_message)
warning_button.pack(pady=5)

def show_error_message():
    messagebox.showerror("Error", "This is an error message.")

error_button = tk.Button(text="Show Error Message", command=show_error_message)
error_button.pack(pady=5)

def ask_question():
    # 顯示一個帶有 "是" 和 "否" 按鈕的對話框，返回值是字串，"yes" 或 "no"。
    result = messagebox.askquestion("Question", "Do you want to proceed?")
    print(f"Your choice: {result}")

question_button = tk.Button(text="Ask Question", command=ask_question)
question_button.pack(pady=5)

def ask_ok_cancel():
    # 顯示一個帶有 "確定" 和 "取消" 按鈕的對話框，返回值是布林值，True  或 False。
    result = messagebox.askokcancel("Confirmation", "Do you want to proceed?")
    print(f"Your choice: {result}")

ok_cancel_button = tk.Button(text="Ask OK/Cancel", command=ask_ok_cancel)
ok_cancel_button.pack(pady=5)

def ask_yes_no():
    # 顯示一個帶有 "是" 和 "否" 按鈕的對話框，返回值是布林值，True  或 False。
    result = messagebox.askyesno("Question", "Do you want to proceed?")
    print(f"Your choice: {result}")

yes_no_button = tk.Button(text="Ask Yes/No", command=ask_yes_no)
yes_no_button.pack(pady=5)

def ask_retry_cancel():
    # 顯示一個帶有 "重試" 和 "取消" 按鈕的對話框，返回值是布林值，True  或 False。
    result = messagebox.askretrycancel("Retry or Cancel", "An error occurred. Retry?")
    print(f"Your choice: {result}")

retry_cancel_button = tk.Button(text="Ask Retry/Cancel", command=ask_retry_cancel)
retry_cancel_button.pack(pady=5)

# 執行主迴圈
form.mainloop()
```

### 3. Simpledialog：對話視窗
![image](https://hackmd.io/_uploads/rJQ3RPaI6.png)

simpledialog 模組是 Tkinter 中提供的一個用於顯示簡單對話視窗模組，主要用於獲取使用者輸入的文字、整數、浮點數等。

常用方法有：
* askstring(title, prompt, **kwargs)：顯示一個用戶可以輸入【文字】的對話視窗。
* askinteger(title, prompt, **kwargs)：顯示一個用戶可以輸入【整數】的對話視窗。
* askfloat(title, prompt, **kwargs)：顯示一個用戶可以輸入【浮點數】的對話視窗。

使用方法範例：
```python=
import tkinter as tk
from tkinter import simpledialog

def show_string_dialog():
    user_input = simpledialog.askstring("輸入", "請輸入您的姓名:")
    if user_input:
        print(f"您的姓名是：{user_input}")
    else:
        print("您取消了輸入")

def show_integer_dialog():
    user_input = simpledialog.askinteger("輸入", "請輸入您的年齡:")
    if user_input is not None:
        print(f"您的年齡是：{user_input}")
    else:
        print("您取消了輸入")

def show_float_dialog():
    user_input = simpledialog.askfloat("輸入", "請輸入您的體重:")
    if user_input is not None:
        print(f"您的體重是：{user_input}")
    else:
        print("您取消了輸入")

form = tk.Tk()
form.title("Simple Dialog 範例")
form.geometry("300x180")

string_button = tk.Button(form, text="輸入字串", command=show_string_dialog)
string_button.pack(pady=10)

integer_button = tk.Button(form, text="輸入整數", command=show_integer_dialog)
integer_button.pack(pady=10)

float_button = tk.Button(form, text="輸入浮點數", command=show_float_dialog)
float_button.pack(pady=10)

exit_button = tk.Button(form, text="離 開", command=form.destroy)
exit_button.pack(pady=10)

form.mainloop()
```

simpledialog 還提供了一個 Dialog 類別，可以用來繼承建立自定義的對話框類別。
```python=
import tkinter as tk
from tkinter import simpledialog

class CustomDialog(simpledialog.Dialog):
    def body(self, master):
        tk.Label(master, text="輸入您的姓名:").grid(row=0, sticky=tk.W)
        tk.Label(master, text="輸入您的年齡:").grid(row=1, sticky=tk.W)

        self.name_entry = tk.Entry(master)
        self.age_entry = tk.Entry(master)

        self.name_entry.grid(row=0, column=1)
        self.age_entry.grid(row=1, column=1)

        return self.name_entry  # 初始焦點在姓名輸入框上

    def apply(self):
        name = self.name_entry.get()
        age = self.age_entry.get()

        # 將姓名和年齡傳回主視窗的標籤
        name_label.config(text="姓名: " + name)
        age_label.config(text="年齡: " + age)

# 主應用程式視窗
form = tk.Tk()
form.title("主視窗")

name_label = tk.Label(form, text="姓名: ")
age_label = tk.Label(form, text="年齡: ")

name_label.grid(row=2, column=0, sticky=tk.W)
age_label.grid(row=3, column=0, sticky=tk.W)

# 設定在主視窗顯示後約100毫秒打開CustomDialog
form.after(100, lambda: CustomDialog(form, title="自定義對話框"))

form.mainloop()
```
### 4. ScrolledText：捲動文字框
若想要有一個多列編輯的文字視窗，但不想處理文字視窗與滾動條元件的位置與關聯問題，則可使用 ScrolledText 元件。此元件需要明確 import scrolledtext 模組才能使用。

![image](https://hackmd.io/_uploads/rJViy4VLa.png)

使用方法範例：
```python=
import tkinter as tk
from tkinter import scrolledtext   # from 套件 import 模組

def insert_text():
    content = entry.get()
    text_widget.insert(tk.END, content + "\n")
    entry.delete(0, tk.END)

# 建立主視窗
form = tk.Tk()
form.geometry("330x280")
form.title("ScrolledText 範例")

# 使用 ScrolledText
text_widget = scrolledtext.ScrolledText(form, width=40, height=10, wrap=tk.WORD)
text_widget.place(x=10, y=10)  # 設定具體的位置

# Entry 與插入按鈕
entry = tk.Entry(form, width=30)
entry.place(x=10, y=230)  # 設定具體的位置
insert_button = tk.Button(form, text="插入文字", command=insert_text)
insert_button.place(x=250, y=230)  # 設定具體的位置

# 執行主循環
form.mainloop()
```

### 5. filedialog：檔案和目錄的選擇視窗

可讓使用者瀏覽檔案系統，選擇特定的檔案或目錄

![image](https://hackmd.io/_uploads/By19oK6U6.png)

常見的方法有
* askopenfilename(**options)：顯示一個對話框，用戶可以選擇要打開的檔案。
* askopenfilenames(**options)：顯示一個對話框，用戶可以選擇要打開的多個檔案。
* asksaveasfilename(**options)：顯示一個對話框，用戶可以指定要儲存的檔案的名稱和位置。
* askdirectory(**options)：顯示一個對話框，用戶可以選擇一個目錄。

使用方法範例：
```python=
import tkinter as tk
from tkinter import filedialog
from tkinter.scrolledtext import ScrolledText

def open_file():
    file_path = filedialog.askopenfilename(
        title="選擇檔案", filetypes=[("Text files", "*.txt"), ("All files", "*.*")]
    )
    if file_path:
        show_selection(file_path)

def open_files():
    file_paths = filedialog.askopenfilenames(
        title="選擇多個檔案", filetypes=[("Text files", "*.txt"), ("All files", "*.*")]
    )
    if file_paths:
        for file_path in file_paths:
            show_selection(file_path)

def save_file():
    file_path = filedialog.asksaveasfilename(
        title="儲存檔案", filetypes=[("Text files", "*.txt"), ("All files", "*.*")]
    )
    if file_path:
        show_selection(file_path)

def choose_directory():
    dir_path = filedialog.askdirectory(title="選擇目錄", initialdir="/path/to/initial/directory")
    if dir_path:
        show_selection(dir_path)

def show_selection(selection):
    result_text.insert(tk.END, selection + "\n")

# 建立主視窗
form = tk.Tk()
form.title("FileDialog 範例")
form.geometry("300x300")

# 按鈕觸發相應的 filedialog 方法
open_button = tk.Button(form, text="打開檔案", command=open_file)
open_button.grid(row=0, column=0, padx=10, pady=10)

open_files_button = tk.Button(form, text="打開多個檔案", command=open_files)
open_files_button.grid(row=0, column=1, padx=10, pady=10)

save_button = tk.Button(form, text="儲存檔案", command=save_file)
save_button.grid(row=1, column=0, padx=10, pady=10)

choose_directory_button = tk.Button(form, text="選擇目錄", command=choose_directory)
choose_directory_button.grid(row=1, column=1, padx=10, pady=10)

# 選擇結果的標籤
label = tk.Label(form, text="選擇：")
label.grid(row=2, column=0, columnspan=2, pady=5)

# ScrolledText 用於顯示選擇的結果
result_text = ScrolledText(form, height=11, width=35, wrap=tk.WORD)
result_text.grid(row=3, column=0, columnspan=2, padx=10, pady=5)

# 啟動主迴圈
form.mainloop()
```

### 6. colorchooser：顏色選擇視窗
可讓使用者選擇顏色。

![image](https://hackmd.io/_uploads/rk8qNxRUT.png)

askcolor() 方法返回一個包含選擇顏色的 tuple，第一個元素是 RGB 顏色值，第二個元素是十六進位格式的顏色值。

範例程式碼：
```python=
import tkinter as tk
from tkinter import colorchooser


def choose_color():
    # 顯示顏色選擇器對話框
    rgb_color, hex_color = colorchooser.askcolor(title="選擇顏色")

    # 更新視窗背景色
    form.config(bg=hex_color)

    # 更新 Label 中的色碼
    color_label.config(text=f"選擇的顏色: {hex_color}", bg=hex_color)


# 建立主視窗
form = tk.Tk()
form.title("colorchooser 範例")
form.geometry("250x100")  # 設置視窗大小

# Label 用於顯示選擇的顏色
color_label = tk.Label(text="選擇的顏色: ")
color_label.grid(row=0, column=0, padx=10, pady=10, columnspan=2)

# 建立一個按鈕，當按下時彈出顏色選擇器對話框
button = tk.Button(text="選擇顏色", command=choose_color)
button.grid(row=1, column=0, pady=10, columnspan=2)

# 啟動主迴圈
form.mainloop()
```

## 進階
### 多視窗與統一事件處理
以下範例示範二個視窗共用事件處理函式的程式碼。
```python=
import tkinter as tk
from tkinter import messagebox

def set_event_handler_for_frames(window):
    # 從所有元件過濾出 Frame 元件
    # winfo_children() 方法會回傳所有子元件的列表
    # isinstance() 方法用來判斷物件是否為指定的類別
    frame_children = [child for child in window.winfo_children() if isinstance(child, tk.Frame)]

    # 一次性為所有的 Frame 實例綁定相同的事件處理函式
    for frame in frame_children:
        frame.bind("<Button-1>", show_message)  # 綁定右鍵單擊事件
        frame.bind("<Button-3>", destroy_form)  # 綁定右鍵單擊事件

def show_message(event):
    # event.widget.master.title() 取得事件來自於哪個視窗
    messagebox.showinfo('Message', f'點擊事件來自於 {event.widget.master.title()}')

def destroy_form(event):
    # event.widget 取得事件來自於哪個元件
    event.widget.destroy()

# Form 1
form1 = tk.Tk()
form1.title('Form 1')
form1.geometry('300x200')

form1.rowconfigure(1, weight=1)
form1.columnconfigure(1, weight=1)

frame11 = tk.Frame(form1, bg='red', width=50, height=50)
frame21 = tk.Frame(form1, bg='green', width=50, height=50)
frame31 = tk.Frame(form1, bg='blue', width=50, height=50)

frame11.grid(row=0, column=0)
frame21.grid(row=1, column=1, sticky='s')
frame31.grid(row=0, column=2)

frame11.bind("<Button-1>", show_message)  # 綁定左鍵單擊事件

# Form 2
form2 = tk.Tk()
form2.title('Form 2')
form2.geometry('300x200')

form2.rowconfigure(1, weight=1)
form2.columnconfigure(1, weight=1)

frame21 = tk.Frame(form2, bg='red', width=50, height=50)
frame22 = tk.Frame(form2, bg='green', width=50, height=50)
frame23 = tk.Frame(form2, bg='blue', width=50, height=50)

frame21.grid(row=0, column=0)
frame22.grid(row=1, column=1, sticky='s')
frame23.grid(row=0, column=2)

set_event_handler_for_frames(form1)  # 為 Form 1 的所有 Frame 元件綁定事件處理函式
set_event_handler_for_frames(form2)  # 為 Form 2 的所有 Frame 元件綁定事件處理函式

# 讓所有視窗進入事件等待狀態
tk.mainloop()
```
**上述範例的學習重點：**
- window.winfo_children()
- isinstance()
- event.widget
    - event.widget.destroy()
- event.widget.master
    - event.widget.master.title()
- tk.mainloop()

## 參考資料
- [【Python/tkinter】ウィジェットの配置(pack)](https://imagingsolution.net/program/python/tkinter/widget_layout_pack/)