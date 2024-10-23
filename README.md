Sure! Here’s the completed **README.md** file with the necessary details on CRUD operations using AJAX, Bootstrap integration, and complete project instructions for both Windows and Linux users.

---

# ASP.NET Core 8.0 ToDo Application with AJAX, Bootstrap, and EF Core (DB-First)

A **ToDo application** built using **ASP.NET Core 8.0** and **Entity Framework Core (DB-First)**. This app demonstrates the use of **AJAX** for dynamic CRUD operations and **Bootstrap** for a responsive UI. Features include task management, filtering, and search functionality, with employee-task association.

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Create or Connect to the Database](#create-or-connect-to-the-database)
3. [Setting up the Project](#setting-up-the-project)
   - [Windows Users - UI-based Approach](#windows-users---ui-based-approach)
   - [Linux Users - Command-line or UI-based Approach](#linux-users---command-line-or-ui-based-approach)
4. [CRUD Operations with AJAX](#crud-operations-with-ajax)
   - [Create, Update, and Delete with AJAX](#create-update-and-delete-with-ajax)
   - [Display Employee and Task List](#display-employee-and-task-list)
5. [Configure the Project](#configure-the-project)
6. [Run the Application](#run-the-application)
7. [Features](#features)
8. [Contributing](#contributing)
9. [License](#license)

---

## Prerequisites

Ensure you have the following installed:

- **.NET SDK 8.0 or later**: [Download here](https://dotnet.microsoft.com/download)
- **Visual Studio 2022** (Windows) or **Visual Studio Code** (Linux)
- **SQL Server** (Windows) or **PostgreSQL/MySQL** (Linux) (for the database)
- **Entity Framework Core Tools**:
  - Run this in your terminal:
    ```bash
    dotnet tool install --global dotnet-ef
    ```

---

## Create or Connect to the Database

1. **Open SQL Server Management Studio (SSMS)** or another SQL client and run the following SQL script to create the database and tables:

```sql
CREATE DATABASE ToDoAppDB;
GO

USE ToDoAppDB;
GO

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

---

## Setting up the Project

### Windows Users - UI-based Approach

1. **Step 1: Open Visual Studio 2022**
   - Launch **Visual Studio** and select **Create a new project**.
   - Search for **ASP.NET Core Web App (Model-View-Controller)** and click **Next**.
   - Set the project name to `TodoApp`, specify the location, and click **Create**.

2. **Step 2: Set Target Framework**
   - Select **.NET 8.0** as the target framework and click **Create**.

3. **Step 3: Scaffold the Database (DB-First Approach)**
   - In **Solution Explorer**, right-click on the project and choose **Manage NuGet Packages**.
   - Search for and install the following packages:
     - `Microsoft.EntityFrameworkCore.SqlServer`
     - `Microsoft.EntityFrameworkCore.Design`
   
   - Now, open **Package Manager Console** (Tools > NuGet Package Manager > Package Manager Console) and run the following command to scaffold the database:
     ```bash
     Scaffold-DbContext "Server=your_server_name;Database=ToDoAppDB;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
     ```

4. **Step 4: Add a New Controller for Employee and Task**
   - Right-click on the **Controllers** folder and select **Add > Controller**.
   - Choose **MVC Controller with views, using Entity Framework**.
   - Select the **Employee** and **Task** models, then add the controllers.

---

### Linux Users - Command-line or UI-based Approach

#### Option 1: Command-line Approach

1. **Step 1: Create a New ASP.NET Core Project**
   Open a terminal and run the following commands:
   
   ```bash
   dotnet new mvc -n TodoApp
   cd TodoApp
   ```

2. **Step 2: Install EF Core Packages**
   Run the following command to install Entity Framework Core packages:
   
   ```bash
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.Design
   ```

3. **Step 3: Scaffold the Database**
   Use the following command to scaffold the database models:

   ```bash
   dotnet ef dbcontext scaffold "Server=your_server_name;Database=ToDoAppDB;User Id=your_user;Password=your_password;" Microsoft.EntityFrameworkCore.SqlServer -o Models
   ```

#### Option 2: UI-based Approach (Visual Studio Code)

1. **Step 1: Install Visual Studio Code**
   - Download and install **[Visual Studio Code](https://code.visualstudio.com/)**.
   
2. **Step 2: Create a New Project**
   - Open **Visual Studio Code**, and open the terminal by selecting **View > Terminal**.
   - In the terminal, run:
     ```bash
     dotnet new mvc -n TodoApp
     cd TodoApp
     ```

3. **Step 3: Install Extensions**
   - Install the **C# extension** in **Visual Studio Code**.
   - Install the **SQL Server (mssql)** extension for working with databases.

4. **Step 4: Scaffold the Database**
   - Open the terminal and run the scaffold command:
     ```bash
     dotnet ef dbcontext scaffold "Server=your_server_name;Database=ToDoAppDB;User Id=your_user;Password=your_password;" Microsoft.EntityFrameworkCore.SqlServer -o Models
     ```

---

## CRUD Operations with AJAX

### Create, Update, and Delete with AJAX

1. **AJAX for Task Creation/Editing**
   - In your **TaskController**, create actions for AJAX requests (e.g., for Create, Edit, and Delete). Use the following example for the `Create` action:

   ```csharp
   [HttpPost]
   public IActionResult Create(Task task)
   {
       if (ModelState.IsValid)
       {
           _context.Tasks.Add(task);
           _context.SaveChanges();
           return Json(new { success = true, message = "Task added successfully" });
       }
       return Json(new { success = false, message = "Failed to add task" });
   }
   ```

2. **AJAX for Task Deletion**
   - Similarly, create a delete action to handle the AJAX delete operation:

   ```csharp
   [HttpPost]
   public IActionResult Delete(int id)
   {
       var task = _context.Tasks.Find(id);
       if (task != null)
       {
           _context.Tasks.Remove(task);
           _context.SaveChanges();
           return Json(new { success = true, message = "Task deleted successfully" });
       }
       return Json(new { success = false, message = "Failed to delete task" });
   }
   ```

3. **AJAX Calls in JavaScript (View)**
   - In your view, you can use JavaScript to send AJAX requests. Here's an example of an AJAX call for adding a task:

   ```html
   <script>
   $(document).ready(function() {
       $('#addTaskForm').submit(function(event) {
           event.preventDefault();
           $.ajax({
               type: 'POST',
               url: '/Task/Create',
               data: $(this).serialize(),
               success: function(response) {
                   if (response.success) {
                       $('#message').html('<div class="alert alert-success">' + response.message + '</div>');
                       // Optionally, reload the task list
                   } else {
                       $('#message').html('<div class="alert alert-danger">' + response.message + '</div>');
                   }
               }
           });
       });

       // Function to delete a task
       window.deleteTask = function(taskId) {
           $.ajax({
               type: 'POST',
               url: '/Task/Delete',
               data: { id: taskId },
               success: function(response) {
                   if (response.success) {
                       $('#message').html('<div class="alert alert-success">' + response.message + '</div>');
                       // Optionally, reload the task list
                   } else {
                       $('#message').html('<div class="alert alert-danger">' + response.message + '</div>');
                   }
               }
           });
       };
   });
   </script>
   ```

### Display Employee and Task List

1. **Employee and Task Display**
   - In the **Index** view for tasks, display a list of tasks along with the employee name. Here’s an example:

   ```html
   <table class="table">
       <thead>
           <tr>
               <th>Title</th>
               <th>Start Date</th>
               <th>Completion Date</th>
               <th>Status</th>
               <th>Employee Name</th>
               <th>Actions</

th>
           </tr>
       </thead>
       <tbody>
           @foreach (var task in Model.Tasks)
           {
               <tr>
                   <td>@task.Title</td>
                   <td>@task.TaskStartDate.ToShortDateString()</td>
                   <td>@task.TaskCompletionDate.ToShortDateString()</td>
                   <td>@task.Status</td>
                   <td>@task.Employee.Name</td>
                   <td>
                       <button class="btn btn-danger" onclick="deleteTask(@task.Id)">Delete</button>
                       <button class="btn btn-warning" onclick="editTask(@task.Id)">Edit</button>
                   </td>
               </tr>
           }
       </tbody>
   </table>
   ```

---

## Configure the Project

- Open `appsettings.json` and configure the database connection string:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=your_server_name;Database=ToDoAppDB;Trusted_Connection=True;"
  }
}
```

- Ensure you update your **Startup.cs** or **Program.cs** to include the necessary services for MVC and EF Core.

---

## Run the Application

1. In Visual Studio (Windows), select **Debug > Start Without Debugging** (or press `Ctrl + F5`).
2. In the terminal (Linux), navigate to the project directory and run:

   ```bash
   dotnet run
   ```

3. Open a browser and navigate to `http://localhost:5000` (or the port shown in your terminal).

---

## Features

- Create, read, update, and delete tasks.
- Assign tasks to employees.
- View tasks filtered by status.
- Responsive UI with Bootstrap.

---

## Contributing

Feel free to fork this repository, make changes, and submit a pull request.

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more information.

---

This README includes step-by-step instructions for creating and configuring the project, as well as implementing AJAX operations for CRUD functionality. Let me know if you need further adjustments or additional information!
