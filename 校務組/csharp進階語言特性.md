# C# 進階語言特性

## 1. 泛型 (Generics)
泛型是一種允許在定義類別、方法或介面時，使用類型參數作為佔位符的技術。這樣的設計可以提升程式碼的重用性、類型安全性，並減少重複程式碼的需求。

透過泛型，我們可以針對多種不同的資料類型編寫通用的類別或方法，而不必為每種資料類型單獨撰寫對應的程式碼。

**傳統設計**
以下是兩個只針對 string 和 int 分別設計的儲存類別：
```csharp=
// 只能儲存字串 (string)
public class StringStorage
{
    private string _data;

    public void SetData(string data)
    {
        _data = data;
    }

    public string GetData()
    {
        return _data;
    }
}

// 只能儲存整數 (int)
public class IntStorage
{
    private int _data;
    public void SetData(int data) => _data = data;
    public int GetData() => _data;
}

class Program
{
    static void Main(string[] args)
    {
        // 儲存字串
        StringStorage stringStorage = new StringStorage();
        stringStorage.SetData("Hello");
        string resultString = stringStorage.GetData();
        Console.WriteLine($"儲存的字串為: {resultString}"); // 輸出: 儲存的字串為: Hello

        // 儲存整數
        IntStorage intStorage = new IntStorage();
        intStorage.SetData(123);
        int resultInt = intStorage.GetData();
        Console.WriteLine($"儲存的整數為: {resultInt}"); // 輸出: 儲存的整數為: 123
    }
}
```

### 1.1 泛型類別
泛型類別是一種可以在單一類別中處理多種資料型別的設計。如果不使用泛型，我們通常需要為每種資料型別建立對應的類別，這樣會導致程式碼重複且難以維護。設計成泛型類別時，所有實例化的物件都會被要求指定型別。

在 C# 的泛型中，T 是一個佔位符（Type Placeholder），代表一個不特定的資料型別。在撰寫泛型類別或泛型方法時，使用 T 可以讓我們的程式碼能夠處理多種不同的資料型別，而不需要為每一個型別分別撰寫重複的程式碼。
```csharp=
public class Storage<T>    // 宣告一個泛型類別，T 是一個佔位符
{
    private T _data;       // 使用 T 作為屬性的型別
    public void SetData(T data) => _data = data;  // 使用 T 作為參數型別
    public T GetData() => _data;  // 使用 T 作為回傳型別
}

class Program
{
    static void Main(string[] args)
    {
        // 具現化 Storage 類別，並指定 T 為 string
        // 使用明確型別宣告
        Storage<string> stringStorage = new Storage<string>();
        stringStorage.SetData("Hello");  // 傳入 string 型別的資料
        string resultString = stringStorage.GetData();  // 回傳值為 string 型別
        Console.WriteLine($"儲存的字串為: {resultString}"); // 輸出: 儲存的字串為: Hello
		
        // 具現化 Storage 類別，並指定 T 為 int
        // 使用隱含型別宣告
        var intStorage = new Storage<int>();
        intStorage.SetData(123);  // 傳入 int 型別的資料
        int resultInt = intStorage.GetData();  // 回傳值為 int 型別
        Console.WriteLine($"儲存的整數為: {resultInt}"); // 輸出: 儲存的整數為: 123
    }
}
```

#### 使用自動實作屬性簡化程式碼
我們可以進一步使用自動實作屬性來簡化程式碼：
```csharp=
// 使用泛型類別並使用自動實作屬性
public class Storage<T>
{
    public T Data { get; set; }
}

class Program
{
    static void Main(string[] args)
    {
        // 具現化 Storage 類別，並指定 T 為 string
        // 使用明確型別宣告
        Storage<string> stringStorage = new Storage<string>();
        stringStorage.Data = "Hello";  // 傳入 string 型別的資料
        string resultString = stringStorage.Data;  // 回傳值為 string 型別
        Console.WriteLine($"儲存的字串為: {resultString}"); // 輸出: 儲存的字串為: Hello
		
        // 具現化 Storage 類別，並指定 T 為 int
        // 使用隱含型別宣告
        var intStorage = new Storage<int>();
        intStorage.Data = 123;  // 傳入 int 型別的資料
        int resultInt = intStorage.Data;  // 回傳值為 int 型別
        Console.WriteLine($"儲存的整數為: {resultInt}"); // 輸出: 儲存的整數為: 123
    }
}
```
優點：
1. 程式碼重用性：同一個泛型類別可以處理不同型別的資料，減少重複程式碼。
2. 型別安全：在編譯階段進行型別檢查，避免型別轉換錯誤。

:::success
- `<T>`：用於類別、方法、介面或委派的宣告時，表示這是泛型類別或泛型方法。
- `T`：用於泛型類別或方法內部，作為欄位、屬性、參數、回傳值時表示具體的型別佔位符，在執行時會被指定的具體型別取代。

若將 T 想像成一個「空盒子」，則
- `<T>` 是宣告這個盒子的存在並告訴使用者它可以裝任何東西。
- `T` 則是你在使用這個盒子時，實際放進去的內容（例如：int 或 string）。
:::

### 1.2 泛型方法 (Generic Method)
泛型方法是一種可以在方法中使用泛型參數的設計，使方法能夠處理不同類型的資料，而不必撰寫多個相同功能但不同類型的版本，不需要讓整個類別都成為泛型，僅在特定的方法中使用泛型來提高靈活性。

**範例：交換兩個變數的值**
以下是一個泛型方法 `Swap<T>`，用來交換兩個變數的值：
```csharp
using System;

class Program
{
    // 泛型方法
    static void Swap<T>(ref T a, ref T b)
    {
        T temp = a;
        a = b;
        b = temp;
    }

    static void Main()
    {
        int x = 5, y = 10;
        Console.WriteLine($"swap之前: x = {x}, y = {y}");     // swap之前: x = 5, y = 10
        Swap(ref x, ref y);
        Console.WriteLine($"swap之後: x = {x}, y = {y}");     // swap之後: x = 10, y = 5

        string s1 = "Hello", s2 = "World";
        Console.WriteLine($"swap之前: s1 = {s1}, s2 = {s2}"); // swap之前: s1 = Hello, s2 = World
        Swap(ref s1, ref s2);
        Console.WriteLine($"swap之後: s1 = {s1}, s2 = {s2}"); // swap之後: s1 = World, s2 = Hello
    }
}
```
**泛型方法的優點**
1. 靈活性：泛型方法可以處理任何型別的資料，無需撰寫多個重複的方法版本。
2. 型別安全：與泛型類別一樣，泛型方法也能在編譯階段進行型別檢查，避免不必要的型別轉換錯誤。

### 1.3 泛型的限制條件 (Generic Constraints)
在 C# 中，泛型（Generic） 提供了強大的靈活性，允許我們建立可重複使用且適應不同型別的類別或方法。然而，泛型的靈活性可能在某些情況下導致意外的型別錯誤。因此，我們可以使用 限制條件 (Constraints) 來限制泛型類型參數的特性或行為，使其更加安全和可控。

泛型限制條件是對泛型類型 T 設定的約束，用來告訴編譯器「T 必須滿足某些特定條件」。這些條件使用 where 關鍵字來定義，並放在類別或方法的泛型宣告之後。

常見的限制條件：
- where T : struct → T 必須是值型別。
- where T : class → T 必須是參考型別。
- where T : new() → T 必須有無參數的建構函式。
- where T : <介面> → T 必須實作某個介面。

**範例1：限制泛型類型必須具有無參數建構函式**
我們希望建立一個工廠類別 `Factory<T>`，該類別可以產生任何類型的實例。但我們希望泛型類型 T 必須具有無參數的建構函式，否則編譯器將報錯。
```csharp
public class Factory<T> where T : new()
{
    public T CreateInstance()
    {
        // 使用無參數建構函式來建立 T 的實例
        return new T();
    }
}

class Program
{
    static void Main(string[] args)
    {
        Factory<MyClass> factory = new Factory<MyClass>();
        MyClass instance = factory.CreateInstance();
        Console.WriteLine($"建立的實例類型為：{instance.GetType().Name}");
    }
}

public class MyClass
{
    public MyClass() // 無參數建構函式
    {
        Console.WriteLine("MyClass 無參數建構函式被呼叫！");
    }
}
// MyClass 無參數建構函式被呼叫！
// 建立的實例類型為：MyClass
```

**範例 2：限制泛型類型必須是實值型別**
在一些數學計算程式中，我們希望泛型類型 T 僅限於實值型別（如 int、float、double），避免非數值型別被使用。此時可以使用 where T : struct。
```csharp
public class Calculator<T> where T : struct
{
    public T Add(T a, T b)
    {
        // 在 C# 中，泛型類別中的 T 不直接支援運算符（如 +）。
        // 因無法對 T 型別的變數直接進行加法運算，故轉型為動態型別 dynamic
        dynamic x = a;     // a 轉型為動態型別
        dynamic y = b;     // b 轉型為動態型別
        return (T)(x + y); // 相加後將結果轉回 T 型別
    }
}

class Program
{
    static void Main(string[] args)
    {
        Calculator<int> intCalculator = new Calculator<int>();
        Console.WriteLine($"加法結果：{intCalculator.Add(3, 5)}"); // 輸出: 加法結果：8

        Calculator<double> doubleCalculator = new Calculator<double>();
        Console.WriteLine($"加法結果：{doubleCalculator.Add(2.5, 3.5)}"); // 輸出: 加法結果：6
    }
}
```

---
    
## 2. 集合型別 (Collection Types)
C# 提供了豐富的集合型別，用於儲存和操作資料。這些集合型別位於 `System.Collections.Generic` 命名空間。

### 2.1 `List<T>`
`List<T>` 是 C# 中最常用的集合型別之一，它是一個動態陣列，可以根據需要自動調整大小。
```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        // 建立一個整數列表
        List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };

        // 添加元素
        numbers.Add(6);

        // 遍歷列表
        foreach (int number in numbers)
        {
            Console.WriteLine(number);  // 1 2 3 4 5 6
        }

        // 檢查元素是否存在
        bool contains3 = numbers.Contains(3);
        Console.WriteLine($"List contains 3: {contains3}"); // List contains 3: True

        // 移除元素
        numbers.Remove(3);

        // 獲取列表大小
        Console.WriteLine($"List size: {numbers.Count}");   // List size: 5
    }
}
```
:::success
物件定義的取捨情境：
```csharp
List<int> numbers1 = new List<int> { 1, 2, 3, 4, 5 };
int[] numbers2 = new int[] { 1, 2, 3, 4, 5 };
```
- `List<int>` 適合需要頻繁操作集合（例如新增、刪除、排序、查找）的場景。
- int[] 適合大小固定且不需要經常變更的集合，尤其是對效能有高要求時。
:::
    
#### :star: List 的初始化方法
##### 1. 使用基本類型初始化
這種方式適合在 List 中存放單一類型的資料，並且可以直接使用大括號 {} 進行初始化。
```csharp=
var numbers = new List<int>();  // 建立一個儲存 int 類型數字的空 List
numbers.Add(1);
numbers.Add(2);
numbers.Add(3);

var numbers = new List<int> { 1, 2, 3 }; //直接初始化
```

##### 2. 使用 Tuple 初始化
Tuple 是一種特殊的類型，可以用來在 List 中存放多個不同類型的資料。這種方式適合需要在 List 中存放多種類型資料的情況，但使用 Tuple 可能會讓代碼可讀性較差。
```csharp=
var tupleList = new List<Tuple<string, int>>
{
    new Tuple<string, int>("Alice", 20),
    new Tuple<string, int>("Bob", 22)
};
```

##### 3. 使用值元組 (ValueTuple) 初始化
值元組 (ValueTuple) 是一種更簡潔的語法，可以替代傳統的 Tuple，並且允許為每個元組的元素命名。
```csharp=
var valueTupleList = new List<(string Name, int Age)>
{
    ("Alice", 20),
    ("Bob", 22)
};
```

##### 4. 使用 record 初始化
record 是 C# 9.0 引入的一種特殊類型，用於表示不可變的資料結構，非常適合用來表示實體資料。
```csharp=
public record Student(string Name, int Age);

var students = new List<Student>
{
    new Student("Alice", 20),
    new Student("Bob", 22)
};
```

##### 5. 使用匿名類型初始化
如果不想專門定義類或 record，可以使用匿名類型來初始化 List。匿名類型在一個代碼塊中臨時使用，並不需要為其定義具體的類或結構。這樣的寫法適合一次性使用，不會在其他地方重用。
```csharp=
var anonymousList = new List
{
    new { Name = "Alice", Age = 20 },
    new { Name = "Bob", Age = 22 }
};
```

### 2.2 Dictionary<TKey, TValue>

`Dictionary<TKey, TValue>` 是一個鍵值對集合，用於儲存和檢索具有唯一鍵的值，適合快速查找值的情境。

```csharp=
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        // 建立一個字典，鍵為字串，值為整數
        Dictionary<string, int> ages = new Dictionary<string, int>
        {
            { "Alice", 30 },
            { "Bob", 25 },
            { "Charlie", 35 }
        };

        // 添加新的鍵值對
        ages["David"] = 28;

        // 遍歷字典
        foreach (var kvp in ages)
        {
            Console.WriteLine($"{kvp.Key} 今年 {kvp.Value} 歲");
        }

        // 檢查鍵是否存在
        if (ages.ContainsKey("Alice"))
        {
            Console.WriteLine($"Alice 的歲數: {ages["Alice"]}");
        }

        // 嘗試獲取值
        if (ages.TryGetValue("Eve", out int eveAge))
        {
            Console.WriteLine($"Eve's age: {eveAge}");
        }
        else
        {
            Console.WriteLine("Eve 在字典中找不到");
        }

        // 移除鍵值對
        ages.Remove("Bob");

        // 獲取字典大小
        Console.WriteLine($"字典大小: {ages.Count}");
    }
}
```

## 3. LINQ（Language Integrated Query）
LINQ 是 C# 中一個強大的查詢語言，它讓開發者可以使用統一的語法來查詢和操作不同的資料來源，例如集合、陣列、物件、資料庫、XML 等等。提高了程式碼的可讀性和開發效率。

**LINQ 支援的資料來源包括：**
- 集合（如 List、Array）：LINQ to Objects
- 資料庫：LINQ to SQL、Entity Framework
- XML 文件：LINQ to XML

LINQ 主要依賴兩個核心介面來實現查詢操作：
- IEnumerable<T>：適用於操作記憶體中的資料集合（如陣列、清單）。
- IQueryable<T>：適用於延遲執行查詢，常見於查詢資料庫。

LINQ 提供了兩種查詢語法：
1. 查詢運算式語法（Query Expression Syntax）：類似於 SQL 查詢的語法。
2. 方法語法（Method Syntax）：使用擴充方法（如 Where、Select）來操作資料，並支援方法鏈（Method Chaining）模式。

:::warning
[RoslynPad](https://github.com/roslynpad/roslynpad) 可取代 LINQPad 的使用
:::
    
1. Query Expression（查詢表達式）
查詢表達式的寫法與 SQL 語法相似，適合用來進行資料查詢操作。它被設計得更接近自然語言，對於那些習慣 SQL 語法的人會覺得更加直觀。
這種寫法用 from、where、orderby 和 select 等關鍵字來組織邏輯，結構類似於 SQL。
```csharp=
string[] words = { "apple", "banana", "cherry", "date", "fig", "grape" };

// 使用 LINQ 查詢的 Query Expression 來篩選和排序字串
var query = from word in words
            where word.Length > 4
            orderby word;

foreach (var word in query)
{
    Console.WriteLine(word);
}
```

2. Method Syntax（方法語法）
方法語法使用擴展方法來操作集合，是基於方法鏈（Method Chaining）的寫法，也常稱作「流暢語法」。大多數 LINQ 方法，例如 Where、Select、OrderBy，都可以使用方法語法來實現。
```csharp=
string[] words = { "apple", "banana", "cherry", "date", "fig", "grape" };

// 使用 LINQ 方法語法來篩選和排序字串
var query = words
    .Where(word => word.Length > 4)  // 篩選字串長度大於 4 的項目
    .OrderBy(word => word)           // 按字母順序排序
    .Select(word => word);           // 選擇篩選後的字串

foreach (var word in query)
{
    Console.WriteLine(word);
}
```

:::info
:star: 查詢表達式與方法語法的比較
1. 語法風格：查詢表達式更接近 SQL，而方法語法更像是一般的 C# 程式碼，特別是使用 Lambda 表達式。
2. 靈活性：方法語法提供了更多的操作靈活性，例如使用 Aggregate、SelectMany 等進階方法，而查詢表達式則在某些情況下較為簡單易讀，適合做常見的查詢操作。
3. 投影與計算：在兩種語法中，都可以進行資料的投影、排序和篩選，但方法語法在處理更複雜的邏輯時往往更適合。
:::
以下 LINQ 的使用以 Method Syntax（方法語法）為主。

### 投影與轉換類
#### Select 方法
Select 用於對原始集合的每一個元素進行轉換，並返回一個新的集合。這個過程稱為「投影」(Projection)，也就是將一組資料映射到另一組結果集合中。投影的概念來自數學中的映射，它不改變原集合的結構或順序，而是通過 Lambda 表達式將每個元素轉換成新的值。
```csharp=
var numbers = new int[] { 1, 2, 3, 4, 5 };
var squares = numbers.Select(n => n * 2); // n => n * 2 是一個 Lambda 表達式

foreach (var square in squares)
{
    Console.WriteLine(square); // 輸出：2, 4, 6, 8, 10
}

var squaresList = squares.ToList();                // 強制將投影結果轉換為 List
Console.WriteLine(string.Join(", ", squaresList)); // 輸出：2, 4, 6, 8, 10
```
    
### SelectMany 方法
SelectMany 用於將每個元素的子集合展開成一個單一的集合。這個方法通常應用於集合的集合（如陣列的陣列或列表的列表），它會將每個子集合的元素依次展開，並將它們合併成一個平坦化的集合。

展開（Flattening）的概念是將嵌套的結構（如多維陣列或子集合）打平，將其內容提取出來並合併成一個單一層級的集合。這個過程不會改變原始集合的元素，但會將它們重新組織為一個不再嵌套的集合。
```csharp=
var arrays = new int[][]
{
    new int[] { 1, 2, 3 },
    new int[] { 4, 5, 6 },
    new int[] { 7, 8, 9 }
};

var allNumbers = arrays.SelectMany(arr => arr); // 將多維陣列展開為單一集合

foreach (var number in allNumbers) 
{
    Console.WriteLine(number); // 輸出：1, 2, 3, 4, 5, 6, 7, 8, 9
}
```
```csharp=
// 定義一個 List<List<int>>
var lists = new List<List<int>>
{
    new List<int> { 1, 2, 3 },
    new List<int> { 4, 5, 6 },
    new List<int> { 7, 8, 9 }
};

// 使用 SelectMany 將多個 List<int> 展開為單一集合
var allNumbers = lists.SelectMany(list => list);

foreach (var number in allNumbers) 
{
    Console.WriteLine(number); // 輸出：1, 2, 3, 4, 5, 6, 7, 8, 9
}
```

:::info
:star: 延遲執行（Deferred Execution）
LINQ 查詢是延遲執行的，這意味著查詢不會立即執行，只有在你真正使用查詢結果（例如使用 foreach 迭代）時才會執行。這個特性可以提高效率，特別是在處理大型資料集時。
:::

### 篩選類
#### Where 方法
Where 用於篩選集合中的元素，僅返回符合條件的那些元素。它的作用是根據提供的條件（通常是 Lambda 表達式）過濾掉不符合條件的元素。

篩選的概念是根據指定的條件表達式，檢查集合中的每一個元素，並返回符合條件的元素。這個過程並不會改變原始集合，而是產生一個新的符合條件的結果集合。這個方法廣泛用於過濾資料，例如找到偶數、符合特定屬性值的物件等。   
```csharp=
var numbers = new int[] { 1, 2, 3, 4, 5 };
var evenNumbers = numbers.Where(n => n % 2 == 0);

foreach (var number in evenNumbers) 
{
    Console.WriteLine(number); // 遍歷並輸出：2, 4
}
    
var evenNumbersList = evenNumbers.ToList();            // 強制將篩選結果轉換為 List
Console.WriteLine(string.Join(", ", evenNumbersList)); // 輸出：2, 4
```

### 排序類
#### OrderBy 方法
OrderBy 用於對集合中的元素進行升冪排序，依據指定的鍵進行比較。它會返回一個按照從小到大的順序排列的集合。這個過程不會改變原集合，而是產生一個新的排序結果集合。
```csharp=
var numbers = new int[] { 5, 1, 4, 2, 3 };
var orderedNumbers = numbers.OrderBy(n => n); // 升冪排序

foreach (var number in orderedNumbers) 
{
    Console.WriteLine(number); // 輸出排序後的結果：1, 2, 3, 4, 5
}

var orderedNumbersList = orderedNumbers.ToList();         // 強制將排序結果轉換為 List
Console.WriteLine(string.Join(", ", orderedNumbersList)); // 輸出：1, 2, 3, 4, 5
```
當然我們也可以自訂排序
```csharp=
var numbers = new int[] { 5, 1, 4, 2, 3 };
var orderedByEvenOdd = numbers.OrderBy(n => n % 2).ThenBy(n => n); // 先按奇偶性，再按數字大小排序

foreach (var number in orderedByEvenOdd) 
{
    Console.WriteLine(number); // 輸出：2, 4, 1, 3, 5（偶數在前，奇數在後，且各自排序）
}
```
#### OrderByDescending 方法
OrderByDescending 用於對集合進行降冪排序。
```csharp=
var numbers = new int[] { 5, 1, 4, 2, 3 };
var orderedNumbers = numbers.OrderByDescending(n => n); // 降冪排序

foreach (var number in orderedNumbers) 
{
    Console.WriteLine(number); // 輸出排序後的結果：5, 4, 3, 2, 1
}
```

### 分組類
#### GroupBy 方法
GroupBy 方法用來將集合中的元素根據某一鍵進行分組。這個方法會返回一個分組結果，每一組都包含具有相同鍵值的元素。

**GroupBy 的回傳結構**
GroupBy 方法返回的是一個實現了 `IEnumerable<IGrouping<TKey, TElement>>` 的集合。每一個 `IGrouping<TKey, TElement>` 實際上就是一個包含了相同鍵值的元素集合，這個結構包含以下兩個關鍵點：
1. Key：每個 IGrouping 物件都有一個 Key 屬性，這個屬性代表了分組時的鍵值。就像範例中的 group.Key，它代表了組中的字串長度。
2. Elements (元素)：IGrouping 是一個包含分組後元素的集合。可以使用迴圈遍歷這個集合來訪問每個分組內的元素。
```csharp=
var words = new string[] { "apple", "banana", "cherry", "date", "fig", "grape" };
var groupedWords = words.GroupBy(w => w.Length); // 根據字串長度進行分組

foreach (var group in groupedWords) 
{
    Console.WriteLine($"Words with length {group.Key}:"); // 輸出每一組的鍵（字串長度）
    foreach (var word in group) 
    {
        Console.WriteLine(word); // 輸出該組中的每個字
    }
}
```
輸出結果會顯示各個字串根據長度分組後的內容：
```
Words with length 5:
apple
grape
Words with length 6:
banana
cherry
Words with length 4:
date
Words with length 3:
fig
```

我們還可以在 GroupBy 之後進行投影，例如計算每個分組中的元素數量或進行其他處理：
```csharp=
var words = new string[] { "apple", "banana", "cherry", "date", "fig", "grape" };
var groupSummary = words
    .GroupBy(w => w.Length)
    .Select(g => new { Length = g.Key, Count = g.Count() });

foreach (var group in groupSummary)
{
    Console.WriteLine($"There are {group.Count} words with length {group.Length}");
}
```
輸出結果：
```
There are 2 words with length 5
There are 2 words with length 6
There are 1 words with length 4
There are 1 words with length 3
```
這樣的投影可以將分組後的結果轉換為所需的格式或統計資訊，讓 GroupBy 變得更加靈活。

### 聚合類
#### Aggregate 方法
Aggregate 用於對集合中的元素進行累積計算，將元素依次應用指定的運算，最終產生一個單一的結果。這個方法通常用於執行像累加、乘積、串聯等操作，並返回一個最終的結果。
```csharp=
var numbers = new int[] { 1, 2, 3, 4, 5 };
var sum = numbers.Aggregate((acc, n) => acc + n); // 將所有數字累加

Console.WriteLine(sum); // 輸出：15
```

Aggregate 也可以指定初始值作為累積的起點，例如：
```csharp=
var numbers = new int[] { 1, 2, 3, 4, 5 };
var sumWithInitialValue = numbers.Aggregate(10, (acc, n) => acc + n); // 初始值為 10

Console.WriteLine(sumWithInitialValue); // 輸出：25（10 + 1 + 2 + 3 + 4 + 5）
```

#### Sum 方法
計算集合中數值的總和。
```csharp=
var numbers = new int[] { 1, 2, 3, 4 };
var sum = numbers.Sum();
Console.WriteLine(sum); // 結果：10
```
    
#### Count 方法
計算集合中的元素數量。
```csharp=
var numbers = new int[] { 1, 2, 3, 4 };
var count = numbers.Count();
Console.WriteLine(count); // 結果：4
```
    
#### Average 方法
計算集合中數值的平均值。
```csharp=
var numbers = new int[] { 1, 2, 3, 4 };
var average = numbers.Average();
Console.WriteLine(average); // 結果：2.5
```
    
#### Max 方法
找到集合中的最大值。
```csharp=
var numbers = new int[] { 1, 2, 3, 4 };
var max = numbers.Max();
Console.WriteLine(max); // 結果：4
```
    
#### Min 方法
找到集合中的最小值。
```csharp=
var numbers = new int[] { 1, 2, 3, 4 };
var min = numbers.Min();
Console.WriteLine(min); // 結果：1
```

### 分區與部分處理類
#### Take 方法
取出集合中的前 N 個元素。
```csharp=
var numbers = new int[] { 1, 2, 3, 4, 5 };
var firstTwo = numbers.Take(2);

foreach (var nm in firstTwo) 
{
    Console.WriteLine(nm); // 結果：1, 2
}
```

#### Skip 方法
跳過集合中的前 N 個元素，返回剩下的元素。
```csharp=
var numbers = new int[] { 1, 2, 3, 4, 5 };
var remaining = numbers.Skip(2);

foreach (var nm in remaining) 
{
    Console.WriteLine(nm); // 結果：3, 4, 5
}
```

#### TakeWhile 方法
取出符合條件的元素，直到條件不再成立。
```csharp=
var numbers = new int[] { 1, 2, 3, 4, 5 };
var result = numbers.TakeWhile(n => n < 4);

foreach (var nm in result) 
{
    Console.WriteLine(nm); // 結果：1, 2, 3
}
```

#### SkipWhile 方法
跳過符合條件的元素，直到條件不再成立。
```csharp=
var numbers = new int[] { 1, 2, 3, 4, 5 };
var result = numbers.SkipWhile(n => n < 4);

foreach (var nm in result) 
{
    Console.WriteLine(nm); // 結果：4 5
}
```

### 查詢與判斷類
對集合進行查詢或檢查，返回單一結果（如單一元素或布林值），而不返回新的集合。                               
#### First 方法
取得集合中符合條件的第一個元素。如果沒有符合條件的元素，則會拋出例外。
```csharp=
var numbers = new int[] { 1, 2, 3, 4 };
var firstEven = numbers.First(n => n % 2 == 0);
Console.WriteLine(firstEven); // 輸出：2
```
                                           
#### FirstOrDefault 方法
取得集合中符合條件的第一個元素。如果沒有符合條件的元素，則返回預設值（數值型別為 0，引用型別為 null）。
```csharp=
var numbers = new int[] { 1, 3, 5 };
var firstEven = numbers.FirstOrDefault(n => n % 2 == 0);
Console.WriteLine(firstEven); // 輸出：0 （因為集合中沒有符合條件的元素）
```
                                           
#### Any 方法
判斷集合中是否有任何一個元素符合指定條件。返回 true 或 false。
```csharp=
var numbers = new int[] { 1, 3, 5 };
var hasEven = numbers.Any(n => n % 2 == 0);
Console.WriteLine(hasEven); // 輸出：False （因為集合中沒有偶數）
```
                                           
#### All 方法
判斷集合中的所有元素是否都符合指定條件。返回 true 或 false。
```csharp=
var numbers = new int[] { 2, 4, 6 };
var allEven = numbers.All(n => n % 2 == 0);
Console.WriteLine(allEven); // 輸出：true （因為集合都是偶數）
```
    
:::success
Entity Framework Core 另外為 LINQ 擴展了不少方法，請參考 [Entity Framework Core 常用方法](https://hackmd.io/XU5PNgOcQQuQvOqSCgYaQA?both#%E5%B8%B8%E7%94%A8%E6%96%B9%E6%B3%95)
:::
    
### LINQ 與 Python 列表推導式和 JavaScript 陣列方法的比較

LINQ、Python 的列表推導式和 JavaScript 的陣列方法都提供了操作集合的強大工具。以下展示了如何在三種語言中過濾偶數並計算它們的平方。雖然語法不同，但概念是相似的。

**C# LINQ**
```csharp=
var evenSquares = numbers.Where(n => n % 2 == 0).Select(n => n * n);
```

**Python 列表推導式**
```python=
even_squares = [n**2 for n in numbers if n % 2 == 0]
```

**JavaScript 陣列方法**
```javascript=
const evenSquares = numbers.filter(n => n % 2 === 0).map(n => n * n);
```

## 4. 非同步程式設計 (async/await)
非同步程式設計是現代應用程式開發中的重要概念，特別是在處理耗時操作（如檔案 I/O、網路請求等）時。想像一下，如果你正在開發一個應用程式，需要從網路下載大量資料。如果使用同步方式，程式會在下載完成前一直等待，導致使用者介面凍結，影響用戶體驗。非同步程式設計可以解決這個問題，讓應用程式在等待耗時操作完成時仍能響應其他操作。
    
C# 提供了 `async` 和 `await` 關鍵字，使得撰寫和管理非同步程式碼變得更加簡單和直覺。
 
### 4.1 async、await 關鍵字
async 關鍵字用於宣告一個方法為非同步方法，表示這個方法內可以使用 await 來等待其他非同步操作。這些方法通常會返回 Task 或 Task<T>
    
await 關鍵字用於等待一個非同步操作的完成，但並不會阻塞整個執行緒。當使用 await 等待一個 Task 或 Task<T> 時，該方法會暫時「掛起」執行，並將控制權交還給呼叫方，讓其他操作可以繼續進行。在等待的非同步操作完成後，該方法會自動恢復執行剩下的程式碼。需要注意的是，await 關鍵字只能在被標記為 async 的方法中使用，這些方法通常會返回 Task 或 Task<T> 以表達其非同步的性質。
    
以下是一個簡單的例子，模擬一個非同步操作（如等待 2 秒）：
```csharp
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main(string[] args) // 宣告此方法為非同步方法
    {
        Console.WriteLine("開始執行程式");
        Console.WriteLine("開始非同步操作");
    
        await Task.Delay(2000);           // 等待一個非同步操作完成(模擬耗時 2 秒)
    
        Console.WriteLine("非同步操作完成");        
        Console.WriteLine("程式執行完畢");
    }
}
```
在這個範例中，Main 方法被標記為 async，並且返回 Task，這表示它會進行非同步運算。await Task.Delay(2000) 是一個非同步操作，模擬等待 2 秒。await 告訴程式在這個非同步操作完成之前暫停執行，並在完成後繼續執行程式碼。

#### 將主程式與非同步程式分離
通常，我們會將非同步操作封裝成單獨的方法，以提高程式的可讀性和可維護性。以下是如何將非同步操作封裝在單獨的方法中的範例：
```csharp=
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main(string[] args) // 宣告此方法為非同步方法
    {
        Console.WriteLine("開始執行程式");
        await DoSomethingAsync();         // 等待一個非同步操作完成
        Console.WriteLine("程式執行完畢");
    }

    static async Task DoSomethingAsync()  // 宣告此方法為非同步方法
    {
        Console.WriteLine("開始非同步操作");
        await Task.Delay(2000);           // 等待一個非同步操作完成(模擬耗時 2 秒)
        Console.WriteLine("非同步操作完成");
    }
}
```
運行流程：
1. Main 方法開始執行：
    - Main 方法會印出「開始執行程式」。
    - 然後，它會 await DoSomethingAsync 方法，這會導致 Main 方法暫時「掛起」並將控制權交還給呼叫者（這裡是主執行緒）。
2. DoSomethingAsync 方法開始執行：
    - DoSomethingAsync 方法會印出「開始非同步操作」。
    - 它會 await Task.Delay(2000)，這會使 DoSomethingAsync 方法暫時「掛起」，並讓其他操作繼續進行。
3. 等待操作完成：
    - 主執行緒在 await Task.Delay(2000) 時可以繼續進行其他操作，而不會被阻塞。
    - 當 Task.Delay 完成後，DoSomethingAsync 方法會「恢復」執行，並印出「非同步操作完成」。
4. DoSomethingAsync 完成：
    - DoSomethingAsync 方法完成後，控制權會返回到 Main 方法，await 表達式會完成，Main 方法會繼續執行，並印出「程式執行完畢」。
    
上述2種程式碼的執行結果如下：
```
開始執行程式
開始非同步操作
（等待 2 秒）
非同步操作完成
程式執行完畢
```

### 4.2 Task 在非同步中的角色
Task 是一個表示非同步操作後返回的物件，當方法返回 Task 時，表示該方法會進行非同步運算並最終完成。Task<T> 則用於表示非同步操作會返回一個值的情況（例如 Task<int> 表示這個非同步方法會返回一個 int 類型的值）。 

非同步方法通常返回以下三種類型之一：
1. `Task`：表示一個不返回值的非同步操作。
2. `Task<T>`：表示一個返回 T 類型值的非同步操作。
3. `void`：主要用於事件處理程序，但不建議在其他情況下使用。
    
#### 4.2.1 返回 Task 的例子

```csharp=
static async Task DoSomethingAsync()
{
    await Task.Delay(1000);
    Console.WriteLine("操作完成");
}
```

#### 4.2.2 返回 Task<T> 的例子

```csharp=
static async Task<int> CalculateAsync()
{
    await Task.Delay(1000);
    return 42;
}

// 使用方式
static async Task Main(string[] args)
{
    int result = await CalculateAsync();
    Console.WriteLine($"計算結果：{result}");
}
```

#### 4.2.3 關於 async void

雖然可以使用 `async void`，但這通常只建議用於事件處理程序。使用 `async void` 方法有幾個缺點：

1. 無法等待 `async void` 方法完成。
2. `async void` 方法中的異常無法被呼叫者捕獲，可能導致應用程式崩潰。

除非絕對必要，否則應該避免使用 `async void`，而是使用 `async Task`。

### 4.3 異常處理

在非同步方法中，可以使用標準的 try-catch 來處理異常。

```csharp=
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main(string[] args)
    {
        try
        {
            await DoSomethingRiskyAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine($"捕獲到異常：{ex.Message}");
        }
    }

    static async Task DoSomethingRiskyAsync()
    {
        await Task.Delay(1000);
        throw new Exception("發生錯誤");
    }
}
```
    
### 4.4 取消非同步操作
有時候，我們需要能夠取消正在進行的非同步操作。C# 提供了 `CancellationToken` 來支援這個功能。

```csharp=
using System;
using System.Threading;
using System.Threading.Tasks;

class Program
{
    static async Task Main(string[] args)
    {
        using (var cts = new CancellationTokenSource())
        {
            Task longRunningTask = DoLongRunningTaskAsync(cts.Token);

            // 等待 3 秒後取消任務
            await Task.Delay(3000);
            cts.Cancel();

            try
            {
                await longRunningTask;
            }
            catch (OperationCanceledException)
            {
                Console.WriteLine("任務被取消");
            }
        }
    }

    static async Task DoLongRunningTaskAsync(CancellationToken cancellationToken)
    {
        for (int i = 0; i < 100; i++)
        {
            if (cancellationToken.IsCancellationRequested)
            {
                cancellationToken.ThrowIfCancellationRequested();
            }
            await Task.Delay(100);
            Console.WriteLine($"處理中... {i}%");
        }
    }
}
```

在這個例子中，我們使用 `CancellationTokenSource` 來建立一個 `CancellationToken`，並在 3 秒後取消任務。

### 4.5 並行執行多個非同步操作
有時候，我們需要同時執行多個非同步操作。`Task.WhenAll` 方法可以幫助我們實現這一點。

```csharp=
using System;
using System.Threading.Tasks;

class Program
{
    static async Task Main(string[] args)
    {
        Task task1 = DoTask1Async();
        Task task2 = DoTask2Async();
        Task task3 = DoTask3Async();
    
        await Task.WhenAll(task1, task2, task3);
        Console.WriteLine("所有任務完成");
    }
    
    static async Task DoTask1Async()
    {
        await Task.Delay(1000);
        Console.WriteLine("任務 1 完成");
    }
    
    static async Task DoTask2Async()
    {
        await Task.Delay(2000);
        Console.WriteLine("任務 2 完成");
    }
    
    static async Task DoTask3Async()
    {
        await Task.Delay(3000);
        Console.WriteLine("任務 3 完成");
    }
}
```
這個例子展示了如何同時啟動多個任務，並等待它們全部完成。
                                
### 4.6 應用範例：非同步檔案讀取
以下是一個如何使用非同步方法讀取檔案內容的範例：
```csharp=
using System;
using System.IO;
using System.Threading.Tasks;

class Program
{
    static async Task Main(string[] args)
    {
        string filePath = "example.txt";
        
        // 首先建立一個測試文件
        await File.WriteAllTextAsync(filePath, "這是一個測試文件的內容。");
        
        Console.WriteLine("開始讀取文件");
        string content = await ReadFileAsync(filePath);
        Console.WriteLine($"文件內容：{content}");
        
        // 清理：刪除測試文件
        File.Delete(filePath);
    }

    static async Task<string> ReadFileAsync(string filePath)
    {
        using (StreamReader reader = new StreamReader(filePath))
        {
            return await reader.ReadToEndAsync();
        }
    }
}
```

在這個例子中：
1. 我們首先建立一個測試文件。
2. 然後使用非同步方法 `ReadFileAsync` 讀取文件內容。
3. 最後，我們刪除測試文件以進行清理。

## 5. Lambda 運算式」(Lambda Expression)
Lambda 運算式是用於定義匿名函式的一種簡潔語法，尤其適合用於 LINQ 查詢。Lambda 運算式的語法可以讓程式碼更簡短、可讀性更高，尤其是在一次性使用的函式中。

### 具名函式
在 C# 中，我們通常使用具名方法來完成特定任務。以下範例中定義了一個方法 IsOlderThan30，用於篩選年齡大於 30 的顧客：
```csharp=
// 定義方法來檢查顧客年齡是否大於 30
public bool IsOlderThan30(Customer customer)
{
    return customer.Age > 30;
}

// 使用 LINQ 查詢，傳入篩選方法
var result = customers.Where(IsOlderThan30);
```
    
### 匿名函式
當函式只需使用一次或僅用於特定範圍時，可以使用匿名函式。匿名函式不需要名稱，僅包含 delegate 關鍵字及函式主體。
```csharp=
// 使用匿名函式進行篩選
var result = customers.Where(delegate (Customer customer)
{
    return customer.Age > 30;
});
```
    
### Lambda 運算式(Lambda Expression)
Lambda 運算式進一步簡化了匿名函式的語法。它不僅省略了 delegate 關鍵字，還可以省略 {} 和 return，大幅減少樣板代碼，讓程式碼更加簡潔。
```csharp=
// 使用 Lambda 運算式進行篩選
var result = customers.Where(c => c.Age > 30);
```
    
### Lambda 運算式的基本結構
> (參數) => 傳回表達式或程式碼區塊
- 參數：以 () 包裹，可以有一個或多個參數。若只有一個參數，() 可省略。
- `=>`：分隔參數和運算式，也被稱為箭頭運算子（arrow operator）。
- 傳回表達式或程式碼區塊：Lambda 的主體，可為單一表達式或多行程式碼（以 {} 包裹）。
```csharp=
x => x * x        // 單一參數和單一表達式
(a, b) => a + b   // 多參數和單一表達式
```
Lambda 運算式在 C# 中經常與 LINQ（Language Integrated Query）結合使用，能夠快速進行集合資料的篩選、投影、排序等操作。
    
## 6. 委派與事件(選讀)
.NET基礎類別庫（BCL）定義了 Action、Func 和 EventHandler，大多時間不需要定義自己的委派與事件。

### 6.1 委派（Delegate）
委派 是 C# 中一種類似於「函式指標」的類型，允許你將方法當作變數來儲存並在之後呼叫。簡單來說就是允許將方法當作參數進行傳遞。

委派的主要功能是可以讓方法的執行時間被延後，或者動態決定要執行哪些方法。
```csharp=
using System;

public class Program
{
    // 定義一個委派，可以指向任何回傳 void 且接收 string 參數的方法
    public delegate void Notify(string message);

    // 定義一個方法，符合委派的簽名
    public static void ShowMessage(string message)
    {
        Console.WriteLine("訊息：" + message);
    }

    public static void Main()
    {
        // 使用委派指向方法
        Notify notifier = ShowMessage;

        // 呼叫委派，也就是執行被委派指向的方法
        notifier("Hello, World!");
    }
}
// 執行結果：
// 訊息：Hello, World!
```
### 6.2 多播委派
委派允許將多個方法指向同一個委派，並依次執行它們。稱之為「多播委派」
```csharp=
using System;

public class Program
{
    // 定義委派
    public delegate void Notify(string message);

    public static void ShowMessage(string message)
    {
        Console.WriteLine("訊息：" + message);
    }

    public static void ShowWarning(string message)
    {
        Console.WriteLine("警告：" + message);
    }

    public static void Main()
    {
        // 委派指向多個方法
        Notify notifier = ShowMessage;
        notifier += ShowWarning;

        // 呼叫委派，會依序執行所有被指向的方法
        notifier("系統更新！");
    }
}
// 執行結果：
// 訊息：系統更新！
// 警告：系統更新！
```
### 6.3 事件(Event)
事件是基於委派的一種更安全的機制，用於通知其他物件某個動作發生。事件只能在類別內部被觸發，而外部的其他類別只能訂閱（+=）或取消訂閱（-=）事件。

事件的組成
1. 委派(Delegate)：定義事件要執行的動作。
2. 事件(Event)：發出通知，告知訂閱者發生了某事。
3. 訂閱者(Subscriber)：負責接收並處理事件通知。
    
步驟解析:
1. 定義委派：宣告一個可以指向處理函式的委派類型，或使用內建的 EventHandler。
2. 宣告事件：使用 event 關鍵字來宣告事件。
3. 訂閱事件：透過 += 操作符來訂閱事件，指定處理函式。
4. 觸發事件：在某個條件下，透過 Invoke 方法來觸發事件。

```csharp=
using System;

public class Publisher
{
    // 使用委派定義事件
    public delegate void Notify(string message);
    public event Notify OnNotify=default!;

    public void TriggerEvent(string message)
    {
        // 觸發事件，通知訂閱者
        OnNotify?.Invoke(message);
    }
}

public class Program
{
    public static void ShowMessage(string message)
    {
        Console.WriteLine("收到通知：" + message);
    }

    public static void Main()
    {
        Publisher publisher = new Publisher();

        // 訂閱事件
        publisher.OnNotify += ShowMessage;

        // 觸發事件
        publisher.TriggerEvent("資料已更新！");
    }
}
// 執行結果：
// 收到通知：資料已更新！
```
    
### 6.4 使用事件進行物件間通信的實作
事件最常用於物件之間的通信，當某個物件的狀態發生變化時，通知訂閱者。這是一種鬆耦合的設計模式，可以讓物件之間的依賴性降到最低。
    
我們來看一個實際的例子，當「影片上傳」這個事件發生時，通知所有訂閱者。
```csharp=
using System;

public class VideoUploader
{
    // 使用 EventHandler<string> 委派來處理影片標題
    public event EventHandler<string> NewVideoUploaded=default!;

    public void UploadVideo(string videoTitle)
    {
        Console.WriteLine("上傳了新影片：" + videoTitle);
        // 觸發事件，通知所有訂閱者
        NewVideoUploaded?.Invoke(this, videoTitle);
    }
}

public class Program
{
    public static void Main()
    {
        VideoUploader uploader = new VideoUploader();

        // 訂閱事件，接收影片上傳的通知
        uploader.NewVideoUploaded += (sender, title) =>
        {
            Console.WriteLine("收到通知：新影片 " + title + " 已上傳！");
        };

        // 上傳影片，觸發事件
        uploader.UploadVideo("C# 學習指南");
    }
}
// 執行結果：
// 上傳了新影片：C# 學習指南
// 收到通知：新影片 C# 學習指南 已上傳！
```

## 7. Action 與 Func
### 7.1 Action
Action 是一種內建的泛型委派，用來表示一個沒有返回值的方法。它可以接收最多 16 個參數。

Action 適用於需要執行某個動作，但不需要返回結果的場合。常用於呼叫函式、事件驅動設計或需要封裝一段邏輯的情境。
    
####  7.1.1 委派沒有參數傳遞
```csharp=
using System;
using System.Threading;

public class Program
{
    // 定義具名方法
    public static void TimeElapsed()
    {
        Console.WriteLine("時間已到！");
    }

    public static void Main()
    {
        // 定義一個 Action，指向具名方法
        Action timeElapsed = TimeElapsed;

        // 設定一個定時器，5秒後執行 TimeElapsed 方法
        Timer timer = new Timer(_ => timeElapsed(), null, 5000, Timeout.Infinite);

        Console.WriteLine("計時開始...");
        Thread.Sleep(6000); // 等待計時完成
    }
}
// 執行結果：
// 計時開始...
// 時間已到！
```
改為匿名方法更簡潔
```csharp=
using System;
using System.Threading;

public class Program
{
    public static void Main()
    {
        // 使用匿名方法取代有名稱的方法
        Action timeElapsed = () =>
        {
            Console.WriteLine("時間已到！");
        };

        // 設定一個定時器，5秒後執行匿名函式
        Timer timer = new Timer(_ => timeElapsed(), null, 5000, Timeout.Infinite);

        Console.WriteLine("計時開始...");
        Thread.Sleep(6000); // 等待計時完成
    }
}
// 執行結果：
// 計時開始...
// 時間已到！
```
    
####  7.1.2 委派有參數傳遞
```csharp=
using System;

public class Program
{
    // 定義有名稱的方法
    public static void BuyBook(string BookName)
    {
        Console.WriteLine("我買的書是: {0}", BookName);
    }

    public static void Main()
    {
        // 定義一個 Action，指向有名稱的方法
        Action<string> BookAction = BuyBook;

        // 呼叫 Action，執行 Book 方法
        BookAction("C#快速上手");
    }
}

// 執行結果：
// 我買的書是: C#快速上手
```
改為匿名方法更簡潔
```csharp=
using System;

public class Program
{
    public static void Main()
    {
        // 使用匿名方法取代具名方法
        Action<string> BookAction = (string BookName) =>
        {
            Console.WriteLine("我買的書是: {0}", BookName);
        };

        // 呼叫 Action，執行匿名方法
        BookAction("C#快速上手");
    }
}
// 執行結果：
// 我買的書是: C#快速上手
```

### 7.2 Func
Func 是 C# 中另一種內建的泛型委派，和 Action 的不同之處在於 Func 可以有返回值。

Func 常用於 LINQ 查詢、資料處理或需要回傳結果的委派。

#### 7.2.1 沒有參數的委派有返回結果
```csharp=
using System;

public class Program
{
    // 定義具名方法，無參數且有返回值
    public static int GetRandomNumber()
    {
        Random random = new Random();
        return random.Next(1, 101); // 返回 1 到 100 之間的隨機數
    }

    public static void Main()
    {
        // 定義一個 Func，沒有參數並返回整數
        Func<int> getRandomNumberFunc = GetRandomNumber;

        // 呼叫 Func，取得返回結果
        int result = getRandomNumberFunc();
        Console.WriteLine("隨機數是: {0}", result);
    }
}

// 執行結果範例：
// 隨機數是: 42
```
改為匿名方法更簡潔
```csharp=
using System;

public class Program
{
    public static void Main()
    {
        // 使用匿名方法取代具名方法，無參數且返回整數
        Func<int> getRandomNumberFunc = () =>
        {
            Random random = new Random();
            return random.Next(1, 101);
        };

        // 呼叫 Func，取得返回結果
        int result = getRandomNumberFunc();
        Console.WriteLine("隨機數是: {0}", result);
    }
}

// 執行結果範例：
// 隨機數是: 42
```
#### 7.2.2 有參數傳遞且返回結果的委派
Func 委派可以接收最多 16 個參數，並且最後一個泛型參數必須是返回值的類型。
```csharp=
using System;

public class Program
{
    // 定義具名方法，接受兩個參數並返回它們的和
    public static int Add(int a, int b)
    {
        return a + b;
    }

    public static void Main()
    {
        // 定義一個 Func，接收兩個整數參數並返回整數結果
        Func<int, int, int> addFunc = Add;

        // 呼叫 Func，並傳遞參數，取得返回結果
        int result = addFunc(5, 3);
        Console.WriteLine("5 + 3 = {0}", result);
    }
}

// 執行結果：
// 5 + 3 = 8
```
改為匿名方法更簡潔
```csharp=
using System;

public class Program
{
    public static void Main()
    {
        // 使用匿名方法取代具名方法，接收兩個參數並返回它們的和
        Func<int, int, int> addFunc = (int a, int b) =>
        {
            return a + b;
        };

        // 呼叫 Func，並傳遞參數，取得返回結果
        int result = addFunc(5, 3);
        Console.WriteLine("5 + 3 = {0}", result);
    }
}

// 執行結果：
// 5 + 3 = 8
```

### 7.3 實際應用
```csharp=
using System;

public class Program
{
    public static void Main()
    {
        // 使用者選擇的運算符號
        Console.WriteLine("選擇運算 (+, -, *, /): ");
        string operation = Console.ReadLine();

        // 定義一個 Func，根據選擇的運算符號來動態選擇對應的運算邏輯 (使用匿名函數)
        Func<int, int, int> calculate = null;

        switch (operation)
        {
            case "+":
                calculate = (int a, int b) => a + b;
                break;
            case "-":
                calculate = (int a, int b) => a - b;
                break;
            case "*":
                calculate = (int a, int b) => a * b;
                break;
            case "/":
                calculate = (int a, int b) =>
                {
                    if (b == 0)
                    {
                        throw new DivideByZeroException("除數不能為 0");
                    }
                    return a / b;
                };
                break;
            default:
                Console.WriteLine("無效的運算符號！");
                return;
        }

        // 要進行計算的兩個數字
        Console.WriteLine("輸入第一個數字: ");
        int num1 = int.Parse(Console.ReadLine());

        Console.WriteLine("輸入第二個數字: ");
        int num2 = int.Parse(Console.ReadLine());

        // 使用選定的運算邏輯進行計算
        try
        {
            int result = calculate(num1, num2);
            Console.WriteLine($"結果: {num1} {operation} {num2} = {result}");
        }
        catch (DivideByZeroException ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
}
```

## 8.  EventHandler
事件 (Event) 是一個物件與其他物件進行溝通的方式，尤其在 GUI 或基於事件驅動的程序中很常見。EventHandler 是 C# 中用來表示事件處理器的一個內建委派 (Delegate)。它用來定義事件觸發時會執行的方法。它就像是一個接收通知並作出反應的管理員。
    
EventHandler 有兩種形式：
1. `EventHandler`：不帶任何參數。
2. `EventHandler<TEventArgs>`：帶有參數，TEventArgs 必須繼承自 EventArgs，用來傳遞事件的相關資料。

### 基本的 EventHandler
```csharp=
using System;

class Program
{
    // 宣告一個事件
    public event EventHandler ButtonClicked = default!;

    static void Main(string[] args)
    {
        Program program = new Program();
        
        // 訂閱事件
        program.ButtonClicked += OnButtonClicked;
        
        // 模擬按鈕被點擊
        program.SimulateButtonClick();
    }

    // 事件處理方法
    private static void OnButtonClicked(object sender, EventArgs e)
    {
        Console.WriteLine("按鈕被點擊了！");
    }

    // 模擬按鈕點擊
    public void SimulateButtonClick()
    {
        Console.WriteLine("按鈕被點擊...");
        ButtonClicked?.Invoke(this, EventArgs.Empty);
    }
}
// 按鈕被點擊...
// 按鈕被點擊了！
```
程式碼解說：
1. 我們宣告了一個名為 `ButtonClicked` 的事件。
2. 在 `Main` 方法中，我們"訂閱"了這個事件，告訴程式當事件發生時該做什麼。
3. `OnButtonClicked` 方法是我們的事件處理器，它定義了當按鈕被點擊時要執行的動作。
4. `SimulateButtonClick` 方法模擬了按鈕被點擊的情況，並觸發了事件。
    
### 8.2 使用 `EventHandler<TEventArgs>`
有時候，我們需要在事件發生時傳遞更多資訊。這時我們可以使用帶參數的 `EventHandler<T>`。
```csharp=
using System;

// 自定義事件參數
public class MessageEventArgs : EventArgs
{
    public string Message { get; }

    public MessageEventArgs(string message)
    {
        Message = message;
    }
}

class ChatRoom
{
    // 宣告帶參數的事件
    public event EventHandler<MessageEventArgs> MessageReceived = default!;

    public void SimulateMessageReceived(string message)
    {
        Console.WriteLine($"收到新訊息: {message}");
        OnMessageReceived(message);
    }

    protected virtual void OnMessageReceived(string message)
    {
        MessageReceived?.Invoke(this, new MessageEventArgs(message));
    }
}

class Program
{
    static void Main(string[] args)
    {
        ChatRoom chatRoom = new ChatRoom();
        
        // 訂閱事件
        chatRoom.MessageReceived += OnMessageReceived;
        
        // 模擬收到訊息
        chatRoom.SimulateMessageReceived("Hello, World!");
    }

    private static void OnMessageReceived(object sender, MessageEventArgs e)
    {
        Console.WriteLine($"處理收到的訊息: {e.Message}");
    }
}
// 收到新訊息: Hello, World!
// 處理收到的訊息: Hello, World!
```
程式碼解說：
1. 我們建立了一個 `MessageEventArgs` 類來攜帶額外的訊息資訊。
2. `ChatRoom` 類使用 `EventHandler<MessageEventArgs>` 來宣告事件，這允許我們傳遞自定義的事件參數。
3. 當模擬收到訊息時，我們建立一個新的 `MessageEventArgs` 實例並觸發事件。
4. 在事件處理方法中，我們可以訪問傳遞的訊息內容。

### 實際範例
#### 範例 1：下載進度更新事件
這個範例模擬一個下載器程式，在下載過程中，會定期觸發 DownloadProgressChanged 事件，讓使用者能夠即時看到下載進度。
```csharp=
using System;
using System.Threading;

class DownloadEventArgs : EventArgs
{
    public int Progress { get; }

    public DownloadEventArgs(int progress)
    {
        Progress = progress;
    }
}

class Downloader
{
    public event EventHandler<DownloadEventArgs> DownloadProgressChanged=default!;

    public void StartDownload()
    {
        for (int i = 1; i <= 100; i++)
        {
            // 模擬下載進度
            Thread.Sleep(50); // 延遲模擬下載時間
            OnDownloadProgressChanged(i); // 每次進度變化時觸發事件
        }
    }

    protected virtual void OnDownloadProgressChanged(int progress)
    {
        DownloadProgressChanged?.Invoke(this, new DownloadEventArgs(progress));
    }
}

class Program
{
    static void Main(string[] args)
    {
        Downloader downloader = new Downloader();
        downloader.DownloadProgressChanged += Downloader_DownloadProgressChanged;

        downloader.StartDownload();
    }

    private static void Downloader_DownloadProgressChanged(object sender, DownloadEventArgs e)
    {
        Console.WriteLine($"下載進度：{e.Progress}%");
    }
}
```
程式碼解說：
- DownloadEventArgs 傳遞當前的下載進度（百分比）。
- Downloader 類模擬下載過程，並定期觸發 DownloadProgressChanged 事件，將進度回報給訂閱者。
- 在主程式中，使用者可以即時接收到進度更新。

#### 範例 2：表單驗證事件
這個範例展示如何在一個簡單的表單中使用 EventHandler 來進行表單驗證，當使用者提交表單時，系統會檢查輸入是否有效，並觸發相應的事件來反饋結果。
```csharp=
using System;

class FormEventArgs : EventArgs
{
    public bool IsValid { get; }
    public string Message { get; }

    public FormEventArgs(bool isValid, string message)
    {
        IsValid = isValid;
        Message = message;
    }
}

class Form
{
    public event EventHandler<FormEventArgs> FormSubmitted=default!;

    public void SubmitForm(string username, string password)
    {
        if (string.IsNullOrWhiteSpace(username) || string.IsNullOrWhiteSpace(password))
        {
            OnFormSubmitted(false, "使用者名稱或密碼不能為空！");
        }
        else
        {
            OnFormSubmitted(true, "表單提交成功！");
        }
    }

    protected virtual void OnFormSubmitted(bool isValid, string message)
    {
        FormSubmitted?.Invoke(this, new FormEventArgs(isValid, message));
    }
}

class Program
{
    static void Main(string[] args)
    {
        Form form = new Form();
        form.FormSubmitted += Form_FormSubmitted;

        Console.WriteLine("請輸入使用者名稱：");
        string username = Console.ReadLine();

        Console.WriteLine("請輸入密碼：");
        string password = Console.ReadLine();

        form.SubmitForm(username, password);
    }

    private static void Form_FormSubmitted(object sender, FormEventArgs e)
    {
        if (e.IsValid)
        {
            Console.WriteLine(e.Message);
        }
        else
        {
            Console.WriteLine($"表單驗證失敗：{e.Message}");
        }
    }
}
```
程式碼解說：
- FormEventArgs 包含表單驗證結果以及相關訊息。
- Form 類定義了一個簡單的表單驗證邏輯，當表單提交後，會觸發 FormSubmitted 事件，根據驗證結果傳遞不同的訊息。
- 在主程式中，表單提交後，會根據是否成功驗證來顯示不同的回饋訊息。

## 參考
- [C# 筆記：使用 var 宣告隱含型別](https://www.huanlintalk.com/2009/01/implicit-type-declaration-in-c-30.html)
- [10天快速學習系列 - C#入門](https://vocus.cc/salon/michaelyang/room/10day-learn/C%23)
- [.NET 8與C# 12新特性導覽](https://www.uuu.com.tw/Public/content/article/24/20240205.htm)
- [LINQ自學筆記](https://ithelp.ithome.com.tw/users/20009278/ironman/450)