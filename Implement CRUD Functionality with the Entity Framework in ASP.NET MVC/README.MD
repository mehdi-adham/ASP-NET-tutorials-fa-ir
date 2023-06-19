# پیاده سازی عملیات CRUD با Entity Framework در ASP.NET MVC  

در این آموزش، کدهای CREATE ، READ ،UPDATE ، DELTE (CRUD) را که MVC scaffolding به طور خودکار در کنترلرها و viewها ایجاد می کند، بررسی و سفارشی می کنیم.


 اینجا نمونه هایی از صفحات وب که ایجاد می کنید آورده شده است:


![Details page image](image1.png)

![Create page image](image2.png)

![Delete page image](image3.png)


**در این آموزش موارد زیر را انجام خواهیم داد:**

 - یک صفحه Detail ایجاد می کنیم
 - صفحه Create را آپدیت می کنیم
 - متد HttpPost editing را آپدیت می کنیم
 - صفحه حذف را آپدیت می کنیم
 - اتصالات پایگاه داده را می بندیم
 - هندل تراکنش ها


## ایجاد صفحه Details 
کد scaffolded برای صفحه Students `Index` ویژگی `Enrollments` را کنار گذاشته است، زیرا این ویژگی دارای collection است. در صفحه `Details،` محتویات collection را در یک جدول HTML نمایش می دهید.

در Controllers\StudentController.cs، متد اکشن برای ویو  `Details ` از متد Find برای یافتن یک موجودیت `Student` استفاده می کند.

```C#

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


**نکته: Route data**
<p dir="rtl">
Route data داده‌ای است که model binder در بخش URL مشخص شده در جدول  routing یافت می‌شود. به عنوان مثال، default route بخش های controller، action و id را مشخص می کند:
نکته: Route_data
Route_data داده‌ای است که model_binder در بخش URL مشخص شده در جدول _routing یافت می‌شود. به عنوان مثال، default_route بخش های _controller، _action و _id را مشخص می کند:
</p>


```C#

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

```C#

@Html.ActionLink("Select", "Index", new { id = item.PersonID  })
```
یا 

```html

<a href="/Select/Index/@item.PersonID">Select</a>
```

در کد زیر، courseID با پارامتری در default route مطابقت ندارد، بنابراین به عنوان query string اضافه می‌شود.

```C#

@Html.ActionLink("Select", "Index", new { courseID = item.CourseID })
```
یا

```html

<a href="/Select/Index/?courseID=@item.CourseID">Select</a>
```

## برای ایجاد صفحه Details

1. فایل Views\Student\Details.cshtml را باز کنید.

همانطور که در مثال زیر نشان داده شده است، هر فیلد با استفاده از یک `DisplayFor` Helper نمایش داده می شود:

```C#

<dt>
    @Html.DisplayNameFor(model => model.LastName)
</dt>
<dd>
    @Html.DisplayFor(model => model.LastName)
</dd>
```

2. پس از قسمت `EnrollmentDate `و بلافاصله قبل از بسته شدن تگ `</dl>`، کدی که در زیر نشان داده شده است را اضافه کنید تا لیستی از ثبت نام ها نمایش داده شود

```C#
<dt>
            @Html.DisplayNameFor(model => model.EnrollmentDate)
        </dt>

        <dd>
            @Html.DisplayFor(model => model.EnrollmentDate)
        </dd>

        <dt>
            @Html.DisplayNameFor(model => model.Enrollments)
        </dt>
        <dd>
            <table class="table">
                <tr>
                    <th>Course Title</th>
                    <th>Grade</th>
                </tr>
                @foreach (var item in Model.Enrollments)
                {
                    <tr>
                        <td>
                            @Html.DisplayFor(modelItem => item.Course.Title)
                        </td>
                        <td>
                            @Html.DisplayFor(modelItem => item.Grade)
                        </td>
                    </tr>
                }
            </table>
        </dd>

    </dl>
</div>
<p>
    @Html.ActionLink("Edit", "Edit", new { id = Model.ID }) |
    @Html.ActionLink("Back to List", "Index")
</p>
```

اگر پس از کپی کردن کد، code indentation اشتباه است، Ctrl+K، Ctrl+D را فشار دهید تا آن را فرمت کند.

3. صفحه Details را با starting program (Ctrl+F5) باز کنید، تب Students را انتخاب کنید و سپس روی پیوند Details برای Alexander Carson کلیک کنید. (اگر Ctrl+F5 را فشار دهید در حالی که فایل Details.cshtml باز است، با خطای HTTP 400 مواجه خواهید شد. این به این دلیل است که ویژوال استودیو سعی می کند صفحه جزئیات را اجرا کند، اما از پیوندی که دانش آموز برای نمایش مشخص شده است دسترسی پیدا نکرده است. اگر این اتفاق افتاد، «Student/Details» را از URL حذف کنید و دوباره امتحان کنید، یا مرورگر را ببندید، روی پروژه کلیک راست کرده و روی View > View in Browser کلیک کنید.)

لیست دروس و نمرات دانش آموزان منتخب را مشاهده می کنید.

4. مرورگر را ببندید.


## آپدیت صفحه Create 

در Controllers\StudentController.cs، اکشن متد Create با ویژگی HttpPost  را با کد زیر جایگزین کنید. این کد یک بلوک try-catch اضافه می کند و شناسه را از ویژگی BindAttribute برای متد scaffolded حذف می کند

```C#
[HttpPost]
[ValidateAntiForgeryToken]
public ActionResult Create([Bind(Include = "LastName, FirstMidName, EnrollmentDate")]Student student)
{
    try
    {
        if (ModelState.IsValid)
        {
            db.Students.Add(student);
            db.SaveChanges();
            return RedirectToAction("Index");
        }
    }
    catch (DataException /* dex */)
    {
        //Log the error (uncomment dex variable name and add a line here to write a log.
        ModelState.AddModelError("", "Unable to save changes. Try again, and if the problem persists see your system administrator.");
    }
    return View(student);
}
```

این کد موجودیت `Student` ایجاد شده توسط  مدل بایندر ASP.NET MVC را به مجموعه موجودیت `Students` اضافه می کند و سپس تغییرات را در پایگاه داده ذخیره می کند.
مدل بایندر به عملکرد ASP.NET MVC اشاره دارد که کار با داده های ارسال شده توسط یک فرم را برای شما آسان تر می کند. یک مدل بایندر مقادیر فرم ارسال شده را به CLR تایپ تبدیل می کند و آنها را در پارامترها به مدل بایندر ارسال می کند.
در این مورد، مدل بایندر یک موجودیت `Student` را با استفاده از مقادیر ویژگی از `فرم` کالکشن نمونه سازی می کند.

شناسه `ID` را از ویژگی Bind حذف کردید زیرا `ID` مقدار کلید اصلی است که SQL Server به صورت خودکار هنگام درج ردیف تنظیم می کند. ورودی کاربر مقدار `ID` را تعیین نمی کند.


**هشدار امنیتی** - ویژگی `ValidateAntiForgeryToken` به جلوگیری از حملات جعل درخواست cross-site کمک می کند. به یک عبارت `Html.AntiForgeryToken()` مربوطه در view نیاز دارد که بعداً خواهید دید.

ویژگی Bind یکی از راه‌های محافظت در برابر over_posting در سناریوهای Create است. برای مثال، فرض کنید موجودیت Student شامل یک ویژگی Secret است که نمی‌خواهید دراین صفحه آن را ست کنید.

```C#
public class Student
{
    public int ID { get; set; }
    public string LastName { get; set; }
    public string FirstMidName { get; set; }
    public DateTime EnrollmentDate { get; set; }
    public string Secret { get; set; }

    public virtual ICollection<Enrollment> Enrollments { get; set; }
}
```

حتی اگر یک فیلد `Secret` در صفحه وب ندارید، یک هکر می تواند از ابزاری مانند  fiddler یا نوشتن مقداری کد جاوا اسکریپت برای ارسال مقدار فرم `Secret` استفاده کند.
بدون اینکه ویژگی BindAttribute فیلدهایی را که مدل بایندر هنگام ایجاد یک نمونه `Student` استفاده می کند محدود کند، مدل بایندر آن مقدار فرم `Secret` را انتخاب کرده و از آن برای ایجاد نمونه موجودیت `Student` استفاده می کند.
سپس هر مقداری که هکر برای فیلد فرم `Secret` مشخص کرده باشد در پایگاه داده شما به روز می شود. تصویر زیر ابزار fiddler را نشان می دهد که فیلد `Secret` (با مقدار "OverPost") را به مقادیر فرم ارسال شده اضافه می کند.

![fiddler image](image5.png)

سپس مقدار "OverPost" با موفقیت به ویژگی `Secret` ردیف درج شده اضافه می شود، اگرچه هرگز قصد نداشتید که صفحه وب بتواند آن ویژگی را ست کند.

بهتر است از پارامتر `Include` با ویژگی `Bind` برای فهرست کردن صریح فیلدها استفاده کنید. همچنین می توانید از پارامتر `Exclude` برای مسدود کردن فیلدهایی که می خواهید حذف کنید استفاده کنید. دلیل امنیت بیشتر `Include` این است که وقتی یک ویژگی جدید به موجودیت اضافه می کنید، فیلد جدید به طور خودکار توسط یک لیست `Exclude` محافظت نمی شود.

شما می توانید با خواندن موجودیت از پایگاه داده و سپس فراخوانی `TryUpdateModel` و ارسال در لیست خصوصیات مجاز صریح، از ارسال .overposting در سناریوهای ویرایش جلوگیری کنید. این روشی است که در این آموزش ها استفاده می شود.

یک راه جایگزین برای جلوگیری از overposting که توسط بسیاری از توسعه‌دهندگان ترجیح داده می‌شود، استفاده از مدل‌های view به جای کلاس‌های موجودیت با اتصال مدل است. فقط ویژگی هایی را که می خواهید به روز کنید در مدل view لحاظ کنید. هنگامی که MVC model_binder به پایان رسید، ویژگی های مدل view را در نمونه موجودیت کپی کنید، به طور اختیاری با استفاده از ابزاری مانند AutoMapper. از db.Entry در entity_instance استفاده کنید تا وضعیت آن را روی Unchanged تنظیم کنید و سپس Property("PropertyName").IsModified را به true در هر ویژگی موجودیتی که در مدل view موجود است تنظیم کنید. این روش در هر دو حالت ویرایش و ایجاد سناریو کار می کند.

به غیر از ویژگی `Bind،` بلوک `try-catch` تنها تغییری است که در کد scaffo.lded ایجاد کرده‌اید. اگر در حین ذخیره شدن تغییرات، استثنایی که از DataException مشتق شده است گرفته شود، یک پیام خطای عمومی نمایش داده می شود. استثناهای DataException گاهی اوقات به‌جای خطای برنامه‌نویسی ناشی از چیزی خارجی برای برنامه است، بنابراین به کاربر توصیه می‌شود دوباره امتحان کند. اگرچه در این نمونه پیاده‌سازی نشده است، یک برنامه کاربردی کیفیت تولید، استثنا را ثبت می‌کند.

کد در Views\Student\Create.cshtml مشابه آنچه در Details.cshtml مشاهده کردید است، با این تفاوت که برای هر فیلد به جای DisplayFor از EditorFor و ValidationMessageFor کمک می شود. این هم کد مربوطه:

```C#

<div class="form-group">
    @Html.LabelFor(model => model.LastName, new { @class = "control-label col-md-2" })
    <div class="col-md-10">
        @Html.EditorFor(model => model.LastName)
        @Html.ValidationMessageFor(model => model.LastName)
    </div>
</div>
```

فایل Create.cshtml همچنین شامل @Html.AntiForgeryToken()، که با ویژگی ValidateAntiForgeryToken در کنترلر کار می کند تا از حملات جعل درخواست cross-site جلوگیری کند.

2. صفحه را با استارت برنامه اجرا کنید، سپس انتخاب تب Students و کلیک بر روی Create New.

3. نام ها را وارد کنید و یک تاریخ نامعتبر هم وارد کنید و روی Create کلیک کنید تا پیام خطا را ببینید.

این اعتبار سنجی سمت سرور است که به طور پیش فرض دریافت می کنید. در آموزش بعدی، نحوه اضافه کردن ویژگی هایی را خواهید دید که کدهایی را برای اعتبارسنجی سمت مشتری ایجاد می کنند. کد هایلایت شده زیر بررسی اعتبارسنجی مدل را در متد Create نشان می دهد.

```C#
if (ModelState.IsValid)
{
    db.Students.Add(student);
    db.SaveChanges();
    return RedirectToAction("Index");
}
```
4. تاریخ را به یک مقدار معتبر تغییر دهید و روی Create کلیک کنید تا دانش آموز جدید در صفحه Index ظاهر شود.

5. مرورگر را ببندید.


## آپدیت متد HttpPost Edit




## آپدیت صفحه Delete 



## بستن کانکشن دیتابیس

برای بستن اتصالات پایگاه داده و آزاد کردن منابعی که در اختیار دارند در اسرع وقت، پس از اتمام کار context instance را از بین ببرید.
به همین دلیل است که کد scaffolded یک متد Dispose را در انتهای کلاس `StudentController` در StudentController.cs ارائه می دهد، همانطور که در مثال زیر نشان داده شده است:

```C#

protected override void Dispose(bool disposing)
{
    if (disposing)
    {
        db.Dispose();
    }
    base.Dispose(disposing);
}
```

کلاس base `Controller` از قبل رابط `IDisposable` را پیاده‌سازی می‌کند، بنابراین این کد به سادگی یک override به متد `Dispose(bool)` اضافه می کند تا حتما آن را از Context instance حذف کند.


## هندل کردن تراکنش ها

به طور پیش فرض Entity Framework به طور ضمنی تراکنش ها را پیاده سازی می کند. در سناریوهایی که در چندین ردیف یا جدول تغییراتی ایجاد می‌کنید و سپس `SaveChanges` را فراخوانی می‌کنید، Entity Framework به‌طور خودکار مطمئن می‌شود که یا همه تغییرات شما موفقیت‌آمیز هستند یا همه ناموفق هستند.
اگر ابتدا برخی تغییرات انجام شود و سپس خطایی رخ دهد، آن تغییرات به طور خودکار برگردانده می شوند.



اکنون مجموعه کاملی از صفحات دارید که عملیات ساده CRUD را برای Student_entities انجام می دهند. شما از MVC _helpers برای تولید عناصر UI برای فیلدهای داده استفاده کردید.
