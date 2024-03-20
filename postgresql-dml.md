![PostgreSQL Tinitiate Image](postgresql_tinitiate.png)

# PostgreSQL
&copy; TINITIATE.COM

##### [Back To Context](./README.md)

# DML - Data Manipulation Language
* In PostgreSQL, DML (Data Manipulation Language) consists of SQL commands that allow users to manipulate data within a database.
* DML commands are used to perform operations such as inserting, updating and  deleting.

## Primary DML commands in PostgreSQL:
### INSERT:
* This command is used to add new rows of data into a table.
* You can specify the values to be inserted into each column of the table.
```sql
-- Insert with column created order
INSERT INTO emp.dept (deptid, dname) VALUES (1000, 'PRODUCTION');
INSERT INTO emp.emp (empid, ename, sal, deptid) VALUES
(101, 'John Doe', 5000.00, 1000);
INSERT INTO emp (empid, ename, sal, deptid) VALUES
(102, 'Jane Smith', 6000.00, 2000);

-- Insert with column names, use positional values
INSERT INTO emp.dept VALUES (2000, 'FOUNDRY');

-- Insert with column names, different order
INSERT INTO emp.dept (dname, deptid) VALUES ('STORES', 3000);

-- Insert without schema mentioning untill you are in the same schema
INSERT INTO dept (deptid, dname) VALUES (4000, 'SALES');

-- Insert all, Insert more data in single insert
INSERT INTO emp.dept (deptid, dname)
VALUES 
    (111, 'TECHNOLOGY'),
    (211, 'FACTORY'),
    (311, 'RETAIL');

-- Insert with select statement (Copy data from another table)
-- Create table dept1
CREATE TABLE emp.dept1 (
    deptid INT,
    dname VARCHAR(100)
);
-- Insert data from dept into dept1
INSERT INTO emp.dept1 (deptid, dname)
SELECT deptid, dname
FROM emp.dept;

-- Incorrect data violations
-- Primary Key violation
INSERT INTO emp.dept (deptid, dname) VALUES (7000, 'MARKETING');

-- DataType Size violation
INSERT INTO emp.dept (deptid, dname) 
VALUES (6, 'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA');

-- Foreign Key violation
INSERT INTO emp VALUES (16, '4A', 11000.00, 6);
```

### UPDATE:
* This command is used to modify existing data in a table.
* You can update one or more columns of existing rows based on a specified condition.
```sql
-- Update salary of an employee
UPDATE emp SET sal = 6200.00 WHERE empid = 101;

-- Update project end date
UPDATE EmpProjects SET EndDate = '2024-06-01'
 WHERE ProjectID = 1 AND EmpID = 101;
```

### DELETE:
* This command is used to remove one or more rows from a table based on a specified condition.
```sql
-- Delete a dept
DELETE FROM dept WHERE deptid = 3000;

-- Remove an employee
DELETE FROM emp WHERE empid = 101;
```

##### [Back To Context](./README.md)