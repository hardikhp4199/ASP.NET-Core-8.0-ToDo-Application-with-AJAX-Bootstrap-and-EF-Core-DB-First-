Below is a detailed `README.md` file for your project, tailored for both **Windows** and **Linux** users. This will cover all the steps from creating a database, scaffolding the project using both UI and command-line approaches, and implementing the required features.

---

# ASP.NET Core 8.0 ToDo Application with AJAX, Bootstrap, and EF Core (DB-First)

A **ToDo application** built using **ASP.NET Core 8.0** and **Entity Framework Core (DB-First)**. This app demonstrates the use of **AJAX** for dynamic CRUD operations and **Bootstrap** for a responsive UI. Features include task management, filtering, and search functionality.

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Create or Connect to the Database](#create-or-connect-to-the-database)
3. [Setting up the Project](#setting-up-the-project)
   - [Windows Users - UI-based Approach](#windows-users---ui-based-approach)
   - [Linux Users - Command-line or UI-based Approach](#linux-users---command-line-or-ui-based-approach)
4. [Configure the Project](#configure-the-project)
5. [Run the Application](#run-the-application)
6. [Features](#features)
7. [Contributing](#contributing)
8. [License](#license)

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

### Step 1: Create a Database

You can either create a new database or use an existing one. Below are instructions for both:

1. **Open SQL Server Management Studio (SSMS)** or use another SQL client for your database.
2. Run the following SQL commands to create the required tables for the application:

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

4. **Step 4: Configure the Project**
   - In **appsettings.json**, add the connection string:
     ```json
     "ConnectionStrings": {
       "DefaultConnection": "Server=your_server_name;Database=ToDoAppDB;Trusted_Connection=True;"
     }
     ```

5. **Step 5: Add a New Controller**
   - Right-click on the **Controllers** folder and select **Add > Controller**.
   - Choose **MVC Controller with views, using Entity Framework**.
   - Select the **Employee** and **Task** models and add the necessary controllers.

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

## Configure the Project

1. **Add the Connection String**:
   In `appsettings.json`, add the connection string to your database:

   ```json
   "ConnectionStrings": {
       "DefaultConnection": "Server=your_server_name;Database=ToDoAppDB;User Id=your_user;Password=your_password;"
   }
   ```

2. **Register the Database Context**:
   In `Program.cs` (or `Startup.cs` for older ASP.NET versions), register the `DbContext`:

   ```csharp
   builder.Services.AddDbContext<ApplicationDbContext>(options =>
       options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));
   ```

---

## Run the Application

To run the application, use the following command:

```bash
dotnet run
```

Or if you are using **Visual Studio**, simply press `F5` to build and run the project.

---

## Features

- **AJAX-based CRUD**: Use AJAX calls to create, update, and delete tasks.
- **Bootstrap for UI**: Responsive interface with Bootstrap components.
- **Task Filtering and Search**: Search and filter tasks by title and status.
- **DB-First Approach**: Database scaffolding using Entity Framework Core.

---

## Contributing

Feel free to submit issues or pull requests if you find bugs or want to improve the application.

---

## License

This project is licensed under the MIT License.

---

This `README.md` provides clear steps for both **Windows** and **Linux** users with UI and command-line options. It includes everything from creating the database, setting up the project, and running the application.
