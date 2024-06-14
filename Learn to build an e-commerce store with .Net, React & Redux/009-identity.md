## Install Packages

برای کار با سیستم auth و identity در دات نت باید کتابخانه های زیر را از طریق NuGet پکیج نصب کنیم:

```cmd
Microsoft.AspNetCore.Authentication.JwtBearer
Microsoft.AspNetCore.Identity.EntityFrameworkCore
```

## Create User Entity

حال نوبت به ساخت کلاس User میرسد.

نیاز داریم این کلاس رو از کلاس `IdentityUser` ارث ببریم. با این کار دیگه نیازی به ساخت property های مربوط به نام، پسورد، ایمیل، یوزرنیم و ... نخواهیم داشت.

## Modifing AppDbContext

در این مرحله، نیاز داریم تا فایل DbContext رو به صورت زیر تغییر بدهیم:

```c#
public class AppDbContext : IdentityDbContext<User>
```

به جای اینکه کلاس AppDbContext رو از `DbContext` ارث ببریم، آن را از کلاس `IdentityDbContext` ارث خواهیم برد.

در قسمت generic هم از کلاس User استفاده میکنیم.

با انجام این کار، دیگر نیازی به ساخت DbSet برای کلاس User نخواهیم داشت.

حال میتوانیم از طریق کد زیر، دیتای مربوط به نقش ها رو نیز در دیتابیس ایجاد (seed) کنیم:

```c#
protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);

            builder.Entity<IdentityRole>().HasData(
                new IdentityRole { Name = "Admin", NormalizedName = "ADMIN" },
                new IdentityRole { Name = "Student", NormalizedName = "STUDENT" }
            );
        }
```

با استفاده از override کردن متد OnModelCreating میتوانیم هنگام ایجاد مدل، دیتای مورد نظرمون رو به دیتابیس اضافه کنیم.

## Modifing Program.cs

حال نوبت به تغییر فایل Program.cs میرسد.
در این قسمت باید سرویس های مربوط به هسته Identity، نقش هایش (اگر نیاز داریم) و EntityFrameworkStore را (برای ساخت جداول مربوطه) اضافه بنماییم.

```c#
builder.Services.AddIdentityCore<User>().AddRoles<IdentityRole>().AddEntityFrameworkStores<AppDbContext>();
builder.Services.AddAuthentication();
builder.Services.AddAuthorization();
```

در نهایت نیز سرویس های Authentication و Authorization را به برنامه اضافه میکنیم.

## Modifing DbInitializer

حال برای seed کردن دیتابیس میتوانیم فایل DbInitializer که در جلسات قبلی ساخته بودیم رو به صورت زیر تغییر بدهیم و دیتای کاربران مورد نیازمون رو با استفاده از `UserManager` به آن اضافه کنیم:

```c#
public static async Task Initialize(StoreContext context, UserManager<User> userManager)
        {
            if (!userManager.Users.Any())
            {
                var user = new User
                {
                    UserName = "bob",
                    Email = "bob@test.com"
                };

                await userManager.CreateAsync(user, "Pa$$w0rd");
                await userManager.AddToRoleAsync(user, "Member");

                var admin = new User
                {
                    UserName = "admin",
                    Email = "admin@test.com"
                };

                await userManager.CreateAsync(admin, "Pa$$w0rd");
                await userManager.AddToRolesAsync(admin, new[] { "Member", "Admin" })
            }
        }
```

همون طور که مشاهده میکنیم، با استفاده از UserManager میتوانیم همه نوع کار مربوط به کاربر مانند: ساخت کاربر، نقش دادن به کاربر، هش کردن کلمه عبور و ... را به راحتی برای ما انجام دهد.

در ادامه نیز فایل Program.cs رو به صورت زیر برای استفاده از UserManager در DbInitializer تغییر میدهیم:

```c#
// these lines blongs to seeding data
var scope = app.Services.CreateScope();
var context = scope.ServiceProvider.GetRequiredService<StoreContext>();
var userManager = scope.ServiceProvider.GetRequiredService<userManager<User>>();
var logger = scope.ServiceProvider.GetRequiredService<ILogger<Program>>();
try
{
    await context.Database.Migrate();
    await DbInitializer.Initialize(context, userManager);
}
catch (Exception ex)
{
    logger.LogError(ex, "A problem occurred during migration");
}
```

در آخر نیز برای ایجاد migration مورد نظرمون از دستور زیر استفاده میکنیم:

```cmd
dotnet ef migrations add IdentityAdded
```
