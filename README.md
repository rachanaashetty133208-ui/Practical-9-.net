# Practical-9-.net
# Placement Management System Using ADO.NET, Data Controls & Crystal Reports

## 1. Project Description

A web-based Placement Management System developed using ASP.NET Web Forms, C#, ADO.NET and SQL Server. It manages student details, company information and placement records through an interactive dashboard.

## 2. Technologies Used

- C#
- ASP.NET Web Forms
- ADO.NET
- SQL Server
- HTML and CSS
- Visual Studio
- Crystal Reports

## 3. Project Structure

```text
PlacementManagementSystem/
│
├── Default.aspx
├── Default.aspx.cs
├── Default.aspx.designer.cs
│
├── PlacementReport.aspx
├── PlacementReport.aspx.cs
├── PlacementReport.aspx.designer.cs
│
├── Site.Master
├── Site.Master.cs
├── Site.Master.designer.cs
│
├── Web.config
├── SQLQuery1.sql
│
├── Reports/
│   └── README.txt
│
└── README.md
```

---

# 4. Database Creation

**File: `SQLQuery1.sql`**

This script creates the database, tables, relationships and sample records. Run it using SQL Server Object Explorer or SQL Server Management Studio.

```sql
USE master;
GO

IF DB_ID('PlacementDB') IS NULL
BEGIN
    CREATE DATABASE PlacementDB;
END;
GO

USE PlacementDB;
GO

-- Create Students table
IF OBJECT_ID('Students', 'U') IS NULL
BEGIN
    CREATE TABLE Students
    (
        StudentId INT IDENTITY(1,1) PRIMARY KEY,
        StudentName VARCHAR(100) NOT NULL,
        Department VARCHAR(50),
        Email VARCHAR(100),
        CGPA DECIMAL(4,2),
        GraduationYear INT
    );
END;
GO

-- Create Companies table
IF OBJECT_ID('Companies', 'U') IS NULL
BEGIN
    CREATE TABLE Companies
    (
        CompanyId INT IDENTITY(1,1) PRIMARY KEY,
        CompanyName VARCHAR(100) NOT NULL,
        Location VARCHAR(100)
    );
END;
GO

-- Create Placements table
IF OBJECT_ID('Placements', 'U') IS NULL
BEGIN
    CREATE TABLE Placements
    (
        PlacementId INT IDENTITY(1,1) PRIMARY KEY,
        StudentId INT UNIQUE,
        CompanyId INT,
        JobRole VARCHAR(100),
        PackageLPA DECIMAL(10,2),
        PlacementDate DATE,
        Status VARCHAR(30),

        CONSTRAINT FK_Placements_Students
        FOREIGN KEY (StudentId)
        REFERENCES Students(StudentId),

        CONSTRAINT FK_Placements_Companies
        FOREIGN KEY (CompanyId)
        REFERENCES Companies(CompanyId)
    );
END;
GO

-- Insert sample students only when the table is empty
IF NOT EXISTS (SELECT 1 FROM Students)
BEGIN
    INSERT INTO Students
    (StudentName, Department, Email, CGPA, GraduationYear)
    VALUES
    ('Rahul Sharma', 'Computer Science', 'rahul@gmail.com', 8.50, 2026),
    ('Priya Patil', 'Information Science', 'priya@gmail.com', 9.10, 2026),
    ('Amit Kumar', 'AI and Data Science', 'amit@gmail.com', 8.20, 2026),
    ('Sneha Rao', 'Computer Science', 'sneha@gmail.com', 8.80, 2026),
    ('Kiran Joshi', 'Electronics', 'kiran@gmail.com', 7.90, 2026);
END;
GO

-- Insert sample companies only when the table is empty
IF NOT EXISTS (SELECT 1 FROM Companies)
BEGIN
    INSERT INTO Companies (CompanyName, Location)
    VALUES
    ('TCS', 'Mumbai'),
    ('Infosys', 'Bangalore'),
    ('Wipro', 'Bangalore');
END;
GO

-- Insert sample placement records only when the table is empty
IF NOT EXISTS (SELECT 1 FROM Placements)
BEGIN
    INSERT INTO Placements
    (StudentId, CompanyId, JobRole, PackageLPA, PlacementDate, Status)
    SELECT
        s.StudentId,
        c.CompanyId,
        'Software Developer',
        6.50,
        '2026-06-10',
        'Placed'
    FROM Students s
    CROSS JOIN Companies c
    WHERE s.StudentName = 'Rahul Sharma'
      AND c.CompanyName = 'TCS';

    INSERT INTO Placements
    (StudentId, CompanyId, JobRole, PackageLPA, PlacementDate, Status)
    SELECT
        s.StudentId,
        c.CompanyId,
        'System Engineer',
        5.00,
        '2026-06-12',
        'Placed'
    FROM Students s
    CROSS JOIN Companies c
    WHERE s.StudentName = 'Priya Patil'
      AND c.CompanyName = 'Infosys';

    INSERT INTO Placements
    (StudentId, CompanyId, JobRole, PackageLPA, PlacementDate, Status)
    SELECT
        s.StudentId,
        c.CompanyId,
        'Web Developer',
        4.50,
        '2026-06-15',
        'Placed'
    FROM Students s
    CROSS JOIN Companies c
    WHERE s.StudentName = 'Sneha Rao'
      AND c.CompanyName = 'Wipro';
END;
GO

-- Display records
SELECT * FROM Students;
SELECT * FROM Companies;
SELECT * FROM Placements;
GO
```

**Note:** If you already created the tables and inserted records, do not repeatedly execute separate INSERT statements. The script above checks whether sample tables contain data before inserting.

---

# 5. Web Configuration

**File: `Web.config`**

```xml
<?xml version="1.0" encoding="utf-8"?>

<configuration>

  <connectionStrings>
    <add name="PlacementDB"
         connectionString="Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=PlacementDB;Integrated Security=True;"
         providerName="System.Data.SqlClient" />
  </connectionStrings>

  <system.web>
    <compilation debug="true" targetFramework="4.8" />
    <httpRuntime targetFramework="4.8" />
    <pages controlRenderingCompatibilityVersion="4.0" />
  </system.web>

</configuration>
```

---

# 6. Master Page

## File: `Site.Master`

```aspx
<%@ Master Language="C#" AutoEventWireup="true"
    CodeBehind="Site.Master.cs"
    Inherits="p9.SiteMaster" %>

<!DOCTYPE html>
<html>
<head runat="server">
    <title>Placement Management System</title>

    <meta name="viewport"
          content="width=device-width, initial-scale=1" />

    <style>
        body {
            margin: 0;
            font-family: Arial, sans-serif;
            background: #f4f6f9;
        }

        .header {
            background: #172554;
            color: white;
            padding: 20px;
            text-align: center;
        }

        .navbar {
            background: #1e40af;
            padding: 14px;
            text-align: center;
        }

        .navbar a {
            color: white;
            text-decoration: none;
            margin: 0 18px;
            font-weight: bold;
        }

        .container {
            padding: 25px;
            min-height: 450px;
        }

        .footer {
            background: #172554;
            color: white;
            text-align: center;
            padding: 15px;
        }
    </style>

    <asp:ContentPlaceHolder ID="head" runat="server">
    </asp:ContentPlaceHolder>
</head>

<body>
    <form id="form1" runat="server">

        <div class="header">
            <h1>Placement Management System</h1>
            <p>Student Placement Dashboard</p>
        </div>

        <div class="navbar">
            <a href="Default.aspx">Dashboard</a>
            <a href="PlacementReport.aspx">Reports</a>
        </div>

        <div class="container">
            <asp:ContentPlaceHolder ID="MainContent"
                runat="server">
            </asp:ContentPlaceHolder>
        </div>

        <div class="footer">
            <p>Placement Management System | ASP.NET Web Forms</p>
        </div>

    </form>
</body>
</html>
```

## File: `Site.Master.cs`

```csharp
using System;

namespace p9
{
    public partial class SiteMaster : System.Web.UI.MasterPage
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }
    }
}
```

## File: `Site.Master.designer.cs`

```csharp
namespace p9
{
    public partial class SiteMaster
    {
        protected global::System.Web.UI.HtmlControls.HtmlHead head;

        protected global::System.Web.UI.WebControls.ContentPlaceHolder
            headContent;

        protected global::System.Web.UI.HtmlControls.HtmlForm form1;

        protected global::System.Web.UI.WebControls.ContentPlaceHolder
            MainContent;
    }
}
```

---

# 7. Placement Dashboard

## File: `Default.aspx`

```aspx
<%@ Page Title="Dashboard"
    Language="C#"
    MasterPageFile="~/Site.Master"
    AutoEventWireup="true"
    CodeBehind="Default.aspx.cs"
    Inherits="p9.Default" %>

<asp:Content ID="Content1"
    ContentPlaceHolderID="MainContent"
    runat="server">

    <style>
        .dashboard-title {
            color: #172554;
            margin-bottom: 25px;
        }

        .cards {
            display: flex;
            flex-wrap: wrap;
            gap: 20px;
            margin-bottom: 30px;
        }

        .card {
            background: white;
            padding: 25px;
            flex: 1;
            min-width: 170px;
            border-radius: 10px;
            box-shadow: 0 2px 8px #ccc;
            text-align: center;
        }

        .card h2 {
            color: #1e40af;
            font-size: 30px;
        }

        .table {
            width: 100%;
            border-collapse: collapse;
            background: white;
        }

        .table th {
            background: #1e40af;
            color: white;
            padding: 12px;
        }

        .table td {
            padding: 10px;
            border: 1px solid #ddd;
            text-align: center;
        }
    </style>

    <h2 class="dashboard-title">Placement Dashboard</h2>

    <div class="cards">

        <div class="card">
            <h3>Total Students</h3>
            <h2>
                <asp:Label ID="lblStudents"
                    runat="server" Text="0" />
            </h2>
        </div>

        <div class="card">
            <h3>Placed Students</h3>
            <h2>
                <asp:Label ID="lblPlaced"
                    runat="server" Text="0" />
            </h2>
        </div>

        <div class="card">
            <h3>Total Companies</h3>
            <h2>
                <asp:Label ID="lblCompanies"
                    runat="server" Text="0" />
            </h2>
        </div>

        <div class="card">
            <h3>Average Package</h3>
            <h2>
                <asp:Label ID="lblPackage"
                    runat="server" Text="0" />
            </h2>
        </div>

    </div>

    <h3>Student Placement Records</h3>

    <asp:GridView ID="gvPlacements"
        runat="server"
        AutoGenerateColumns="true"
        CssClass="table"
        EmptyDataText="No placement records found.">
    </asp:GridView>

</asp:Content>
```

## File: `Default.aspx.cs`

```csharp
using System;
using System.Configuration;
using System.Data;
using System.Data.SqlClient;

namespace p9
{
    public partial class Default : System.Web.UI.Page
    {
        string connectionString =
            ConfigurationManager.ConnectionStrings[
                "PlacementDB"].ConnectionString;

        protected void Page_Load(object sender, EventArgs e)
        {
            if (!IsPostBack)
            {
                LoadDashboard();
                LoadPlacementRecords();
            }
        }

        private void LoadDashboard()
        {
            using (SqlConnection con =
                new SqlConnection(connectionString))
            {
                con.Open();

                string studentQuery =
                    "SELECT COUNT(*) FROM Students";

                using (SqlCommand cmd =
                    new SqlCommand(studentQuery, con))
                {
                    lblStudents.Text =
                        cmd.ExecuteScalar().ToString();
                }

                string placedQuery =
                    "SELECT COUNT(*) FROM Placements " +
                    "WHERE Status = 'Placed'";

                using (SqlCommand cmd =
                    new SqlCommand(placedQuery, con))
                {
                    lblPlaced.Text =
                        cmd.ExecuteScalar().ToString();
                }

                string companyQuery =
                    "SELECT COUNT(*) FROM Companies";

                using (SqlCommand cmd =
                    new SqlCommand(companyQuery, con))
                {
                    lblCompanies.Text =
                        cmd.ExecuteScalar().ToString();
                }

                string packageQuery =
                    "SELECT AVG(PackageLPA) FROM Placements " +
                    "WHERE Status = 'Placed'";

                using (SqlCommand cmd =
                    new SqlCommand(packageQuery, con))
                {
                    object result = cmd.ExecuteScalar();

                    if (result != DBNull.Value)
                    {
                        lblPackage.Text =
                            Convert.ToDecimal(result)
                            .ToString("0.00") + " LPA";
                    }
                    else
                    {
                        lblPackage.Text = "0.00 LPA";
                    }
                }
            }
        }

        private void LoadPlacementRecords()
        {
            using (SqlConnection con =
                new SqlConnection(connectionString))
            {
                string query = @"
                    SELECT
                        s.StudentName,
                        s.Department,
                        c.CompanyName,
                        p.JobRole,
                        p.PackageLPA,
                        p.PlacementDate,
                        p.Status
                    FROM Placements p
                    INNER JOIN Students s
                        ON p.StudentId = s.StudentId
                    INNER JOIN Companies c
                        ON p.CompanyId = c.CompanyId
                    ORDER BY p.PlacementDate DESC";

                using (SqlDataAdapter adapter =
                    new SqlDataAdapter(query, con))
                {
                    DataTable dt = new DataTable();

                    adapter.Fill(dt);

                    gvPlacements.DataSource = dt;
                    gvPlacements.DataBind();
                }
            }
        }
    }
}
```

## File: `Default.aspx.designer.cs`

```csharp
namespace p9
{
    public partial class Default
    {
        protected global::System.Web.UI.WebControls.Label
            lblStudents;

        protected global::System.Web.UI.WebControls.Label
            lblPlaced;

        protected global::System.Web.UI.WebControls.Label
            lblCompanies;

        protected global::System.Web.UI.WebControls.Label
            lblPackage;

        protected global::System.Web.UI.WebControls.GridView
            gvPlacements;
    }
}
```

---

# 8. Placement Report Page

## File: `PlacementReport.aspx`

This basic report page works without Crystal Reports installed. It provides a place to add the Crystal Reports viewer after installing the required SAP components.

```aspx
<%@ Page Title="Placement Reports"
    Language="C#"
    MasterPageFile="~/Site.Master"
    AutoEventWireup="true"
    CodeBehind="PlacementReport.aspx.cs"
    Inherits="p9.PlacementReport" %>

<asp:Content ID="Content1"
    ContentPlaceHolderID="MainContent"
    runat="server">

    <h2>Placement Reports</h2>

    <p>
        This page is used for generating placement reports
        using Crystal Reports.
    </p>

    <asp:Label ID="lblReportMessage"
        runat="server"
        Text="Install SAP Crystal Reports Developer Edition and configure a .rpt report file to enable report viewing."
        ForeColor="Blue">
    </asp:Label>

</asp:Content>
```

## File: `PlacementReport.aspx.cs`

```csharp
using System;

namespace p9
{
    public partial class PlacementReport : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
            if (!IsPostBack)
            {
                lblReportMessage.Text =
                    "Crystal Reports setup is required to display reports.";
            }
        }
    }
}
```

## File: `PlacementReport.aspx.designer.cs`

```csharp
namespace p9
{
    public partial class PlacementReport
    {
        protected global::System.Web.UI.WebControls.Label
            lblReportMessage;
    }
}
```

---

# 9. Crystal Reports Integration

To enable actual Crystal Reports functionality:

1. Install SAP Crystal Reports Developer Edition for Visual Studio.
2. Install the matching Crystal Reports runtime.
3. Restart Visual Studio.
4. Create a Crystal Report named `PlacementReport.rpt`.
5. Connect the report to the `PlacementDB` database.
6. Add the `CrystalReportViewer` control to `PlacementReport.aspx`.
7. Add the required Crystal Reports assemblies to the project.

### Example Crystal Reports code-behind

After installing Crystal Reports and adding its references, replace the basic report page code-behind with:

```csharp
using System;
using CrystalDecisions.CrystalReports.Engine;
using CrystalDecisions.Shared;

namespace p9
{
    public partial class PlacementReport : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
            if (!IsPostBack)
            {
                ReportDocument report =
                    new ReportDocument();

                string reportPath =
                    Server.MapPath(
                        "~/Reports/PlacementReport.rpt");

                report.Load(reportPath);

                report.SetDatabaseLogon(
                    "",
                    "",
                    @"(localdb)\MSSQLLocalDB",
                    "PlacementDB");

                CrystalReportViewer1.ReportSource = report;
                CrystalReportViewer1.DataBind();

                Session["PlacementReport"] = report;
            }
            else
            {
                if (Session["PlacementReport"] != null)
                {
                    CrystalReportViewer1.ReportSource =
                        Session["PlacementReport"];
                }
            }
        }

        protected void Page_Unload(object sender, EventArgs e)
        {
            // Dispose the report when appropriate
            // after the viewer has finished using it.
        }
    }
}
```

Add the Crystal Reports viewer to the ASPX page after installation:

```aspx
<%@ Register Assembly="CrystalDecisions.Web"
    Namespace="CrystalDecisions.Web"
    TagPrefix="CR" %>

<CR:CrystalReportViewer
    ID="CrystalReportViewer1"
    runat="server"
    AutoDataBind="true"
    Width="100%"
    Height="600px" />
```

**Important:** The Crystal Reports example is optional until the SAP assemblies, viewer control and actual `PlacementReport.rpt` file are installed and configured. The basic project pages above do not require those assemblies.

---

# 10. ADO.NET Components Used

| Component | Purpose |
|---|---|
| SqlConnection | Connects to SQL Server |
| SqlCommand | Executes SQL queries |
| SqlDataAdapter | Retrieves database records |
| DataTable | Stores retrieved data |
| ExecuteScalar() | Retrieves a single value |
| GridView | Displays placement records |

---

# 11. How to Run the Project

1. Install Visual Studio with ASP.NET and web development support.
2. Open the project in Visual Studio.
3. Connect to `(localdb)\MSSQLLocalDB`.
4. Execute `SQLQuery1.sql`.
5. Check that `PlacementDB` and its three tables are created.
6. Verify the connection string in `Web.config`.
7. Build the project.
8. Right-click `Default.aspx` and select **Set As Start Page**.
9. Press **F5** to run the application.
10. Open the Reports link to view the report setup page.

---

# 12. Expected Output

The dashboard displays:

- Total number of students
- Number of placed students
- Total number of companies
- Average placement package
- Student placement records in a GridView

The report page provides the starting point for Crystal Reports integration.

---

# 13. Conclusion

The Placement Management System provides a centralized way to manage student, company and placement information. It uses ASP.NET Web Forms, C#, ADO.NET and SQL Server to retrieve and display placement data through an interactive dashboard. Crystal Reports can be integrated to generate detailed placement reports.

---

**Project Title:** Placement Management System Using ADO.NET, Data Controls & Crystal Reports

**Framework:** ASP.NET Web Forms (.NET Framework 4.8)

**Programming Language:** C#

**Database:** SQL Server LocalDB
