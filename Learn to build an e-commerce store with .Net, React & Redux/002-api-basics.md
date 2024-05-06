## Create API Solution and Project

می توانیم از دات نت کامند لاین برای ساخت پروژه هم استفاده کنیم!

به عنوان مثال میتوان از `dotnet -h` برای مشاهده لیست دستورات و یا از `dotnet --info` برای مشاهده اطلاعات نسخه نصب شده از فریمورک و سایر کتابخانه های آن استفاده کرد.

میتوانیم از دستور `dotnet new list` برای مشاهده لیستی از پروژه های قابل ایجاد و ساخت بهره ببریم.

برای ایجاد پروژه ابتدا یک دایرکتوری با نام پروژه ایجاد میکنیم. سپس وارد آن دایرکتوری شده و دستور `dotnet new sln` را اجرا میکنیم. این دستور یک فایل solution با نام پروژه برای ما ایجاد خواهد کرد.

حال با استفاده از دستور زیر یک پروژه از نوع `.NET WEB API` , و با نام API ایجاد میکنیم:

```cmd
dotnet new webapi -o API
```

بعد از انجام مراحل فوق، باید پروژه را با استفاده از دستور زیر به solution ساخته شده اضافه کنیم:

```cmd
dotnet sln add API
```

در نهایت هم برای اجرای پروژه، بعد از ورود به دایرکتوری آن، از دستور زیر استفاده میکنیم:

```cmd
dotnet run
```

میتوانیم از کد زیر نیز برای اجرای برنامه در حالت develope استفاده کنیم.

```cmd
dotnet watch
dotnet watch --no-hot-reload
```

## Create First Entity Class

برای ایجاد کلاس مدل ها، ابتدا یک دایرکتوری به نام `Entities` یا `Models` ایجاد میکنیم.

سپس فایل های کلاس هایمان را با پسوند `.cs` درون این دایرکتوری قرار خواهیم داد.

## What is DbContext Class

کلاس `DbContext` مانند یک gateway عمل کرده و برنامه ما را به دیتابیس متصل میکند. برقراری اتصال به دیتابیس و مدیریت این کانکشن بر عهده این کلاس میباشد.

هر DbContext می تواند یک یا بیشتر از یک `DbSet` داشته باشد. هر DbSet نماینده یک جدول در دیتابیس می باشد.

## Adding the DbContext Class

ابتدا اکستنشن `NuGet Gallery` را نصب کرده و وارد آن می شویم.

سپس عبارت `microsoft.entityframeworkcore` را جستجو میکنیم و کتابخانه های زیر را با توجه به ورژن دات نت مورد استفاده، نصب میکنیم:

```cmd
Microsoft.EntityFrameworkCore.SqlServer
Microsoft.EntityFrameworkCore.Design
```

پس از نصب، فایل `API.csproj` پروژه به صورت زیر تغییر خواهد کرد:

```c#
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>disable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.OpenApi" Version="8.0.4" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="8.0.4">
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
      <PrivateAssets>all</PrivateAssets>
    </PackageReference>
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="8.0.4" />
    <PackageReference Include="Swashbuckle.AspNetCore" Version="6.4.0" />
  </ItemGroup>

</Project>
```

حال برای انجام ادامه مسیر، نیاز به ساخت یک دایرکتوری به نام `Data` در پروژه داریم که فایل ها و کلاس های مربوط به دیتا و دیتابیس را در آن قرار خواهیم داد.

سپس در این دایرکتوری یک کلاس به نام `StoreContext.cs` یا نام مورد نظر خودمون می سازیم و کد زیر را در آن قرار می دهیم:

```c#
using API.Entities;
using Microsoft.EntityFrameworkCore;

namespace API.Data
{
    public class StoreContext : DbContext
    {
        public StoreContext(DbContextOptions options) : base(options)
        {
        }

        public DbSet<Product> Products { get; set; }
    }
}
```

در کد فوق، یک کلاس با ارث بری از کلاس `DbContext` ایجاد کردیم که درون آن یک constructor قرار دارد.

در آخر نیز به ازای هر یک از جداول و مدل هایی که داریم، یک `DbSet` به صورت فوق ایجاد میکنیم.

سپس به فایل `Program.cs` مراجعه میکنیم و تکه کد زیر را به عنوان service اضافه میکنیم:

```c#
builder.Services.AddDbContext<StoreContext>(opt =>
{
    opt.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"));
});
```

نکته: در تعریف و افزودن سرویس ها به فایل Program.cs ترتیب افزودن سرویس ها تاثیر و اهمیتی ندارد! اما در ادامه همین فایل، هنگام افزودن middleware ها، حتما مراقب ترتیب فراخوانی ها باشید!

در نهایت نیز فایل `appsettings.Development.json` یا `appsettings.json` را به صورت زیر تغییر میدهیم و قسمت connectionStrings را به آن اضافه میکنیم:

```json
"ConnectionStrings": {
    "DefaultConnection": "Server=.;Database=apiTest;Trusted_Connection=True;TrustServerCertificate=True"
  }
```

## Create Entity Migrations

برای ساخت و انجام مهاجرت ها، نیاز به استفاده از ابزار و کنسول داریم. برای نصب آن عبارت `dotnet ef nuget` را گوگل میکنیم.

در نتیجه به دستور زیر میرسیم و از این دستور برای نصب `dotnet ef` استفاده میکنیم:

```cmd
dotnet tool install --global dotnet-ef --version 8.0.4
```

دستور فوق، ابزار `dotnet ef` را به صورت گلوبال روی سیستم نصب میکند.

و درصورتی که بخواهیم از نصب شدن آن اطمینان حاصل کنیم یا آن را به صورت گلوبال آپدیت کنیم، میتوانیم از دستور های زیر استفاده کنیم:

```cmd
dotnet tool list -g
dotnet tool update --global dotnet-ef
```

بعد از نصب میتوانیم این ابزار را با دستورات زیر فراخوانی کنیم:

```cmd
dotnet-ef
dotnet ef
```

در این مرحله برای ایجاد و ساخت migration از طریق این ابزار، ابتدا اگر برنامه در حال اجرا باشد، آن را متوقف میکنیم، سپس به دایرکتوری پروژه رفته و دستور زیر را در terminal اجرا میکنیم:

```cmd
dotnet-ef migrations add InitialCreate -o Data/Migrations
```

در کد فوق، یک مهاجرت به نام `InitialCreate` و در مسیر `Data/Migrations` ایجاد مینماییم.

در مرحله بعد، برای اجرای این مهاجرت ها، از دستور زیر استفاده میکنیم:

```cmd
dotnet-ef database update
```

## Generate Seed Data

ابتدا یک کلاس در دایرکتوری Data به نام `DbInitializer` میسازیم و دیتای مورد نظر را از طریق آن seed میکنیم.

```c#
using API.Entities;

namespace API.Data
{
    public static class DbInitializer
    {
        public static void Initialize(StoreContext context)
        {
            if (context.Products.Any()) return;

            var products = new List<Product>
            {
                new Product{
                    Name = "test",
                    Description = "testDescription",
                    Price = 123123,
                    PictureUrl = "test",
                    Type = "test",
                    Brand = "test",
                    QuantityInStock = 2,
                },
                new Product{
                    Name = "test2",
                    Description = "testDescription2",
                    Price = 1231223,
                    PictureUrl = "test2",
                    Type = "test2",
                    Brand = "test2",
                    QuantityInStock = 3,
                },
            };

            // first way to loop (recomended)
            foreach (var product in products)
            {
                context.Products.Add(product);
            }

            // second way to loop
            context.Products.AddRange(products);

            context.SaveChanges();
        }
    }
}
```

کلاس فوق را از نوع استاتیک میسازیم تا در هنگام صدا زدن متد های آن، نیازی به ساخت شی جدید از آن نداشته باشیم.

در ابتدای تابع Initialize بررسی میکنیم که اگر دیتایی درون جدول مورد نظر وجود داشت، مابقی تابع اجرا نشود!

سپس کد های زیر را به فایل `Program.cs` و در خط قبل از `app.Run();` اضافه میکنیم:

```c#
// these lines blongs to seeding data
var scope = app.Services.CreateScope();
var context = scope.ServiceProvider.GetRequiredService<StoreContext>();
var logger = scope.ServiceProvider.GetRequiredService<ILogger<Program>>();
try
{
    context.Database.Migrate();
    DbInitializer.Initialize(context);
}
catch (Exception ex)
{
    logger.LogError(ex, "A problem occurred during migration");
}
```

در کد فوق، یک scope ایجاد کردیم. وظیفه این اسکوپ در واقع نگه داشتن یا hold کردن یک سرویس به نام StoreContext میباشد.
پس از نگه داشتن سرویس مورد نظر، آن را درون متغیری به نام context ذخیره میکنیم.

حال درون یک try catch دستور migrate را به شیوه فوق اجرا میکنیم تا اگر دیتابیس وجود نداشت، آن را بسازد و یا اگر مهاجرت های انجام نشده ای موجود بود، آنها را انجام دهد.

و در نهایت متد Initialize را صدا میزنیم تا عمل seed کردن دیتابیس را انجام دهیم.

از کلاس ILogger و متد LogError هم استفاده میکنیم تا ارور های احتمالی را در cmd به ما نمایش دهد.

## Create API Controller (Synchronous Code)

برای ایجاد کنترلر هایمان، ابتدا اقدام به ساخت دایرکتوری به نام `Controllers` در دایرکتوری پروژه میکنیم.

سپس یک کلاس به نام `ProductsController.cs` در این دایرکتوری ایجاد میکنیم. بهتره از اسم های جمع برای نام گذاری کنترلر هایمان استفاده کنیم. همینطور باید کلمه Controller را نیز در انتهای نام کنترلر ذکر کنیم.

شکل کلی و پایه یک API کنترلر در دات نت به صورت زیر می باشد:

```c#
using Microsoft.AspNetCore.Mvc;

namespace API.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class ProductsController : ControllerBase
    {

    }
}
```

در این کد، از attribute ای به نام `ApiController` و route ای به نام `api/[controller]` استفاده میکنیم.

برای ادامه کار، نیاز داریم که دیتابیس کانکشن مون را به وسیله dependency injection به کنترلر بیافزاییم و بدین وسیله StoreContext را به سازنده این کلاس اضافه کنیم.

کنترلر ما به کد زیر تغییر میکند:

```c#
using API.Data;
using Microsoft.AspNetCore.Mvc;

namespace API.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class ProductsController : ControllerBase
    {
        private readonly StoreContext context;
        public ProductsController(StoreContext context)
        {
            this.context = context;
        }
    }
}
```

حال متد های مورد نظر را پیاده میکنیم:

```c#
        [HttpGet]
        public ActionResult<List<Product>> GetProducts()
        {
            var products = context.Products.ToList();
            return Ok(products);
        }

        [HttpGet("{id}")]
        public ActionResult<Product> GetProduct(int id)
        {
            return context.Products.Find(id);
        }
```

نکات:

- نوع خروجی متد ها از نوع `ActionResult` میباشد و بنا به تایپ مقدار بازگشتی از متد، آن را داخل `<>` تعریف مینماییم.
- تایپ متد ها و مسیر ها از نوع `HttpGet` میباشد و در متد دوم، میتوانیم درخواست ورودی از مسیر نیز داشته باشیم. به صورت `HttpGet("{id}")`
- در API نویسی و برای بازگردان کد 200 به همراه تبدیل آن به JSON میتوانیم از متد هایی مانند `OK()` استفاده کنیم.

در نهایت، فراموش نکنید که کنترلر ها را در فایل Program.cs و در بخش سرویس ها، به صورت زیر اضافه کنید:

```c#
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.

builder.Services.AddControllers(); // add this
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();
```

البته توجه داشته باشید که کنترلر ها نیز در فایل Program.cs مپ شده باشند. برای این کار کد های زیر را قبل از `app.Run();` قرار میدهیم:

```c#
app.UseAuthorization();

app.MapControllers(); // this one

app.Run();
```

## Create API Controller (Asynchronous Code)

در حالت sync وقتی ریکوئست به سمت سرور ارسال میشه و در اون متد نیاز داریم تا دیتابیس دستوراتی را اجرا کند و نتیجه آن را به ما بازگرداند، ممکن است به دلیل حجم بالای دیتا، زمان پاسخ دهی افزایش پیدا کند و در نتجیه اون thread ای که مسئول این ریکوئست هستش تا پایان این زمان به حالت بلاک شده در می آید و لذا ممکن است در ساعات شلوغی سرور، به دلیل تعداد بالای ریکوئست ها، دچار کمبود thread شویم و در کاربران با ارور یا معطلی روبرو شوند.

برای رفع این مشکل میتوانیم از کد نویسی به صورت async استفاده کنیم و در زمانی که برنامه نیاز داره تا منتظر دیتابیس بمونه، thread آزاد بشه و به بقیه ریکوئست ها رسیدگی کنه و در زمانی که پاسخ دیتابیس آماده شد، دوباره thread به این ریکوئست اختصاص پیدا میکند و ادامه کار را انجام خواهد داد.

برای این منظور کد های کنترلر را به صورت زیر تغییر میدهیم:

```c#
using API.Data;
using API.Entities;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;

namespace API.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class ProductsController : ControllerBase
    {
        private readonly StoreContext _context;
        public ProductsController(StoreContext context)
        {
            _context = context;
        }

        [HttpGet]
        public async Task<ActionResult<List<Product>>> GetProducts()
        {
            var products = await _context.Products.ToListAsync();
            return Ok(products);
        }

        [HttpGet("{id}")]
        public async Task<ActionResult<Product>> GetProduct(int id)
        {
            return await _context.Products.FindAsync(id);
        }
    }
}
```

تغییرات عبارتند از:

افزودن عبارت async به متد های مورد نظر و سپس افزودن عبارت await به ابتدای خطی که قرار منتظرش بمانیم.

نوع خروجی تابع را نیز از نوع Task تعریف میکنیم.

نکته مهم:
برای تعریف پراپرتی های private درون کلاس ها، بهتره نام آنها را با `_` شروع کنیم! در این صورت دیگر نیازی به استفاده از کلمه `this` هم نداریم!

## Add to git

دستور ساخت فایل gitignore:

```cmd
dotnet new gitignore
```

و برای امنیت بیشتر فایل `appsettings.json` را نیز به فایل gitignore اضافه میکنیم.
