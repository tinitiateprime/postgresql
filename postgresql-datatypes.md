![PostgreSQL Tinitiate Image](postgresql_tinitiate.png)

# PostgreSQL
&copy; TINITIATE.COM

##### [Back To Context](readme.md)

# PostgreSQL Data Types
* PostgreSQL provides a range of data types, each with unique attributes and advantages.

## Commonly used data types in PostgreSQL
* **Numeric** data types are used to store numbers. There are several different numeric data types available, each with its own range and precision. The most common numeric data types are:

    * **INTEGER (INT)** - Stores whole numbers from -2147483648 to 2147483647. 
        * **Example:** `age INT`.

    * **BIGINT** - Stores whole numbers from -9223372036854775808 to 9223372036854775807. 
        * **Example:** `employee_id BIGINT`.
    * **DECIMAL** - Stores numbers with a fixed number of decimal places. For example, DECIMAL(5,2) can store numbers from -9999.99 to 9999.99. 
        * **Example:** `price DECIMAL(10,2)`.
    * **NUMERIC** - Stores numbers with a variable number of decimal places. 
        * **Example:** `quantity NUMERIC`.
    * **REAL** - Stores floating-point numbers with 6 digits of precision. 
        * **Example:** `temperature REAL`.
    * **DOUBLE PRECISION** - Stores floating-point numbers with 15 digits of precision. 
        * **Example:** `distance DOUBLE PRECISION`.
* **Date and Time** data types are used to store dates and times. The most common date and time data types are:
    * **DATE** - Stores a date in the format YYYY-MM-DD. 
        * **Example:** `birthdate DATE`.

    * **TIMESTAMP** - Stores a date and time with timezone in the format YYYY-MM-DD HH:MM:SS. 
        * **Example:** `created_at TIMESTAMP`.
    * **TIMESTAMPTZ** - Stores a date and time with timezone in the format YYYY-MM-DD HH:MM:SS+TZ. 
        * **Example:** `updated_at TIMESTAMPTZ`.
    * **TIME** - Stores a time of day. 
        * **Example:** `appointment_time TIME`.
* **String** data types are used to store text. The most common string data types are:
    * **CHARACTER(n)** - Stores a fixed-length string of length n. 
        * **Example:** `country_code CHARACTER(2)`.

    * **VARCHAR(n)** - Stores a variable-length string with a maximum length of n. 
        * **Example:** `name VARCHAR(50)`.
    * **TEXT** - Stores variable-length strings without any length limit. 
        * **Example:** `description TEXT`.
* **Binary** data types are used to store binary data, such as images or files.
    * **BYTEA** - Stores binary data in variable-length format. 
        * **Example:** `image BYTEA`.
* **Additional Data types** include:
    * **JSON** - Stores JSON data. 
        * **Example:** `metadata JSON`.
    
    * **JSONB** - Stores JSON data in a binary format. 
        * **Example:** `preferences JSONB`.
    * **UUID** - Stores a universally unique identifier. 
        * **Example:** `session_id UUID`.
    * **BOOLEAN** - Stores true or false values. 
        * **Example:** `is_active BOOLEAN`.
    * **MONEY** - Stores monetary values. 
        * **Example:** `salary MONEY`.
    * **XML** - Stores XML data. 
        * **Example:** `document XML`.
    * **ARRAY** - Stores arrays of any data type. 
        * **Example:** `tags TEXT[]`.
    * **TSVECTOR** - Stores full-text search data. 
        * **Example:** `search_vector TSVECTOR`.
    * **TSQUERY** - Stores full-text search queries. 
        * **Example:** `query TSQUERY`.
    * **CIDR** - Stores IPv4 or IPv6 network addresses. 
        * **Example:** `ip_address CIDR`.
    * **INET** - Stores IPv4 or IPv6 host addresses. 
        * **Example:** `host_address INET`.
    * **MACADDR** - Stores MAC addresses. 
        * **Example:** `mac_address MACADDR`.
    * **BIT(n)** - Stores fixed-length bit strings. 
        * **Example:** `permissions BIT(8)`.
    * **VARBIT(n)** - Stores variable-length bit strings. 
        * **Example:** `flags VARBIT(16)`.

##### [Back To Context](readme.md)