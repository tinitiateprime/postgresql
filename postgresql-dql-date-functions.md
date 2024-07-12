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
-- Add a specific number of days from a date
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
### Weekday Function (EXTRACT):
* Returns the day of the week (0 for Sunday, 1 for Monday, etc.) from a date or timestamp.
```sql
-- Retrieve the day of the week (0 for Sunday, 1 for Monday, etc.) from
-- the 'hiredate' column
SELECT EXTRACT(DOW FROM hiredate) FROM employees.emp;
```
### Date to String (Various Formats)
* You can convert a date to a string in various formats using the `TO_CHAR(arg1, arg2)` function.
```sql
-- YYYY-MM-DD
SELECT empno, ename, TO_CHAR(hiredate, 'YYYY-MM-DD') AS hiredate
FROM employees.emp;

-- MM/DD/YYYY
SELECT empno, ename, TO_CHAR(hiredate, 'MM/DD/YYYY') AS hiredate
FROM employees.emp;

-- DD/MM/YYYY
SELECT empno, ename, TO_CHAR(hiredate, 'DD/MM/YYYY') AS hiredate
FROM employees.emp;

-- Mon DD, YYYY
SELECT empno, ename, TO_CHAR(hiredate, 'Mon DD, YYYY') AS hiredate
FROM employees.emp;

-- YYYYMMDD
SELECT empno, ename, TO_CHAR(hiredate, 'YYYYMMDD') AS hiredate
FROM employees.emp;

-- DD-MM-YYYY
SELECT empno, ename, TO_CHAR(hiredate, 'DD-MM-YYYY') AS hiredate
FROM employees.emp;

-- YYYY/MM/DD
SELECT empno, ename, TO_CHAR(hiredate, 'YYYY/MM/DD') AS hiredate
FROM employees.emp;

-- DD MMM YYYY
SELECT empno, ename, TO_CHAR(hiredate, 'DD Mon YYYY') AS hiredate
FROM employees.emp;
```
### DateTime to String (Various Formats)
* To convert a datetime to a string in various formats, you can use the `TO_CHAR(arg1, arg2)` function, same like date to a string but with different format codes.
```sql
-- YYYY-MM-DD HH:MI:SS
SELECT empno, ename, TO_CHAR(hiredate, 'YYYY-MM-DD HH24:MI:SS') AS hiredate
FROM employees.emp;
-- hiredate doesn't have time parts, so only date will show up

-- Using CURRENT_TIMESTAMP
SELECT TO_CHAR(CURRENT_TIMESTAMP, 'YYYY-MM-DD HH24:MI:SS') AS datetimetostring;

-- YYYY-MM-DD HH:MI:SS:MMM
SELECT TO_CHAR(CURRENT_TIMESTAMP, 'YYYY-MM-DD HH24:MI:SS.MS') AS datetimetostring;

-- DD Mon YYYY HH:MI:SS:MMM
SELECT TO_CHAR(CURRENT_TIMESTAMP, 'DD Mon YYYY HH24:MI:SS.MS') AS datetimetostring;

-- Mon DD YYYY HH:MI:SS:MMMAM (or PM)
SELECT TO_CHAR(CURRENT_TIMESTAMP, 'Mon DD YYYY HH:MI:SS.MSAM') AS datetimetostring;
```
### String to Date (Various Formats)
* You can convert a string in various formats to a date using the `TO_DATE(arg2, arg3)` function.
```sql
-- YYYY-MM-DD
SELECT TO_DATE('2023-04-15', 'YYYY-MM-DD') AS date;

-- MM/DD/YYYY
SELECT TO_DATE('04/15/2023', 'MM/DD/YYYY') AS date;

-- DD.MM.YYYY
SELECT TO_DATE('15.04.2023', 'DD.MM.YYYY') AS date;

-- Mon DD, YYYY
SELECT TO_DATE('Apr 15, 2023', 'Mon DD, YYYY') AS date;

-- YYYYMMDD
SELECT TO_DATE('20230415', 'YYYYMMDD') AS date;
```
### String to DateTime (Various Formats)
* You can convert a string in various formats to a datetime using the `TO_TIMESTAMP(arg2, arg3)` function.
```sql
-- YYYY-MM-DD HH:MI:SS
SELECT TO_TIMESTAMP('2023-04-15 13:30:45', 'YYYY-MM-DD HH24:MI:SS') AS datetime;

-- MM/DD/YYYY HH:MI:SS
SELECT TO_TIMESTAMP('04/15/2023 13:30:45', 'MM/DD/YYYY HH24:MI:SS') AS datetime;

-- DD.MM.YYYY HH:MI:SS
SELECT TO_TIMESTAMP('15.04.2023 13:30:45', 'DD.MM.YYYY HH24:MI:SS') AS datetime;

-- Mon DD YYYY HH:MI:SS:MMM
SELECT TO_TIMESTAMP('Apr 15 2023 01:30:45:375', 'Mon DD YYYY HH24:MI:SS.MS') AS datetime;

-- YYYYMMDD HH:MI:SS
SELECT TO_TIMESTAMP('20230415 13:30:45', 'YYYYMMDD HH24:MI:SS') AS datetime;
```
### DateTime and TimeZone
#### Date, Timezones, UTC, and Offsets
* **Date and Time**: In computing, dates and times are represented using various data types. A common approach is to use a datetime data type that includes both date and time information.
* **Timezones**: Timezones are regions of the Earth that have the same standard time. Each timezone is usually offset from Coordinated Universal Time (UTC) by a certain number of hours and minutes. For example, Eastern Standard Time (EST) is UTC-5, meaning it is 5 hours behind UTC.
* **UTC**: Coordinated Universal Time (UTC) is the primary time standard by which the world regulates clocks and time. It is not adjusted for daylight saving time. UTC is often used as a reference point for converting between different timezones.
* **Offsets**: An offset is the difference in time between a specific timezone and UTC. It is usually expressed as a positive or negative number of hours and minutes. For example, UTC+2 means the timezone is 2 hours ahead of UTC, while UTC-8 means the timezone is 8 hours behind UTC.
* **Applying an Offset to a Date Datatype Column**: To apply an offset to a datetime column, you can use the `INTERVAL` function.
```sql
-- This query will return the value of hiredate adjusted by 5 hours
SELECT hiredate + INTERVAL '5 hours' AS adjusted_datetime
FROM employees.emp;
```
#### Cast a DateTime to DateTime with TimeZone (in UTC, EST, and IST TimeZones)
* You can cast a datetime to a datetime with timezone using the `AT TIME ZONE` function.
```sql
SELECT 
    '2024-04-17 15:30:00'::timestamp AS OriginalDateTime,
    '2024-04-17 15:30:00'::timestamp AT TIME ZONE 'UTC' AS UTCDateTime,
    '2024-04-17 15:30:00'::timestamp AT TIME ZONE 'EST' AS ESTDateTime,
    '2024-04-17 15:30:00'::timestamp AT TIME ZONE 'IST' AS ISTDateTime;
```
* Demonstration in Code Block
```sql
DO $$ 
DECLARE
    datetime TIMESTAMP := '2024-04-17 15:30:00';
    utcDateTime TIMESTAMP;
    estDateTime TIMESTAMP;
    istDateTime TIMESTAMP;
BEGIN
    -- Cast the variables to UTC, EST, and IST
    utcDateTime := datetime AT TIME ZONE 'UTC';
    estDateTime := datetime AT TIME ZONE 'EST';
    istDateTime := datetime AT TIME ZONE 'IST';

    -- Raise notice for output
    RAISE NOTICE 'OriginalDateTime: %', datetime;
    RAISE NOTICE 'UTCDateTime: %', utcDateTime;
    RAISE NOTICE 'ESTDateTime: %', estDateTime;
    RAISE NOTICE 'ISTDateTime: %', istDateTime;
END $$;
```
#### Cast a DateTime Timezone to another TimeZone
* As SQL
```sql
SELECT 
    '2024-04-17 15:30:00'::timestamp AT TIME ZONE 'UTC' AS UTCDateTime,
    ('2024-04-17 15:30:00'::timestamp AT TIME ZONE 'UTC') AT TIME ZONE 'EST' AS ESTDateTime;
```
* As code block
```sql
DO $$ 
DECLARE
    utcDateTime TIMESTAMP := '2024-04-17 15:30:00'::timestamp AT TIME ZONE 'UTC';
    estDateTime TIMESTAMP;
BEGIN
    estDateTime := utcDateTime AT TIME ZONE 'EST';

    RAISE NOTICE 'UTCDateTime: %', utcDateTime;
    RAISE NOTICE 'ESTDateTime: %', estDateTime;
END $$;
```


##### [Back To Context](./README.md)
***
| &copy; TINITIATE.COM |
|----------------------|