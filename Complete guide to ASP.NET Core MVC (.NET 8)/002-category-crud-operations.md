# Category CRUD Operations

## Create Category Model

برای ساخت مدل در دات نت، بر روی فولدر model کلیک راست کرده، منوی add و سپس گزینه class را انتخاب می نماییم.
سپس یک کلاس به نام Category.cs ایجاد میکنیم.

برای ایجاد یک property در کلاس میتوانیم صرفا با نوشتن کلمه کلیدی `prop` و سپس فشردن کلید `tab` آن را بنویسیم.

مثال:

```c#
namespace WebApplication1.Models
{
    public class Category
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public int DisplayOrder { get; set; }
    }
}
```

در این حالت، دات نت به صورت پیشفرض مقدار Id یا CategoryId را به عنوان کلید درنظر خواهد گرفت. اما اگر بخواهیم از نام دیگری به عنوان کلید استفاده کنیم، باید از data annotation ای به نام Key استفاده کنیم.

مثال:

```c#
using System.ComponentModel.DataAnnotations;

namespace WebApplication1.Models
{
    public class Category
    {
        [Key]
        public int Custom_Id { get; set; }
        public string Name { get; set; }
        public int DisplayOrder { get; set; }
    }
}
```

ولی اگر از نام Id یا CategoryId استفاده کنیم، نیازی به استفاده از `Key` نخواهیم داشت و این کار به صورت اتوماتیک توسط دات نت صورت میگیرد.

یکی دیگر از Data Annotation های پر استفاده `Required` می باشد. اگر بخواهیم یک پارامتر مقدار نال را نپذیرد (`not Null` باشد) میتوانیم از این کلید استفاده کنیم.

مثال:

```c#
using System.ComponentModel.DataAnnotations;

namespace WebApplication1.Models
{
    public class Category
    {
        public int Id { get; set; }
        [Required]
        public string Name { get; set; }
        public int DisplayOrder { get; set; }
    }
}
```

## NuGet Packages

برای انجام migration ها نیاز به نصب پکیجی به نام `Microsoft.EntityFrameworkCore` از طریق NuGet داریم.

برای این منظور بر روی پروژه کلیک راست کرده و گزینه Manage NuGet Packages را انتخاب میکنیم.

علاوه بر این، پکیج های `Microsoft.EntityFrameworkCore.SqlServer` و `icrosoft.EntityFrameworkCore.Tools` را نیز نصب میکنیم.

اگر گزینه edit project file رو مشاهده کنید، خواهید دید که این سه پکیج به فایل پروژه افزوده شده اند.

مثل:

```c#
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.EntityFrameworkCore" Version="8.0.4" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="8.0.4" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="8.0.4">
      <PrivateAssets>all</PrivateAssets>
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
    </PackageReference>
  </ItemGroup>

</Project>
```

حال نوبت به نصب `SqlServer` و `SqlServer Management SSMS` می باشد.

حال می بایست تنظیمات دیتابیس را داخل برنامه اضافه کنیم، برای این منظور باید فایل `appsettings.json` را ویرایش کنیم و مقدار زیر را در آن اضافه کنیم.

```c#
"ConnectionStrings": {
  "DefaultConnection": "Server=.;Database=firstTest;Trusted_Connection=True;TrustServerCertificate=True"
}
```

در این مثال، آدرس سرور را `.` و نام دیتابیس را `firstTest` قرار میدهیم. البته میتوانیم به جای نام `DefaultConnection` از نام دلخواه دیگر نیز استفاده کنیم.

## Setup ApplicationDbContext

برای این منظور یک فولدر به نام `Data` درون روت پروژه ایجاد میکنیم. سپس یک کلاس به نام `ApplicationDbContext.cs` در آن ایجاد مینماییم.

کد زیر را داخل فایل قرار میدهیم:

```c#
using Microsoft.EntityFrameworkCore;

namespace WebApplication1.Data
{
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options)
        {

        }
    }
}
```

پس از این، فایل `Program.cs` را به صورت زیر تغییر میدهیم
و سرویس زیر را به آن اضافه میکنیم:

```c#
builder.Services.AddDbContext<ApplicationDbContext>(options =>
options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));
```

## Create Database

برای این منظور از منوی بالا، گزینه tools و سپس NuGet Package Manager و سپس `Package Manager Console` را انتخاب میکنیم.

حال برای ساخت دیتابیس کافی است از دستور `update-database` استفاده بنماییم!

## Create Migrations

برای ساخت migration ها و اعمال آن ها روی دیتابیس باید به شیوه زیر عمل کنیم:

ابتدا به ازای هر مدلی که ساختیم، باید یک خط کد زیر را به فایل `ApplicationDbContext.cs` اضافه کنیم.

```c#
public DbSet<Category> Categories { get; set; }
```

نام مدل را داخل قسمت generic و نام جدول مورد نظر را در قسمت نام property قرار می دهیم. مثلا: از مدل Category یک جدول به نام Categories ایجاد می کنیم.

حال مجددا به Package Manager Console باز میگردیم و این بار دستور `add-migration` را به همراه نامی برای این مهاجرت انتخاب می کنیم.

مثال:

```cmd
add-migration AddCategoryTableToDb
```

با انجام این کار، یک فولدر جدید به نام Migrations به دایرکتوری پروژه ما اضافه خواهد شد که در آن تمامی migration های ساخته شده قرار خواهند گرفت.

حال برای اعمال migration ها روی دیتابیس باید مجدد دستور `update-database` را اجرا کنیم.

## add Controller

برای افزودن کنترلر باید توجه داشت که کلمه `Controller` در انتهای نام فایل قرار بگیرد.

## Seeding Db Tables

برای تغذیه دیتابیس همزمان با انجام migration ها باز هم به سراغ فایل `ApplicationDbContext.cs` می رویم و این بار متدی به نام `OnModelCreating` را override میکنیم.

مثال:

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Category>().HasData(
        new Category { Id=1, Name="test1", DisplayOrder=1},
        new Category { Id=2, Name="test2", DisplayOrder=2},
        new Category { Id=3, Name="test3", DisplayOrder=3}
        );
    //base.OnModelCreating(modelBuilder);
}
```

حال برای اجرای تغذیه دیتابیس، ابتدا با استفاده از دستور `add-migration` یک مهاجرت با نام دلخواه ایجاد می کنیم و پس از آن با دستور `update-database` دیتابیس را آپدیت می نماییم.

مثال:

```cmd
add-migration SeedCategoryTable
update-database
```

## get Data from Db

فرض کنیم می خواهیم دیتا های مدل Category را دریافت کنیم و از آنها در کنترلر استفاده کنیم، برای این منظور ابتدا باید اتصال دیتابیس را فراخوانی کنیم.

برای این منظور:

```c#
using Microsoft.AspNetCore.Mvc;
using WebApplication1.Data;
using WebApplication1.Models;

namespace WebApplication1.Controllers
{
    public class CategoryController : Controller
    {
        private readonly ApplicationDbContext _db;
        public CategoryController(ApplicationDbContext db)
        {
            _db = db;
        }
        public IActionResult Index()
        {
            List<Category> objCategoryList = _db.Categories.ToList();
            return View();
        }
    }
}
```

توضیحات کد فوق: ابتدا یک property به نام `_db` و از نوع پرایوت و فقط خواندنی ایجاد میکنیم.

سپس یک `ctor (constructor)` ایجاد میکنیم که یک ورودی از نوع ApplicationDbContext خواهد پذیرفت و در آن مقدار دریافت شده را درون متغیر \_db ذخیره میکنیم.

حال میتوانیم از طریق این متغیر `_db` به دیتابیس دسترسی داشته باشیم.

به عنوان مثال میتوانیم با استفاده از کد زیر تمام دیتا های موجود در جدول Categories را با متد `ToList()` دریافت کنیم و آن را در متغیری از نوع List با تایپ Category ذخیره کنیم.

## Display Data in Views

برای نمایش دیتا کنترلر در ویو، ابتدا باید آن را از طریق متد view() به سمت ویو ارسال کنیم.

```c#
public IActionResult Index()
{
    List<Category> objCategoryList = _db.Categories.ToList();
    return View(objCategoryList);
}
```

سپس فایل ویو را به صورت زیر تغییر میدهیم:

```c#
@model List<Category>

<h1>hello</h1>
<table class="table table-bordered table-striped">
    <thead>
        <tr>
            <th>نام</th>
            <th>ترتیب نمایش</th>
        </tr>
    </thead>
    <tbody>
        @foreach(var obj in Model.OrderBy(u=>u.DisplayOrder))
        {
            <tr>
                <td>@obj.Name</td>
                <td>@obj.DisplayOrder</td>
            </tr>
        }
    </tbody>
</table>
```

در خط اول باید با استفاده از `@model` و سپس تعیین نوع گونه داد، دیتای ارسال شده را دریافت کنیم. سپس میتوانیم از کد های C# نیز در کد HTML استفاده کنیم. به عنوان مثال میتوانیم از `@foreach` برای ایجاد یک حلقه for بهره ببریم.

توجه: کلمه `@model` در بالا با حرف کوچک باید باشد ولی در سایر قسمت های کد که میخواهیم از آن استفاده کنیم، باید از کلمه `Model` با حرف اول بزرگ استفاده کنیم.

## Add Method and View

در این بخش میخواهیم متد و صفحه افزودن رکورد جدید برای مدل Category را ایجاد کنیم.

ابتدا متد زیر را به کنترلر مورد نظر اضافه می کنیم:

```c#
public IActionResult Create()
{
    return View();
}
```

حال کد های ویو را به صورت زیر تغیر میدهیم:

```c#
@model Category

<h1>صفحه ایجاد دسته بندی جدید</h1>

<form method="post">
    <label asp-for="Name"></label>
    <input asp-for="Name" />
    <label asp-for="DisplayOrder"></label>
    <input asp-for="DisplayOrder" />
    <button type="submit">submit</button>
</form>
```

در اینجا یک فرم ساده برای دریافت اطلاعات از کاربر ایجاد کرده ایم. در ابتدای این فرم مشخص می کنیم که `@model Category` مدل مورد استفاده ما از نوع Category می باشد.

سپس می توانیم از قابلیت خفن TagHelper در فرم استفاده کنیم و به صورت خودکار، نام، نوع و لیبل فیلد های input را ایجاد بنماییم.

به دلیل اینکه در ابتدای این فایل بیان کردیم که میخواهیم از مدل Category استفاده کنیم، لذا VS به صورت اتوماتیک پراپرتی های موجود در این مدل را در TagHelper به ما پیشنهاد خواهد داد.

نکته: برای اینکه بتوانیم UI زیباتری داشته باشیم و کاربر به صورت مستقیم نام ستون های جداول دیتابیس را مشاهده نکند، میتوانیم از DataAnnotation ای به نام `DisplayName()` بهره ببریم.

برای این منظور فایل مدل را به صورت زیر تغییر میدهیم:

```c#
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace WebApplication1.Models
{
    public class Category
    {
        public int Id { get; set; }
        [Required]
        [DisplayName("Category Name")]
        public string Name { get; set; }
        [DisplayName("Display Order")]
        public int DisplayOrder { get; set; }
    }
}
```

## Insert to Db

در این بخش میخواهیم فرم ثبت شده را توسط کنترلر دریافت کنیم و آن را در دیتابیس ذخیره کنیم.

ابتدا یک متد دیگر ولی با همان نام Create درون کنترلر مورد نظر ایجاد میکنیم:

```c#
public IActionResult Create()
{
    return View();
}
[HttpPost]
public IActionResult Create(Category obj) {
    _db.Categories.Add(obj);
    _db.SaveChanges();
    return RedirectToAction("Index");
}
```

این متد جدید، از نوع `HttpPost` خواهد بود و یک ورودی از نوع Category می پذیرد.
سپس با استفاده از متغیر مربوط به دیتابیس `_db.Categories.Add(obj)` ورودی را به متد Add از جدول Categories ارسال می کنیم.

در آخر نیز باید تغییرات دیتابیس را با دستور `_db.SaveChanges();` ذخیره کنیم.

حال برای زیباتر و اصولی تر شدن کار، میتوانیم متد را به اکشن دیگری return کنیم.

متد `return RedirectToAction("Index", "Category");` یک ورودی اجباری به نام ActionName و ورودی اختیاری به نام ControllerName دارد.

## Validations

برای افزودن validation ها در دات نت به صورت زیر عمل میکنیم:

ابتدا فایل مدل مورد نظر را به شیوه زیر تغییر می دهیم:

```c#
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace WebApplication1.Models
{
    public class Category
    {
        public int Id { get; set; }
        [Required]
        [DisplayName("Category Name")]
        [MaxLength(30)]
        public string Name { get; set; }
        [DisplayName("Display Order")]
        [Range(1, 100, ErrorMessage = "متن ارور دلخواه")]
        public int DisplayOrder { get; set; }
    }
}
```

حال فایل ویو را با استفاده از TagHelper ها به شیوه زیر تغییر میدهیم:

```c#
@model Category

<h1>صفحه ایجاد دسته بندی جدید</h1>

<form method="post">
    <label asp-for="Name"></label>
    <input asp-for="Name" />
    <span asp-validation-for="Name"></span>
    <label asp-for="DisplayOrder"></label>
    <input asp-for="DisplayOrder" />
    <span asp-validation-for="DisplayOrder"></span>
    <button type="submit">submit</button>
</form>
```

و در آخر نیز فایل کنترلر را نیز به شیوه زیر تغییر میدهیم:

```c#
public IActionResult Create()
{
    return View();
}
[HttpPost]
public IActionResult Create(Category obj) {
    if (ModelState.IsValid)
    {
        _db.Categories.Add(obj);
        _db.SaveChanges();
        return RedirectToAction("Index");
    }
    return View();
}
```

## Custom Validations

میتوانیم علاوه بر validation های موجود، قوانین جدیدی و کاستوم شده با متن ارور های دلخواه نیز اضافه کنیم.

```c#
public IActionResult Create()
{
    return View();
}
[HttpPost]
public IActionResult Create(Category obj) {
    if (obj.Name == obj.DisplayOrder.ToString())
    {
        ModelState.AddModelError("Name", "متن ارور دلخواه");
    }
    if (ModelState.IsValid)
    {
        _db.Categories.Add(obj);
        _db.SaveChanges();
        return RedirectToAction("Index");
    }
    return View();
}
```

در بخش TagHelper ها نیز میتوانیم با استفاده از کد زیر، تمام ارور ها را در یک جا نشان دهیم:

```c#
<div asp-validation-summary="All"></div>
<div asp-validation-summary="ModelOnly"></div>
```

در حالت ModelOnly ارور هایی نمایش داده میشوند که مربوط به فیلد یا پراپرتی خاصی نباشند!

## Import View in View

فرض کنید میخواهیم یک فایل Partial View مانند ValidationScriptsPartial.cshtml را به فایل ویو ساخت دسته بندی اضافه کنیم تا بتوانیم به صورت سمت کلاینت بخشی از validation ها را انجام دهیم.

برای این منظور باید فایل Partial View را به صورت زیر به فایل فرم اضافه کنیم:

```c#
@section Scripts{
    @{
        <partial name="_ValidationScriptsPartial" />
    }
}
```

کد فوق، فایل partial را به بخشی به نام Scripts که در فایل `_Layout.cshtml` تعریف شده، منتقل می کند تا این فایل در آن قسمت لود شود.

اگر به فایل `_Layout` نگاه کنیم، این تکه کد را مشاهده میکنیم که از آن برای تعریف و ایجاد یک بخش یا همان section استفاده میکنیم:

```c#
@await RenderSectionAsync("Scripts", required: false)
```

## Edit and Delete Controllers

حال میخواهیم بخش های ویرایش و حذف رکورد ها را پیاده کنیم. برای این منظور ابتدا لینک های آنها را به صورت زیر و به طور داینامیک تولید میکنیم:

```c#
@foreach(var obj in Model.OrderBy(u=>u.DisplayOrder))
{
    <tr>
        <td>@obj.Name</td>
        <td>@obj.DisplayOrder</td>
        <td><a asp-controller="Category" asp-action="Edit" asp-route-id="@obj.Id">ویرایش</a></td>
        <td><a asp-controller="Category" asp-action="Delete" asp-route-id="@obj.Id">حذف</a></td>
    </tr>
}
```

در کد فوق و در قسمت تگ a مشاهده میکنیم که از پارامتر جدیدی به نام `asp-route-` استفاده شده است. با استفاده از این پارامتر، میتوانیم هر نوع دیتایی را به لینک اضافه کنیم و هر اسم دلخواهی برای آن پارامتر تعیین کنیم:

```cmd
asp-route-id
asp-route-categoryId
and etc...
```

حال به پیاده سازی متد مربوطه در کنترلر میپردازیم:

```c#
public IActionResult Edit(int? id)
{
    if (id == null || id == 0) {
        return NotFound();
    }

    // three ways of getting data
    Category? categoryFromDb1 = _db.Categories.Find(id);
    Category? categoryFromDb2 = _db.Categories.FirstOrDefault(u=>u.Id==id);
    Category? categoryFromDb3 = _db.Categories.Where(u=>u.Id==id).FirstOrDefault();

    if (categoryFromDb1 == null)
    {
        return NotFound();
    }

    return View(categoryFromDb1);
}
```

در مثال فوق از سه روش برای گرفتن دیتا استفاده شده است.
