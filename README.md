# ASP.NET-Core-8.0-ToDo-Application-with-AJAX-Bootstrap-and-EF-Core-DB-First-
A ToDo app built with ASP.NET Core 8.0 using Entity Framework Core (DB-First) and AJAX for dynamic CRUD operations. Features Bootstrap for a responsive UI, task management, filtering, and search functionality. Learn modern ASP.NET practices, including validation, foreign key relationships, and seamless AJAX integration for a smooth user experience.

To develop a **ToDo application** using ASP.NET Core 8.0 (with EF Core for the DB-first approach) and AJAX calls for handling CRUD operations, I will walk you through the necessary steps. I'll include the schema setup, database-first approach, AJAX functionality, Bootstrap UI, filtering, and view options.

### Steps to Create the Application

#### 1. **Database Schema**

You have two tables: `Employee` and `Task`. Below is the schema in SQL:

```sql
CREATE TABLE Employee (
    Id INT IDENTITY(1,1) PRIMARY KEY,
    Name NVARCHAR(100) NOT NULL,
    DOB DATE,
    Email NVARCHAR(100),
    MobileNumber NVARCHAR(20),
    Gender NVARCHAR(10),
    JoiningDate DATE,
    Experience INT
);

CREATE TABLE Task (
    Id INT IDENTITY(1,1) PRIMARY KEY,
    Title NVARCHAR(200),
    TaskStartDate DATE,
    TaskCompletionDate DATE,
    Status NVARCHAR(50),
    EmployeeId INT FOREIGN KEY REFERENCES Employee(Id)
);
```

#### 2. **DB-First Approach Setup**

You will generate the database context and models from the database schema.

1. **Install EF Core Tools:**
    ```bash
    dotnet tool install --global dotnet-ef
    dotnet add package Microsoft.EntityFrameworkCore.SqlServer
    dotnet add package Microsoft.EntityFrameworkCore.Design
    ```

2. **Scaffold DB Context (for Windows/Linux):**
   ```bash
   dotnet ef dbcontext scaffold "Server=your_server_name;Database=your_database_name;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models
   ```

This will create models for the `Employee` and `Task` tables and a `DbContext` to interact with the database.

#### 3. **ASP.NET Core 8.0 Project Setup**

1. **Create the Project (Windows/Linux):**
    ```bash
    dotnet new mvc -n TodoApp
    cd TodoApp
    ```

2. **Add Database Connection String:**

   In `appsettings.json`, add the connection string:

   ```json
   "ConnectionStrings": {
      "DefaultConnection": "Server=your_server_name;Database=your_database_name;Trusted_Connection=True;"
   }
   ```

3. **Modify `Startup.cs` to add EF Core context:**

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDbContext<ApplicationDbContext>(options =>
           options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

       services.AddControllersWithViews();
   }
   ```

#### 4. **Create the Employee and Task Controllers**

1. Use scaffolding to create the controllers and views:

    ```bash
    dotnet aspnet-codegenerator controller -name EmployeeController -m Employee -dc ApplicationDbContext -outDir Controllers -async -udl
    dotnet aspnet-codegenerator controller -name TaskController -m Task -dc ApplicationDbContext -outDir Controllers -async -udl
    ```

2. **AJAX Setup for Task CRUD:**
   In the `TaskController`, create a method to handle AJAX requests:

   ```csharp
   [HttpPost]
   public async Task<IActionResult> CreateTask(Task task)
   {
       if (ModelState.IsValid)
       {
           _context.Add(task);
           await _context.SaveChangesAsync();
           return Json(new { success = true, message = "Task created successfully" });
       }
       return Json(new { success = false, message = "Validation failed" });
   }
   ```

#### 5. **View Setup using Bootstrap**

1. **Layout with Menu (including ToDo section):**
   
   In `Views/Shared/_Layout.cshtml`, update the navigation menu to include options for employees and tasks:

   ```html
   <nav class="navbar navbar-expand-lg navbar-light bg-light">
       <div class="collapse navbar-collapse" id="navbarNav">
           <ul class="navbar-nav">
               <li class="nav-item">
                   <a class="nav-link" href="/Employee">Employees</a>
               </li>
               <li class="nav-item">
                   <a class="nav-link" href="/Task">Tasks</a>
               </li>
           </ul>
       </div>
   </nav>
   ```

2. **Task View with Form Validation (No Alerts, Inline Error Messages):**

   In `Views/Task/Create.cshtml`:

   ```html
   <form id="taskForm">
       <div class="form-group">
           <label for="Title">Title</label>
           <input type="text" class="form-control" id="Title" name="Title" required />
           <span asp-validation-for="Title" class="text-danger"></span>
       </div>
       <div class="form-group">
           <label for="TaskStartDate">Start Date</label>
           <input type="date" class="form-control" id="TaskStartDate" name="TaskStartDate" required />
           <span asp-validation-for="TaskStartDate" class="text-danger"></span>
       </div>
       <button type="submit" class="btn btn-primary">Save</button>
   </form>
   <div id="message"></div>
   ```

#### 6. **AJAX Call for Submitting the Form**

Use AJAX to handle the form submission:

```html
<script>
$(document).ready(function () {
    $('#taskForm').on('submit', function (e) {
        e.preventDefault();
        $.ajax({
            url: '/Task/CreateTask',
            type: 'POST',
            data: $(this).serialize(),
            success: function (result) {
                if (result.success) {
                    $('#message').html('<div class="alert alert-success">' + result.message + '</div>');
                } else {
                    $('#message').html('<div class="alert alert-danger">' + result.message + '</div>');
                }
            }
        });
    });
});
</script>
```

#### 7. **Listing Page with Custom Filters and Search**

1. In `Views/Task/Index.cshtml`, add the filtering and search UI:

   ```html
   <input type="text" id="searchBox" class="form-control" placeholder="Search by Title" />
   <select id="statusFilter" class="form-control">
       <option value="">All</option>
       <option value="Pending">Pending</option>
       <option value="Completed">Completed</option>
   </select>
   <table class="table table-bordered" id="taskTable">
       <thead>
           <tr>
               <th>Title</th>
               <th>Start Date</th>
               <th>Status</th>
           </tr>
       </thead>
       <tbody>
           @foreach (var task in Model)
           {
               <tr>
                   <td>@task.Title</td>
                   <td>@task.TaskStartDate.ToShortDateString()</td>
                   <td>@task.Status</td>
               </tr>
           }
       </tbody>
   </table>
   ```

2. **AJAX Filter and Search:**

   ```html
   <script>
   $(document).ready(function () {
       $('#searchBox, #statusFilter').on('input change', function () {
           $.ajax({
               url: '/Task/Search',
               type: 'GET',
               data: {
                   search: $('#searchBox').val(),
                   status: $('#statusFilter').val()
               },
               success: function (data) {
                   $('#taskTable tbody').html(data);
               }
           });
       });
   });
   </script>
   ```

#### 8. **Custom Filter Method in Controller**

In `TaskController.cs`:

```csharp
[HttpGet]
public IActionResult Search(string search, string status)
{
    var tasks = _context.Tasks.AsQueryable();

    if (!string.IsNullOrEmpty(search))
    {
        tasks = tasks.Where(t => t.Title.Contains(search));
    }

    if (!string.IsNullOrEmpty(status))
    {
        tasks = tasks.Where(t => t.Status == status);
    }

    return PartialView("_TaskList", tasks.ToList());
}
```

### Conclusion

This flow demonstrates creating a **ToDo application** using the **DB-first approach** with ASP.NET Core 8.0, **AJAX** for asynchronous CRUD operations, and **Bootstrap** for the UI. The search and filtering functionalities are also implemented via AJAX for better UX. You can use the command-line instructions above for both **Windows** and **Linux** platforms.
