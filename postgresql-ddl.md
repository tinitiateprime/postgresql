![PostgreSQL Tinitiate Image](postgresql_tinitiate.png)

# PostgreSQL
&copy; TINITIATE.COM

##### [Back To Context](./README.md)

# DDL - Data Definition Language
* In PostgreSQL, DDL (Data Definition Language) refers to a set of SQL commands used to define, modify, and remove the structure of database objects. These objects include tables, indexes, views, schemas, sequences, and more. 
* DDL statements are essential for setting up the database schema, defining relationships between tables, and ensuring data integrity. They provide the framework for organizing and managing data within PostgreSQL databases.

## Primary DDL commands in PostgreSQL:
### CREATE:
* Used to create new database objects like tables, indexes, views, schemas, sequences, etc.
```sql
-- Use database
USE DATABASE tinitiate;

-- Schema DDL
CREATE SCHEMA emp;
CREATE USER ti WITH PASSWORD 'Tinitiate!23';
ALTER SCHEMA emp OWNER TO ti;

-- Set the schema where you want to create the DB objects
SET search_path TO emp;

-- DDL Create Command
-- Create dept table
CREATE TABLE dept (
    deptid  integer,
    dname   varchar(100)
);

-- Create emp table
CREATE TABLE emp (
    empid    integer,
    ename    varchar(100),
    sal      numeric(7,2),
    deptid   integer
);

-- Create projects table
CREATE TABLE projects (
    ProjectID      integer,
    ProjectName    varchar(100),
    ProjectBudget  numeric(12,2)
);

-- Create empprojects table
CREATE TABLE EmpProjects (
    EP_ID       integer,
    EmpID       integer,
    ProjectID   integer,
    StartDate   date,
    EndDate     date
);
```

### ALTER:
* Modifies the structure of existing database objects, such as adding or dropping columns from a table.
```sql
-- Alter table "dept": Rename the column "dname" to "department_name".
ALTER TABLE emp.dept RENAME COLUMN dname TO department_name;
-- To change back to previous
ALTER TABLE emp.dept RENAME COLUMN department_name TO dname;

-- Alter table "emp": Add a new column called "hire_date" of type DATE.
ALTER TABLE emp.emp ADD COLUMN hire_date DATE;
-- To change back to previous
ALTER TABLE emp.emp DROP COLUMN hire_date;

-- Alter table "projects":
-- Change the data type of the column "ProjectBudget" to DECIMAL(12,2).
ALTER TABLE emp.projects ALTER COLUMN ProjectBudget TYPE DECIMAL(12,2);
-- To change back to previous
ALTER TABLE emp.projects ALTER COLUMN ProjectBudget TYPE NUMERIC(12,2);

-- Alter table "EmpProjects": Drop the column "EndDate".
ALTER TABLE emp.EmpProjects DROP COLUMN EndDate;
-- To change back to previous
ALTER TABLE emp.EmpProjects ADD COLUMN EndDate DATE;
```

### DROP:
* Deletes existing database objects, such as tables, indexes, or views.
```sql
-- To drop dept table in emp schema
DROP TABLE emp.dept;

-- To again create it
CREATE TABLE emp.dept (
    deptid  integer,
    dname   varchar(100)
);
```

##### [Back To Context](./README.md)
***
| &copy; TINITIATE.COM |
|----------------------|