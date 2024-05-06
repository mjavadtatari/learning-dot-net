# Getting Started

### Tools Needed

- .NET 7/8
- Visual Studio 2022/Preview
- SSMS 2018

#### How to create a new project

1. open visual studio 2022
2. create new project
3. search for `MVC` and select one with `(model-view-controller)`
4. choose a name for project and solution (a solution can have multiple projects in it)
5. select framework verison and authentication methods
6. (optional) you can add your project to your github by creating a repository
7. run the project by clicking on the `https` button

###### project file

for accecing the project file `.csproj` right click on project name and then select `Edit Project File`

###### properties dir

there is a `lunchSettings.json` file in this directory which allows you to define settings that managing iisSettings, profiles and etc.
in profiles you can identify EnvironmentVariables too! like `ASPNETCORE_ENVIRONMENT` variable the can hold (Development or Production)

###### wwwroot dir

in this directory we can store all of our static files like css, js, libraries, images and etc.

###### appsettings file

in this file you can host all of your connection strings, secret keys and etc.

###### program file

in `Program.cs` file, we create a builder of WebApplication and then adding some services to it. also we can configure the request pipeline (pipeline means that when a request comes to the application, how do you want to process that).

for example if you want to use MVC architecture, you should add `builder.Services.AddControllersWithViews();` as a service to container in this file.

##### basic Routing in .NET

in `Program.cs` file we see `app.UseRouting();` which means adding our routings to the application. also there is another configure named `app.MapControllerRoute` which works while the request not matching any of routes in the routing file!

```c#
app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");
```

in this code we define a default pattern to call a method named `Index` in a controller named `Home` with or without `id` (that `? - question mark` means it can be null)
