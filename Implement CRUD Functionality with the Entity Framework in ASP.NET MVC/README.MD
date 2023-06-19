# پیاده سازی عملکرد CRUD با Entity Framework در ASP.NET MVC  


در این آموزش، کدهای ایجاد، خواندن، به روز رسانی، حذف (CRUD) را که MVC scaffolding به طور خودکار در کنترلرها و ها ایجاد می کند، بررسی و سفارشی می کنید.

```
**نکته**

اجرای الگوی repository به منظور ایجاد یک لایه انتزاعی بین کنترلرو لایه دسترسی به داده، یک روش معمول است.
```


## ایجاد صفحه Details 
کد scaffolded برای صفحه Students `Index` ویژگی `Enrollments` را کنار گذاشته است، زیرا این ویژگی دارای collection است. در صفحه `Details،` محتویات collection را در یک جدول HTML نمایش می دهید.

در Controllers\StudentController.cs، متد اکشن برای ویو  `Details ` از متد Find برای یافتن یک موجودیت `Student` استفاده می کند.

``` c#

public ActionResult Details(int? id)
{
    if (id == null)
    {
        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
    }
    Student student = db.Students.Find(id);
    if (student == null)
    {
        return HttpNotFound();
    }
    return View(student);
}
```

مقدار کلید به عنوان پارامتر id به متد ارسال می شود و از route data در لینک Details در صفحه Index می آید.

<div dir="rtl">
## نکته: Route data
Route data داده‌ای است که model binder در بخش URL مشخص شده در جدول  routing یافت می‌شود. به عنوان مثال، default route بخش های controller، action و id را مشخص می کند:
نکته: Route_data
Route_data داده‌ای است که model_binder در بخش URL مشخص شده در جدول _routing یافت می‌شود. به عنوان مثال، default_route بخش های _controller، _action و _id را مشخص می کند:
</div>

``` C#

routes.MapRoute(
    name: "Default",
    url: "{controller}/{action}/{id}",
    defaults: new { controller = "Home", action = "Index", id = UrlParameter.Optional }
);
```

**Example:**
`http://localhost:1230/Instructor/Index/1?courseID=2021`

رشته `?courseID=2021` یک مقدار query string است. اگر `id` را به عنوان مقدار query string ارسال کنید، model binder نیز کار خواهد کرد:

ادرس های URL توسط عبارات ActionLink در Razor view ایجاد می شوند. در کد زیر، پارامتر id با default route مطابقت دارد، بنابراین id به route data اضافه می شود.

``` c#

@Html.ActionLink("Select", "Index", new { id = item.PersonID  })
```
یا 

``` c#
<a href="/Select/Index/@item.PersonID">Select</a>
```

در کد زیر، courseID با پارامتری در default route مطابقت ندارد، بنابراین به عنوان query string اضافه می‌شود.

``` C#

@Html.ActionLink("Select", "Index", new { courseID = item.CourseID })
```
یا

``` C#

<a href="/Select/Index/?courseID=@item.CourseID">Select</a>
```

## برای ایجاد صفحه Details

