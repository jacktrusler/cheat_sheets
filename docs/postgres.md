# PostgreSQL
![PostgreSQL](./svgs/postgreql.svg "PostgreSQL")
*Big elephant database, everyone uses it so may as well get used to it ig.* 

### Installing on CentOS

[Here's the installation URL](https://www.postgresql.org/download/linux/redhat/)  
**Install the repository RPM:**

    sudo dnf install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm

**Install PostgreSQL:**

    sudo dnf install -y postgresql15-server

**Optionally initialize the database and enable automatic start:**

    sudo /usr/pgsql-15/bin/postgresql-15-setup initdb
    sudo systemctl enable postgresql-15
    sudo systemctl start postgresql-15

### Get all up in the data's business
`psql` to get into the database shell, might have to --   
`su postgres` first. By default postgresql adds a user named postgres with no password and all privileges.

### Commands you will forget for sure and have to look up on a cheat sheet lmao
[Good cheatsheet](https://postgrescheatsheet.com/#/databases)  

| Command   | Description    |
|--------------- | --------------- |
| `\l`   | list databases   |
|`\du` | shows all users and permissions  |
|`\c database-name` | moves you into database  |
|`\dt` | lists all tables in current schema (how things are categorized in db)  |
|`\d+ table-name` | lists table deets  |
|`SHOW hba_file` | shows the config file for postgres if you have sudo access  |
|`\password user` | changes password for user if you have appropriate permissions|
|`\conninfo` | shows the port the database is running on|
|`alter user <user> with superuser;`| makes user a superuser if current acc has superuser|

    CREATE DATABASE mydb  

    CREATE TABLE IF NOT EXISTS sometable (
      id SERIAL PRIMARY KEY,
      name VARCHAR(50) NOT NULL,    
      phone NUMERIC(10) NOT NULL
    );

    INSERT INTO sometable (param1, param2) VALUES (val1, val2);
    
import schema using .sql file  

    psql -h hostname -d databasename -U username -f file.sql -L logfile.log
    psql -h jsonbournesupremacy -d jsonb -U postgres -f file.sql -L logfile.log

If you're using DBeaver, when you set up a database connection click on the PostgreSQL tab and check "Show all databases"  
(why is this unchecked by default I have no idea)
### Querying 
[SQLbolt](https://sqlbolt.com/). A good intro to SQL.  
[w3Schools](https://www.w3schools.com/sql/sql_distinct.asp). A good tutorial / reference to sql statements.  

| Command   | Description    |
|--------------- | --------------- |
| `SELECT column1, column2 FROM table` | grabs all from columns |
| `WHERE condition AND/OR condition2` | Conditional |
| `INNER LEFT RIGHT FULL JOIN table2` | Joins a second table with 4 options of overlap |
| `GROUP BY column` | Groups all 

__Select__  

```sql
    SELECT role, AVG(years_employed) as Average_years_employed
    FROM employees
    GROUP BY role;
```

__Joins__  
```sql
    SELECT title, prices FROM movies
    INNER JOIN expenses  
      ON movies.id = expenses.movie_id;
```
INNER JOIN matches only where both tables share a key (movies.id = expenses.movie_id)
```sql
    SELECT title, prices FROM movies
    LEFT JOIN expenses  
      ON movies.id = expenses.movie_id;
```
LEFT JOIN gives back all values from first table (movies), ids that don't match have null values.
```sql
    SELECT title, prices FROM movies
    RIGHT JOIN expenses  
      ON movies.id = expenses.movie_id;
```
RIGHT JOIN gives back all values from second table (movies), ids that don't match have null values.
```sql
    SELECT title, prices FROM movies
    FULL JOIN expenses  
      ON movies.id = expenses.movie_id;
```
FULL JOIN returns all values from both tables, null values appear in each respectively.

__Group by__  
The GROUP BY clause works by grouping rows that have the same value in the column specified.

There's a HAVING clause which is used specifically with the GROUP BY clause to allow us to filter grouped
rows from the result set. Without GROUP BY, a WHERE clause will work instead.
```sql
    SELECT role, SUM(years_employed)
    FROM employees
    GROUP BY role
    HAVING role = "Engineer";
```


## DBeaver
The database solution for people who like free stuff. 

If all databases aren't appearing after connection try: 

right click on connection > Edit connnection > PostgreSQL tab > check "show all databases"
