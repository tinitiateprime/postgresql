![PostgreSQL Tinitiate Image](postgresql_tinitiate.png)

# PostgreSQL
&copy; TINITIATE.COM

##### [Back To Context](./README.md)

# DQL - Date Functions
* Date functions in PostgreSQL are used to perform various operations on date and timestamp data stored in the database.
* They allow for manipulation, extraction, formatting, and calculation of dates and times.

## Date functions in PostgreSQL:
### Current Date (CURRENT_DATE):
* Returns the current date.
```sql
-- Retrieve the current date
SELECT CURRENT_DATE;
```
### Current Timestamp (CURRENT_TIMESTAMP):
* Returns the current date and time.
```sql
-- Retrieve the current timestamp
SELECT CURRENT_TIMESTAMP;
```
### Extract Function (EXTRACT):
* Extracts a specific component (such as year, month, day) from a date or timestamp.
```sql
-- Extract the year from the 'hiredate' column in the employees table
SELECT EXTRACT(YEAR FROM hiredate) FROM employees.emp;
```
### Age Function (AGE):
* Calculates the difference between a date or timestamp and the current date or timestamp, returning the result as an interval.
```sql
-- Calculate the age of each employee based on their 'hiredate'
SELECT AGE(hiredate) FROM employees.emp;
```
### Date Trunc Function (DATE_TRUNC):
* Truncates a date or timestamp to a specific precision (such as year, month, day).
```sql
-- Truncate the 'hiredate' column to the nearest month
SELECT DATE_TRUNC('month', hiredate) FROM employees.emp;
```
### Date Part Function (DATE_PART):
* Returns a specific component (such as year, month, day) from a date or timestamp.
```sql
-- Retrieve the day of the month from the 'hiredate' column
SELECT DATE_PART('day', hiredate) FROM employees.emp;
```
### Date Addition/Subtraction:
* Adds or subtracts a specified interval (such as days, months) from a date or timestamp.
```sql
-- Add or subtract a specific number of days from a date
SELECT hiredate + INTERVAL '7 days' FROM employees.emp;

-- Subtract 6 months from the 'hiredate' column in the employees table
SELECT hiredate - INTERVAL '6 months' FROM employees.emp;
```
### Date Formatting (TO_CHAR):
* Formats a date or timestamp as a string according to a specified format.
```sql
-- Format the 'hiredate' column in a specific date format
SELECT TO_CHAR(hiredate, 'YYYY-MM-DD') FROM employees.emp;
```
### Weekday Function (EXTRACT and DATE_PART):
* Returns the day of the week (0 for Sunday, 1 for Monday, etc.) from a date or timestamp.
```sql
-- Retrieve the day of the week (0 for Sunday, 1 for Monday, etc.) from
-- the 'hiredate' column
SELECT EXTRACT(DOW FROM hiredate) FROM employees.emp;
```

##### [Back To Context](./README.md)