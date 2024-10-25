# C# 應用開發與框架使用

## 相依性插入(Dependency Injection, DI)
相依性插入（DI）是 ASP.NET Core 的核心概念，旨在減少物件之間的相依性，提升程式的可測試性，並避免程式緊密綁定於特定的實作。透過 DI，物件的依賴關係可以由外部管理，促進了功能拆分和靈活性。

範例：假設我們有一輛車（Car）依賴於引擎（Engine）
```csharp=
public class Engine
{
    public void Start()
    {
        Console.WriteLine("引擎啟動...");
    }
}

public class Car
{
    private Engine _engine;

    public Car()
    {
        _engine = new Engine();  // Car 得建立 Engine 實例才能使用，類別間耦合太高
    }

    public void Drive()
    {
        _engine.Start();
        Console.WriteLine("車輛行駛中...");
    }
}
```

為了降低耦合度，我們可以透過建構函數將 Engine 的實例傳入 Car 類別：
```csharp=
public class Car
{
    private Engine _engine;

    // 使用建構函數插入 Engine
    public Car(Engine engine)
    {
        _engine = engine;
    }

    public void Drive()
    {
        _engine.Start();
        Console.WriteLine("車輛行駛中...");
    }
}
```

使用時，我們先建立 Engine 實例，然後再用它來初始化 Car：
```csharp=
Engine myEngine = new Engine();
Car myCar = new Car(myEngine);
```
這裡的 Car 物件依賴 Engine，但 Car 自己不負責建立 Engine，而是由外部傳遞進來。這正是依賴注入的基本概念。這樣的做法是功能拆分的設計，但它也體現了 DI 的精神，因為把依賴關係移交給外部管理。

### 在 ASP.NET Core 中實現 DI
ASP.NET Core 提供了一個服務容器（Service Container），用來管理應用程式中的所有依賴項。服務容器可以看作是一個集中式的管理機制，負責建立和注入所需的依賴，並簡化了複雜的物件管理。

當你將應用程式拆分成獨立的類別（例如 Engine 和 Car）後，管理這些類別之間的依賴變得至關重要。在小型應用中，你可以手動傳遞依賴，但在大型應用中，這會變得複雜。這時，ASP.NET Core 的服務容器便派上用場，它自動管理依賴關係，讓你無需手動處理。

通常，我們會在 Program.cs 檔案中設置 DI 容器。當我們使用 WebApplication.CreateBuilder(args) 時，這個方法會建立一個 builder 物件，這個物件是 WebApplicationBuilder 類別的實例。雖然 builder 並不是最終的應用程式實例，但它包含了構建應用程式所需的配置，包括：
1. DI 容器：內建的依賴注入容器，用來管理所有服務的註冊與解析。
2. IServiceCollection：用來註冊應用程式中的依賴關係。
3. 應用程式的配置和環境設置：如配置檔、日誌等。

所有服務（例如資料庫上下文、應用程式服務、控制器等）都會在 builder.Services 上註冊，這是 IServiceCollection 類型的集合，負責儲存所有服務的註冊資訊：

```csharp=
// Program.cs
var builder = WebApplication.CreateBuilder(args); // 建立一個 WebApplicationBuilder 物件，專司配置和設置

// 註冊 DI 提供者
builder.Services.AddTransient<Engine>();          // 每次請求會產生新的實例

// 以下是不同的生命周期註冊方式
// builder.Services.AddScoped<MyDbContext>();     // 每個 HTTP 請求共用同一個實例
// builder.Services.AddSingleton<MySingleton>();  // 整個應用程式生命週期共用同一個實例

// 註冊完服務後，使用 builder.Build() 來建構 WebApplication 類物件
var app = builder.Build();

app.MapGet("/", (Car car) =>
{
    car.Drive();  // 這裡的 Car 物件和 Engine 物件會自動由 DI 容器提供
    return "Car is running";
});

app.Run(); // WebApplication 類別的方法，用來啟動 HTTP 請求處理的循環。
```
builder 物件作為中間處理的過程，簡化了複雜度，而最終的 app 物件則是 WebApplication類別的實例。這樣的設計使得 DI 更加靈活，並且將應用程式的組件解耦，便於管理和測試。

---

## Entity Framework Core 框架
Entity Framework 是一個 ORM(Object Relational Mapping)框架，可以大幅減少開發時期大量的撰寫資料庫語法進行資料存取，並且能透過強型別來取得與操作物件資料。

Entity Framework Core (以下簡稱EF Core) 是 2016 年微軟將 Entity Framework 6 (以下簡稱EF6) 重寫，大部分 APIs 都跟 EF6 相同，介面相似但底層幾近砍掉重練，以輕巧、可擴充及跨平台取勝。

EF Core 是 ASP.NET Core 應用程式中操作資料庫的首選框架，你可以使用 C# 物件來表示資料庫中的資料表，並藉由 LINQ 查詢來操作資料庫，而不需要直接撰寫 SQL 查詢語句。

EF Core 以 Model 為中心，定義好 Model，更換 Data Provider 即可改用不同資料庫，EF Core 內建 SQL Server、SQLite、InMemory(僅測試用，無法持久保存資料) 三種資料庫實作。

EF Core 使用的整體流程：
1. 建立資料模型：對應資料表定義物件結構。
2. 註冊資料庫服務
    1. 定義資料庫連線字串：透過 appsettings.json 中的連接字串配置資料庫。
    2. 建立資料庫上下文：建立與資料庫表的映射和互動。
    3. 註冊資料庫服務：在 Program.cs 中設定資料庫上下文 DbContext 的注入。
3. 建立資料庫：使用 EF Core 遷移來建立或更新資料庫結構。

:::info
EF Core 提供了兩種開發方式 DB First 與 Code First
- DB First：適合已有資料庫的情況，能自動產生模型。
- Code First：適合先設計模型再資料庫的情況，可以輕鬆控制資料庫結構的變更

本筆記以 Code First 為主。
:::

### 全域安裝 EF Core CLI 工具
列出所有全域安裝的 .NET CLI 工具，並顯示工具的版本
```bash=
dotnet tool list -g
```
> `dotnet tool` 指令可安裝或管理可擴充 .NET 體驗的工具。

![image](https://hackmd.io/_uploads/SkvxP-Ulkx.png)

若沒有安裝過，請輸入下列指令安裝 EF Core CLI 工具，後續才能使用 dotnet ef 指令
```bash=
dotnet tool install --global dotnet-ef
```

### 專案安裝 EF Core 套件
#### 1.GUI 方式安裝
在 ASP.NET Core 專案中安裝 EF Core 相關的 NuGet 套件。
1. 選取 [專案]>[管理 NuGet 套件]。
2. 在 [NuGet 封裝管理員] 頁面中，選擇 [nuget.org] 作為 [套件來源]。
3. 從 [瀏覽] 索引標籤中，搜尋 Microsoft.EntityFrameworkCore.SqlServer，在清單中選取 [Microsoft.EntityFrameworkCore.SqlServer]，然後點選右側的 [安裝]。然後套用變更與接受授權。
4. 從 [瀏覽] 索引標籤中，搜尋 Microsoft.EntityFrameworkCore.Tools，在清單中選取 [Microsoft.EntityFrameworkCore.Tools]，然後點選右側的 [安裝]。然後套用變更與接受授權。

![image](https://hackmd.io/_uploads/SJ4vEl22C.png)

#### 2.套件管理員主控台下指令安裝
或在 Visual Studio 的套件管理員主控台中輸入以下指令
```bash=
# 安裝 EF Core 連接 SQL Server 的資料庫驅動
Install-Package Microsoft.EntityFrameworkCore.SqlServer
# 安裝 EF Core 的命令支援，供開發過程中使用
Install-Package Microsoft.EntityFrameworkCore.Tools
```

#### 3.終端機下指令安裝
或在終端機中輸入以下指令
```bash=
# 安裝 EF Core 連接 SQL Server 的資料庫驅動
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
# 安裝 EF Core 的命令支援，供開發過程中使用
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

:::info
關注點分離：UI 邏輯屬於檢視表。 輸入邏輯位於控制器。 商務邏輯則位於模型。 
:::
![image](https://hackmd.io/_uploads/r1uI0kdpR.png)

### 建立資料模型（Model）
在 Entity Framework Core 中，實體（Entity）通常就是指模型（Model）。每個實體類別對應資料庫中的一個資料表，每個實體屬性對應到資料表中的欄位。

在 Models 資料夾中建立 Student.cs 模型檔案。
```csharp=
public class Student
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
    public string Grade { get; set; }
}
```
#### 導航屬性
導航屬性（Navigation Properties）通常用於表示多個實體之間的關聯。在實體之間有關聯的情況下，導航屬性允許您在查詢一個實體時，同時載入與之相關的其他實體。這在處理關聯時非常有用，特別是在需要急切載入（Eager Loading）關聯資料的情況下。

導航屬性（Navigation Properties）是 Entity Framework Core 提供的一個特性，導航屬性通常與 Include 方法一起使用。
```csharp=
public class Order
{
    public int Id { get; set; }
    public DateTime OrderDate { get; set; }
    public string CustomerName { get; set; }

    // 導航屬性：一個訂單可以有多個訂單明細
    public required List<OrderDetail> OrderDetails { get; set; }
}

public class OrderDetail
{
    public int Id { get; set; }
    public int OrderId { get; set; }
    public string ProductName { get; set; }
    public int Quantity { get; set; }

    // 導航屬性：訂單明細屬於一個訂單
    public Order Order { get; set; }
}
```

### 定義資料庫連線字串
在 appsettings.json 中定義資料庫連線字串。

appsettings.json 是一個集中管理應用程式設定的地方。在這裡存放連線字串有助於將配置與代碼分開，使得修改設定時不必重新編譯代碼。在不同的環境（開發、測試、正式）可以使用不同的配置檔。例如 appsettings.{Environment}.json 檔案中定義不同的設定，這樣可以輕鬆管理不同環境的配置。
```jsonld=
{
  ...
  ...,
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=StudentDb;Trusted_Connection=True;"
  }
}
```

### 建立建立資料庫上下
建立資料庫上下文 ApplicationDbContext 類別的做法是在專案根目錄新增項目：ApplicationDbContext.cs。

```csharp=
public class ApplicationDbContext : DbContext
{
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

    public DbSet<Student> Students { get; set; }
}
```
**程式碼說明：**

ApplicationDbContext 是我們定義的資料庫上下文類別，它繼承自 DbContext
，包含了建構子和所有的 `DbSet<T>` 屬性，這些屬性代表了資料庫中的表格。
    
DbContext 是 EF Core 提供的基底類別，用來處理與資料庫的互動。具體來說，它包含了與資料庫進行查詢、儲存、更新等操作的功能。任何繼承自 DbContext 的類別都會被視為資料庫的上下文環境，用來管理對資料庫的連線以及實體（表格）之間的映射。
    
Students 屬性就是 `DbSet<Student>` 類型，它代表了資料庫中的 "Student" 表格，我們可以透過這個 DbSet 來新增、刪除、修改或查詢學生的資料。

建構子接收到類型為 `DbContextOptions<ApplicationDbContext>` 的 options 物件，這個 options 物件定義在 Program.cs 中，options 是透過依賴注入 (Dependency Injection, DI) 機制來傳入的。options 物件包含了資料庫連線的設定，例如資料庫提供者（如 SQL Server）、連線字串以及其他相關的資料庫配置。

這個建構子沒有內容，而是直接繼承父類別的建構子，但將 options 物件作為父類別(DbContext)的建構子的參數傳遞過去。作用是，當 ApplicationDbContext 被實例化時，它會直接呼叫 父類別 DbContext 的建構子，並將 options 參數傳遞給父類別 DbContext，讓 DbContext 使用這個 options 物件進行初始化。

### 註冊資料庫服務
註冊資料庫服務就是在 Program.cs 中設定 DbContext 和資料庫連線字串，以便透過依賴注入 (Dependency Injection) 來使用它。

```csharp=
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));
```

builder.Services.AddDbContext 是 ASP.NET Core 中用來註冊資料庫上下文（DbContext）的擴充方法，通常用於與 Entity Framework Core (EF Core) 一起使用。這個方法將資料庫上下文加入至依賴注入 (Dependency Injection, DI) 容器，讓應用程式可以在控制器、服務或其他類別中透過建構函式注入的方式使用資料庫上下文。
    
AddDbContext 方法將 ApplicationDbContext 類別註冊到 DI 容器中，這樣你可以在需要的地方注入 ApplicationDbContext，而不需要手動管理其生命週期，EF Core 可以自動處理資料庫的連線。

### 遷移（Migrations）與資料庫建立
遷移是一種版本控制機制，可以跟踪資料庫結構的變化。當你更改資料模型時，你可以建立新的遷移來更新資料庫結構。
    
透過 EF Core 的遷移工具，可以自動建立資料庫。在套件管理器主控台(Package Manager Console)中執行以下命令
```csharp=
// 建立初始的遷移
Add-Migration InitialCreate

// Update-Database 命令會根據遷移文件自動更新資料庫結構，這樣可以確保資料庫結構與模型類別保持一致。
Update-Database
```
或在終端機下輸入 EF Core CLI 指令
```bash=
# 產生遷移檔案 InitialCreate(Migrations目錄)，包含建立資料庫表的指令
dotnet ef migrations add InitialCreate

# 根據遷移檔案中的指令建立或更新資料庫結構
dotnet ef database update
```
如果對實體模型進行了更改（例如添加或修改屬性），後續需要添加新的遷移並更新資料庫。
```bash=
dotnet ef migrations add <MigrationName>
dotnet ef database update
```
    
### 建立控制器(Controller)
控制器負責處理用戶的 HTTP 請求，並返回相應的結果。

首先在 Controllers 資料夾中按右鍵 》click 加入 》 click 控制器
![image](https://hackmd.io/_uploads/BkzVHMS6A.png)

點選【使用 Entity Framework 執行檢視的 MVC 控制器】，再按【加入】
![image](https://hackmd.io/_uploads/ryPaSGrp0.png)

選擇正確的模型類別與 DbContext 類別，再按【新增】
![image](https://hackmd.io/_uploads/BJZ5UfBp0.png)

等待 Scaffolding 自動建立對應的 StudentsControoler.cs 與對應的檢視網頁
![image](https://hackmd.io/_uploads/HJJwVfr6R.png)
![image](https://hackmd.io/_uploads/B13LQMHTR.png)
    
以下為 Scaffolding 自動產生 StudentsControoler.cs 的程式碼

程式碼展開
:::spoiler
```csharp=
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Rendering;
using Microsoft.EntityFrameworkCore;
using studentCRUD;
using studentCRUD.Models;

namespace studentCRUD.Controllers
{
    public class StudentsController : Controller
    {
        private readonly ApplicationDbContext _context;

        public StudentsController(ApplicationDbContext context)
        {
            _context = context;
        }

        // GET: Students
        public async Task<IActionResult> Index()
        {
            return View(await _context.Students.ToListAsync());
        }

        // GET: Students/Details/5
        public async Task<IActionResult> Details(int? id)
        {
            if (id == null)
            {
                return NotFound();
            }

            var student = await _context.Students
                .FirstOrDefaultAsync(m => m.Id == id);
            if (student == null)
            {
                return NotFound();
            }

            return View(student);
        }

        // GET: Students/Create
        public IActionResult Create()
        {
            return View();
        }

        // POST: Students/Create
        // To protect from overposting attacks, enable the specific properties you want to bind to.
        // For more details, see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<IActionResult> Create([Bind("Id,Name,Age,Grade")] Student student)
        {
            if (ModelState.IsValid)
            {
                _context.Add(student);
                await _context.SaveChangesAsync();
                return RedirectToAction(nameof(Index));
            }
            return View(student);
        }

        // GET: Students/Edit/5
        public async Task<IActionResult> Edit(int? id)
        {
            if (id == null)
            {
                return NotFound();
            }

            var student = await _context.Students.FindAsync(id);
            if (student == null)
            {
                return NotFound();
            }
            return View(student);
        }

        // POST: Students/Edit/5
        // To protect from overposting attacks, enable the specific properties you want to bind to.
        // For more details, see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<IActionResult> Edit(int id, [Bind("Id,Name,Age,Grade")] Student student)
        {
            if (id != student.Id)
            {
                return NotFound();
            }

            if (ModelState.IsValid)
            {
                try
                {
                    _context.Update(student);
                    await _context.SaveChangesAsync();
                }
                catch (DbUpdateConcurrencyException)
                {
                    if (!StudentExists(student.Id))
                    {
                        return NotFound();
                    }
                    else
                    {
                        throw;
                    }
                }
                return RedirectToAction(nameof(Index));
            }
            return View(student);
        }

        // GET: Students/Delete/5
        public async Task<IActionResult> Delete(int? id)
        {
            if (id == null)
            {
                return NotFound();
            }

            var student = await _context.Students
                .FirstOrDefaultAsync(m => m.Id == id);
            if (student == null)
            {
                return NotFound();
            }

            return View(student);
        }

        // POST: Students/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public async Task<IActionResult> DeleteConfirmed(int id)
        {
            var student = await _context.Students.FindAsync(id);
            if (student != null)
            {
                _context.Students.Remove(student);
            }

            await _context.SaveChangesAsync();
            return RedirectToAction(nameof(Index));
        }

        private bool StudentExists(int id)
        {
            return _context.Students.Any(e => e.Id == id);
        }
    }
}
```
StudentsController 控制器負責處理有關學生的操作，如顯示、建立、編輯和刪除學生資料。StudentsController 控制器與 ApplicationDbContext 互動來進行 CRUD 操作。這樣我們就可以在應用程式中執行資料操作而不需要直接編寫 SQL 查詢。
:::

### 對應的 View
View 負責顯示給使用者的資料。它們將模型資料渲染成 HTML，讓使用者可以查看和操作資料。View 也包含表單和使用者界面元素，讓使用者可以提交資料或執行操作（例如建立、編輯、刪除資料）。

以下為 Scaffolding 自動為每個 Action 建立對應的 View (Index.cshtml, Create.cshtml, Edit.cshtml, Details.cshtml, Delete.cshtml)。

程式碼展開
:::spoiler
#### Views/Students/Index.cshtml
```html=
@model IEnumerable<studentCRUD.Models.Student>

@{
    ViewData["Title"] = "Index";
}

<h1>Index</h1>

<p>
    <a asp-action="Create">Create New</a>
</p>
<table class="table">
    <thead>
        <tr>
            <th>
                @Html.DisplayNameFor(model => model.Name)
            </th>
            <th>
                @Html.DisplayNameFor(model => model.Age)
            </th>
            <th>
                @Html.DisplayNameFor(model => model.Grade)
            </th>
            <th></th>
        </tr>
    </thead>
    <tbody>
@foreach (var item in Model) {
        <tr>
            <td>
                @Html.DisplayFor(modelItem => item.Name)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Age)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Grade)
            </td>
            <td>
                <a asp-action="Edit" asp-route-id="@item.Id">Edit</a> |
                <a asp-action="Details" asp-route-id="@item.Id">Details</a> |
                <a asp-action="Delete" asp-route-id="@item.Id">Delete</a>
            </td>
        </tr>
}
    </tbody>
</table>
```

#### Views/Students/Create.cshtml
```html=
@model studentCRUD.Models.Student

@{
    ViewData["Title"] = "Create";
}

<h1>Create</h1>

<h4>Student</h4>
<hr />
<div class="row">
    <div class="col-md-4">
        <form asp-action="Create">
            <div asp-validation-summary="ModelOnly" class="text-danger"></div>
            <div class="form-group">
                <label asp-for="Name" class="control-label"></label>
                <input asp-for="Name" class="form-control" />
                <span asp-validation-for="Name" class="text-danger"></span>
            </div>
            <div class="form-group">
                <label asp-for="Age" class="control-label"></label>
                <input asp-for="Age" class="form-control" />
                <span asp-validation-for="Age" class="text-danger"></span>
            </div>
            <div class="form-group">
                <label asp-for="Grade" class="control-label"></label>
                <input asp-for="Grade" class="form-control" />
                <span asp-validation-for="Grade" class="text-danger"></span>
            </div>
            <div class="form-group">
                <input type="submit" value="Create" class="btn btn-primary" />
            </div>
        </form>
    </div>
</div>

<div>
    <a asp-action="Index">Back to List</a>
</div>

@section Scripts {
    @{await Html.RenderPartialAsync("_ValidationScriptsPartial");}
}
```

#### Views/Students/Edit.cshtml
```html=
@model studentCRUD.Models.Student

@{
    ViewData["Title"] = "Edit";
}

<h1>Edit</h1>

<h4>Student</h4>
<hr />
<div class="row">
    <div class="col-md-4">
        <form asp-action="Edit">
            <div asp-validation-summary="ModelOnly" class="text-danger"></div>
            <input type="hidden" asp-for="Id" />
            <div class="form-group">
                <label asp-for="Name" class="control-label"></label>
                <input asp-for="Name" class="form-control" />
                <span asp-validation-for="Name" class="text-danger"></span>
            </div>
            <div class="form-group">
                <label asp-for="Age" class="control-label"></label>
                <input asp-for="Age" class="form-control" />
                <span asp-validation-for="Age" class="text-danger"></span>
            </div>
            <div class="form-group">
                <label asp-for="Grade" class="control-label"></label>
                <input asp-for="Grade" class="form-control" />
                <span asp-validation-for="Grade" class="text-danger"></span>
            </div>
            <div class="form-group">
                <input type="submit" value="Save" class="btn btn-primary" />
            </div>
        </form>
    </div>
</div>

<div>
    <a asp-action="Index">Back to List</a>
</div>

@section Scripts {
    @{await Html.RenderPartialAsync("_ValidationScriptsPartial");}
}
```

#### Views/Students/Details.cshtml
```html=
@model studentCRUD.Models.Student

@{
    ViewData["Title"] = "Details";
}

<h1>Details</h1>

<div>
    <h4>Student</h4>
    <hr />
    <dl class="row">
        <dt class = "col-sm-2">
            @Html.DisplayNameFor(model => model.Name)
        </dt>
        <dd class = "col-sm-10">
            @Html.DisplayFor(model => model.Name)
        </dd>
        <dt class = "col-sm-2">
            @Html.DisplayNameFor(model => model.Age)
        </dt>
        <dd class = "col-sm-10">
            @Html.DisplayFor(model => model.Age)
        </dd>
        <dt class = "col-sm-2">
            @Html.DisplayNameFor(model => model.Grade)
        </dt>
        <dd class = "col-sm-10">
            @Html.DisplayFor(model => model.Grade)
        </dd>
    </dl>
</div>
<div>
    <a asp-action="Edit" asp-route-id="@Model?.Id">Edit</a> |
    <a asp-action="Index">Back to List</a>
</div>
```

#### Views/Students/Delete.cshtml
```html=
@model studentCRUD.Models.Student

@{
    ViewData["Title"] = "Delete";
}

<h1>Delete</h1>

<h3>Are you sure you want to delete this?</h3>
<div>
    <h4>Student</h4>
    <hr />
    <dl class="row">
        <dt class = "col-sm-2">
            @Html.DisplayNameFor(model => model.Name)
        </dt>
        <dd class = "col-sm-10">
            @Html.DisplayFor(model => model.Name)
        </dd>
        <dt class = "col-sm-2">
            @Html.DisplayNameFor(model => model.Age)
        </dt>
        <dd class = "col-sm-10">
            @Html.DisplayFor(model => model.Age)
        </dd>
        <dt class = "col-sm-2">
            @Html.DisplayNameFor(model => model.Grade)
        </dt>
        <dd class = "col-sm-10">
            @Html.DisplayFor(model => model.Grade)
        </dd>
    </dl>
    
    <form asp-action="Delete">
        <input type="hidden" asp-for="Id" />
        <input type="submit" value="Delete" class="btn btn-danger" /> |
        <a asp-action="Index">Back to List</a>
    </form>
</div>
```
:::

:::info
Scaffolding 機制主要用於 ASP.NET Core 專案，能夠自動產生常見的 MVC（Model-View-Controller）模式程式碼結構。
:::
    
### 實體之間的關係
在 ASP.NET Core 和 EF Core 中，當涉及多個實體（例如 Product 和 Category 等）時，我們常會使用關聯（關係）的概念來表示這些實體之間的聯繫。這些關聯分為一對多 (One-to-Many)、多對多 (Many-to-Many) 以及一對一 (One-to-One) 的關係。

#### 一對多關係
假設我們有一個 Category 實體，表示產品的分類，每個產品 (Product) 屬於一個分類，但一個分類 (Category) 可以有多個產品。這樣的情況就構成了一對多的關係。

1. 建立實體關係
首先，我們需要定義兩個實體：Product 和 Category。
- Product 中的 CategoryId 是外鍵，用來表示此產品屬於哪個 Category。
- Product 有一個導覽屬性 Category，使你可以訪問此產品所屬的分類。
- Category 有一個 Products 集合，用來表示這個分類中的所有產品。
```csharp=
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
    
    // 外鍵（Foreign Key）用來標示此產品屬於哪個分類
    public int CategoryId { get; set; }
    
    // 導覽屬性（Navigation Property）讓 EF Core 知道這個產品對應到一個 Category
    public Category Category { get; set; }
}

public class Category
{
    public int Id { get; set; }
    public string Name { get; set; }

    // 導覽屬性：一個分類包含多個產品
    public ICollection<Product> Products { get; set; }
}
```
2. 配置實體之間的關係
在 DbContext 中，EF Core 會自動根據外鍵來推斷這些關係，但我們也可以透過 Fluent API 明確指定：
```csharp=
public class AppDbContext : DbContext
{
    public DbSet<Product> Products { get; set; }
    public DbSet<Category> Categories { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // 配置一對多的關係：一個 Category 有多個 Products
        modelBuilder.Entity<Product>()
            .HasOne(p => p.Category)   // 一個 Product 有一個 Category
            .WithMany(c => c.Products) // 一個 Category 有多個 Products
            .HasForeignKey(p => p.CategoryId); // 外鍵配置
    }
}
```

3. CRUD 操作中的關係運用
現在我們有了 Product 和 Category 的關係，可以開始在控制器中進行操作。
3.1 顯示產品及其分類：
在 ProductsController 的 Index 方法中，我們可以使用 Include 這個 Entity Framework 提供的擴展方法來載入關聯的分類資料：
```csharp=
public async Task<IActionResult> Index()
{
    var products = await _context.Products
        .Include(p => p.Category) // 加入分類的資料
        .ToListAsync();
    return View(products);
}
```
:::info
Include 是 Entity Framework 提供的擴展方法，具備急切載入（Eager Loading）特性，可以同時從資料庫讀取主檔資料和其關聯的明細檔資料，避免「N+1 查詢」的問題，從而減少查詢次數並提升效能。這樣的設計能使資料的讀取更有效率，特別是在處理有關聯的資料時，能夠一次性獲得所需的所有資料。
:::

3.2 新增產品並指定分類：
當新增產品時，必須指定 CategoryId 以表示此產品屬於哪個分類：
```csharp=
[HttpPost]
public async Task<IActionResult> Create(Product product)
{
    if (ModelState.IsValid)
    {
        _context.Add(product);
        await _context.SaveChangesAsync();
        return RedirectToAction(nameof(Index));
    }
    
    // 如果 ModelState 無效，重新回傳視圖和資料
    return View(product);
}
```
在 View 中，可以提供一個下拉選單，讓使用者選擇產品的分類：
```html=
<form asp-action="Create">
    <div>
        <label>Product Name</label>
        <input asp-for="Name" />
    </div>
    <div>
        <label>Price</label>
        <input asp-for="Price" />
    </div>
    <div>
        <label>Category</label>
        <select asp-for="CategoryId" asp-items="ViewBag.Categories"></select>
    </div>
    <button type="submit">Create</button>
</form>
```
3.3 在 Controller 中準備分類資料：
為了使 Category 出現在下拉選單中，可以在 Create 的 GET 方法中準備分類資料：
```csharp
public IActionResult Create()
{
    ViewBag.Categories = new SelectList(_context.Categories, "Id", "Name");
    return View();
}
```

### Entity Framework Core 常用方法
在 Entity Framework Core 中，大多數的方法都有同步和非同步的版本。非同步方法的名稱通常以 Async 結尾。
- 同步方法: 適用於不需要非同步操作的情境。
- 非同步方法: 適用於需要非同步操作的情境，特別是在處理 I/O 操作（如資料庫查詢、檔案讀寫、網路請求）時，可以避免阻塞主執行緒，提高應用程式的效能和反應速度。

以下方法介紹基於下列2個實體進行
    
**Order 實體**
```csharp=
public class Order
{
    public int Id { get; set; }
    public DateTime OrderDate { get; set; }
    public string CustomerName { get; set; }

    // 導航屬性：一個訂單可以有多個訂單明細
    public required List<OrderDetail> OrderDetails { get; set; }
}
```
> Order 實體有一個導航屬性 OrderDetails，表示一個訂單可以有多個訂單明細。

**OrderDetail 實體**
```csharp=
public class OrderDetail
{
    public int Id { get; set; }
    public int OrderId { get; set; }
    public string ProductName { get; set; }
    public int Quantity { get; set; }
}
```

**資料庫上下文定義**
```csharp=
public class ApplicationDbContext : DbContext
{
    public DbSet<Order> Orders { get; set; }
    public DbSet<OrderDetail> OrderDetails { get; set; }

    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }
}
```

**在控制器中注入 ApplicationDbContext**
```csharp=
public class OrdersController : ControllerBase
{
    private readonly ApplicationDbContext _context;

    public OrdersController(ApplicationDbContext context)
    {
        _context = context;
    }
}
```

#### 常用方法介紹
LINQ 原生的方法主要用於在記憶體中的集合上進行查詢操作，Entity Framework Core 提供的擴充方法，並不是 LINQ 原生的方法，當您安裝並使用 Entity Framework Core 時，這些方法會被添加到您的查詢上下文中，使您能夠更方便地進行資料庫操作。
1. Add / AddAsync
將新實體添加到上下文中，並在保存更改時插入到資料庫中。
- 同步: Add
```csharp=
var newOrder = new Order { /* 初始化屬性 */ };
_context.Orders.Add(newOrder);
_context.SaveChanges();
```
- 非同步: AddAsync
```csharp=
var newOrder = new Order { /* 初始化屬性 */ };
await _context.Orders.AddAsync(newOrder);
await _context.SaveChangesAsync();
```

2. Find / FindAsync
根據主鍵查找實體。如果實體已經存在於上下文中，則從上下文中返回；否則，從資料庫中查找。
- 同步: Find
```csharp=
var order = _context.Orders.Find(orderId);
```
- 非同步: FindAsync
```csharp=
var order = await _context.Orders.FindAsync(orderId);
```

3. FirstOrDefault / FirstOrDefaultAsync
根據指定條件查找符合條件的第一個實體。如果沒有找到，則返回 null。
- 同步: FirstOrDefault
```csharp=
var order = _context.Orders.FirstOrDefault(o => o.Id == orderId);
```
- 非同步: FirstOrDefaultAsync
```csharp=
var order = await _context.Orders.FirstOrDefaultAsync(o => o.Id == orderId);
```
    
4. SingleOrDefault / SingleOrDefaultAsync
根據指定條件查找符合條件的唯一實體。如果沒有找到，則返回 null；如果找到多個，則拋出異常。
- 同步: SingleOrDefault
```csharp=
var order = _context.Orders.SingleOrDefault(o => o.Id == orderId);
```
- 非同步: SingleOrDefaultAsync
```csharp=
var order = await _context.Orders.SingleOrDefaultAsync(o => o.Id == orderId);
```
    
5. Include
Include 方法用於實現 Eager Loading（急切載入），指定在查詢主實體時應該同時載入的相關導航屬性。這樣可以在一次查詢中獲取到完整的主實體及其相關的導航屬性，避免了後續的多次查詢，提高了效能。
- 同步: Include
```csharp=
var order = _context.Orders
                    .Include(o => o.OrderDetails)  // 急切載入 OrderDetails
                    .FirstOrDefault(o => o.Id == orderId); // 根據 Id 查詢訂單
```
- 非同步: Include 本身沒有非同步版本，但可以與其他非同步方法一起使用。
```csharp=
var order = await _context.Orders
                          .Include(o => o.OrderDetails)  // 急切載入 OrderDetails
                          .FirstOrDefaultAsync(o => o.Id == orderId); // 根據 Id 查詢訂單
```
- 導航屬性: 用來表示實體之間的關聯，允許您在查詢一個實體時同時載入相關的其他實體。
- 急切載入（Eager Loading）: 使用 Include 方法可以在查詢主實體時同時載入導航屬性，從而在一次查詢中獲取到完整的資料。
    
6. Remove
將實體標記為刪除，並在保存更改時從資料庫中刪除。
- 同步: Remove
```csharp=
var order = _context.Orders.Find(orderId);
if (order != null)
{
    _context.Orders.Remove(order);
    _context.SaveChanges();
}
```
- 非同步: Remove 本身沒有非同步版本，但可以與其他非同步方法一起使用。
```csharp=
var order = await _context.Orders.FindAsync(orderId);
if (order != null)
{
    _context.Orders.Remove(order);
    await _context.SaveChangesAsync();
}
```
    
7. Update
將實體標記為已修改，並在保存更改時更新到資料庫中。
- 同步: Update
```csharp=
var order = _context.Orders.Find(orderId);
if (order != null)
{
    order.OrderDate = DateTime.Now; // 假設更新訂單日期
    _context.Orders.Update(order);
    _context.SaveChanges();
}
```
- 非同步: Update 本身沒有非同步版本，但可以與其他非同步方法一起使用。
```csharp=
var order = await _context.Orders.FindAsync(orderId);
if (order != null)
{
    order.OrderDate = DateTime.Now; // 假設更新訂單日期
    _context.Orders.Update(order);
    await _context.SaveChangesAsync();
}
```
    
8. SaveChanges / SaveChangesAsync
將上下文中的所有變更保存到資料庫中。
- 同步: SaveChanges
```csharp=
_context.SaveChanges();
```
- 非同步: SaveChangesAsync
```csharp=
await _context.SaveChangesAsync();
```
    
9. ToList / ToListAsync
當查詢結果包含多個實體時，將查詢結果轉換為列表。
- 同步: ToList
```csharp=
var orders = _context.Orders.ToList();
```
- 非同步: ToListAsync
```csharp=
var orders = await _context.Orders.ToListAsync();
```

10. Any / AnyAsync
判斷是否存在符合指定條件的任何實體。
- 同步: Any
```csharp=
bool exists = _context.Orders.Any(o => o.Status == "Pending");
```
- 非同步: AnyAsync
```csharp=
bool exists = await _context.Orders.AnyAsync(o => o.Status == "Pending");
```

####  查詢多個實體和單個實體的區別
1. 查詢多個實體時使用 ToListAsync
當查詢結果包含多個實體時，使用 ToListAsync 將結果轉換為列表。
```csharp=
public async Task<IActionResult> Index()
{
    var orders = await _context.Orders.Include(o => o.OrderDetails).ToListAsync();
    
    return View(orders);
}
```
返回值: List<Order>，包含多個訂單。
    
2. 查詢單個實體時使用 FirstOrDefaultAsync
當查詢結果只包含單個實體時，使用 FirstOrDefaultAsync 或 SingleOrDefaultAsync 查找單個實體。
```csharp=
public async Task<IActionResult> Details(int? id)
{
    var order = await _context.Orders
        .Include(o => o.OrderDetails)
        .FirstOrDefaultAsync(m => m.Id == id);

    return View(order);
}
```
返回值: Order，單一的訂單對象。
    
---

## [ABP（Asp.Net Boilerplate）框架](https://aspnetboilerplate.com/)
ABP 是「ASP.NET Boilerplate Project (ASP.NET 樣板項目)」的簡稱。
ABP 是一個開源的且說明文件良好的應用框架，起始的想法是，“開發一款為所有公司和開發者通用的框架！”。
    
它不僅僅是一個框架，更提供了一個基於 DDD 和最佳實踐的健壯的體系模型。它的目標是簡化開發過程，讓開發者能夠專注於業務邏輯，而不是繁瑣的基礎設施。

### 步驟 1：安裝 ABP CLI 工具
1. 開啟 Visual Studio 2022，並打開「終端機」或「命令提示字元」。
2. 輸入以下命令安裝 ABP CLI 工具
```bash=
dotnet tool install -g Volo.Abp.Cli       # 安裝 ABP CLI 
abp --version                             # 確認 ABP CLI 安裝成功

dotnet tool update -g Volo.Abp.Studio.Cli # 更新 ABP CLI 
```

### 步驟2：建立專案
1. 在 Visual Studio 2022 中，選擇「檔案」>「新建」>「專案」。
2. 在「建立新專案」對話框中，選擇「ASP.NET Core Web 應用程式」並點擊「下一步」。
3. 在「設定專案」對話框中，輸入專案名稱（例如 MyAbpApp）和位置，然後點擊「建立」。
4. 在「建立新的 ASP.NET Core Web 應用程式」對話框中，選擇「空白」模板，並確保選擇「ASP.NET Core 8.0（或更高版本）」作為目標框架。點擊「建立」。

### 步驟3：開啟專案並安裝 NuGet 套件
安裝 abp 相關套件後會在專案檔中(*.csproj)加上 ItemGroup 小節，如下圖

![image](https://hackmd.io/_uploads/HkcRJCP0R.png)

以下擇一進行即可。

<!-- 建議使用 ABP CLI ，有助於確保安裝的 ABP 套件與 ABP 框架無縫整合。 -->
   
#### 3.1 使用 NuGet 套件管理器 圖形介面
1. 打開剛才建立的專案 (MyAbpApp.sln)
2. 右鍵點擊專案名稱，選擇「管理 NuGet 套件」。
3. 在「瀏覽」選項卡中，搜尋並安裝
    - Volo.Abp.AspNetCore.Mvc
    - Volo.Abp.AspNetCore.Mvc.UI
    - Volo.Abp.EntityFrameworkCore
    - Volo.Abp.EntityFrameworkCore.SqlServer

#### 3.2 使用 .NET CLI 
```bash=
cd /d C:\Repos\MyAbpApp\MyAbpApp # 切換到專案目錄
dotnet add package Volo.Abp.AspNetCore.Mvc
dotnet add package Volo.Abp.AspNetCore.Mvc.UI
dotnet add package Volo.Abp.EntityFrameworkCore
dotnet add package Volo.Abp.EntityFrameworkCore.SqlServer
```

#### 3.3 使用 ABP CLI 
```bash=
cd /d C:\Repos\MyAbpApp\MyAbpApp # 切換到專案目錄
abp add-package Volo.Abp.AspNetCore.Mvc
abp add-package Volo.Abp.AspNetCore.Mvc.UI
abp add-package Volo.Abp.EntityFrameworkCore
abp add-package Volo.Abp.EntityFrameworkCore.SqlServer
```

### 步驟4：配置 ABP 模組
1. 打開 Program.cs 文件
2. 在 Program.cs 文件中，配置 ABP 框架
```csharp=
// var builder = WebApplication.CreateBuilder(args);
    
// 添加 ABP 模組
builder.Services.AddApplication<MyAbpAppModule>();

// var app = builder.Build();

// 初始化 ABP
app.InitializeApplication();

app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
});

// app.Run();
```

### 步驟5：建立和管理模組
1. 在 Modules 文件夾中，右鍵點擊並選擇「添加」>「新建項目」，選擇「類」並命名為 MyModule.cs。
2. 在 MyModule.cs 文件中，編寫代碼如下
```csharp=
public class MyModule : AbpModule
{
    public override void ConfigureServices(ServiceConfigurationContext context)
    {
        // 配置服務
    }
}
```

### 步驟6：使用 ABP UI 元件
1. 在 Views 文件夾中，右鍵點擊並選擇「添加」>「新建項目」，選擇「Razor Page」並命名為 Index.cshtml。
2. 在 Index.cshtml 文件中，使用 ABP 提供的 UI 元件編寫代碼如下
```csharp=
@page
@model IndexModel
<h1>Hello, ABP!</h1>
```
    
### 步驟7：執行應用程序
在 Visual Studio 2022 中，選擇「偵錯」>「開始偵錯」或按 Ctrl + F5 鍵來執行應用程序。

## 參考連結
- [開始使用 ASP.NET Core MVC](https://learn.microsoft.com/zh-tw/aspnet/core/tutorials/first-mvc-app/start-mvc?view=aspnetcore-8.0&tabs=visual-studio)
- [EF Core 筆記 1 - 概論](https://blog.darkthread.net/blog/efcore-notes-1/)
- [[鐵人賽Day22] - Entity Framework Core / Code first](https://ithelp.ithome.com.tw/articles/10208362)
- [菜雞新訓記 (3): 使用 Dapper 來連線到資料庫 CRUD 吧](https://igouist.github.io/post/2021/05/newbie-3-dapper/)