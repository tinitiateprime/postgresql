![PostgreSQL Tinitiate Image](postgresql_tinitiate.png)

# PostgreSQL
&copy; TINITIATE.COM

##### [Back To Context](./README.md)

# DDL - Constraints
* In PostgreSQL, Data Definition Language (DDL) constraints are rules applied to the structure of a database table.
* These constraints ensure data integrity and consistency by enforcing certain conditions on the data being inserted, updated, or deleted in the table.
* These constraints are applied to columns when the table is created or altered.

## Here are the common types of DDL constraints in PostgreSQL:
### NOT NULL Constraint:
* This constraint ensures that a column cannot contain NULL values.
* It enforces that every row in the table must have a value for that column.
```sql
-- NOT NULL Constraint on empid and ename columns in the emp table
ALTER TABLE emp
ALTER COLUMN empid SET NOT NULL,
ALTER COLUMN ename SET NOT NULL;

-- We can also specify this constraint while table creation; for example
CREATE TABLE staff (
    id SERIAL PRIMARY KEY,
    name VARCHAR NOT NULL,
    age INTEGER
);
```

### UNIQUE Constraint:
* This constraint ensures that the values in a column (or a group of columns) are unique across all rows in the table.
```sql
-- UNIQUE Constraint to the empid column in the emp table
ALTER TABLE emp
ADD CONSTRAINT unique_empid UNIQUE (empid);

-- We can also specify this constraint while table creation; for example
CREATE TABLE students (
    student_id SERIAL,
    email VARCHAR UNIQUE,
    name VARCHAR
);
```

### CHECK Constraint:
* This constraint specifies a condition that must be satisfied for each row in the table.
* It allows you to define custom rules for data validation.
```sql
-- CHECK Constraint on ProjectBudget column in the projects table
ALTER TABLE projects
ADD CONSTRAINT chk_project_budget CHECK (ProjectBudget > 0);

-- We can also specify this constraint while table creation; for example
CREATE TABLE products (
    product_id SERIAL PRIMARY KEY,
    product_name VARCHAR,
    price DECIMAL,
    quantity INTEGER,
    CHECK (price > 0 AND quantity >= 0)
);
```

### PRIMARY KEY Constraint:
* This constraint uniquely identifies each record in a table and ensures that there are no duplicate values in the specified column(s).
```sql
-- PRIMARY KEY Constraint on deptid column in the dept table
ALTER TABLE dept
ADD CONSTRAINT pk_dept PRIMARY KEY (deptid);

-- PRIMARY KEY Constraint on empid column in the emp table
ALTER TABLE emp 
ADD CONSTRAINT pk_emp PRIMARY KEY (empid);

-- PRIMARY KEY Constraint on projectid column in the projects table
ALTER TABLE projects 
ADD CONSTRAINT pk_projects PRIMARY KEY (ProjectID);

-- PRIMARY KEY Constraint on EP_ID column in the EmpProjects table
ALTER TABLE EmpProjects
ADD CONSTRAINT pk_empprojects PRIMARY KEY (EP_ID);

-- We can also specify this constraint while table creation; for example
CREATE TABLE customers (
    customer_id INTEGER PRIMARY KEY,
    order_date DATE
);
```

### FOREIGN KEY Constraint:
* This constraint establishes a relationship between two tables.
* It ensures referential integrity by enforcing a link between the data in the foreign key column(s) and the primary key or unique key in another table.
```sql
-- FOREIGN KEY Constraint on deptid column in the emp table referencing
-- the deptid column in the dept table
ALTER TABLE emp
ADD CONSTRAINT fk_deptid FOREIGN KEY (deptid) REFERENCES dept(deptid);

-- FOREIGN KEY Constraints on EmpID and ProjectID columns in the
-- EmpProjects table referencing the respective columns in the
-- emp and projects tables
ALTER TABLE EmpProjects
ADD CONSTRAINT fk_emp_id FOREIGN KEY (EmpID) REFERENCES emp(empid),
ADD CONSTRAINT fk_project_id FOREIGN KEY (ProjectID)
 REFERENCES projects(ProjectID);

-- We can also specify this constraint while table creation; for example
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    customer_id INTEGER REFERENCES customers(customer_id),
    -- Assuming there is a column customer_id in customers table
    order_date DATE
);
```

##### [Back To Context](./README.md)
***
| &copy; TINITIATE.COM |
|----------------------|