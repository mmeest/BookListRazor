<p align="center"><img src="https://user-images.githubusercontent.com/34022590/112759777-385c4100-8ffd-11eb-9725-8c1411b7f299.png" width="300px"></p>

<h1 align="center">
    <strong>Book List Razor</strong>
</h1>
<h3 align="center">
    <p>Book List in ASP.NET Core with Razor pages</p>
</h3>
<h3 align="center">
    <a target="_blank" href="https://visualstudio.microsoft.com/">Visual Studio</a>
    <span> . </span>    
    <a target="_blank" href="https://dotnet.microsoft.com/download">.NET Core</a>
    <span> . </span>    
    <a target="_blank" href="https://www.microsoft.com/en-us/sql-server/sql-server-downloads">SQL Server 2019</a>
    <span> . </span>
    <a target="_blank" href="https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15">MS SQL Management Studio</a>
    <span> . </span>
    <a target="_blank" href="https://sweetalert2.github.io/">Sweet Alert 2</a>
    <span> . </span>
    <a target="_blank" href="https://codeseven.github.io/toastr/">toastr</a>
    <span> . </span>
    <a target="_blank" href="https://datatables.net/">Datatables</a>
    <span> . </span>
    
    
  <a target="_blank" href="https://restfulapi.net/">restfulapi.net</a>
	<span> · </span>
  <a target="_blank" href="https://nodejs.org/en/">Node.js</a>
	<span> · </span>
  <a target="_blank" href="https://swagger.io/specification/">OpenAPI</a>
	<span> · </span>
  <a target="_blank" href="https://www.postman.com/">Postman</a>
	<span> · </span>
  <a target="_blank" href="https://insomnia.rest/">Insomnia</a>
</h3>
<br><br>

<hr>

### Contents
- [Tools needed](#tools-needed)
- [Packages](#packages)
- [Files](#files)
- [Tag Helpers](#tag-helpers)
- [Creating App](#creating-app)
- [Database Connection](#database-connection)
- [Book List View](#book-list-view)
- [Create View](#create-view)
- [Edit View](#edit-view)
- [Delete Function](#delete-function)
- [Validation Script](#validation-script)

<hr>

### Tools needed
* Visual Studio 2019                - https://visualstudio.microsoft.com/
* .NET Core                         - https://dotnet.microsoft.com/download
* SQL Server 2019                   - https://www.microsoft.com/en-us/sql-server/sql-server-downloads
* MS SQL Server Management Studio   - https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15

For styling:
* SweetAlert2                       - https://sweetalert2.github.io/
* toastr                            - https://codeseven.github.io/toastr/
* DataTables                        - https://datatables.net/

<hr>

### Packages
In nuget package Manager we will add
* Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation
* Microsoft.EntityFrameworkCore
* Microsoft.EntityFrameworkCore.SqlServer
* Microsoft.EntityFrameworkCore.Tools

<hr>

### Files
| Type | Name | Purpose |
|-|-|-|
| setting | ./appsetings.json | Containing logging and connectionstring for database |
| program | ./Program.cs | Containing main method for starting our App |
| program | ./Startup.cs | Containing configure services, configure method and middlewares |
| setting | ./Pages/Shared/__ValidationScriptsPartial.cshtml | For JS or jQuery scripts for validation |
| view | ./Pages/Shared/__Layout.cshtml | Main components for our app page |
| view | ./Pages/Index.cshtml | First main view of our application |
| view | ./Pages/Booklist/Index.cshtml | Main list page for displaying books table |
| view | ./Pages/Booklist/Create.cshtml | Page for inserting new book in database |
| view | ./Pages/Booklist/Edit.cshtml | Page for editing existing book in database |
| view | ./Pages/Booklist/Upsert.cshtml | Page for creating new and editing existing books in database |
| model | ./Pages/Index.cshtml.cs | Model for main page |
| model | ./Model/Book.cs | Book model for book objects in our database |
| model | ./Model/ApplicationDbContext.cs | Database model |
| model | ./Pages/Booklist/Index.cshtml.cs | Model for our book list page |
| model | ./Pages/Booklist/Create.cshtml.cs | Model for creating new db record |
| model | ./Pages/Booklist/Edit.cshtml.cs | Model for editing db record |
| model | ./Pages/Booklist/Upsert.cshtml.cs | Model for creating new or editing existing db record |
| script | ./wwwroot/js/BookList.js | For displaying database records with JavaScript table |

<hr>

### Tag Helpers
https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/intro?view=aspnetcore-5.0
* Tag Helpers are introudced with ASP.NET Core
* Tag Helpers enable server-side code to participate in creating and rendering HTML elements in Razor files
* Tag Helpers are very focused around the html elements and much more natural to use

| Tag Helper | Usage |
|-|-|
| asp-area | For routing |
| asp-page="/Index" | For routing(pages) |
| asp-for="Books.FirstOrDefault().Author | For getting author value from db record into our page |
| asp-validation-for="Book.ISBN" | For validating data input |

<hr>

### Creating App
We'll create new project with 'ASP.NET Core Web Application with Razor pages' template and name it 'BookListRazor'

We add needed packages to our project.\
And in 'Startup.cs' add Razor pages service
```
services.AddRazorPages().AddRazorRuntimeCompilation();
```

We'll add 'Model' folder and inside that class 'Book.cs'
```
using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using System.Linq;
using System.Threading.Tasks;

namespace BookListRazor.Model
{
    public class Book
    {
        [Key]
        public int Id { get; set; }
        [Required]
        public string Name { get; set; }
        public string Author { get; set; }
        public string ISBN { get; set; }
    }
}
```

<hr>

### Database Connection
In Microsoft SQL Server Management Studio we'll get server name for our connection\
and create connectionstring in 'appsetings.json' something like that
```
  "ConnectionStrings": {
    "DefaultConnection": "Server=DESKTOP-JVV34R1\\SQLEXPRESS02;Database=BookListRazor;Trusted_Connection=True;MultipleActiveResultSets=True"
  },
```

And we add in Model folder 'ApplicationDbContext.cs' class
```
using Microsoft.EntityFrameworkCore;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace BookListRazor.Model
{
    public class ApplicationDbContext : DbContext           // inherit from EntityFramework 'DbContext' class
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options)
        {

        }
        public DbSet<Book> Book { get; set; }
    }
}
```

And we add dbContext service in 'Startup.cs'
```
services.AddDbContext<ApplicationDbContext>(option => option.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));
```

For next we have to add migration in terminal(creating script to use accross database)
```
add-migration AddBookToDb
```

Creating actual database and table
```
update-database
```

Now our database is created.

<hr>

<p align="center"><img src="/BookListRazor/pic/list.png" width="450px"></p>

### Book List View
In 'Pages' folder we create new folder named 'BookList' and inside that new Razor page named 'Index'

With Foreach function we create table to display database records in form
```
<div class="col-12 border p-3 mt-3">
        <form method="post">
            @if (Model.Books.Count() > 0)
            {
                <table class="table table-striped border">
                    <tr class="table-secondary">
                        <th>
                            <label asp-for="Books.FirstOrDefault().Name"></label>
                        </th>
                        <th>
                            @*@Html.DisplayNameFor(m => m.Books.FirstOrDefault().Author)*@
                            <label asp-for="Books.FirstOrDefault().Author"></label>
                        </th>
                        <th>
                            <label asp-for="Books.FirstOrDefault().ISBN"></label>
                        </th>
                        <th>

                        </th>
                    </tr>
                    @foreach (var item in Model.Books)
                    {
                        <tr>
                            <td>
                                @Html.DisplayFor(m => item.Name)
                            </td>
                            <td>
                                @Html.DisplayFor(m => item.Author)
                            </td>
                            <td>
                                @Html.DisplayFor(m => item.ISBN)
                            </td>
                            <td>
                                <button asp-page-handler="Delete" asp-route-id="@item.Id" onclick="return confirm('Are you sure you want to delete?')" class="btn btn-danger btn-sm">Delete</button>
                                <a asp-page="Edit" asp-route-id="@item.Id" class="btn bg-success btn-sm text-white">Edit</a>
                            </td>
                        </tr>
                    }
                </table>
            }
            else
            {
                <p>No books available.</p>
            }
        </form>
    </div>
```

<hr>

<p align="center"><img src="/BookListRazor/pic/create.png" width="450px"></p>

### Create view
We'll add 'Create' view in 'BookList' folder with 'Name', 'Author' and 'ISBN' fields\
and 'Create' and 'Back to List' button

<hr>

<p align="center"><img src="/BookListRazor/pic/edit.png" width="450px"></p>

### Edit view
We'll add 'Edit' view in 'BookList' folder with 'Name', 'Author' and 'ISBN' fields\
and 'Update' and 'Back to List' button

<hr>

<p align="center"><img src="/BookListRazor/pic/delete.png" width="450px"></p>

### Delete function
We could add similar delete page for every record but we do it in our list page.\
For that we add to Delete button in our list view page
```
<button asp-page-handler="Delete" asp-route-id="@item.Id" onclick="return confirm('Are you sure you want to delete?')" class="btn btn-danger btn-sm">Delete</button>
```

<hr>

### Validation Script
For client side validation we add to 'Create.cshtml' 'Edit.cshtml' and 'Upsert.cshtml'
```
@section Scripts{
    <partial name="_ValidationScriptsPartial" />
}
```

<hr>

# HAPPY CODING !!! :)
