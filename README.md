# Incident & Employee Performance Data Analytics 

This is an interactive Power BI dashboard to track incidents of a software company and it's employees performance.

## Scenario
A product-based software company handles customer queries through a Customer Service portal. All the users raise Incidents if they encounter any problem with the product. So, the company wants to know the statistics and reports through a dashboard to understand their employee performance and identify areas for improvement.

## Problem Statement

This dashboard helps the organization understand their employee performance better. The problem is that the company needs a database to create a dashboard. So, the company hires a data engineer to create a database management system in SSMS, handle the data orchestration with the existing data first, and then make a visualization dashboard through Power BI and draw required insights.

## The formulation of a dashboard via Power BI entails the following procedural steps

- **SSMS and SQL Server Installation:** Download and Install SQL Server Management Studio and SQL server. Set up SQL server Connection in SSMS using credentials or windows authentication (Skip this step if already installed).
- **Database Creation:** Create a new Database named [Incident_Data]
- **Tables Creation:** Create tables in the following order using below create queries. 
- _**Tables Names**_ - [Department, Category, Incident_Status, Employee, Incident]
    - _**Department:**_ [Id (Primary Key), Department_Name]
    
    ```
    CREATE TABLE Department (
    Id INT PRIMARY KEY,
    Department_Name VARCHAR(255) NOT NULL
    );
    ```
    - _**Category:**_ [Id (Primary Key), Issue_Type]

    ```
    CREATE TABLE Category (
    Id INT PRIMARY KEY,
    Issue_Type VARCHAR(255) NOT NULL
    );
    ```

    - _**Incident_Status:**_ [Id (Primary Key), Status]

    ```
    CREATE TABLE Incident_Status (
    Id INT PRIMARY KEY,
    Status VARCHAR(255) NOT NULL
    );
    ```

    - _**Employee:**_ [Id (Primary Key), User_Name, Department_Id]
        
        **Employee[Department_Id] -> Department[Id]**
		
    ```
    CREATE TABLE Employee (
    Id INT PRIMARY KEY,
    User_Name VARCHAR(255) NOT NULL,
    Department_Id INT NOT NULL,
    FOREIGN KEY (Department_Id) REFERENCES Department(Id)
    );
    ```
    - _**Incident:**_ [Id (Primary Key), Incident_Status_Id, Reassignment_Count, Reopen_Count, Made_SLA, Created_By, Created_At, Assigned_To, Updated_By, Updated_At, Resolved_By, Resolved_At, Category, Urgency, Priority]
        
        **Incident[Created_By] -> Employee[Id]**
        
        **Incident[Assigned_To] -> Employee[Id]**
        
        **Incident[Updated_By] -> Employee[Id]**
        
        **Incident[Resolved_By] -> Employee[Id]**
        
        **Incident[Category] -> Category[Id]**
        
        **Incident[Incident_Status_Id] -> Incident_Status[Id]**

    ```
    CREATE TABLE Incident (
    Id VARCHAR(255) PRIMARY KEY NOT NULL,
    Incident_Status_Id INT NOT NULL,
    Reassignment_Count INT,
    Reopen_Count INT,
    Made_SLA VARCHAR(255),
    Created_By INT NOT NULL,
    Created_At VARCHAR(255) NOT NULL,
    Assigned_To INT,
    Updated_By INT,
    Updated_At VARCHAR(255),
    Resolved_By INT NULL,
    Resolved_At VARCHAR(255) NULL,
    Category INT NOT NULL,
    Urgency VARCHAR(255) NOT NULL,
    Priority VARCHAR(255) NOT NULL,
    FOREIGN KEY (Incident_Status_Id) REFERENCES Incident_Status(Id),
    FOREIGN KEY (Created_By) REFERENCES Employee(Id),
    FOREIGN KEY (Assigned_To) REFERENCES Employee(Id),
    FOREIGN KEY (Updated_By) REFERENCES Employee(Id),
    FOREIGN KEY (Resolved_By) REFERENCES Employee(Id),
    FOREIGN KEY (Category) REFERENCES Category(Id)
    );
    ```
- **ER Diagram:** Design an ER diagram in SSMS using ‘Database diagrams’

![ER_Diagram](https://github.com/Navyaka/Incident-Tracker/assets/79855759/5df89df9-450c-44dc-9bf4-2e1d7b9eec20)

- **Data Insertion:** Insert provided .csv files using ‘Import Wizard’ in SSMS with ‘Flat File’ as Data Source and ‘Microsoft OLE DB Provider for SQL Server’ as Destination Source, then verify the data insertion.

- **Data Manipulation:**
  - First Update all rows of ‘Resolved_At’ to ‘NULL’ in the ‘Incident’ Table where ‘Resolved_By’ value is ‘0’. 
  ```
  Update Incident set Resolved_At = NULL where Resolved_By = 0
  ```
  - Next, Update all rows of ‘Resolved_By’ to ‘NULL’ in the ‘Incident’ Table where ‘Resolved_By’ value is ‘0’. 
   
   ```
   Update Incident set Resolved_By = NULL where Resolved_By = 0
   ```
    - Now, Insert (3142, ‘Jane Foster’, 3) details into the ‘Employee’ table.
   ```
   Insert into Employee(Id, User_Name, Department_Id) Values (3142,'Jane Foster', 3)
   ```
   - Verify Insertion using select query.
    ```
    select * from Employee where id=3142
    ```
    - Delete that record from the table.
    ```
    Delete from Employee where Id=3142
    ```
- **Data Analysis:** Examine different metrics and trends within the dataset to gain insights and identify patterns. This involves calculating key performance indicators, analyzing data based on various categories, and identifying areas for improvement. The goal is to extract valuable insights that can inform decision-making and drive operational efficiency.

  ```
   --Total Incident Count: Count of Distinct  Incidents
   SELECT Count(DISTINCT Id) AS 'No of Incidents'
   FROM   incident
  ```
    ![Total Incident Count](https://github.com/Navyaka/Incident-Tracker/assets/79855759/39999fd8-cbff-4e0e-820f-8ee6944952bc)

  ```
  --Total Active Incident Count: Count of Distinct  Incidents which are Currently not resolved
  SELECT Count(DISTINCT Id) AS 'No of Active Incidents'
  FROM   incident
  WHERE  incident_status_id <> 6

  ```
  ![Total Active Incident Count](https://github.com/Navyaka/Incident-Tracker/assets/79855759/228b93eb-9579-4a7d-a705-7f86e03a32e7)

  ```
  --Total resolved   Incident Count: Count of Distinct  Incidents which are  resolved
   SELECT Count(DISTINCT Id) AS 'No of Resolved Incidents'
   FROM   incident
   WHERE  incident_status_id = 6

  ```
  ![Total resolved   Incident Count](https://github.com/Navyaka/Incident-Tracker/assets/79855759/c1706791-28aa-4f18-babe-600cda2fe9bf)

  ```
  --Total Incident Count Priority: Count of Distinct  Incidents categorized by priority.
   SELECT priority,
       Count(DISTINCT Id) AS 'No of Incidents'
  FROM   incident
  GROUP  BY priority 
  ```
  ![Total Incident Count Priority](https://github.com/Navyaka/Incident-Tracker/assets/79855759/f714e951-8828-4222-a664-fd41cb70ca38)

  ```
  --Total Incident Count Status: Count of Distinct  Incidents categorized by Status.
  SELECT i_s.status,
       Count(DISTINCT i.Id) AS 'No of Incidents'
  FROM   incident i
       INNER JOIN incident_status i_s
               ON i_s.id = i.incident_status_id
  GROUP  BY i_s.status 
  ```
  ![Total Incident Count Status](https://github.com/Navyaka/Incident-Tracker/assets/79855759/04233e2d-1d92-47fc-b491-1c4dd8f9dd72)

  ```
  --Total resolved   Incident Count Issue Type: Count of Distinct  Incidents categorized by Issue Type.
  SELECT c.issue_type,
       Count(DISTINCT i.Id) AS 'No of Incidents'
  FROM   incident i
       INNER JOIN category c
               ON c.id = i.incident_status_id
  GROUP  BY c.issue_type 

  ```
  ![Total resolved   Incident Count Issue Type](https://github.com/Navyaka/Incident-Tracker/assets/79855759/1a77387a-4a6f-4682-994a-110448b470e9)

  ```
  --Total resolved   Incident Count Department: Count of Distinct  Incidents categorized by Department.
  SELECT d.department_name,
       Count(DISTINCT i.Id) AS 'No of Incidents'
  FROM   incident i
       INNER JOIN employee e
               ON e.id = i.created_by
       INNER JOIN department d
               ON d.id = e.department_id
  WHERE Incident_Status_Id = 6
  GROUP  BY d.department_name

  ```
  ![Total resolved   Incident Count Department](https://github.com/Navyaka/Incident-Tracker/assets/79855759/7c189a00-5b1b-40ff-ae33-c31b90d5d9bb)

  ```
  --Department wise Category Incident Count: Break down Distinct incident counts by 'department' and 'type of issue'.
  SELECT d.department_name,
       c.issue_type,
       Count(DISTINCT i.Id) AS 'No of Incidents'
  FROM   incident i
       INNER JOIN employee e
               ON e.id = i.created_by
       INNER JOIN category c
               ON c.id = i.incident_status_id
       INNER JOIN department d
               ON d.id = e.department_id
  GROUP  BY d.department_name,
          c.issue_type
  ORDER  BY d.department_name

  ```
  ![Department wise Category Incident Count](https://github.com/Navyaka/Incident-Tracker/assets/79855759/5fe1fc21-ef8a-44c2-895d-b8611ea416af)

  ```
  --Average Resolution Time for each Type of Priority: Calculate the average time taken to resolve incidents based on priority levels.
   SELECT priority,
       Avg(Datediff(day, Created_At, Resolved_At)) AS
       'Average Days to Resolve'
  FROM   incident
        WHERE  incident_status_id = 6
  Group By priority
  ```
  ![Average Resolution Time for each Type of Priority](https://github.com/Navyaka/Incident-Tracker/assets/79855759/9f3fb1d4-ab82-40ee-89a4-ad6ca9c31322)
  ```
  --Closed Incidents without Proper Resolution: Identify incidents that were closed without a proper resolution.
  SELECT Id, Reopen_Count
  FROM   incident
  WHERE  reopen_count > 0
  ORDER  BY reopen_count DESC 
  ```
  ![Closed Incidents without Proper Resolution](https://github.com/Navyaka/Incident-Tracker/assets/79855759/5fcd4f25-228c-4923-92b2-6ff462d832d8)

  ```
  --Employee Leaderboard: Rank employees based on their incident count and display Employee Names with Rank, Incident count, Average resolution time in days.
  Select Top 15 e.User_Name 'User Name',
       Rank()
         OVER (
           ORDER BY Count(i.ID) DESC) AS 'Rank',
       count(i.Id) 'No of Resolved Incidents',
  Avg(Datediff(day, i.Created_At, i.Resolved_At)) AS 'Avg Resolution Time Days'
  from Incident i
  left join Employee	e on e.id = i.Resolved_By
  where Incident_Status_Id = 6
  group by e.User_Name
  ```
  ![Employee Leaderboard](https://github.com/Navyaka/Incident-Tracker/assets/79855759/d52ce94d-e308-44d3-8c6a-2af4e909b22c)

  ```
  --View for Incident Statistics: Create a view that includes detailed statistics for each resolved incident, such as the Id, Name of employee resolved, opened time, resolved time, time taken to resolve, priority, and average time taken for that particular priority type of that incident.
  CREATE VIEW Incident_Statistics AS
  Select i.Id Id, e.User_Name 'User Name', i.Created_At, i.Resolved_At, Datediff(day, i.Created_At, i.Resolved_At) as Resolved_In, i.Priority, pa.[Avg Resolution Time Days]
  from Incident i
  Inner join Employee	e on e.id = i.Resolved_By
  Inner Join (Select Priority,  
  Avg(Datediff(day, Created_At, Resolved_At)) AS 'Avg Resolution Time Days'
  from Incident
  where Incident_Status_Id = 6
  group by Priority) Pa on pa.Priority = i.Priority
  where Incident_Status_Id = 6
  group by i.Id, e.User_Name, i.Created_At, i.Resolved_At, Datediff(day, i.Created_At, i.Resolved_At), i.Priority, pa.[Avg Resolution Time Days]

  SELECT * FROM Incident_Statistics

  ```
  ![View for Incident Statistics](https://github.com/Navyaka/Incident-Tracker/assets/79855759/bc207498-fe24-44f8-89a9-68f7003ac15f)

  ```
  --Stored Procedure for Employee Employee Leaderboard on Priority(Input Parameter): Develop a stored procedure that generates a leaderboard of employees based on incident count with priority as input parameter. Display Employee Names with Rank, Incident count, Priority, Average resolution time in days.
  Create Procedure GetEmployeeLeaderboardByPriority
    @Priority NVARCHAR(50)
  AS
  BEGIN
  SELECT
        e.User_Name, i.Priority,
		Rank()
         OVER (
          Partition by i.Priority ORDER BY Count(i.ID) DESC) AS 'Rank',
        COUNT(i.Id) AS Incident_Count,
        AVG(DATEDIFF(Day, i.Created_At, i.Resolved_At)) AS Avg_Resolution_Time_Days
    FROM
        Incident i
    INNER JOIN
        Employee e ON i.Resolved_By = e.Id
    WHERE
        i.Priority = @Priority AND 
		i.Resolved_At IS NOT NULL
    GROUP BY
        e.User_Name,i.Priority
  END;

  EXEC GetEmployeeLeaderboardByPriority '1 - Critical'
  ```
  ![Stored Procedure for Employee Employee Leaderboard on Priority(Input Parameter)](https://github.com/Navyaka/Incident-Tracker/assets/79855759/9e51ec40-8a94-4860-843b-e6ae4790cfd9)


- Connect to SQL server and import data from SQL Server to Power BI
- **Data Visualization Power BI Dashboard:** 
  - Card Charts for KPI’s of Total Incident Count and Active Incident Count

  - Pie Chart for Incident Count on Status basis
  - Donut Chart for Category Incident Count
  - Bar Chart for Department Incident Count
  - Import ‘Incident_Statistics’ view data from SQL server to Power BI to create a table. Now apply conditional formatting on ‘time taken to resolve’ and ‘average time taken for that particular priority type of that incident’ columns to create heatmap visualization.
  - Add Slicers for Status, Category, Urgency, Priority to control the charts
  - Remove Interaction of ‘Status’ slicer from Heatmap, and KPI’s 
- Upload the Report (.pbix file) in the PowerBI workspace from your local machine.
- Review the report to ensure everything looks as expected. Check for any errors or issues that need to be addressed. Publish the report when ready.

  ![Incident_Tracker_Dashboard](https://github.com/Navyaka/Incident-Tracker/assets/79855759/fcdd261a-b1df-46f3-b72b-197b95ee0331)
